# SSH

## **1. 初见 SSH**

SSH是一种协议标准，用于在网络主机之间进行加密的一种协议，其目的是实现**安全远程登录**以及其它**安全网络服务**。

> SSH 仅仅是一**协议标准**，其具体的实现有很多，既有开源实现的 OpenSSH，也有商业实现方案。使用范围最广泛的当然是开源实现 OpenSSH。

为什么要搞这么个协议呢？其实，很久很久以前，互联网通信都是明文的，一旦在中间环节被某些中间商截获了，我们的通信内容就暴漏无疑。

所以芬兰就有这么一位叫做 Tatu Ylonen 的人设计了 SSH 协议，将信息加密，这样就像上面说的，即使我们的登陆信息在中间被人截获了，我们的密码也不会被泄露。

目前 SSH 协议已经在全世界广泛被使用，且已经在成为各个 Linux 发行版的标配。

## **2. SSH 工作原理**

在讨论 SSH 的原理和使用前，我们需要分析一个问题：**为什么需要 SSH？**

从 1.1 节 SSH 的定义中可以看出，SSH 和 Telnet、FTP 等协议主要的区别在于**安全性**。这就引出下一个问题：**如何实现数据的安全呢？**首先想到的实现方案肯定是对数据进行**加密**。加密的方式主要有两种：

1. 对称加密（也称为秘钥加密）
2. 非对称加密（也称公钥加密）

所谓对称加密，指加密解密使用同一套秘钥。如下图所示：

![img](https://ask.qcloudimg.com/http-save/yehe-5449090/vkxl8q2ur1.png)

图1-1：对称加密-Client端

![img](https://ask.qcloudimg.com/http-save/yehe-5449090/pja5so5q72.png)

图1-2：对称加密-Server端

对称加密的加密强度高，很难破解。但是在实际应用过程中不得不面临一个棘手的问题：**如何安全的保存密钥呢？**

尤其是考虑到数量庞大的 Client 端，很难保证密钥不被泄露。一旦一个 Client端的密钥被窃取，那么整个系统的安全性也就不复存在。为了解决这个问题，**非对称加密**应运而生。非对称加密有两个密钥：**“公钥”**和**“私钥”**。

> 两个密钥的特性：公钥加密后的密文，只能通过对应的私钥进行解密。而通过公钥推理出私钥的可能性微乎其微。

下面看下使用非对称加密方案的登录流程：

![img](https://ask.qcloudimg.com/http-save/yehe-5449090/q4vanjgmyc.jpeg)

图1-3：非对称加密登录流程

1. 远程 Server 收到 Client 端用户 TopGun 的登录请求，Server 把自己的公钥发给用户。
2. Client 使用这个公钥，将密码进行加密。
3. Client 将加密的密码发送给 Server端。
4. 远程 Server 用自己的私钥，解密登录密码，然后验证其合法性。
5. 若验证结果，给 Client 相应的响应。

> 私钥是 Server 端独有，这就保证了 Client 的登录信息即使在网络传输过程中被窃据，也没有私钥进行解密，保证了数据的安全性，这充分利用了非对称加密的特性。

### 这样就一定安全了吗？

上述流程会有一个问题：**Client 端如何保证接受到的公钥就是目标 Server 端的？** 如果一个攻击者中途拦截 Client 的登录请求，向其发送自己的公钥，Client 端用攻击者的公钥进行[数据加密](https://cloud.tencent.com/solution/domesticencryption?from=10680)。攻击者接收到加密信息后再用自己的私钥进行解密，不就窃取了 Client 的登录信息了吗？这就是所谓的中间人攻击。

![img](https://ask.qcloudimg.com/http-save/yehe-5449090/rcwv2g4nqc.jpeg)

图1-4：中间人攻击

### **2.1 SSH 中是如何解决这个问题的？**

#### **2.1.1 基于口令的认证**

从上面的描述可以看出，问题就在于**如何对 Server 的公钥进行认证？**在 https中可以通过 CA 来进行公证，可是 SSH 的 **Publish key**和 **Private key** 都是自己生成的，没法公证。

只能通过 Client 端自己对公钥进行确认。通常在第一次登录的时候，系统会出现下面提示信息：

```javascript
The authenticity of host 'ssh-server.example.com (12.18.429.21)' can't be established.
RSA key fingerprint is 98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d.
Are you sure you want to continue connecting (yes/no)?
```

上面的信息说的是：无法确认主机 ssh-server.example.com（12.18.429.21）的真实性，不过知道它的公钥指纹，是否继续连接？

> 之所以用 fingerprint 代替 key，主要是 key 过于长（RSA 算法生成的公钥有1024 位），很难直接比较。所以，对公钥进行 Hash 生成一个 128 位的指纹，这样就方便比较了。

如果输入 **yes** 后，会出现下面信息：

```javascript
Warning: Permanently added 'ssh-server.example.com,12.18.429.21' (RSA) to the list of known hosts.
Password: (enter password)
```

该 host 已被确认，并被追加到文件 **known_hosts** 中，然后就需要输入密码，之后的流程就按照图 1-3 进行。

#### **2.1.2 基于公钥认证**

在上面介绍的登录流程中可以发现，每次登录都需要输入密码，很麻烦。SSH 提供了另外一种可以免去输入密码过程的登录方式：公钥登录。流程如下：

![img](https://ask.qcloudimg.com/http-save/yehe-5449090/avi6hg6iib.jpeg)

图1-5：公钥认证流程

1. Client 将自己的公钥存放在 Server 上，追加在文件 authorized_keys 中。注意：Client 端的 Public key 是 Client 手动 Copy 到 Server端的，SSH 建立连接过程中没有公钥的交换操作。
2. Server 端接收到 Client 的连接请求后，会在 authorized_keys 中匹配到 Client 的公钥 pubKey，并生成随机数 R，用 Client 的公钥对该随机数进行加密得到 pubKey(R)，然后将加密后信息发送给 Client。
3. Client 端通过私钥进行解密得到随机数 R，然后对随机数 R 和本次会话的 SessionKey 利用 MD5 生成摘要 Digest1，发送给 Server 端。
4. Server 端会也会对 R 和 SessionKey 利用同样摘要算法生成 Digest2。
5. Server 端会最后比较 Digest1 和 Digest2 是否相同，完成认证过程。

注意：在步骤1中，Client 将自己的公钥存放在 Server 上。需要用户手动将公钥 Copy 到 Server 上。这就是在配置 SSH 的时候进程进行的操作。下图是 GitHub 上 SSH keys 设置视图：

![img](https://ask.qcloudimg.com/http-save/yehe-5449090/48zrpjs5x0.jpeg)

GitHub 中 SSH keys 设置

在步骤 2 中，Server 端根据什么信息在 authorized_keys 中进行查找的呢？主要是根据 Client 在认证的开始会发送一个 KeyID 给 Server，这个 KeyID 会唯一对应该 Client 的一个 PublicKey，Server 就是通过该 KeyID 在 authorized_keys 进行查找对应的 PublicKey。

## **3. SSH 实践**

### **3.1 生成密钥操作**

经过上面的原理分析，下面三行命令的含义应该很容易理解了：

```javascript
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 0600 ~/.ssh/authorized_keys
```

ssh-keygen 是用于生产密钥的工具。

- -t：指定生成密钥类型（rsa、dsa、ecdsa 等）
- -P：指定 passphrase，用于确保私钥的安全
- -f：指定存放密钥的文件（公钥文件默认和私钥同目录下，不同的是存放公钥的文件名需要加上后缀 .pub）

首先看下面 ~/.ssh 中的四个文件：

![img](https://ask.qcloudimg.com/http-save/yehe-5449090/vd8f93e91b.jpeg)

SSH 涉及文件

1. id_rsa：保存私钥
2. id_rsa.pub：保存公钥
3. authorized_keys：保存已授权的客户端公钥
4. known_hosts：保存已认证的远程主机 ID（关于 known_hosts 详情，见文末更新内容）

四个角色的关系如下图所示：

![img](https://ask.qcloudimg.com/http-save/yehe-5449090/1yvqjsvcoi.jpeg)

SSH 结构简图

> 需要注意的是：一台主机可能既是 Client，也是 Server。所以会同时拥有authorized_keys 和 known_hosts。

### **3.2 登录操作**

```javascript
# 以用户名user，登录远程主机host
$ ssh user@host

# 本地用户和远程用户相同，则用户名可省去
$ ssh host

# SSH默认端口22，可以用参数p修改端口
$ ssh -p 2017 user@host
```

## **4. 其它一些补充**

下面关于 SSH 的 **known_hosts** 机制的一些补充。

### **4.1 known_hosts 中存储的内容是什么？**

known_hosts 中存储是已认证的远程主机 host key，每个 SSH Server 都有一个 **secret, unique ID, called a host key**。

### **4.2 host key 何时加入 known_hosts 的？**

当我们第一次通过 SSH 登录远程主机的时候，Client 端会有如下提示：

```javascript
Host key not found from the list of known hosts.
Are you sure you want to continue connecting (yes/no)?
```

此时，如果我们选择 **yes**，那么该 host key 就会被加入到 Client 的known_hosts 中，格式如下：

```javascript
# domain name+encryption algorithm+host key
example.hostname.com ssh-rsa AAAAB4NzaC1yc2EAAAABIwAAAQEA...
```

### **4.3 为什么需要 known_hosts？**

最后探讨下为什么需要 known_hosts，这个文件主要是通过 Client 和 Server的双向认证，从而避免中间人（**man-in-the-middle attack**）攻击，每次Client 向 Server 发起连接的时候，不仅仅 Server 要验证 Client 的合法性，Client 同样也需要验证 Server 的身份，SSH Client 就是通过 known_hosts 中的 host key 来验证 Server 的身份的。

> 这种方案足够安全吗？当然不，比如第一次连接一个未知 Server 的时候，known_hosts 还没有该 Server 的 host key，这不也可能遭到**中间人**攻击吗？这可能只是安全性和可操作性之间的折中吧。

## SSH 如何配置

### 连接方式

基本的ssh连接方法是

```bash
ssh username@ip
```

`username`表示该机器的用户名，`ip`表示对应的ip地址。比如，笔者在`10.22.75.212`的用户名是`qiangzibro`，只需要在终端输入

```bash
ssh qiangzibro@10.22.75.212
```

接下来，终端会提示你一条信息，输入yes回车，会提示你输入密码，就像这样。

![img](https://pic3.zhimg.com/v2-bbc1298be24fdfb6a63775e751f21a3a_b.png)

先别着急使用下去，稍加配置可以让我们使用得更加舒心、安全。

### 基本配置

#### 给ip地址取别名

长长的ip地址不好记，给ip地址取个别名吧！在mac或者linux上，可以编辑`/etc/hosts`这个文件，由于是系统文件，需要使用管理员权限。编辑器可以自选，笔者使用的是`neovim`

```bash
sudo nvim /etc/hosts
```

比如我想给`10.22.75.177`取名叫`lab1`，添加下面一行：

```bash
10.22.75.177 lab1
```

在以后的使用中，凡是需要用到ip地址，可以直接用别名代理，比如

```bash
ping lab1
```

#### 给特定主机上的用户取别名

ip地址取完别名后，我们可以使用类似

```bash
ssh qiangzibro@lab1
```

的方式进行连接，实际上，这样的连接方式还可以进一步简化。

也就是说，给`qiangzibro@lab1`也取个别名

不建议自己造轮子，早年间笔者曾写过比如`alias sshqiang="ssh qiangzibro@lab1”`的别名，其实没有太大别要，因为进行下面配置可以让以后使用更方便：

类unix系统（mac或者linux）可以直接编辑`~/.ssh/config`这个文件，如果没有，自己创建一个。语法如下

```bash
Host l1
HostName lab1
Port 22
User qiangzibro
```

配置很简单，四行分别表示别名、远程主机ip、远程主机ssh端口、远程主机用户名。然后我们可以用

```bash
ssh l1
```

进行连接。

#### 免密码登录

经常使用密码登录，一个问题是有安全风险，另外一个是麻烦，太懒了啊，每次输密码多麻烦！ssh还提供一种使用密匙验证的方式进行登录，相信大家在配置github免密登录时也遇到过。百度百科上对其解释如下：

> 原理是你必须为自己创建一对密匙，并把公用密匙放在需要访问的服务器上。如果你要连接到SSH服务器上，客户端软件就会向服务器发出请求，请求用你的密匙进行安全验证。服务器收到请求之后，先在该服务器上你的主目录下寻找你的公用密匙，然后把它和你发送过来的公用密匙进行比较。如果两个密匙一致，服务器就用公用密匙加密“质询”（challenge）并把它发送给客户端软件。客户端软件收到“质询”之后就可以用你的私人密匙解密再把它发送给服务器。

也就是说，把本地公钥拷贝到远程服务器上，就不需要每次登录使用密码了。具体讲，是把本地`~/.ssh/id_rsa.pub`内的内容拷贝到远程`~/.ssh/authorized_keys`文件里。首先看看本地有没有公钥：

```text
cat ~/.ssh/id_rsa.pub
```

没有，则生成一个

```text
ssh-keygen -t rsa
```

一路回车按下去，便生成在了`~/.ssh/id_rsa.pub`

你可以使用复制粘贴最原始的方法，而这种操作也有命令简化了：

```bash
ssh-copy-id l1 # 将本地公钥拷贝到远程名为l1用户下，也就是/home/qiangzibro/.ssh/authorized_keys里
```
