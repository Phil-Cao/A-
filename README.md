# A-
实验目的】
通过八数码问题了解并掌握A*算法
八数码问题介绍：
八数码问题：在3×3的方格棋盘上，摆放着1到8这八个数码，有1个方格是空的，其初始状态如图1所示，要求对空格执行空格左移、空格右移、空格上移和空格下移这四个操作使得棋盘从初始状态到目标状态。
例如：
2	8	3			1	2	3
1	6	4			8	0	4
7	0	5			7	6	5
(a) 初始状态          (b) 目标状态
图1 八数码问题示意图
请任选一种盲目搜索算法（广度优先搜索或深度优先搜索）或任选一种启发式搜索方法（全局择优搜索，加权状态图搜索，A 算法或 A* 算法）编程求解八数码问题（初始状态任选）。选择一个初始状态，画出搜索树，填写相应的OPEN表和CLOSED表，给出解路径，对实验结果进行分析总结，得出结论。
【实验原理】
A*算法介绍：
A*算法是一种常用的启发式搜索算法。
在A*算法中，一个结点位置的好坏用估价函数来对它进行评估。A*算法的估价函数可表示为： 
f'(n) = g'(n) + h'(n) 
这里，f'(n)是估价函数，g'(n)是起点到终点的最短路径值（也称为最小耗费或最小代价），h'(n)是n到目标的最短路经的启发值。由于这个f'(n)其实是无法预先知道的，所以实际上使用的是下面的估价函数：
f(n) = g(n) + h(n) 
其中g(n)是从初始结点到节点n的实际代价，h(n)是从结点n到目标结点的最佳路径的估计代价。在这里主要是h(n)体现了搜索的启发信息，因为g(n)是已知的。用f(n)作为f'(n)的近似，也就是用g(n)代替g'(n)，h(n)代替h'(n)。这样必须满足两个条件：（1）g(n)>=g'(n)（大多数情况下都是满足的，可以不用考虑），且f必须保持单调递增。（2）h必须小于等于实际的从当前节点到达目标节点的最小耗费h(n)<=h'(n)。第二点特别的重要。可以证明应用这样的估价函数是可以找到最短路径的。

A*算法的做法：
1）建立一个队列，计算初始结点的估价函数f，并将初始结点入队，设置队列头和尾指针。
2）取出队列头（队列头指针所指）的结点，如果该结点是目标结点，则输出路径，程序结束。否则对结点进行扩展。 
3）检查扩展出的新结点是否与队列中的结点重复，若与不能再扩展的结点重复（位于队列头指针之前），则将它抛弃；若新结点与待扩展的结点重复（位于队列头指针之后），则比较两个结点的估价函数中g的大小，保留较小g值的结点。跳至第五步。
4）如果扩展出的新结点与队列中的结点不重复，则按照它的估价函数f大小将它插入队列中的头结点后待扩展结点的适当位置，使它们按从小到大的顺序排列，最后更新队列尾指针。
5）如果队列头的结点还可以扩展，直接返回第二步。否则将队列头指针指向下一结点，再返回第二步。

A*算法流程图：
八数码问题：在3×3的方格棋盘上，摆放着1到8这八个数码，有1个方格是空的，其初始状态如图1所示，要求对空格执行空格左移、空格右移、空格上移和空格下移这四个操作使得棋盘从初始状态到目标状态。
例如：
2	8	3			1	2	3
1	6	4			8		4
7	0	5			7	6	5
(a) 初始状态          (b) 目标状态
图1 八数码问题示意图
请任选一种盲目搜索算法（广度优先搜索或深度优先搜索）或任选一种启发式搜索方法（全局择优搜索，加权状态图搜索，A 算法或 A* 算法）编程求解八数码问题（初始状态任选）。选择一个初始状态，画出搜索树，填写相应的OPEN表和CLOSED表，给出解路径，对实验结果进行分析总结，得出结论。
 


用A*算法求解八数码问题的一个实例：
 
细节分析：
核心算法伪代码：
　 创建两个表，OPEN表保存所有已生成而未考察的节点，CLOSED表中记录已访问过的节点。算起点的估价值，将起点放入OPEN表。
　　while(OPEN!=NULL)
　　{
　　从OPEN表中取估价值f最小的节点n;
if(n节点==目标节点)
{break;}
　　for(当前节点n 的每个子节点X)
　　 {
　　算X的估价值;
　　if(X in OPEN)
　　  {
if( X的估价值小于OPEN表的估价值 )
{把n设置为X的父亲;
　　更新OPEN表中的估价值; //取最小路径的估价值}
　　  }
if(X inCLOSE) 
{
if( X的估价值小于CLOSE表的估价值 )
{把n设置为X的父亲;
　　更新CLOSE表中的估价值;
　　把X节点放入OPEN //取最小路径的估价值}
　　  }
if(X not inboth)
{把n设置为X的父亲;
　　求X的估价值;
　　并将X插入OPEN表中; //还没有排序}
　　 }//end for
　　将n节点插入CLOSE表中;
　　按照估价值将OPEN表中的节点排序; //实际上是比较OPEN表内节点f的大小，从最小路径的节点向下进行。
　　}//end while(OPEN!=NULL)
保存路径，即 从终点开始，每个节点沿着父节点移动直至起点

八数码问题的一个状态实际上是0~9的一个排列，对于任意给定的初始状态和目标，不一定有解，也就是说从初始状态不一定能到达目标状态。因为排列有奇排列和偶排列两类，从奇排列不能转化成偶排列。如果一个数字0~8的随机排列871526340，用F(X)表示数字X前面比它小的数的个数，全部数字的F(X)之和为Y=∑(F(X))，如果Y为奇数则称原数字的排列是奇排列，如果Y为偶数则称原数字的排列是偶排列。因此，可以在运行程序前检查初始状态和目标状态的排序的奇偶行是否相同，相同则问题可解，应当能搜索到路径。否则无解。
由于每次需要对open表的节点进行扩展，而进行扩展时需进行以下判断：
1.是否已经进行过扩展；
2.先辈节点是否进行过扩展；
3.open表中是否已存在该节点；
4.close表中是否已存在该节点；
如果是的，则删除该节点，在处理这些情况的时候要谨慎指针的变化，若果有一个指针操作出现问题，可能会导致整个open表或close表无法继续使用！


输入数据：输入初始状态和目标状态
初始状态：x x x                     目标状态：x  x  x
          x x x                               x  x  x
          x x x                               x  x  x
输出数据：输出从初始状态到目标状态的路线及估价函数的取值情况

【实验结果】
命令界面输入起始状态与目标状态：
 
运行结果保留在Result.txt文档中：
 
 
 