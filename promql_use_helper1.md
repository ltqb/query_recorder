Metrics类型
根据不同监控指标之间的差异，Prometheus定义了4中不同的指标类型(metric type)：Counter（计数器）、Gauge（仪表盘）、Histogram（直方图）、Summary（摘要）。
最常用的两种数据类型:

counter：此类型的指标其工作方式和计数器一样，只增不减（除非系统发生重置）。例如 node_cpu_seconds_total{mode="idle"} cpu空闲时间。
Gauge：用于反应该样本的当前状态，该样本数可增可减。例如可用内存大小。
较难理解的两种数据类型:
Histogram与Summary：都是比例型的数值，统计数据的分布情况，如最小值，最大值，中间值，中位数，75百分位，90百分位，95百分位，98百分位，99百分位和99.9百分位（percentiles），近似于百分比估算数值。两种区别在于summary分位数是客户端计算上报，histogram中位数涉及服务端计算。
此处Http_response_time来解释此类型数据:
http请求响应时间：一次用户http请求在系统传输和执行过程中总共花费的时间，nginx的access_log有一列就为此时间。
需求:抓取某个业务当天access_log，并监控用户的访问时间，如何操作？
方案1：所有请求访问时间总和求平均值 假如当天100w次访问平均值为0.05s
情景1： 假如当天发生线上故障：中午1:00 - 1:05,整体用户请求响应时间0.5-1s。此种方案无法反映此段故障。
情景2： 慢请求，有一小部分用户由于程序bug，或系统或其他原因，请求响应时间比平均值大很多，甚至5s，10s 。此种方案也无法反应此情况。
方案2：基于Histogram的metrics类型（prometheus提供了一个基于Histogram算法的函数可供直接使用）分别统计出各段响应时间的用户:
~=0.0.5s 的请求数 ， 0~0.05s的请求数 ，>2s的请求数 ，>10s的请求数
函数介绍
以下两个函数都用于counter类型数据
increase( )
eg:某一分钟cpu使用时间 increase()[1m]=该分钟末cpu使用时间-该分钟初cpu使用时间

rate(）
eg:某一分钟cpu使用率 rate()[1m]=某一分钟cpu使用时间/60s
即 rate()=increase( )/60s

topk(3,rate(监控项(表达式))) 可用于监控单核cpu使用率前3 > xxx告警

sum() by ()
将某表达式查出的所有数据相加即为sum(), 再将相加的值通过某个特性进行分组，即为 sum() by()
例如监控某台主机（非单核）的cpu使用率

count()
对于满足某个表达式的监控项进行计数
eg: cpu使用率大于80%的服务器超过30台告警

_over_time()
以下函数允许聚合给定范围向量的每个系列随时间的变化并返回具有每系列聚合结果的即时向量：

avg_over_time(range-vector): 范围向量内每个度量指标的平均值。
min_over_time(range-vector): 范围向量内每个度量指标的最小值。
max_over_time(range-vector): 范围向量内每个度量指标的最大值。
sum_over_time(range-vector): 范围向量内每个度量指标的求和值。
count_over_time(range-vector): 范围向量内每个度量指标的样本数据个数。
quantile_over_time(scalar, range-vector): 范围向量内每个度量指标的样本数据值分位数，φ-quantile (0 ≤ φ ≤ 1)
stddev_over_time(range-vector): 范围向量内每个度量指标的总体标准偏差。
`stdvar_over_time(range-vector): 范围向量内每个度量指标的总体标准方差。

eg：
该主机1天内node_load5的最大值
max_over_time (node_load5{instance="1.1.1.1:9100"}[24h])

1h内的内存使用率 100 * (1 - ((avg_over_time(node_memory_MemFree_bytes[1h]) + avg_over_time(node_memory_Cached_bytes[1h]) + avg_over_time(node_memory_Buffers_bytes[1h])) / avg_over_time(node_memory_MemTotal_bytes[1h])))
