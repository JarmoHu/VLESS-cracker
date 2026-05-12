我纯网络安全技术研究，对任何一方没有任何态度，只是对兴趣的全面好奇。

做这些改进的目的只是为了说明假阳性率真的不是他的问题

所有代码改进都是gemini pro的功劳，我纯小白哈

服务端复现步骤(ubuntu/debian)

```shell
apt install libpcap-dev build-essential
git clone https://github.com/JarmoHu/VLESS-cracker.git
cd VLESS-cracker
make
chmod +x vless-cracker-v1
```

然后输入 ``ip addr``查看你的网卡接口 一般为 eth0 或者 ens0, 我这里为 ens3

假设你的reality端口为443

```
sudo ./vless-cracker-v1 -i ens3 -f "tcp port 443"
```

随便跑一下协议，拿到A的序列。比如我的输出
``
[2026-05-12 01:58:47.480747] TLS ReplayProbe MISMATCH, VLESS/REALITY connection detected after 1/1 confirmations. client=<客户端ip>:53308 server=<服务器ip>:443 sni=www.frankfurt-school.de probes=20 cmp=X[D] result=A{X=TO,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,TO,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT}|B{X=TO,TO,TO,ALERT,ALERT,TO,ALERT,TO,TO,TO,ALERT,TO,FIN,TO,TO,TO,TO,FIN,TO,ALERT}
``

可以看到A的序列为 TO,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,TO,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT
这个序列和服务端版本有关，为了降低假阳性率，可以通过-e参数进行限制。

```
sudo ./vless-cracker-v1 -i ens3 -f "tcp port 443" -e "TO,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,TO,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT,ALERT"
```

然后你随便跑一下协议，看看能不能识别到。

我反正这边假阳率明显降低

我只能说代码的健壮性一般，有时候能测有时候不行，我怀疑是释放tls攻击被服务器屏蔽了

<img width="1280" height="358" alt="image" src="https://github.com/user-attachments/assets/fbf25de2-dec8-475a-b2cc-bdb93d7487d2" />
