# GitHome
# !/bin/bash

# es集群IP地址
IP_LIST=(
192.168.0.100
192.168.0.101
192.168.0.102
)

# 创建秘钥
function create_keygen(){
echo "开始创建密钥"
su - root <<EOF
 expect -c"
    spawn ssh-keygen -t rsa -P '' -f /root/.ssh/id_rsa
      set timeout 30
      expect {
        \"*Overwrite*\" {send \"y\r\";exp_continue}
      }
  "
EOF
if [ $? -eq 0 ];then
 echo "创建秘钥成功"
else
 echo "创建秘钥失败"
fi
}

# 同步密钥
function sycn_keygen(){
echo "开始同步密钥到其他服务器"
for ((i=0;i<${#HOST_IP[*]};i++))
do
su root<<EOF
expect -c"
 spawn ssh-copy-id -i /root/.ssh/id_rsa.pub root@${IP_LIST[i]}
 expect {
     \"*yes/no*\" {send \"yes\r\";exp_continue}
     \"*password:*\" {send \"$password\r\";exp_continue}
  }"
EOF
done
if [ $? -eq 0 ];then
  echo "设置免秘钥成功"
else
  echo "设置免秘钥失败"
fi
}

ssh_keygen_fun
copy_ssh_keygen
