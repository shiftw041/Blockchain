# 区块链实验
## 搭建环境
参考网址：<https://github.com/ZCXu1/HUST-CSE-Experiments/tree/main/%E5%8C%BA%E5%9D%97%E9%93%BE%E6%8A%80%E6%9C%AF%E5%8F%8A%E5%BA%94%E7%94%A8/%E5%AE%9E%E9%AA%8C%E4%B8%80>
### 安装git
```
sudo apt install git
```
### 安装curl
```
sudo apt install curl
```
### 安装docker
下载版本：24.0.5
```
sudo apt update		//更新安装包
sudo apt install docker.io
```
启动Docker服务并将其设置为开机启动：（我自己没搞）
```
sudo systemctl start docker
sudo systemctl enable docker
```

添加docker官方GPG密钥
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg
sudo apt-key add -在新版本ubuntu中apt-key被弃用
```

设立仓库：
```
 sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```
下载docker-compose：
版本：1.25.5
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
给docker-compose可执行权限：
sudo chmod +x /usr/local/bin/docker-compose
```


### 配置go环境
参考网址：
<https://ping.chinaz.com/http://dl-ssl.google.com>
<https://www.cnblogs.com/tc310/p/9202298.html>  

为了顺利通过goole网址下载文件，需配置一下环境
```
sudo gedit /etc/hosts
```
将下述添加在文档最后面

```
#Download 下载
120.253.253.33 dl.google.com
74.125.203.190 dl-ssl.google.com

 #Groups
203.208.46.146 groups.google.com

 #Google URL Shortener
203.208.46.146 goo.gl

 #Google App Engine
203.208.46.146 appengine.google.com

 #Android Developer
74.125.113.121 developer.android.com
```
然后下载go：
```
wget https://golang.google.cn/dl/go1.19.2.linux-amd64.tar.gz -O - | sudo tar -xz -C /usr/loca
```
打开文件为vim ~/.bashrc   (可能未安装vim需下载)

在按照文档添加环境变量时需注意lyan为用户名，所以我的用户名为sandra所以用sandra替换lyan

还需要保证root权限能执行go命令

```
sudo vim /etc/profile
```
在最后一行添加
```
export PATH=$PATH:/usr/local/go/bin
```
在终端
```
source /etc/profile
```

### 下载jq
版本：1.6
```
sudo apt install jq
```

## 实验一

### 搭建fabric环境：
```
git clone https://github.com/hyperledger/fabric-samples
```
将文件夹中的install-fabric.sh下载并放入到clone的fabric-samples文件夹
然后进入目录fabric-samples执行
```
sudo chmod +x install-fabric.sh
sudo ./install-fabric.sh docker
./install-fabric.sh b
cd test-network
sudo ./network.sh up
```

### 任务二
随着任务一的终端执行
```
sudo ./network.sh createChannel
```
如果遇到：
```
Error: proposal failed (err: bad proposal response 500: "JoinChain" for channelID = mychannel failed because of validation of configuration block, because of Failed capabilities check: [Application capability V2_5 is required but not supported])
```


修改``configtx``文件夹中``configtx.yaml``文件
将
```
Application: &ApplicationCapabilities
    # V2.5 for Application enables the new non-backwards compatible
    # features of fabric v2.5, namely the ability to purge private data.
    # Prior to enabling V2.5 application capabilities, ensure that all
    # peers on a channel are at v2.5.0 or later.
    V2_5: true
```
将``2_5``改成``2_0``即可

在桌面终端
```
git clone https://github.com/yy158775/blockchain-exp
cd vote-smartcontract
go mod tidy
```

```
vim ~/.bashrc
alias sudo='sudo env PATH=$PATH:/usr/local/go/bin LD_LIBRARY_PATH=$LD_LIBRARY_PATH'
source ~/.bashrc
sudo ./network.sh deployCC -ccn basic -ccp ../../blockchain-exp/vote-smartcontract -ccl go

cd vote-app
go mod tidy

sudo chmod +r fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/users/User1@org1.example.com/msp/keystore/priv_sk
sudo chmod +x fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/users/User1@org1.example.com/msp/keystore/priv_sk

```
在运行程序前，需将``main.go``文件进行修改
```
	mspID         = "Org1MSP"
	cryptoPath    = "../test-network/organizations/peerOrganizations/org1.example.com"
	certPath      = cryptoPath + "/users/User1@org1.example.com/msp/signcerts/cert.pem"
	keyPath       = cryptoPath + "/users/User1@org1.example.com/msp/keystore/"
	tlsCertPath   = cryptoPath + "/peers/peer0.org1.example.com/tls/ca.crt"
	peerEndpoint  = "localhost:7051"
	gatewayPeer   = "peer0.org1.example.com"
	channelName   = "mychannel"
	chaincodeName = "basic"
````