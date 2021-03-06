1：RSA密钥交换算法

RSA的核心涉及公钥私钥的概念

（1）：使用公钥加密的数据只有私钥能解密

（2）：使用私钥加密的数据只有公钥能解密

 

我们构建这么一种场景，服务器配置有公钥+私钥，客户端是离散的。

 

RSA算法流程文字描述如下：

（1）：任意客户端对服务器发起请求，服务器首先发回复自己的公钥到客户端（公钥明文传输）。

（2）：客户端使用随机数算法，生成一个密钥S，使用收到的公钥进行 加密，生成C，把C发送到服务器。

（3）：服务器收到C，使用公钥对应的私钥进行解密，得到S。

（4）：上述交换步骤后，客户端和服务器都得到了S，S为密钥（预主密钥）。

 

    我们来看看上述过程中，为何第三方无法得到S。首先第一步后，客户端有公钥，服务器有公钥和私钥。由于公钥是明文传输的，所以可以假设第三方也有公钥。

第二步后，客户端发送C，服务器能够使用自己的私钥进行解密，而第三方只有公钥，无法解密。即第三方无法计算得到S。
上述中，服务器发送的公钥在SSL中是通过certificate报文发送的，certificate中的包含了公钥。C是通过Client key exchange报文发送的。

其实，在实际SSL实际设计中，S其实并没有直接被当成密钥加密，这里为了描述原理，省去了对S后续进行KDF等操作，并不影响实际理解RSA。

 

RSA有一个问题，就是如果私钥泄漏，即私钥被第三方知道，那么第三方就能从C中解密得到S，即只要保存所有的A和B的报文，等到私钥被泄漏的那一天，或者有办法快从C中计算S的方法出现（量子计算机分解大素数），那么A和B就没有什么私密性可言了。

这就是所谓的前向不安全，私钥参与了密钥交换，安全性取决于私钥是否安全保存。

有网友问了这么一个问题：为何客户端不也安装一个公钥私钥，然后客户端和服务器交互的时候，各自传送给对方公钥，然后各自拿对方的公钥加密数据发送给对方，然后各自拿私钥解密收到的数据？

先不说性能，我们看RSA加解密算法，若要加密m，那么需要计算

m^e mod n

如果m > n，我们记作m = n + k

那么原式子 (n + k)^e mod n

多项式展开，除了最后一项k^e ，其余的每一项都有n，故mod n后，

k^e mod n

换句话说，如果m大于n，那么其加密的结果和k的结果时一样的，这就有二义性了，所以RSA本身就不允许m>n的情况出现。所以拿来直接加密数据时不可取的。



