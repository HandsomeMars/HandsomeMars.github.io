

基本概念
1. openssl是一个安全套接字层密码库，囊括主要的密码算法、常用密钥、证书封装管理功能及实现ssl协议。OpenSSL整个软件包大概可以分成三个主要的功能部分：SSL协议库libssl、应用程序命令工具以及密码算法库libcrypto。
2. SSL：Secure Socket Layer，安全套接字层协议，分为SSLv2和SSLv3两个版本，TSL在SSL3.0基础之上提出的安全通信标准化版。主要是为了加密传输数据而产生的协议，能使用户/服务器应用之间的通信不被攻击者窃听，并且始终对服务器进行认证，还可选择对用户进行认证。

 SSL协议建立在可靠的传输层协议（TCP,UDP,SCTP）之上，应用层协议（HTTP,FTP,TELNRET）在SSL之上，SSL协议在应用层协议通信之前就已经完成了加密算法、服务器认证等工作。http协议调用了ssl协议,那么他就变成了https(http –>ssl–>https)。主要功能两个：
   a. 加密解密在网络中传输的数据包，同时保护这些数据不被修改和伪造。
    应用层——>ssl解密——>传输层
    传输层——>ssl加密认证——>应用层
   b. 验证网络对话中双方的身份。

3. SSL握手协议：SSL包含两个子协议，一个是包协议，一个是子协议，包协议说明SSL的数据包是如何封装的；握手协议说明通信双方协商通信双方决定使用什么算法及算法使用的key。暂时只讨论握手协议的过程。

   - Client向服务器发送自己支持的协议版本（如TLS1.2）、client生成的随机数、自己加密算法的一些配置。

   - Server 收到Client请求后向客户端response:确认使用加密通信协议的版本、生成一个随机数、确认使用加密的方法、server certificate（服务器证书）。

   - Client 验证服务器证书，在确认无误后取出其公钥，并发送随机数 Pre-Master-Key（用于公钥加密）、编码变更通知（通信双方都用商定好的密钥进行通信;即随后的信息都将用双方商定好的加密方法和密钥发送. ）

   - Server验证完client的身份之后，用自己的私有密钥解密得到pre-master-key后,然后双方利用这个pre-master key来共同协商，得到master secret。返回信息给client

   - 双方用master一起产生真正的session key，这就是他们在剩下的过程中的对称加密的key了。这个key还可以用来验证数据完整性。双方再交换结束信息。握手结束。
4. 证书Certificate和证书颁发机构CA（certification authority）
 证书是建立公共密钥和某个实体之间联系的数字化的文件。它包含的内容有：版本信息（X.509也是有三个版本的）、系列号、证书接受者名称、颁发者名称、证书有效期、公共密钥、一大堆的可选的其他信息、CA的数字签名。证书由CA颁发，由CA决定该证书的有效期，由该CA签名。每个证书都有唯一的系列号。证书的系列号和证书颁发者来决定某证书的唯一身份。常用的证书是采用X.509结构的，这是一个国际标准证书结构，
  CA是第三方机构，被你信任，由它保证证书的确发给了应该得到该证书的人。CA自己有一个庞大的public key数据库，用来颁发给不同的实体。CA也是一个实体，它也有自己的公共密钥和私有密钥。

5. 加密算法
 1）对称加密：指加密和解密使用相同密钥的加密算法。对称加密算法的优点在于加解密的高速度和使用长密钥时的难破解性
  常见的对称加密算法：DES、3DES、DESX、AES、RC4、RC5、RC6等
  2）非对称加密：指加密和解密使用不同密钥的加密算法，也称为公私钥加密
  常见的非对称加密算法：RSA、DSA（数字签名用）等
  3）Hash算法：Hash算法它是一种单向算法，用户可以通过Hash算法对目标信息生成一段特定长度的唯一的Hash值，却不能通过这个Hash值逆向获得目标信息
  常见的Hash算法：MD2、MD4、MD5、SHA、SHA-1等

# SSL

Secure Socket Layer，安全套接字层协议，分为SSLv2和SSLv3两个版本，TSL在SSL3.0基础之上提出的安全通信标准化版。

# ca证书格式

|  格式  |                                                              |                                                              |
| :----: | ------------------------------------------------------------ | ------------------------------------------------------------ |
|  KEY   | 私钥文件。一般指PEM格式                                      |                                                              |
|  CRT   | 证书文件。可以是PEM格式                                      |                                                              |
|  PEM   | 证书文件格式（*.pem）。<br/>----BEGIN CERTIFICATE-----  <br/> Base64编码的二进制 <br/> ----END CERTIFICATE-----<br/> | 对于CER、CRT格式的证书，您可通过直接修改证书文件扩展名的方式将其转换成PEM格式。例如，将server.crt证书文件直接重命名为server.pem |
|  CSR   | 证书请求文件。                                               | 生成 X509 数字证书前,一般先由用户提交证书申请文件,然后由 CA 来签发证书<br/>1. 用户生成自己的公私钥对;<br/>2.构造自己的证书申请文件,符合 PKCS#10 标准。该文件主要包括了用户信息、公钥以及一些可选的属性信息,并用自己的私钥给该内容签名;<br/>3.用户将证书申请文件提交给 CA;<br/>4.CA 验证签名,提取用户信息,并加上其他信息(比如颁发者等信息),用 CA 的私钥签发数字证书;<br/>5.说明:数字证书(如x.509)是将用户(或其他实体)身份与公钥绑定的信息载体。一个合法的数字证书不仅要符合 X509 格式规范,还必须有 CA 的签名。用户不仅有自己的数字证书,还必须有对应的私钥。X509v3 数字证书主要包含的内容有:证书版本、证书序列号、签名算法、颁发者信息、有效时间、持有者信息、公钥信息、颁发者 ID、持有者 ID 和扩展项。<br/> |
|  DER   | 辨别编码规则 (DER) 可包含所有私钥、公钥和证书                | 它是无报头的 － PEM 是用文本报头包围的 DER                   |
|  PFX   | 公钥加密标准 #12 (PKCS#12) 可包含所有私钥、公钥和证书。其以二进制格式存储，也称为 PFX 文件 | 通常可以将Apache/OpenSSL使用的“KEY文件 + CRT文件”格式合并转换为标准的PFX文件，你可以将PFX文件格式导入到微软IIS 5/6、微软ISA、微软Exchange Server等软件。转换时需要输入PFX文件的加密密码。 |
|  JKS   | 通常可以将Apache/OpenSSL使用的“KEY文件 + CRT文件”格式”转换为标准的Java Key Store(JKS)文件 | JKS文件格式被广泛的应用在基于JAVA的WEB服务器、应用服务器、中间件。你可以将JKS文件导入到TOMCAT、 WEBLOGIC 等软件。 |
|  KDB   | 通常可以将Apache/OpenSSL使用的“KEY文件 + CRT文件”格式转换为标准的IBM KDB文件 | KDB文件格式被广泛的应用在IBM的WEB服务器、应用服务器、中间件。你可以将KDB文件导入到IBM HTTP Server、IBM Websphere 等软件。 |
|  OCSP  | 在线证书状态协议(OCSP,Online Certificate Status Protocol,rfc2560)用于实时表明证书状态 | OCSP 客户端通过查询 OCSP 服务来确定一个证书的状态,可以提供给使用者一个或多个数字证书的有效性资料，它建立一个可实时响应的机制，让用户可以实时确认每一张证书的有效性，解决由CRL引发的安全问题。。OCSP 可以通过 HTTP协议来实现。rfc2560 定义了 OCSP 客户端和服务端的消息格式。<br/> |
|  CER   | 一般指使用DER格式的证书                                      |                                                              |
|  CRL   | 证书吊销列表 (Certification Revocation List) 是一种包含撤销的证书列表的签名数据结构 | CRL 是证书撤销状态的公布形式,CRL 就像信用卡的黑名单,用于公布某些数字证书不再有效。CRL 是一种离线的证书状态信息。它以一定的周期进行更新。CRL 可以分为完全 CRL和增量 CRL。在完全 CRL 中包含了所有的被撤销证书信息,增量 CRL 由一系列的 CRL 来表明被撤销的证书信息,它每次发布的 CRL 是对前面发布 CRL 的增量扩充。基本的 CRL 信息有:被撤销证书序列号、撤销时间、撤销原因、签名者以及 CRL 签名等信息。基于 CRL 的验证是一种不严格的证书认证。CRL 能证明在 CRL 中被撤销的证书是无效的。但是,它不能给出不在 CRL 中的证书的状态。如果执行严格的认证,需要采用在线方式进行认证,即 OCSP 认证。一般是由CA签名的一组电子文档，包括了被废除证书的唯一标识（证书序列号），CRL用来列出已经过期或废除的数字证书。它每隔一段时间就会更新，因此必须定期下载该清单，才会取得最新信息。<br/> |
|  SCEP  | 简单证书注册协议                                             | 基于文件的证书登记方式需要从您的本地计算机将文本文件复制和粘贴到证书发布中心，和从证书发布中心复制和粘贴到您的本地计算机。 SCEP可以自动处理这个过程但是CRLs仍然需要手工的在本地计算机和CA发布中心之间进行复制和粘贴。 |
| PKCS7  | 加密消息语法(pkcs7),是各种消息存放的格式标准                 | 这些消息包括:数据、签名数据、数字信封、签名数字信封、摘要数据和加密数据 |
| PKCS12 | pkcs12 (个人数字证书标准)用于存放用户证书、crl、用户私钥以及证书链 | pkcs12 中的私钥是加密存放的                                  |

# CA证书

## 自签证书(根)

不被浏览器信任,适合内部使用。

1. 生成证书私钥

   ```bash
   openssl genrsa -out ca.key 2048
   ```

2. 生成签名请求

   ```bash
   openssl req -new -key ca.key -out ca.csr 
   ```

3. 生成签名证书

   ```bash
   openssl x509 -req -days 365 -in ca.csr -signkey ca.key -out ca.crt
   ```

4. 转换证书格式

   ```bash
   openssl x509 -in ca.crt -out ca.pem -outform PEM
   ```

   

## 证书签发

1. 生成证书私钥

   ```bash
   openssl genrsa -out xxx.key 2048
   ```

2. 生成签名请求

   ```bash
   openssl req -new -key xxx.key -out xxx.csr 
   ```

3. ca授权客户端证书

   ```bash
   openssl ca -in xxx.csr -out xxx.crt -cert ca.crt -keyfile ca.key
   ```
