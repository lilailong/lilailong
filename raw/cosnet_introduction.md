# COSNET中执行框架、各个变量以及函数的意义
## 1 .
 class  |                         variables | 内存显示
 ------- |-------------------------- |-----------
** crf&reader**   |v_features：节点特征  |['bifollowers','followers',.......]
	     |f_classes:   边的类型|['friends','family','teachers',......]
	     |nodes:所有节点|[nodes1,nodes2......]
	     |factors｜edges:所有边|[factor1,factor2,factor3,.....]
             |f_class_map：边的类型索引 | \{'friends':0,'family':1,'teachers':2\}
             |f_feature_index:每个类型的边的权重索引| [0,3,6] (这里我们假设每个类型的边有3个类型的权重)
             |f_dim:累计的边的权重的数量|  上例中为3*3=9
             |v_in_reservoir：训练池buffer| [-1,-1,-1],哪个节点进入池中，其值改成该节点的索引
             |r_nodes:在池子中的节点 |[nodes1,nodes2,nodes3...]
             |r_edges:池子节点中的边 | [factor1,factor2,...]
             |delete_nodes:|
             |final_nodes: |
~~~~~~~~|~~~~~~~~~~~|~~~~~~~~~~~~~~~~~~~~
**Node**|number:节点的索引|第0个节点或者第一个节点.....
	|feature_map:特征及值 |{"bifollowers":0.0005,"followers":0.8888....}
	|known:true作为训练集、false为测试集 |true or false
	|label:该匹配对是否是匹配的| 0或者1
	|edge_list:该节点所连结的边的集合|[factor1,factor2,factor3...]
	|learning_maximizer:学习阶段的最大值|
	|inference_minmizer:推测阶段的最小值|
	|**&&function**|
	|learning_duals $$$\theta$$$ |每个节点计算能量value，如果这个节点有边相连，计算$$$\lambda$$$,其中$$\theta=\lambda+value/negibors$$ 
	|learning_duals_bar $$$\lambda$$$|辅助$$$\theta$$$
~~~~~~~~|~~~~~~~~~~~|~~~~~~~~~~~~~~~~~~~~
**factor** |key|边的类型的索引，例如'friends'的边索引是0、'family'的边索引是1
	|nodes|每条边连接着两个节点
	|learning_duals ***|各阶段的中间变量	
	
### Reader
local variables | Content | 
------------    | ------------- |
tokens (读nodes)         | ['?1','followers:0.005',bifollowes:'0.06',....]  |
tokens (读edges)         | ['#edge','622','56457','friend']
input   | [   tokens[0],tokens[1]  ]  |
tokens_of_tokens  | ['bifollwers','0.005'] 
	     
### Exp
Exp | variables| Content
------------ | ------------- | ------------
names | 各个测试数据集  | ["retweet_new",.....]
labeling_rates| 用于测试的数据占测试总量的占比  | [0.01,0.03,.....1]
reservoir_size|训练池子的大小|[100,100,2000,...]
	     
### Learning
Learning | variables| Content
------------ | ------------- | ------------
nodes | 用作训练的nodes集合 | [nodes[0],.....]
factors| nodes所连结的边 | [factor1,factor2....]
theta|Paramter参数|一共2\*v_features+f_class\*3个参数
	  	
	     
	     
	     
	     
	     
## 2.程序执行 
1. 进入main函数，调用test函数，test函数会首先进入到crf类，在crf中首先完成一定的初始化，（注意再python中一切皆为对象，所以在构造函数中传来传去的参数其实是同一片内存地址，例如说将在类CRF中v_feature传到类Reader中，类Reader对变量v_feature的操作，等到返回到crf中的时候同样生效）。
2. 程序进入到读文件步骤：该read_from_file函数的目的是加载文件到内存中处理，先将节点中所有的**特征属性**储存在v_feature中，再将边的**类别**储存f_class中，然后进入到下一个阶段，遍历所有节点，将所有节点的特征值保存在对应的特征上，即feature_map上，注意每个节点的label都有数值，这也是为了之后计算指标方便。
3. streaming_train阶段，流式训练阶段
   * shuffle,如果原始数据并没有进行混乱处理，则进行随机混乱操作
   * 构造训练池，其中的数据以lable_rate占比用作测试集，其他用于测试
   
----------
4. Learn阶段
	* 计算每个节点的能力，并有边的的节点计算出learning_duals($$$\theta$$$)和learning_duals_bar($$$\lambda$$$)
	* 计算整个社交网络的能量dual,首先计算正则化，该项的目的是防止过拟合，
	dual+=0.5*||W||^2
	* 计算有边的节点的能量和这些边的能量(由于边的能量的不可缺失性，如果所连节点的是unknown则直接随机一个数值)
	* 计算独立的节点的能量(计算测试集的节点，也就是node.known==true)
	* 综上，目标方程的大小计算出来，接下来我们是去优化它。
5. Projected gradient梯度优化阶段
	* 计算dW,$$$d\theta$$$,$$$d\lambda$$$
	* 学习率＝$$\frac{\gamma }{{||\{ dW,d\lambda \} ||}}$$6. 更新参数
	* 更新W,$$$d\theta$$$,$$$d\lambda$$$，再进入Learning阶段反复训练
	I
-----------	
7. Inference阶段(这个阶段我们利用上述的模型去预测池子里的所有匹配对)	$$\mathop {\min }\limits_Y (Y;X,W)$$	