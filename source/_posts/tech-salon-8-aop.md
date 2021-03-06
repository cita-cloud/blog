---
title: 技术沙龙第八期-AOP
date: 2022-01-18
categories: 应用
---

.now：


hello大家好，这两天考虑了很久要分享什么，最后决定就从最近身边发现的可优化的小方案入手吧。今天给大家分享的是基于Spring AOP实现某些重要信息的加解密跟脱敏。

.now：


AOP面向切面编程，通过预编译方式和运行期间动态代理实现程序功能统一维护，这些原理网上一搜一大把，这次就不多搬那些原理过来了，主要就讲讲应用于加解密脱敏方面。

.now：


因为日常业务开发中，经常有碰到一些比较重要的用户信息，比如手机号，身份证号，邮箱，我们就直接大咧咧地明文保存到数据库里，万一数据库被黑，这些信息就全暴露了，用户就可能遭受到短信轰炸，身份证号被冒用等恶劣行为，数据安全很重要，所以就有必要进行加密后保存，查询时解密返回，页面脱敏显示比如手机号只展示前三后四，如：18812349876 置换为 188****9876。

.now：


之前看到有些项目返回时是手动调用工具类替换后再返回，如下这样：

![](1.jpeg)

.now：


如果多个业务需要做加解密脱敏处理，每次新增都自己手动调用加密方法，每次查询都自己手动进行解密或者脱敏处理，就会多出很多重复代码，代码就显得很笨重。特别是后续万一修改了公共方法，就有可能要对应所有调用它的地方。

.now：


本次介绍的就是基于Spring AOP 注解的方式，实现加解密脱敏，对service层的业务代码无侵入。

.now：


涉及的代码如下，DecryptField，DesensitizedField，EncryptField三个注解用于标记需要处理的字段，NeedDecrypt，NeedDesensitized，NeedEncrypt三个注解用于标记切入点，DecryptAspect，DesensitizedAspect，EncryptAspect三个切面配置类，具体实现真正的加解密脱敏。

![](2.jpeg)

.now：


这里先提一下Spring AOP 中的通知类型，Spring AOP 中有5种通知类型分别如下：

![](3.jpeg)

.now：


这次介绍的主要就用到了Before和AfterReturning，在请求过来时，新增方法调用前进行加密配置，在查询返回后进行解密脱敏

.now：


接下来具体使用看看，首先看一下加密的切面类EncryptAspect，切面里定义了切入点是指定的注解@NeedEncrypt，在before方法里进行具体的加密处理，先找到被注解@EncryptField标记的字段，然后对该字段的值进行加密处理。里面具体的加密方式就可以自己自定义了，看自己需求。

![](4.jpeg)

![](5.jpeg)

.now：


我们只需要在方法添加【@NeedEncrypt】注解

.now：


在需要加密字段添加注解【@EncryptField】

.now：


调用接口，查看数据库，就可以看到对应的字段加密成功

.now：

![](6.jpeg)

![](7.jpeg)

![](8.jpeg)

![](9.jpeg)

同样的，只需要在方法添加【@NeedDecrypt】注解，

.now：


需要解密字段添加注解【@DecryptField】

.now：


调用接口，就可以看到对应的字段解密成功

.now：

![](10.jpeg)

![](11.jpeg)

![](12.jpeg)

![](13.jpeg)


脱敏也是如此，在方法添加【@NeedDesensitized】注解，需要脱敏的字段添加注解【@DesensitizedField】

.now：


最后调用接口可以看到返回脱敏成功

![](14.jpeg)

![](15.jpeg)

![](16.jpeg)

.now：


由于都是一样的原理，就没有对解密跟脱敏的切面类做截图说明了，具体的规则可以根据需求自由发挥下

.now：


这样，我们就在不修改service层源代码的前提下，去为系统的重要用户信息添加了加解密脱敏通用功能，提高程序的可重用性，同时提高了开发的效率。

.now：


我的介绍就到这里了，介绍的比较简单，但我觉得在日常开发中还是比较实用的，希望能对大家有所帮助。
