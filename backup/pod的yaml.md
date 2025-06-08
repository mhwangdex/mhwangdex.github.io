```
以下是比较详细的资源清单介绍

KIND:     Pod  #资源类型
VERSION:  v1   #资源版本

FIELDS:        #资源可配置的属性，如下
apiVersion: v1   	#必选的一级属性，版本号，例如v1
kind: Pod			#必选的一级属性，资源类型，例如Pod
metadata:			#必选的一级属性，元数据
    name:			#资源名称
    namespace: test	#资源所属的名称空间，例如dev，默认为default名称空间
    labels:			#自定义标签列表
     - name:		#标签名称
spec:				#必选的一级属性
 containers:		#Pod中容器列表
 - name:			#容器名称
   image:			#容器镜像名称
   imagePullPolicy:	#镜像的拉取策略
   command:			#容器的启动命令列表，如不指定，使用打包时使用的启动命令
   args:			#容器的启动命令参数列表
   workingDir:		#容器的工作目录
   volumeMounts:	#挂载到容器内部的存储卷配置
   - name:			#引用pod定义的共享存储卷的名称
     mountPath:		#存储卷在容器内mount的绝对路径，应少于512字节
     readOnly:		#是否为只读模式
   ports:			#需要暴露的端口号列表
   - name:			#端口的名称
     containerPort:	#容器需要监听的端口号
     hostPort:		#容器所在的主机需要监听的端口号，默认与Container相同
     protocol:		#端口协议，支持TCP/UDP,默认为TCP
   env:				#容器运行前需要设置的环境变量列表
   - name:			#环境变量名称
     value:			#环境变量的值
   resources:		#资源限制和请求的设置		
     limits:		#资源最大限制的设置
        CPU:		#CPU资源限制，单位为core数，将用于docker run --cpu-shares参数
        memory:		#内存资源限制，单位可以为Mib/Gib，将用于docker run --memory参数
     requests:		#资源最小请求的设置
        CPU:		#CPU请求，容器启动的初始可用数量
        memory:		#内存请求，容器启动的初始可用数量
   lifecycle:		#生命周期钩子
     postStart:		#容器启动后立即执行此钩子，如果执行失败，会根据重启策略进行重启
     preStop:		#容器终止前执行此钩子，无论结果如何，容器都会终止
   livenessProbe:	#对Pod内个容器健康检查设置，当探测容器无响应后将自动重启该容器
   tcpSocket:		#对Pod内容器健康检查方式
   initialDelaySeconds:		#容器启动完成后，首次探测时间，单位为秒
   timeoutSeconds:			#对容器健康检查探测等待相应的超时时间，单位秒，默认1秒
   periodSeconds:			#对容器监控检查的定期探测时间设置，单位秒，默认10秒一次
 restartPolicy:		#Pod的重启策略
 nodeName:			#设置pod调度到指定的node节点上
 nodeSelector:		#设置Pod调度到指定的label的node节点上
 imagePullSecrets:	#拉取镜像时，使用secret名称，以key:secretkey格式指定
 hostNetwork:		#是否使用主机网络模式，默认为false，如果设置为true，表示使用宿主机网络
 volumes:			#在该Pod上定义共享存储卷列表
 - name:			#共享存储卷名称
   emptyDir:		#类型为emptyDir的存储卷
   hostPath:		#类型为hostPath的存储卷
     path:			#Pod所在宿主机的目录
   secret:			#类型为secret的存储卷，挂载secret对象到容器内部
   configMap:		#类型为configMap的存储卷，挂载configMap对象到容器内部
```