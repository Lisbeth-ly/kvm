# kvm
## KVM 使用virsh命令来创建虚拟机
用已有的yz-gpu-base-k8s-400g.img镜像配合yz-gpu-k8s007.hogpu.cc.xml文件来创建虚拟机
**具体步骤如下：**
开启一个虚拟机
1 cd /var/lib/libvirt/images/
2 拷贝初始镜像
`cp yz-gpu-base-k8s-400g.img yz-gpu-k8s006.hogpu.cc.img`
3 编辑镜像的xml文件，进行创建（其中可以删除掉uuid一行，会重新创建）
`virsh dumpxml yz-gpu-k8s004.hogpu.cc > /tmp/abc.xml`
`mv /tmp/abc.xml /tmp/yz-gpu-k8s006.hogpu.cc.xml` 
`vim yz-gpu-k8s006.hogpu.cc.xml`
4 按照xml配置来创建虚拟机(用define和create创建虚拟机是有区别的)
（define  丛xml配置文件创建主机但是不启动；create  同样是丛xml配置文件创建主机，但是可以指定很多选项，比如是否启动，是否连接控制台）
`virsh define yz-gpu-k8s006.hogpu.cc.xml`
5 检查配置文件 
`virsh edit yz-gpu-k8s006.hogpu.cc`
6 启动虚拟机（yz-gpu-k8s006.hogpu.cc名字只是相对于宿主机来说的名字，并不是虚拟机真正的名字）
`virsh start yz-gpu-k8s006.hogpu.cc`
彻底删除虚拟机
1. 删除虚拟机   
`virsh destroy yz-gpu-k8s006.hogpu.cc.img`
2. 解除标记     
`virsh undefine yz-gpu-k8s006.hogpu.cc.img`

*参考：https://blog.csdn.net/tiantao2012/article/details/78249295*
