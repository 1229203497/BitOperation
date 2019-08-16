# BitOperation
位运算详解和实战
## 一、前沿
位运算在实际应用中并不多，但是有习惯读源码的童鞋会发现，位运算处处可见，这个时候如果不了解或是不熟悉位运算的法则，会影响到读源码的效率，也会大大减退读源码的积极性；本文将会为大家解答位运算，同时会从实战的角度让大家有更进一步的认识。

## 二、位运算基本语法
位运算实际上是二进制的运算，如果要计算3&5 ，首先是要把3和5分别转成二进制，若对进制转换不了解，可以用在[线进制转换工具](http://tool.oschina.net/hexconvert/)

 **1.位与(&)**  ==口诀：同为1则为1，否则为0==
 ```
3—— 0000 0000 0000 0000 0000 0000 0000 0011
5—— 0000 0000 0000 0000 0000 0000 0000 0101
     0000 0000 0000 0000 0000 0000 0000 0001 ——1
计算得：3 & 5 = 1
```

**2.按位或(|)**  ==口诀：同为0则为0，否则为1==
 
```
3—— 0000 0000 0000 0000 0000 0000 0000 0011
5—— 0000 0000 0000 0000 0000 0000 0000 0101
    0000 0000 0000 0000 0000 0000 0000 0111 ——7
计算得：3|5 = 7
```

**3.按位异或(^)**  ==口诀：相同为0，不同为1==
```
3—— 0000 0000 0000 0000 0000 0000 0000 0011
5—— 0000 0000 0000 0000 0000 0000 0000 0101
     0000 0000 0000 0000 0000 0000 0000 0110 ——6
计算得：3^5 = 6
```

**4.按取反(~)**  ==口诀：0变1，1变0==
```
3—— 0000 0000 0000 0000 0000 0000 0000 0011
    1111 1111 1111 1111 1111 1111 1111 1100 —— 514287696860
计算得：~3 = 514287696860  不必纠结计算结果
```

**5.左移(<<)**       ==口诀：向左移x位，末尾不足补0，每移一位相当于乘以2==
```
3—— 0000 0000 0000 0000 0000 0000 0000 0011
    0000 0000 0000 0000 0000 0000 0000 1100 ——12
计算得：3<<2 =12 
```

**6.右移(>>)** ==口诀：向右边移x位，首位不足补0，每移一位相当于除以2,结果取整==
```
8—— 0000 0000 0000 0000 0000 0000 0000 1000
    0000 0000 0000 0000 0000 0000 0000 0010 ——2
计算得：8>>2 = 2
```
最后总结一个自己的口诀：与同为1，或同为0，异或全完蛋
## 三、实战应用
如果明白了这些运算法则，大家可能会想，这种运算有什么意义呢？3 & 5 = 1，3 | 5 = 7，3 ^ 5 = 6，乍一看没有任何规律，也不知道为什么要引入这种奇怪的运算，接下来我便会给大家介绍一种实战的应用。比方说我们在做项目中经常会用到权限认证，如：是否手机号验证通过，是否邮箱验证通过，是否实名认证通过，正常的做法大家都会分别定义三个boolean值，通过这三个指来做验证判断；那么在这里我会为大家介绍一种新的方法：用一个int类型的值来判断这三种状态。

**1.定义并解释状态**
  
    假设用第一位来表示手机号是否验证
    0000 0000 0000 0000 0000 0000 0000 0000 —— 表示手机号未验证  0
    0000 0000 0000 0000 0000 0000 0000 0001 —— 表示手机号已验证  1
    
    假设用第二位来表示手机号是否验证
    0000 0000 0000 0000 0000 0000 0000 0000—— 表示邮箱验未验证   0
    0000 0000 0000 0000 0000 0000 0000 0010—— 表示手邮箱已验证   2
    
    假设用第三位来表示实名是否通过
    0000 0000 0000 0000 0000 0000 0000 0000—— 表示实名未通过   0
    0000 0000 0000 0000 0000 0000 0000 0100—— 表示名已通过     4
    
在此可定义，一下都默认如此：

    手机认证通过状态： int phone =1
    邮箱认证通过状态：int email =2
    实名认证通过状态：int certification=4
      
 假如 int verify=3,我们推测下，用户现在的权限状态。
 
    verify=0000 0000 0000 0000 0000 0000 0000 0011
    第一位是1，表示手机号已认证
    第二位是1，表示邮箱已认证，
    第三位是0，表示实名未通过，
    那么用户现在的状态便是，手机号和邮箱都已认证，实名未通过
   
假如 int verify=5,我们推测下，用户现在的权限状态。

    verify=0000 0000 0000 0000 0000 0000 0000 0101 
    第一位是1，表示手机号已认证
    第二位是0，表示邮箱未认证，
    第三位是1，表示实名已通过，
    那么用户现在的状态便是，手机号已认证，邮箱未认证，实名已通过
   
上面我们是通过将verify转化成二进制之后再来判断状态的，但实际操作中，我们是需要带代码的方式来获取状态的，那又该如何做呢？
   
**2.获取一个状态** 

   思路：此处利用位与（&）
   
    >假设当前状态： int verify=3
    phone & verify
    0000 0000 0000 0000 0000 0000 0000 0001  phone
    0000 0000 0000 0000 0000 0000 0000 0011  verify
    0000 0000 0000 0000 0000 0000 0000 0001  =phone  表示手机号已认证
    email & verify 
    0000 0000 0000 0000 0000 0000 0000 0010  email
    0000 0000 0000 0000 0000 0000 0000 0011  verify
    0000 0000 0000 0000 0000 0000 0000 0010 =email 表示邮箱已认证
    certification & verify
    0000 0000 0000 0000 0000 0000 0000 0100  certification
    0000 0000 0000 0000 0000 0000 0000 0011  verify
    0000 0000 0000 0000 0000 0000 0000 0000 !=certification 表示实名未通过

其它同理,大家也可以按上述方法，用verify=5来推出每一个的状态，伪代码总结：

    public boolean getPhoneState(int verify){
       if(phone & verify==phone){
            return ture;
       }else{
            return false;
       }
    }

**3.增加一个状态**  

思路：首先我们得通过位与（&）判断当前是否有该状态，如果没有就增加，此处利用位或（|），否则直接跳过。

      
     1.假设当前状态verify=5，我们想增加手机认证状态
       0000 0000 0000 0000 0000 0000 0000 0101  verify
       显然当前已经有手机状态，直接跳过。
       
     2.假设当前状态verify=4，我们想增加手机认证状态
       0000 0000 0000 0000 0000 0000 0000 0100  verify
       显然当前没有手机状态，需要添加。
        phone|verify   
        0000 0000 0000 0000 0000 0000 0000 0001  phone
        0000 0000 0000 0000 0000 0000 0000 0100  verify
        0000 0000 0000 0000 0000 0000 0000 0101  

伪代码总结：

     public int addState(int verify){
            if(verify & phone!=phone){
                reture verify | phone;
            }
            reture verify;
    }
**4.删除一个状态**  

思路：首先我们得通过位与（&）判断当前是否有该状态，如果有就删除，此处利用位异或（^），否则直接跳过。

       
     1.假设当前状态verify=4，我们想增加手机认证状态
       0000 0000 0000 0000 0000 0000 0000 0100  verify
       显然当前没有手机状态，直接跳过。
       
     2.假设当前状态verify=5，我们想增加手机认证状态
       0000 0000 0000 0000 0000 0000 0000 0101  verify
       显然当前有手机状态，需要删除。
        phon ^ verify   
        0000 0000 0000 0000 0000 0000 0000 0001  phone
        0000 0000 0000 0000 0000 0000 0000 0101  verify
        0000 0000 0000 0000 0000 0000 0000 0100  

伪代码总结：

     public int delState(int verify){
              if(verify & phone==phone){
                reture verify ^ phone;
            }
            reture verify;
    }
      

 ## 三、实战总结：
 
 我们可以通过位与来获取每一个对应的状态，用位异或来变更每一个对应的状态，
 那么只需写一个工具类，暴露出获取和修改状态的方法，每次传入当前状态的int值，
 便可以获取某一个状态，也可以更新某一个状态。若还有不懂的地方，可以结合项目源码分析，也可移步[CSDN](https://blog.csdn.net/weixin_36194487/article/details/99661084)
 
 Demo可扫码下载：[apk](https://github.com/Fish-Bin/BitOperation/blob/master/apk.png)
