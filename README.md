# kvm
## KVM 使用virsh命令来创建虚拟机
     用已有的yz-gpu-base-k8s-400g.img镜像配合yz-gpu-k8s007.hogpu.cc.xml文件来创建虚拟机
     具体步骤如下：
### 一：开启一个虚拟机
    1. cd /var/lib/libvirt/images/
    2. 拷贝初始镜像
    `cp yz-gpu-base-k8s-400g.img yz-gpu-k8s006.hogpu.cc.img`
    3. 编辑镜像的xml文件，进行创建（其中可以删除掉uuid一行，会重新创建）
    `virsh dumpxml yz-gpu-k8s004.hogpu.cc > /tmp/abc.xml`
    `mv /tmp/abc.xml /tmp/yz-gpu-k8s006.hogpu.cc.xml` 
    `vim yz-gpu-k8s006.hogpu.cc.xml`
    4. 按照xml配置来创建虚拟机(用define和create创建虚拟机是有区别的)
    （define  丛xml配置文件创建主机但是不启动；create  同样是丛xml配置文件创建主机，但是可以指定很多选项，比如是否启动，是否连接控制台）
   
    `virsh define yz-gpu-k8s006.hogpu.cc.xml`
    5. 检查配置文件 
    `virsh edit yz-gpu-k8s006.hogpu.cc`
    6. 启动虚拟机（yz-gpu-k8s006.hogpu.cc名字只是相对于宿主机来说的名字，并不是虚拟机真正的名字）
    `virsh start yz-gpu-k8s006.hogpu.cc`
### 二：彻底删除虚拟机
    1. 删除虚拟机   
    `virsh destroy yz-gpu-k8s006.hogpu.cc.img`
    2. 解除标记     
    `virsh undefine yz-gpu-k8s006.hogpu.cc.img`

*参考：https://blog.csdn.net/tiantao2012/article/details/78249295*
# kvm
## KVM 使用virsh命令来创建虚拟机
     用已有的yz-gpu-base-k8s-400g.img镜像配合yz-gpu-k8s007.hogpu.cc.xml文件来创建虚拟机
     具体步骤如下：
### 一：开启一个虚拟机
    1. cd /var/lib/libvirt/images/
    2. 拷贝初始镜像
    `cp yz-gpu-base-k8s-400g.img yz-gpu-k8s006.hogpu.cc.img`
    3. 编辑镜像的xml文件，进行创建（其中可以删除掉uuid一行，会重新创建）
    `virsh dumpxml yz-gpu-k8s004.hogpu.cc > /tmp/abc.xml`
    `mv /tmp/abc.xml /tmp/yz-gpu-k8s006.hogpu.cc.xml` 
    `vim yz-gpu-k8s006.hogpu.cc.xml`
    4. 按照xml配置来创建虚拟机(用define和create创建虚拟机是有区别的)
    （define  丛xml配置文件创建主机但是不启动；create  同样是丛xml配置文件创建主机，但是可以指定很多选项，比如是否启动，是否连接控制台）
   
    `virsh define yz-gpu-k8s006.hogpu.cc.xml`
    5. 检查配置文件 
    `virsh edit yz-gpu-k8s006.hogpu.cc`
    6. 启动虚拟机（yz-gpu-k8s006.hogpu.cc名字只是相对于宿主机来说的名字，并不是虚拟机真正的名字）
    `virsh start yz-gpu-k8s006.hogpu.cc`
### 二：彻底删除虚拟机
    1. 删除虚拟机   
    `virsh destroy yz-gpu-k8s006.hogpu.cc.img`
    2. 解除标记     
    `virsh undefine yz-gpu-k8s006.hogpu.cc.img`
### 三：查看物理机有几块卡：
```lspci |grep NVIDIA
02:00.0 VGA compatible controller: NVIDIA Corporation GP102 [TITAN X] (rev a1)
03:00.0 VGA compatible controller: NVIDIA Corporation GP102 [TITAN X] (rev a1)
06:00.0 VGA compatible controller: ASPEED Technology, Inc. ASPEED Graphics Family (rev 30)
84:00.0 VGA compatible controller: NVIDIA Corporation GP102 [TITAN X] (rev a1)
85:00.0 VGA compatible controller: NVIDIA Corporation GP102 [TITAN X] (rev a1)
```
### 四：查看gpu卡的对应pci情况：
```
virsh nodedev-list —tree
virsh nodedev-list --tree
computer
  |
  +- pci_0000_00_00_0
|
  +- pci_0000_00_02_0
  |   |
  |   +- pci_0000_02_00_0
  |   +- pci_0000_02_00_1
  |
  +- pci_0000_00_03_0
  |   |
  |   +- pci_0000_03_00_0
  |   +- pci_0000_03_00_1

...
  |
  +- pci_0000_80_02_0
  |   |
  |   +- pci_0000_84_00_0
  |   +- pci_0000_84_00_1
  |
  +- pci_0000_80_03_0
  |   |
  |   +- pci_0000_85_00_0
  |   +- pci_0000_85_00_1
  |
...
```
### 查看那块卡被占用及具体信息：
```
virsh nodedev-dumpxml pci_0000_02_00_0
<device>
  <name>pci_0000_02_00_0</name>
  <path>/sys/devices/pci0000:00/0000:00:02.0/0000:02:00.0</path>
  <parent>pci_0000_00_02_0</parent>
  <driver>
    <name>vfio-pci</name>
  </driver>
  <capability type='pci'>
    <domain>0</domain>
    <bus>2</bus>
    <slot>0</slot>
    <function>0</function>
    <product id='0x1b00'>GP102 [TITAN X]</product>
    <vendor id='0x10de'>NVIDIA Corporation</vendor>
    <iommuGroup number='45'>
      <address domain='0x0000' bus='0x02' slot='0x00' function='0x0'/>
      <address domain='0x0000' bus='0x02' slot='0x00' function='0x1'/>
    </iommuGroup>
    <numa node='0'/>
    <pci-express>
      <link validity='cap' port='0' speed='8' width='16'/>
      <link validity='sta' speed='2.5' width='16'/>
    </pci-express>
  </capability>
</device>
```

*参考：https://blog.csdn.net/tiantao2012/article/details/78249295*
=======
