## split
```
split -l 2482 ../BLM/BLM.txt -d -a 4 BLM_
```

## rpm解压
```
rpm2cpio xxx.rpm | cpio -div
```

## openssl
```
openssl 非对称加密算法RSA命令详解
http://www.cnblogs.com/gordon0918/p/5363466.html
openssl rsa -in RSA.pem -des3 -passout pass:123456 -out E_RSA.pem
```

## ansible安装
```
sed -i s/archive/us.cn.archive/g /etc/apt/sources.list
apt-get update
apt-get install -y build-essential libssl-dev libffi-dev

apt install python-setuptools
easy_install pip
pip install ansible

apt-get install software-properties-common
apt-add-repository ppa:ansible/ansible
apt-get update
apt-get install ansible
```

## sublime刷新
```
// Refresh folder list with F5
{ "keys": ["f5"], "command": "refresh_folder_list" }
```

## node安装
```
curl -sL https://deb.nodesource.com/setup_6.x | bash -
apt-get install nodejs -y
```

## docker自定义registry
```
{"registry-mirrors": ["http://118.190.10.52:5000","https://ndxcc50c.mirror.aliyuncs.com"],"insecure-registries":["118.190.10.52:5000"]}
```

## 磁盘格式转换
```
vmkfstools -i hills-ubuntu-000002.vmdk mapple-thin.vmdk -d thin -a buslogic
```

## 修改linux字符集
```
[root@103 test]# export LC_ALL=zh_CN.UTF-8
[root@103 test]# locale
```

## vue init
```
npm install vue
npm install vue-cli
vue init nuxt-community/starter-template nuxt
cd nuxt
npm install
```

## 虚拟机安装vagrant调试
```
sudo /sbin/vboxconfig

VBoxManage modifyvm "geekbang_default_1506500906445_84672" --vrdeaddress "0.0.0.0"
netstat -tulpen

apt-get install cpu-check
kvm-ok
sudo check-bios-nx --verbose

/etc/vmware/config
vhv.enable = "TRUE"
```

## ubuntu挂载虚拟机磁盘
```
https://www.synology.com/zh-tw/knowledgebase/DSM/tutorial/Storage/How_can_I_recover_data_from_my_DiskStation_using_a_PC

apt-get install lvm2
mdadm --assemble --scan
```
系统启动到request_module: runaway loop modprobe binfmt-464c挂起
利用U盘系统，挂载硬盘出现：mount: unknown filesystem type 'LVM2_member'
解决办法：
需要安装 lvm2
然后按一下步骤：
```
1、查看物理卷：pvs
PV       VG       Fmt   Attr PSize PFree 
   /dev/sda2   VolGroup00 lvm2 a- 279.22G 32.00M

2、查看卷组：vgs
VG       #PV #LV #SN Attr VSize VFree 
   VolGroup00 1 4 0 wz--n- 279.22G 32.00M
3、查看逻辑卷：lvdisplay

--- Logical volume ---
   LV Name             /dev/VolGroup00/LogVol03
   VG Name             VolGroup00
   LV UUID             YhG8Fu-ZGPk-qt8D-AxgC-DzOU-dg1F-z71feI
   LV Write Access        read/write
  LV Status              unenable
   # open                 1
   LV Size             245.97 GB
   Current LE          7871
   Segments             1
   Allocation          inherit
   Read ahead sectors     auto
   - currently set to     256
   Block device           253:2
4、如未激活，需要激活逻辑卷：vgchange -ay /dev/VolGroup00
LV Status              available
5、挂载逻辑卷：mount   /dev/VolGroup00/LogVol03   /home/lvm
```