#include <iostream>
#include <stdlib.h>
#include <windows.h>
#include <fstream>
using namespace std;

#define size 3

typedef int status[size][size];

typedef struct Node
{
    status data;                    //节点所存储的状态
    struct Node *parent;            //指向节点的父亲节点

    struct SpringLink *child;       //指向节点的后继节点
    struct Node *next;              //指向链表的后一个节点
    int f_value;                    //由初始状态经由当前节点至目标状态的总耗散值
    int g_value;                    //由初始状态经到当前节点实际耗散值
    int h_value;                    //由当前节点到目标状态的预计耗散值
}NNode, *PNode;


//定义表述指向当前节点的扩展节点的链表
typedef struct SpringLink
{
    struct Node *pointData;
    struct SpringLink *next;
}SPLink, *PSPLink;

PNode open;
PNode closed;

//计算棋盘状态的逆序数
int InverseNumber(status a)
{
    int i, j, sum=0;
    int data_chang[size*size]={0};

    //将二维数组转换成一维数组，以方便求逆序数
    for(i=0;i<size;i++)
    {
        for(j=0;j<size;j++)
        {
            data_chang[i*size+j]=a[i][j];
        }
    }


    //计算序列中除零外的逆序数
    for(i=0;i<=size*size;i++)
    {
        if(data_chang[i]!=0)
        {
            for(j=i;j>=0;j--)
            {
                if(data_chang[i]<data_chang[j])
                {
                    sum++;
                }
            }
        }
    }
    return sum;
}

//判断是否存在解决方案
bool hasSolution(status startStatus,status targetStatus)
{
    int startInverseNumber=InverseNumber(startStatus);
    int tatgetInverseNumber=InverseNumber(targetStatus);

    //判断初始状态和目标状态除零外的序列逆序数奇偶性，相同则可求值，不同则不可求
    if( (startInverseNumber%2) != (tatgetInverseNumber%2) )
    {
        return false;
    }
    else
    {
        return true;
    }
}


//初始化一个空链表
void initLink(PNode &Head)
{
    Head = (PNode)malloc(sizeof(NNode));
    Head->next = NULL;
}


//判断链表是否为空
bool isEmpty(PNode Head)
{
    if(Head->next == NULL)
    {
        return true;
    }
    else
    {
        return false;
    }
}


void popNode(PNode &Head , PNode &FNode)
{
    if(isEmpty(Head))
    {
        FNode = NULL;
        return;
    }
    FNode = Head->next;
    Head->next = Head->next->next;
    FNode->next = NULL;
}

void addSpringNode(PNode &Head , PNode newData)
{
    PSPLink newNode = (PSPLink)malloc(sizeof(SPLink));
    newNode->pointData = newData;

    newNode->next = Head->child;
    Head->child = newNode;
}

void freeSpringLink(PSPLink &Head)
{
    PSPLink tmm;

    while(Head != NULL)
    {
        tmm = Head;
        Head = Head->next;
        free(tmm);
    }
}

void freeLink(PNode &Head)
{
    PNode tmn;

    tmn = Head;
    Head = Head->next;
    free(tmn);

    while(Head != NULL)
    {
        freeSpringLink(Head->child);
        tmn = Head;
        Head = Head->next;
        free(tmn);
    }
}

//向普通链表中添加一个节点
void addNode(PNode &Head , PNode &newNode)
{
    newNode->next = Head->next;
    Head->next = newNode;
}

//向非递减排列的链表中添加一个节点
void addAscNode(PNode &Head , PNode &newNode)
{
    PNode P;
    PNode Q;

    P = Head->next;
    Q = Head;
    while(P != NULL && P->f_value < newNode->f_value)
    {
        Q = P;
        P = P->next;
    }
    //上面判断好位置之后，下面就是简单的插入节点了
    newNode->next = Q->next;
    Q->next = newNode;
}

//计算节点到目标状态的预计耗散值
int computeh_value(PNode theNode,status targetStatus)
{
    int num = 0;
    for(int i = 0 ; i < 3 ; i++)
    {
        for(int j = 0 ; j < 3 ; j++)
        {
            if(theNode->data[i][j] != targetStatus[i][j])
            {
                num++;
            }
        }
    }
    return num;
}

//计算节点的f，g，h值
void computeAllValue(PNode &theNode , PNode parentNode, status targetStatus)
{
    if(parentNode == NULL)
    {
        theNode->g_value = 0;
    }
    else
    {
        theNode->g_value = parentNode->g_value + 1;
    }

    theNode->h_value = computeh_value(theNode,targetStatus);
    theNode->f_value = theNode->g_value + theNode->h_value;
}

//初始化函数，进行算法初始条件的设置
void initial(status startStatus,status targetStatus)
{
    //初始化open以及closed表
    initLink(open);
    initLink(closed);

    //初始化起始节点，令初始节点的父节点为空节点
    PNode NULLNode = NULL;
    PNode StartNode = (PNode)malloc(sizeof(NNode));
    for(int i = 0 ; i < 3 ; i++)
    {
        for(int j = 0 ; j < 3 ; j++)
        {
            StartNode->data[i][j] = startStatus[i][j];
        }
    }
    StartNode->parent = NULL;
    StartNode->child = NULL;
    StartNode->next = NULL;
    computeAllValue(StartNode, NULLNode,targetStatus);

    //起始节点进入OPEN表
    addAscNode(open , StartNode);
}

//将B节点的状态赋值给A节点
void statusAEB(PNode &ANode , PNode BNode)
{
    for(int i = 0 ; i < 3 ; i++)
    {
        for(int j = 0 ; j < 3 ; j++)
        {
            ANode->data[i][j] = BNode->data[i][j];
        }
    }
}


//两个节点是否有相同的状态
bool hasSameStatus(PNode ANode , PNode BNode)
{
    for(int i = 0 ; i < size ; i++)
    {
        for(int j = 0 ; j < size ; j++)
        {
            if(ANode->data[i][j] != BNode->data[i][j])
                return false;
        }
    }
    return true;
}

//节点与其祖先节点是否有相同的状态
bool hasAnceSameStatus(PNode OrigiNode , PNode AnceNode)
{
    while(AnceNode != NULL)
    {
        if(hasSameStatus(OrigiNode , AnceNode))
            return true;
        AnceNode = AnceNode->parent;
    }
    return false;
}

//取得方格中空的格子的位置
void getPosition(PNode theNode , int &row , int &col)
{
    for(int i = 0 ; i < size ; i++)
    {
        for(int j = 0 ; j < size ; j++)
        {
            if(theNode->data[i][j] == 0)
            {
                row = i;
                col = j;
                return;
            }
        }
    }
}

//交换两个数字的值
void changeAB(int &a , int &b)
{
    int c;
    c = b;
    b = a;
    a = c;
}

//检查相应的状态是否在某一个链表中
bool inLink(PNode spciNode , PNode theLink , PNode &theNodeLink , PNode &preNode)
{
    preNode = theLink;
    theLink = theLink->next;

    while(theLink != NULL)
    {
        if(hasSameStatus(spciNode , theLink))
        {
            theNodeLink = theLink;
            return true;
        }
        preNode = theLink;
        theLink = theLink->next;
    }
    return false;
}

//产生节点的后继节点链表
void SpringLink(PNode theNode , PNode &spring, status targetStatus)
{
    int row;
    int col;

    getPosition(theNode , row , col);

    //空的格子右边的格子向左移动
    if(col != 2)
    {
        PNode rlNewNode = (PNode)malloc(sizeof(NNode));
        statusAEB(rlNewNode, theNode);
        changeAB(rlNewNode->data[row][col], rlNewNode->data[row][col + 1]);
        if(hasAnceSameStatus(rlNewNode, theNode->parent))
        {
            free(rlNewNode);//与父辈相同，丢弃本节点
        }
        else
        {
            rlNewNode->parent = theNode;
            rlNewNode->child = NULL;
            rlNewNode->next = NULL;
            computeAllValue(rlNewNode, theNode, targetStatus);
            //将本节点加入后继节点链表
            addNode(spring, rlNewNode);
        }
    }
    //空的格子左边的格子向右移动
    if(col != 0)
    {
        PNode lrNewNode = (PNode)malloc(sizeof(NNode));
        statusAEB(lrNewNode, theNode);
        changeAB(lrNewNode->data[row][col], lrNewNode->data[row][col - 1]);
        if(hasAnceSameStatus(lrNewNode, theNode->parent))
        {
            free(lrNewNode);//与父辈相同，丢弃本节点
        }
        else
        {
            lrNewNode->parent = theNode;
            lrNewNode->child = NULL;
            lrNewNode->next = NULL;
            computeAllValue(lrNewNode, theNode, targetStatus);
            addNode(spring , lrNewNode);
        }
    }
    //空的格子上边的格子向下移动
    if(row != 0)
    {
        PNode udNewNode = (PNode)malloc(sizeof(NNode));
        statusAEB(udNewNode , theNode);
        changeAB(udNewNode->data[row][col], udNewNode->data[row - 1][col]);
        if(hasAnceSameStatus(udNewNode, theNode->parent))
        {
            free(udNewNode);//与父辈相同，丢弃本节点
        }
        else
        {
            udNewNode->parent = theNode;
            udNewNode->child = NULL;
            udNewNode->next = NULL;
            computeAllValue(udNewNode, theNode, targetStatus);
            //将本节点加入后继节点链表
            addNode(spring, udNewNode);
        }
    }
    //空的格子下边的格子向上移动
    if(row != 2)
    {
        PNode duNewNode = (PNode)malloc(sizeof(NNode));
        statusAEB(duNewNode, theNode);
        changeAB(duNewNode->data[row][col], duNewNode->data[row + 1][col]);
        if(hasAnceSameStatus(duNewNode, theNode->parent))
        {
            free(duNewNode);//与父辈相同，丢弃本节点
        }
        else
        {
            duNewNode->parent = theNode;
            duNewNode->child = NULL;
            duNewNode->next = NULL;
            computeAllValue(duNewNode, theNode, targetStatus);
            //将本节点加入后继节点链表
            addNode(spring , duNewNode);
        }
    }
}

//输出给定节点的状态
void outputStatus(PNode stat)
{
    ofstream out("result.txt",ios::app);
    if(out.is_open())
    {
        for(int i = 0 ; i < 3 ; i++)
        {
            for(int j = 0 ; j < 3 ; j++)
            {
                out << stat->data[i][j] << " ";
            }
            out << endl;
        }
    }

}

//输出最佳的路径
void outputBestRoad(PNode goal)
{
    int deepnum = goal->g_value;

    if(goal->parent != NULL)
    {
        outputBestRoad(goal->parent);
    }
    ofstream out("result.txt",ios::app);
    if(out.is_open())
    {
        out << "No." << deepnum-- << " step's state:" << endl;
    }

    outputStatus(goal);
}


void AStar(status startStatus,status targetStatus)
{
    PNode tmpNode;                      //指向从open表中拿出并放到closed表中的节点的指针
    PNode spring;                       //tmpNode的后继节点链
    PNode tmpLNode;                     //tmpNode的某一个后继节点
    PNode tmpChartNode;
    PNode thePreNode;                   //指向将要从closed表中移到open表中的节点的前一个节点的指针
    bool getGoal = false;               //标识是否达到目标状态
    long numcount = 1;                  //记录从open表中拿出节点的序号

    initial(startStatus,targetStatus);  //对函数进行初始化
    initLink(spring);                   //对后继链表的初始化
    tmpChartNode = NULL;

    ofstream out("result.txt",ios::app);
    if(out.is_open())
    {
        out << "The status of OPEN: " << endl;
    }

    while(!isEmpty(open))
    {
        //从OPEN表中拿出f值最小的元素,并将拿出的元素放入CLOSED表中
        popNode(open, tmpNode);
        addNode(closed, tmpNode);

        ofstream out("result.txt",ios::app);
        if(out.is_open())
        {
            out << "No. " << numcount++ << "state:" << endl;
            outputStatus(tmpNode);
            out << "f(x):" << tmpNode->f_value << endl;
            out << "g(x):" << tmpNode->g_value << endl;
            out << "h(x):" << tmpNode->h_value << endl;
        }

        //如果拿出的元素是目标状态则跳出循环
        if(computeh_value(tmpNode, targetStatus) == 0)
        {
            getGoal = true;
            break;
        }

        //产生当前检测节点的后继(与祖先不同)节点列表，产生的后继节点的parent属性指向当前检测的节点
        SpringLink(tmpNode, spring, targetStatus);

        //遍历检测节点的后继节点链表
        while(!isEmpty(spring))
        {
            popNode(spring , tmpLNode);
            //状态在OPEN表中已经存在，thePreNode参数在这里并不起作用
            if(inLink(tmpLNode , open , tmpChartNode , thePreNode))
            {
                addSpringNode(tmpNode , tmpChartNode);
                if(tmpLNode->g_value < tmpChartNode->g_value)
                {
                    tmpChartNode->parent = tmpLNode->parent;
                    tmpChartNode->g_value = tmpLNode->g_value;
                    tmpChartNode->f_value = tmpLNode->f_value;
                }
                free(tmpLNode);
            }
            //状态在CLOSED表中已经存在
            else if(inLink(tmpLNode, closed, tmpChartNode, thePreNode))
            {
                addSpringNode(tmpNode, tmpChartNode);
                if(tmpLNode->g_value < tmpChartNode->g_value)
                {
                    PNode commu;
                    tmpChartNode->parent = tmpLNode->parent;
                    tmpChartNode->g_value = tmpLNode->g_value;
                    tmpChartNode->f_value = tmpLNode->f_value;
                    freeSpringLink(tmpChartNode->child);
                    tmpChartNode->child = NULL;
                    popNode(thePreNode, commu);
                    addAscNode(open, commu);
                }
                free(tmpLNode);
            }
            //新的状态即此状态既不在OPEN表中也不在CLOSED表中
            else
            {
                addSpringNode(tmpNode, tmpLNode);
                addAscNode(open, tmpLNode);
            }
        }
    }

    //目标可达的话，输出最佳的路径
    if(getGoal)
    {
        ofstream out("result.txt",ios::app);
        if(out.is_open())
        {
            out << endl;
            out << "The length:" << tmpNode->g_value << endl;
        }
        outputBestRoad(tmpNode);
    }

    //释放节点所占的内存
    freeLink(open);
    freeLink(closed);
}

int main()
{

    //开始状态和目标状态
    status startStatus;
    status targetStatus;
    cout<<"Please input the start solution: "<<endl;
    for(int i=0;i<size;i++)
        for(int j=0;j<size;j++)
        {
            cin>>startStatus[i][j];
        }
    cout<<"Please input the goal solution: "<<endl;
    for(int i=0;i<size;i++)
        for(int j=0;j<size;j++)
        {
            cin>>targetStatus[i][j];
        }

    if(hasSolution(startStatus,targetStatus))
    {
        AStar(startStatus,targetStatus);
    }
    else
    {
        cout << "No result！" << endl;
    }

    system("pause");
}
