CA(Certificate Authority)是数字证书认证中心，常称为证书颁发机构，申请者提交自己的公钥和一些个人信息(如申请者国家，姓名，单位等)给CA，CA对申请者的这些信息单向加密生成摘要信息，然后使用自己的私钥加密整个摘要信息，这样就得到了CA对申请者的数字签名，在数字签名上再加上CA自己的一些信息(如CA的机构名称，CA层次路径等)以及该证书的信息(如证书有效期限)，就得到了所谓的数字证书。

如果某用户信任了该CA，就获取了该CA的公钥（实际上信任CA的其中一个作用就是获取CA公钥），使用该公钥解密数字证书就可以验证申请者的信息以及申请者公钥的可靠性（申请者的公钥只被CA的私钥加密，解密该私钥后只是需要验证可靠性）。

这里的关键是CA使用自己的私钥给申请者加密，那么如何保证CA是可信并且合法的呢？根CA是通过自签署数字证书的方式标榜自己的可信性和合法性，第一级子CA由根CA颁发合法数字证书，第二级直至所有的子CA都由上一级子CA颁发数字证书。对于多级子CA只需要信任根CA即可，因为获取了根CA的公钥，可以解密第一级子CA的证书并获取验证第一级子CA的公钥，层层递进，最终获取到为申请者颁发数字证书的机构并获取它的公钥。

另一种情况，虽然信任了根CA，但如果根CA授权的某个二级或者某个二级授权的三级CA不在信任列表中，就需要将这个中间的CA补齐，否则中间出现断层，无法成功解密该中间CA颁发的证书。通常这种中间CA的证书称为chain证书，例如"xxx.com.chain.crt"。如果购买的证书发给你的文件中包含了chain.crt文件，则说明他是中间CA，且有些浏览器或操作系统中没有内置它的CA，这时应该将chain证书也配置到web server上。

正是这些根CA和子CA组成了PKI。

信任CA后，每次接收到需要解密的数字证书时，还要去该颁发机构指定网站的证书吊销列表（CRL）中查询该证书是否被吊销，对于吊销后的证书应该不予以信任，这是信任CA的第二个作用。导致证书被吊销的可能性不少，例如申请者的私钥被黑客获取，申请者申请吊销等。

也有公司使用自签的证书，例如某些银行、12306有时候就要求下载证书并安装。使用自签证书的好处当然是省钱、方便啦。