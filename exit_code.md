Exit code 
https://komodor.com/learn/exit-codes-in-containers-and-kubernetes-the-complete-guide/#:~:text=Exit%20Code%20139%20means%20that,it%20does%20not%20have%20access.https://komodor.com/learn/exit-codes-in-containers-and-kubernetes-the-complete-guide/#:~:text=Exit%20Code%20139%20means%20that,it%20does%20not%20have%20access.
CODE #	NAME	WHAT IT MEANS
Exit Code 0	Purposely stopped	Used by developers to indicate that the container was automatically stopped
Exit Code 1	Application error	Container was stopped due to application error or incorrect reference in the image specification
Exit Code 125	Container failed to run error	The docker run command did not execute successfully
Exit Code 126	Command invoke error	A command specified in the image specification could not be invoked
Exit Code 127	File or directory not found	File or directory specified in the image specification was not found
Exit Code 128	Invalid argument used on exit	Exit was triggered with an invalid exit code (valid codes are integers between 0-255)
Exit Code 134	Abnormal termination (SIGABRT)	The container aborted itself using the abort() function.
Exit Code 137	Immediate termination (SIGKILL)	Container was immediately terminated by the operating system via SIGKILL signal
Exit Code 139	Segmentation fault (SIGSEGV)	Container attempted to access memory that was not assigned to it and was terminated
Exit Code 143	Graceful termination (SIGTERM)	Container received warning that it was about to be terminated, then terminated
Exit Code 255	Exit Status Out Of Range	Container exited, returning an exit code outside the acceptable range, meaning the cause of the error is not known
Below we’ll explain how to troubleshoot failed containers on a self-managed host and in Kubernetes, and provide more details on all of the exit codes listed above.

The Container Lifecycle
To better understand the causes of container failure, let’s discuss the lifecycle of a container first. Taking Docker as an example – at any given time, a Docker container can be in one of several states:

Created – the Docker container is created but not started yet (this is the status after running docker create, but before actually running the container)
Up – the Docker container is currently running. This means the operating system process managed by the container is running. This happens when you use the commands docker start or docker run can happen using docker start or docker run.
Paused – the container process was running, but Docker purposely paused the container. Typically this happens when you run the Docker pause command
Exited – the Docker container has been terminated, usually because the container’s process was killed
