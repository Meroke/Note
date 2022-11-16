### 1.逻辑回归

给出x,$x\in R^{nx}$ ，想要 $\hat{y}=  P(y=1 | x),0\leq i \leq 1$

属性: $w \in R^{nx}, b \in R$

输出： $\hat y = \sigma(W^T x +b)$

$\sigma(z) = {1}/(1+e^{-z})$, z越大，结果越趋向1;反之趋向0

loss function 损失函数：$\delta(\hat{y}, y) = -(ylog\hat y+ (1-y)log(1-\hat y))$

cost function 代价函数: 

![image-20220824213858464](E:\File\TyporaNote\img\DeepL\image-20220824213858464.png)



### 3.1 正态分布与平方损失

意义：用正态分布来求解使得线性回收收敛的参数

![image-20220829222345713](E:\File\TyporaNote\img\DeepL\image-20220829222345713.png)

![image-20220829222416242](E:\File\TyporaNote\img\DeepL\image-20220829222416242.png)



![image-20220829222549832](E:\File\TyporaNote\img\DeepL\image-20220829222549832.png)

​                                                  			   $\epsilon  = x - 均值 \mu$  

![image-20220829222938360](E:\File\TyporaNote\img\DeepL\image-20220829222938360.png)

结论： 3.1.15 结果数值越小，其参数值越能让线性回收收敛