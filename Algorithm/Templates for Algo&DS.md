# Templates for Algo&DS

## 二叉树

### 二叉树非递归遍历

#### 先序

```c++
//沿着左子树往下访问，同时将访问节点的右孩子入栈
void vistAlongLeftBranch(node* root,stack<node*> &st){
	while(root){
		visit(root);//先序在此处访问节点
		st.push(root->rchild);
		root=root->lchild;
	}
}
void preOrder(node* root){
	stack<node*> st;
	node *p=root;
	while(1){
		visitAlongLeftBranch(p,st);
		if(st.empty())
			break;
		p=st.top();
		st.pop();
	}
}
```

#### 中序

```c++
//将左分支依次入栈，出栈时访问节点并将其右子树按同样方式入栈
void goAlongLeftBranch(node* root,stack<node*> &st){
	while(root){//中序不在该函数内visit，而是在出栈时visit
		st.push(root);
		root=root->lchild;
	}
}
void inOrder(node* root){
	stack<node*> st;
	node* p=root;
	while(1){
		goAlongLeftBranch(p,st);
		if(st.empty())
			break;
		p=st.top();
		st.pop();
		visit(p);//中序在此处访问节点，只是将先序中vistAlongLeftBranch中的两句移到了这里
		p=p->rchild;
	}
}
```

#### 后序

```C++
//后序的逆序与先序对称
//按中右左入栈，出栈时即是左右中
//将中右左形式的先序遍历的visit改成入ans栈，最后统一访问即可
void goAlongRightBranch(node* root,stack<node*> &st,stack<node*> &ans){
	while(root){//注意对照先序
		ans.push(root);//等同于先序中的visit()
		st.push(root->lchild);//st存放右分支链的左孩子
		root=root->rchild;
	}
}
void postOrder(node* root){
	stack<node*> st,ans;
	node* p=root;
	while(1){
		goAlongRightBranch(p,st,ans);
		if(st.empty())
			break;
		p=st.top();
		st.pop();
	}
	while(!ans.empty()){//依次访问ans栈中所有节点
		visit(ans.top());
		ans.pop();
	}
}
```

#### 层次

```c++
//层次遍历
void levelOrder(node *root){
	queue<int> q;
	q.push(root);
	node* temp=NULL;
	while(!q.empty()){
		temp=q.front();
		q.pop();
		vist(temp);
		if(temp->lchild)
			q.push(temp->lchild);
		if(temp->rchild)
			q.push(temp->rchild);
	}
}
```

### 二叉树的构造

中序可以和先序、后续、层次任意一个确定一棵二叉树

```C++
//通过中序遍历和先序序列构造一棵二叉树
node* create(int pre[],int in[],int preL,int preR,int inL,int inR){
	if(preL>preR)//先序序列长度小于0，直接返回
		return NULL;
	node* root=new node;
	root->data=pre[preL];
	int k;
	for(k=inL;k<=inR;k++){
		if(in[k]==pre[preL])
			break;
	}
	int numLeft=k-inL;
	root->lchild=create(pre,in,preL+1,preL+numLeft,inL,k-1);
	root->rchild=create(pre,in,preL+numLeft+1,preR,k+1,inR);
	return root;
}
```

### 二叉树的高度

```c++
int Depth(node *root){
	if(!root)
		return 0;
	return 1+max(Depth(root->lchild),Depth(root->rchild));
}
```

### 二叉树宽度递归 

```c++
int cnt[MaxSize];//hash，cnt[i]为i层节点数
void getBiWid(node *root,int k,int &MaxWith){ //k变量为当前层数 默认从第一层开始
	if(!root)
		return;
	cnt[k]++;
	MaxWidth=max(MaxWidth,cnt[k]);//更新最大宽度
	getBiWid(root->lchild,k+1);
	getBiWid(root->rchild,k+1);
}
int width(node *root){
	int MaxWidth=0;
	getBiWid(root,1,MaxWidth);
	return MaxWidth;
}
```

## 二叉查找树BST

```c++
struct node{
	int data;
	node* lchild=NULL;
	node* rchild=NULL;
	node(int x):data(x){}
};
```

### BST插入

O(logn)

```C++
void insert(node* &root,int x){//需要改变root本身，所以要用引用
	if(root==NULL){
		root=new node(x);//这就是root设为引用的原因
		return;
	}
	if(x==root->data)//说明节点已存在，不用插入
		return；
	else if(x<root->data)
		insert(root->lchild,x);
	else
		insert(root->rchild,x);
}
```

### BST建立

O(nlogn)

```C++
node* create(int data[],int n){
	node* root=NULL;
	for(int i=0;i<n;i++)
		insert(root,data[i]);
	return root;
}
```

### BST查找

#### 递归

O(logn)

```C++
node* search(node* root,int x){
	if(!root)
		return NULL;
	if(x==root->data)
		return root;
	else if(x<root->data)
		return search(root->lchild,x);
	else
		return search(root->rchild,x);
}
```

#### 非递归

O(logn)

```c++
node* search(node* root,int x){
	while(root&&x!=root->data){
		if(x<root->data)
			root=root->lchild;
		else
			root=root->rchild;
	}
	return root;
}
```

#### 寻找权值最大的节点

```C++
node* findMax(node* root){//最右节点权值最大
	while(root->rchild)//注意这里一定是判断孩子为空作为停止条件，因为最终是要将root返回的
		root=root->rchild;
	return root;
}
```

#### 寻找权值最小节点

```C++
node* findMin(node* root){//最左节点权值最小
	while(root->lchild)
		root=root->lchild;
	return root;
}
```

### BST删除节点

```c++
//删除data为x的节点
void deleteNode(node* &root,int x){//注意是引用，因为会直接修改root
	if(root==NULL)
		return;
	if(root->data==x){//删除节点分三种情况，叶子，有左孩，有右孩
		if(!root->lchild&&!root->rchild){//叶子结点直接删除
			delete root;
			root=NULL;//防止野指针
		}else if(root->lchild){//若欲删除节点有左子树，那么用其前驱覆盖它，然后递归删除其前驱
			node* pre=findMax(root->lchild);//左子树中的最大节点是根节点的前驱
			root->data=pre->data;//用前驱覆盖root
			deleteNode(pre,pre->data);//BST的规则被破坏了，因此要递归删除节点pre
		}else{//若欲删除节点有右子树，那么用其后继覆盖它，然后递归删除其后继
			node *next=findMin(root->rchild);//右子树中的最小节点是根节点的后继
			root->data=next->data;//用后继覆盖root
			deleteNode(next,next->data);//BST的规则被破坏了，因此要递归删除next
		}
	}else if(x<root->data){
		deleteNode(root->lchild,x);
	}else{
		deleteNode(root->rchild,x);
	}
}
```

## 平衡二叉查找树AVL

```C++
struct node{
	int data,height;//v为权值，height为当前子树高度
	node* lchild=NULL;
	node* rchild=NULL;
	node(int x，int h):data(x),height(h){}
};
```

### AVL树高

```c++
//获取以root为根节点的子树的当前height
int getHeight(node* root){
	if(root==NULL)
		return 0;
	return root->height;
}
```

### 平衡因子

```c++
int getBalanceFactor(node* root){
	if(root==NULL)
		return 0;
	return getHeight(root->lchild)-getHeight(root->rchild);//左子树高度减右子树高度
}
```

### AVL失衡调整

原则：只要将最靠近插入节点的失衡节点调整到正常，路径上的所有节点就都会平衡

#### 更新节点height

```C++
void updateHeight(node* root){
	root->height=1+max(getHeight(root->lchild),getHeight(root->rchild));
}
```

#### 左旋

```C++
//左旋，root的右孩子换到root的位置，root变为其左孩子
void lRotate(node* &root){//会直接改变root，所以用引用
	node* temp=root->rchild;//temp是root的右孩子
	root->rchild=temp->lchild;//左旋时，temp的左孩子变成root的右孩子，其height最终并不变化
	temp->lchild=root;//temp的左孩子root变为
	updateHeight(root);//一定要先更新root的，因为此时root已在temp下层，height是根据子树高度计算的
	updateHeight(temp);
	root=temp;//temp替代root的位置,这是root加引用的原因
}
```

#### 右旋

```C++
void rRotate(node* &root){
	node* temp=root->lhild;
	root->lchild=temp->rchild;
	temp->rchild=root;
	updateHeight(root);
	updateHeight(temp);
	root=temp;
}
```

### 带平衡调整的AVL插入

```C++
//注意LR和RL的情况下，插入节点经过调整就不是叶子了，LL和RR插入节点调整后仍是叶子
void insert(node* &root,int x){
	if(root==NULL){
		root=new node(x,1);//节点高度初始为1
		return;
	}
	if(x==root->data){
		return;
	}else if(x<root->data){
		insert(root->lchild,x);//先插入，再调整
		updateHeight(root);//插完了更新树高，递归写法，因此是自下而上依次更新树高
		if(getBalanceFactor(root)==2){
			if(getBalanceFactor(root->lchild)==1){//在左子树的左子树上插入导致失衡，LL型，右旋
				rRotate(root);
			}else if(getBalanceFactor(root->lchild)==-1){//在左子树的右子树上插入导致失衡，LR型
				lRotate(root->lchild);//root的左子树先左旋，成为LL型
				rRotate(root);
			}
		}
	}else{
		insert(root->rchild,x);
		updateHeight(root);//递归写法，因此是自下而上依次更新树高
		if(getBalanceFactor(root)==-2){
			if(getBalanceFactor(root->rchild)==-1){//在右子树的右子树上插入导致失衡，RR型，左旋
				lRotate(root);
			}else if(getBalanceFactor(root->rchild)==1){//在右子树的左子树上插入导致失衡，RL型
				rRotate(root->rchild);//root的右子树先右旋，成为RR型
				lRotate(root);
			}
		}
	}
}
```

### AVL的建立

```C++
node* create(int data[],int n){
	node* root=NULL;
	for(int i=0;i<n;i++){
		insert(root,data[i]);
	}
	return root;
}
```

## 线段树

线段树是一种二叉树，每个叶子结点存储元素本身，非叶子节点存储区间内元素的统计值（区间和、区间最值、区间最大公约数等），可以在logn时间内执行区间修改和区间查询。

![image-20230213095421001](Templates for Algo&DS.assets/image-20230213095421001.png)

```c++
//root下标从1开始
#define lc root<<1
#define rc root<<1|1

class Node {
public:
    int l, r;//维护区间左右端点，为闭区间
    int sum;//叶子节点存储本身值，非叶子节点存储统计值，此处为区间和
    int add;//lazy标记，懒惰修改，只修改对查询有用的节点
    Node(int ls=0,int rs=0, int value=0, int lazy=0)
        :l(ls),r(rs),sum(value),add(lazy)
        {}
}tr[N<<2];//可以看作一个完全二叉树最多多一层，可以用四倍于数据量的数组存储

```

### 构造SegmentTree

```C++
//以维护区间和线段树为例

void pushUp(int root) {
//上传到根节点就是令节点的val等于左右节点val之和
	tr[root].sum=tr[lc].sum+tr[rc].sum;
}

//递归建树 O(logn)
void build(int root,int l,int r){
    tr[root]={l,r,w[l],0};
    if(l==r)//是叶子则返回
        return;
    int mid=l+r>>2;//不是叶子则从区间中点裂开；
    build(lc,l,m);
    build(rc,m+1,r);
    pushUp(root);//回溯，维护非叶节点
}
```

### 区间修改

```C++
//采用懒惰修改, O(logn)

void pushDown(int root) {
//如果Lazy没有被标记，就直接返回，否则要更新节点值，以及左右子节点的lazy值，并将当前节点的lazy值清空
    if (!tr[root].add) {
        return;
    }else{
        //维护统计量
    	tr[lc].sum+=(tr[lc].r-tr[lc].l+1)*tr[root].add;
        tr[rc].sum+=(tr[rc].r-tr[rc].l+1)*tr[root].add;
        
        //标记下放
        tr[lc].add+=tr[root].add;
        tr[rc].add+=tr[root].add;
        
        tr[root].add=0;
    }
}

void update(int root,int x, int y, int k){
    //覆盖维护区间，则修改
    if(tr[root].l>=x && tr[root].r<=y){
        tr[root].sum+=(tr[root].r-tr[root].l+1)*k;
        tr[root].add+=k;//懒惰修改，k为增加量
        return;
    }
    int mid=tr[p].l+tr[p].r>>1;//不覆盖则裂开
    pushDown(root);
    if(x<=mid)
        update(lc,x,y,k);
    if(y>mid)
        update(rc,x,y,k);
    pushUp(root);
}
```

### 区间查询

```C++
//O(logn)
int query(int root, int x, int y){
    if(tr[root].l>=x&&tr[root].r<=y)
        return tr[root].sum;//节点维护区间是查询区间的子区间则返回sum
    int mid=tr[root],l+tr[root].r>>1;//不覆盖则裂开
    pushDown(p);
    int sum=0;
    if(x<=mid)
        sum+=query(lc,x,y);
    if(y>mid)
     	sum+=query(rc,x,y);
    return sum;
}
```

### LeetCode

##### 732. 我的日程安排表Ⅲ

```c++
//线段树动态开点法模板

class MyCalendarThree {
public:
    int N=1e9;

    class Node{
    public:
        Node *left,*right;
        int l,r;
        int maxRepeat;
        int add;//lazy tag
        Node(int start,int end)
            :left(nullptr),
            right(nullptr),
            l(start),
            r(end),
            maxRepeat(0),
            add(0){}
    };

    Node *root=new Node(0,N);

    void pushUp(Node *p){
        p->maxRepeat=max(p->left->maxRepeat,p->right->maxRepeat);
    }

    void pushDown(Node *p){

        int mid = (p->l+p->r)>>1;
        if(p->left==nullptr) 
            p->left = new Node(p->l,mid);
        if(p->right==nullptr) 
            p->right = new Node(mid+1,p->r);
        if(p->add==0) 
            return;

        p->left->add += p->add;
        p->right->add += p->add;
        p->left->maxRepeat += p->add;
        p->right->maxRepeat += p->add;
        p->add = 0;
    }

    void update(Node *p, int l,int r){
        if(l<=p->l&&r>=p->r){
            p->maxRepeat+=1;
            p->add+=1;
            return;
        }
        pushDown(p);
        int mid=(p->l+p->r)>>1;
        if(mid>=l)
            update(p->left,l,r);
        if(mid<r)
            update(p->right,l,r);
        pushUp(p);
    }
  
    int book(int startTime, int endTime) {
        update(root,startTime,endTime-1);
        return root->maxRepeat;
    }
};

```

```C++
//差分数组法：一种辅助数组，记录原始数组相邻元素之间的差，即原数组中第i个位置值减去原数组第i-1个位置的值。假设我们频繁的对数组进行范围更新，则只需要更新端点即可。

class MyCalendarThree {
public:
    map<int,int> cnt;
    MyCalendarThree() {

    }
    
    int book(int startTime, int endTime) {
        cnt[startTime]++;
        cnt[endTime]--;
        int ans=0;
        int maxBook=0;
        for(auto &c:cnt){
            ans=max(ans,maxBook+=c.second);// 所有[startTime，endTime)的嵌套层数
        }
        return ans;
    }
};
```



## 堆

一种完全二叉树，不稳定排序

```C++
//完全二叉树可以用数组存储
int heap[MAXN],n;
```

### 堆调整

```C++
//堆调整核心算法 从下标1开始存储，大顶堆（priority_queue默认也是大顶堆）
//对heap数组在[low,high]范围进行下滤
void downAdjust(int low,int high){
	int i=low;
	int j=i*2;//i为欲调整节点，j为其左孩子
	while(j<=high){//存在孩子节点
		if(j+1<=high&&heap[j+1]>heap[j]){//右孩子存在且右孩子大于左孩子
			j=j+1;//j存储右孩子下标
		}
		if(heap[j]>heap[i]){//较大的孩子大于父亲
			swap(heap[i],heap[j]);//将两个孩子中较大的那个与其父节点交换
			i=j;//保持i为欲调整节点
			j=i*2;//j为i的左孩子
		}else{
			break;//两孩子的权值均比欲调整节点小，调整结束
		}
	}
}
```

### 建堆

```C++
void creatHeap(){
	for(int i=n/2;i>=1;i--)//建堆过程是自下而上自右而左从第一个非叶节点开始的
		downAdjust(i,n);//对每个非叶节点都进行下滤
}
```

### 删除堆顶元素

```C++
void pop(){
	heap[1]=heap[n--];//用最后一个元素覆盖堆顶,元素数量-1
	dowAdjust(1,n);//对堆顶元素进行下滤
}
```

### 插入元素

```C++
//上滤，high为新添加元素的数组下标，low一般为1
void upAdjust(int low,int high){
	int i=high;
	int j=i/2;//j为欲调整节点的父节点
	while(j>=low){//与下滤对称的结构，只不过没有兄弟节点比较大小
		if(heap[i]>heap[j]){//欲上滤的节点比其父亲大
			swap(heap[i],heap[j]);
			i=j;//保持i为欲调整节点
			j=i/2;//j为i的父节点
		}else{
			beak;//父节点权值比欲调整节点权值大，调整结束
		}
	}
}
//插入元素
void insert(int x){
	heap[++n]=x;
	upAdjust(1,n);
}
```

### 堆排序

O(nlogn),n次堆调整

```c++
//不断将堆顶元素与当前堆末尾元素交换，也就将当前堆最大值固定在当前堆末尾，然后将少了最大节点的堆调整为大顶堆；
//最后大顶堆heap数组就成为一个递增数组,这也解释了为什么priority_queue的比较函数是相反的
void heapSort(){//整个堆排的算法核心就是先建堆，再不断交换堆顶堆尾，然后再将堆顶下滤
	createHeap();
	for(int i=n;i>1;i--){//倒着枚举，直到堆中只有一个元素
		swap(heap[1],heap[i]);
		downAdjust(1,i-1);//将换到堆顶的节点下滤调整成堆
	}
	//完成后heap[]内元素变为递增
}
```

## 哈夫曼树

### 哈夫曼树的WPL

```C++
//哈夫曼树可以不唯一，但WPL一定唯一
//哈夫曼树的WPL等于所有非叶子结点的权值之和
//压缩效率为WPL/(总字符数*编码长度)
//编码长度为log2(字符集内元素个数)向上取整
int WPL(int A[],int n){//A中存放所有带权节点
	priority_queue<int,vector<int>,greater<int> > q;//利用堆，每次取两个最小节点合并
	for(int i=0;i<n;i++)
		q.push(A[i]);
	int x,y,ans=0;//分别存放堆顶两个元素和当前wpl
	while(q.size()>1){//注意此处用q.size()>1，为了让堆中至少有两个节点时才会进行合并
		x=q.top();
		q.pop();
		y=q.top();
		q.pop();
		q.push(x+y);
		ans+=x+y;//WPL就是Huffman tree中非叶子结点权值之和
	}
	return ans;
}
```

### Huffman编码，又称前缀编码

```C++
//前缀编码让每个字符作为Huffman tree的叶子结点，其意义在于不产生混淆，让解码能够正常进行
//一个串中每个字符的使用频率就是该字符作为叶子结点的权值，使用频率越高，该字符编码就越短，这样起到压缩的作用
//哈夫曼编码是能使字符串编码成01串后长度最短的前缀编码
```



## 图

### 存储

#### 邻接表（链式存储）

```c++
struct ArcNode{//边表节点
	int adjvex;
	ArcNode *next;
};
struct VNode{//顶点表节点
	int data;
	ArcNode *first;
};
struct Graph{//图
	VNode vertexes[MaxVertexNum];
	int vexnum,arcnum;
};


```

#### 邻接矩阵（二维数组）

### DFS

```C++
//链式存储的邻接表，与vector存储其实一样、
vector<int> G[MAXV];
int n;//顶点数
bool vis[MAXV]={false};
```

#### 递归

```C++
void DFS(int u,int depth){//depth记录当前层数，初始值为1
	vis[u]=true;
	visit(u);
	for(int i=0;i<G[u].size();i++){
		int v=G[u][i];
		if(vis[v]==false)
			DFS(v,depth+1);
	}
}
```

#### 非递归

```C++
//用栈实现非递归DFS
//用栈实现时，遍历方式从右至左进行
void noneRecursionDFS(int x){
	stack<int> st;
	st.push(x);
	vis[x]=true;
	while(!st.empty()){
		int u=st.top();
		st.pop();
		visit(u);
		for(int i=0;i<G[u].size();i++){
			int v=G[u][i];
			if(vis[v]==false){
				st.push(v);
				vis[v]=true;
			}
		}
	}
}
```

#### 对图内每个点进行DFS

```C++
void DFSTrave(){
	for(int u=0;u<n;u++)
		if(vis[u]==false)
			DFS(u,1);//第一层，depth为1
}
```

### BFS

```C++
bool inq[MAXV]={false};//此处inq与vis含义不同，inq是记录入队过的节点，因为节点可能在队中但还未访问，如果表示vis的含义，那么可能重复访问
void BFS(int x){
	queue<int> q;
	q.push(x);//第一步，将x入队
	inq[x]=true;
	while(!q.empty()){//队非空时循环
		int u=q.front();
		q.pop();
		visit(u);
		for(int i=0;i<G[u].size();i++){
			int v=G[u][i];
			if(inq[v]==false){
				q.push(v);
				inq[v]=true;
			}
		}
	}
}
```

#### 对图内每个点进行BFS

```C++
void BFSTrave(){
	for(int u=0;u<n;u++)
		if(inq[u]=false)
			BFS(q);
}
```

#### BFS求无权图的单源最短路径

```C++
//0和1表示无边或有边
//利用广度优先遍历总是按照距离由近到远来遍历图中每个顶点的性质决定
int n,G[MAXV][MAXV];//邻接矩阵版本
int d[MAXV]={INF}//起点到其他点的最短路径集合
bool inq[MAXV]={false};
void BFS(int s){
	queue<int> q;
	q.push(s);
	inq[s]=true;
	d[s]=0;
	while(!q.empty()){
		int u=q.front();
		q.pop();
		for(int v=0;v<n;v++){
			if(inq[v]==false&&G[u][v]!=0){
				q.push(v);
				inq[v]=true;
				d[v]=d[u]+1；//无权图
			}
		}
	}
}
```

### 最短路径

#### Dijkstra单源最短路径

O(V^2)，可用堆优化到(VlogV) 

```c++
//邻接表版本
struct arc{
	int v,dis;//弧头指向的节点编号，边权
}；//弧的数据结构
vector<arc> G[MAXV];
int n;
int d[MAXV]={INF};//起点到其他点最短路径集合
int pre[MAXV];//pre[v]表示从起点到顶点v的路径上v的前一个顶点(新添加)
bool vis[MAXV]={false};

void Dijkstra(int s){//每次从未访问过的可访问结点中取离原点最近的
	for(int i=0;i<n;i++)
		pre[i]=i;//初始状态每个节点的前驱为自身
	d[s]=0;//起点到自身的距离为0
	for(int i=0;i<n;i++){//对每个节点都进行一次
		int u=-1,MIN=INF;//u是未访问节点中离原点最近的节点编号，MIN存储这个距离
		for(int j=0;j<n;j++){//找到未访问节点中离原点最近的节点
			if(vis[j]==false&&d[j]<MIN){
				u=j;
				MIN=d[j];
			}
		}
		if(u==-1)//其他顶点与起点s均不连通
			return;
		vis[u]=true;//将未访问节点中离源点最近的加入到已访问集合中，体现了djkstra的思想
		for(int j=0;j<G[u].size();j++){//以新加入的节点为中介进行辐射，优化起点与所有与u临接节点的距离
			int v=G[u][j].v;
			if(vis[v]==false&&d[u]+G[u][j].dis<d[v]){
				d[v]=d[u]+G[u][j].dis;//更新起点至v的最小距离
				pre[v]=u;//记录原点到达v的最短路径上v的前驱是u
			}
		}
	}
}
```

#### 递归求最短路径

```c++
//s为起点编号，v为终点编号
//递归也可用栈实现 
void path(int s,int v){
	if(v==s){//递归到s=v时，输出起点
		cout<<v<<endl;
		return;
	}
	path(s,pre[v]);
	cout<<v<<endl;//从最深处return回来，输出每一层的顶点号
}
```

#### Floyd

O(V^3)

```c++
//算法思想：如果存在顶点k作为中介点时i到j之间的距离缩短，那么就更新dis[i][j]
int n;//顶点数
int dis[MAXV][MAXV]
void Floyd(){
	for(int k=0;k<n;k++)//k是中介点，k的循环必须放在最外层。即每次选取一个点作为中介点，然后看其他任意两点间距离能否缩短
		for(int i=0;i<n;i++)
			for(int j=0;j<n;j++)
				if(dis[i][k]!=INF&&dis[k][j]!=INF&&dis[i][k]+dis[k][j]<dis[i][j])
					dis[i][j]=dis[i][k]+dis[k][j];
}
```

### 最小生成树

#### Prime

O(V^2) 可用堆优化到O(VlogV) 只和vertex相关，适合稠密图

```c++
//和Dijkstra思路相同
//邻接矩阵版
int n,G[MAXV][MAXV]
int d[MAXV]={INF}//顶点与集合S的最短距离,仅含义与Dijkstra的d[]不同
bool vis[MAXV]={false};

int Prim(){ //初始点号为0，返回值为最小生成树的边权之和WPL
	d[0]=0;
	int ans=0;
	for(int i=0;i<n;i++){
		int u=-1;MIN=INF;
		for(int j=0;j<n;j++){
			if(vis[j]==false&&d[j]<MIN){
				u=j;
				MIN=d[j];
			}
		}
		if(u==-1)//不连通
			return -1;
		vis[u]=true;//将未访问节点中离当前生成树最近的加入到已访问集合中
		ans+=d[u];//将与集合S距离最小的边加入最小生成树
		for(int v=0;v<n;v++){
			if(vis[v]==false&&G[u][v]!=INF&&G[u][v]<d[v])//节点未访问&&以u为中介可达v&&使得v距离集合S更近
				d[v]=G[u][v];//更新v到集合S的距离，整个循环相当于将视野增加了以u能直接到达的那些节点，待后续加入生成树时使用
		}
	}
	return ans;
}
```

#### Kruskal

```C++
struct edge{
	int h,t;//边的两个端点，虽然叫head和tail，但实际是无向图
	int dis;
}E[MAXE];//边集
//比较函数
bool cmp(edge a,edge b){
	return a.dis<b.dis;
}
//并查集，用来判断测试的边的两端是否在不同连通块中
int father[MAXV];
int findFather(int v){
	if(v==father[v]){
		return v;
	}else{
		int F=findFather(father[v]);
		father[v]=F;//路径压缩
		return F;
	}
}
//按边权从小到大将两个顶点不在一个连通块中的边加入最小生成树，否则将边舍弃
//边贪心策略，因此复杂和边数有关，适合稀疏图，复杂度O(ElogE)
int kruskal(int n,int m){//n个节点m条边
	int ans=0,cnt=0;//统计边权之和，当前生成树边数
	for(int i=0;i<n;i++)//节点编号0~n-1
		father[i]=i;
	sort(E,E+m,cmp);//sort传函数指针
	for(int i=0;i<m;i++){
		int faH=findFather(E[i].h);
		int faT=findFather(E[i].t);
		if(faH!=faT){
			father[faT]=faH;
			ans+=E[i].dis;
			cnt++;
			if(cnt==n-1)//边数等于顶点数-1时说明树已生成
				return ans;
		}
	}
	return -1;//cnt!=n-1,图不连通，无法生成最小树
}
```

### 有向图拓扑排序

#### BFS实现topolocialSort

```C++
vector<int> G[MAXV];//不考虑权值，所以G[u][i]默认为v；
int n,inDegree[MAXV];//inDegree记录每个顶点的入度（遍历一遍即可得到），入度为0时顶点入队

bool topologicalSort(){
	int cnt=0;//记录已排序的节点数
	queue<int> q;
	for(int i=0;i<n;i++){
		if(inDegree[i]==0){//将所有入度为0的顶点入队
			q.push(i);
		}
	}
	while(!q.empty()){
		int u=q.front();
		q.pop();
		cnt++;//u加入了拓扑序列，节点数+1；
		visit(u);
		for(int i=0;i<G[u].size();i++){
			int v=G[u][i];
			inDegree[v]--;//切断u到v的弧，即v的入度-1
			if(inDegree[v]==0)//v入度为0时入队
				q.push(v);
		}
		//G[u].clear();//清空顶点u的所有出边(如无必要可不写）
	}
	if(cnt==n)//拓扑排序完成，所有顶点都进入序列
		return true;
	else
		return false;
}
```

#### DFS实现topologicalSort

```C++
//其思想就是拓扑排序中顶点顺序在时间上继起，因此加入time标记，在DFS调用结束时，对各顶点计时
//祖先的结束时间必然大于子孙的结束时间,因此时间从大到小排序就是拓扑排序
vector<int> G[MAXV];
int n;//顶点数
bool vis[MAXV]={false};
int time=0;
map<int,int,greater<int> > hash;//key为time，value为顶点编号，底层红黑树实现，根据key自动排序
void DFS(int u){
	vis[u]=true;
	for(int i=0;i<G[u].size();i++){
		int v=G[u][i];
		if(vis[v]==false)
			DFS(v);
	}
	hash[++time]=u;
}
void topologicalSort(){//打印
	DFS(0);
	for(auto &c:hash){//根据结束时间递减排序，依次打印
		cout<<c;
	}
}
```

## 查找

### 二分查找

所有单调线性表都可以利用“二分”思想

#### 递归写法

```c++
//二分查找值为x的节点
//递归算法
//注意比较与非递归的写法，递归写法最后判断相等的情况,因为递归在出栈时运算，是逆序的
int binarySearchRecursion(int A[],int left,int right,int x){
	if(left>right)
		return -1;
	int mid=(low+high)/2;
	if(x<A[mid])
		return binarySearchRecursion(A,left,mid-1,x);
	else if(x>A[mid]){
		return binarySearchRecursion(A,mid+1,right,x);
	else
		return mid;
}
```

#### 非递归写法

```c++
int binarySearch(int A[],int left,int right,int x){
	int mid;
	while(left<=right){
		mid=(left+right)/2;
		if(A[mid]==x)
			return mid;
		else if(x<A[mid])
			right=mid-1;
		else
			left=mid+1;
	}
	return -1;
}
```

### 下界

```c++
//x为下界即x应该在一个递增子序列的开始位置
//返回递增序列第一个大于等于x的元素的位置（假设x不存在，那么返回的是x应在的位置）
int lowerBound(int A[],int left,int right,int x){
	int mid;
	while(left<right){//left==right表示找到了唯一位置，即使元素不存在，这也是该元素应在的位置
		mid=(left+right)/2;
		if(x<=A[mid])//x小于等于中间数，说明所求位置一定在mid或其左侧
			right=mid;
		else//x大于中间数，说明所求位置一定在mid右侧
			left=mid+1;
	}
	return mid;
}
```

### 上界

```C++
//x为上界即x应该在一个递增子序列的最后一个元素右边一个位置（左闭右开）
//返回递增序列第一个大于x的元素的位置（假设x不存在，那么返回的是x应在的位置）
int upperBound(int A[],int left,int right,int x){
	int mid;
	while(left<right){
		mid=(left+right)/2;
		if(x<A[mid])//仅仅此处与lowerBound不同，没有等号。
			right=mid;
		else
			left=mid+1;
	}
	return mid;
}
```

## 排序

### 插入排序

#### 直接插入排序

```C++
void insertSort(int A[],int n){
	for(int i=1;i<n;i++){//进行n-1趟排序
		int temp=A[i],j=i;
		while(j>0&&temp<A[j-1]){//temp==A[j-1]时退出，所以是稳定排序
			A[j]=A[j-1];
			j--;
		}
		A[j]=temp;
	}
}
```

#### 折半插入排序

```C++
//仅减少了比较次数，移动次数未减少，所以仍是O(n^2)
void binaryInsertSort(int A[],int n){
	for(int i=1;i<n;i++){
		int temp=A[i],left=0,right=i-1,mid;
		while(left<right){//在已排序列中二分找到第一个大于temp的元素的位置，同upperbound
			mid=(left+right)/2;
			if(temp<A[mid])
				right=mid;
			else
				left=mid+1;
		}
		for(int j=i;j>mid;j--){
			A[j]=A[j-1];
			j--;
		}
		A[j]=temp;
	}
}
```

#### 希尔排序

```C++
//即缩小增量排序,实质就是分组插入,是一种不稳定排序（相同数在不同组中被交换时可能相对位置会改变）
void shellSort(int A[],int n){//元素下标0~n-1
	for(int d=n/2;d>=1;d/=2){//只是比传统插排多了这层循环来进行分割
		for(int i=d;i<n;i++){//d号元素是无序部分第一个元素
			int temp=A[i],j=i;
			while(j-d>=0&&temp<A[j-d]){//思想与插入排序类似，只不过是间隔为d进行插入排序
				A[j]=A[j-d];
				j-=d;
			}
			A[j]=temp;
		}	
	}
}
```

### 交换排序

#### 冒泡排序

```C++
void bubbleSort(int A[],int n){
	for(int i=0;i<n-1;i++){//n-1趟排序
		int flag=false;//是否交换的标志，若某趟没有交换，说明已有序
		for(int j=0;j<n-i-1;j++){//从前往后排，后面的元素先有序
			if(a[j]>a[j+1]){
				swap(a[j],a[j+1]);
				flag=true;
			}
		}
		if(flag==false)//每趟排序检查是否产生交换，未产生交换就说明已经有序
			return;
	}
}
```

#### 快速排序

```C++
//快速排序是不稳定排序
//核心思想是划分
int partition(int A[],int left,int right){//划分，[left,right]闭区间
	int pivot=A[left];
	while(left<right){//双指针从两端往中间靠拢同时进行覆盖
		while(left<right&&A[right]>=pivot)
			right--;
		A[left]=A[right];
		while(left<right&&A[left]<=pivot)
			left++;
		A[right]=A[left];
	}
	A[left]=pivot;/left==right位置是最后一次被覆盖的位置
	return left;//返回存放枢轴的最后位置
}

void quickSort(int A[],int left,int right){
	if(left<right){
		int pivotPos=Partition(A,left,right);//每次划分过后，枢轴的位置就确定了
		quickSort(A,left,pivotPos-1);//枢轴左侧区间进行划分
		quickSort(A,pivotPos+1,right);//枢轴右侧区间进行划分
	}
}
```

### 选择排序

#### 简单选择排序

```C++
//将待排序列中最小的与待排序列第一个元素交换
void selectSort(int A[],int n){
	for(int i=0;i<n-1;i++){//共进行n趟排序，第一趟时，A[0]视为待排序序列第一个元素
		int minPos=i
		for(int j=i+1;j<n;j++){
			if(A[j]<A[minPos]){
				minPos=j;
			}
		}
		swap(A[i],A[minPos]);
	}
	return;
}
```

### 归并排序

#### 二路归并排序

```C++
//待排序列为两个有序序列
//核心是merge函数
void merge(int A[],int L1,int R1,int L2,int R2){
	int i=L1,j=L2;//双指针
	int temp[MAXN],idx=0;//temp临时存放合并后的数组，idx为其下标
	while(i<=R1&&j<=R2){
		if(A[i]<=A[j])
			temp[idx++]=A[i++];
		else
			temp[idx++]=A[j++];
	}
	while(i<=R1)
		temp[idx++]=A[i++];
	while(j<=R2)
		temp[idx++]=A[j++];
	for(i=0;i<=idx;i++)//把合并后的序列赋值回A
		A[L1++]=temp[i];
}
```

##### 递归写法

```C++
void mergeSort(int A[],int left,int right){
	if(left<right){
		int mid=(left+right)/2;
		mergeSort(A,left,mid);
		mergeSort(A,mid+1,right);
		merge(A,left,mid,mid+1,right);
	}
}
```

##### 非递归写法

```C++
void mergeSort(int A[],int n){
	for(int step=2;step/2<=n;step*=2){//每step个元素一组，组内前step/2和后step/2个元素进行合并
		for(int i=0;i<n;i+=step){
			int mid=i+step/2-1;
			if(mid+1<n)
				merge(A,i,mid,mid+1,min(i+step-1,n-1);
		}
	}
}
```

### 基数排序



## 哈希

散列表建立了关键字和存储地址之间的一种映射关系

### 散列函数

##### 直接定址法：

线性 H(key)=akey+b

##### 除留余数法：

H(key)=key%p,p为小于等于表长的最大素数

##### 数字分析法

##### 平方取中法：

取关键字平方后中间几位

### 二次聚集

两个第一个哈希地址不同的记录争夺同一个后继哈希地址的现象称为“二次聚集”

#### 冲突处理方法

##### 闭散列(开放定址法)

线性探测法：容易造成聚集现象，大大降低查找效率
平方探测法：可以避免“堆积”但是不能探测到散列表中全部位置
再散列法：使用两个散列函数，第一个散列函数冲突时，使用第二个散列函数计算增量 
伪随机法

##### 开散列(拉链法）

将映射到同一位置的不同关键字存储在一个线性链表中。有点像邻接表。	 

## 暴力搜索

### LeetCode

#### DFS

##### 63.不同路径 III

```C++
//四个方向+有障碍+格子不能重复走
```



## 贪心

### LeetCode

##### 45.跳跃游戏Ⅱ

```C++
class Solution {
public:
    int jump(vector<int>& nums) {
        int n=nums.size();
        int end=0;
        int farthest=0;
        int jumps=0;
        for(int i=0;i<n-1;i++){
            farthest=max(nums[i]+i,farthest);//更新从i位置可以达到的最远位置
            //该位置可以从上一次跳跃达到的最远位置再跳一次达到则jumps++
            if(end==i){
                jumps++;
                end=farthest;//更新可达的最远位置
                if (farthest >= n - 1) 
                    break;
            }
        }
        return jumps;
    }
};
```

##### 55.跳跃游戏

```C++
//同时更新上一跳可达最远距离和判断当前位置是否可达
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int n=nums.size();
        int farthest=0;
        for(int i=0;i<n-1;i++){
            farthest=max(farthest,i+nums[i]);//更新可达的最远距离，代表进行了一次跳跃
            //上一跳可达的最远距离小于等于当前数组下标代表当前位置不可达
            if(farthest<=i)
                return false;
        }
        return true;
    }
};
```

##### 121. 买卖股票的最佳时机

```C++
//秒杀题
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int minPrice = INT_MAX;
        int maxProfit = 0;
        for (int p:prices){
            minPrice=min(p,minPrice);
            maxProfit=max(maxProfit,p-minPrice);
        }
        return maxProfit;
    }
};
```

##### 122. 买卖股票的最佳时机 II

```C++
//还是贪心
//低买高卖一定赚钱
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int sz=prices.size();
        int ans=0;
        for(int i=0;i<sz-1;i++){
            if(prices[i+1]>prices[i])
                ans+=(prices[i+1]-prices[i]);
        }
        return ans;
    }
};
```



## 动态规划

递归是自顶向下的，而dp是自底向上的，因此可以使用迭代完成。

### 一般形式：求最值

dp本质上是运筹学的一种最优化方法

### 核心问题：穷举

计算机求最值肯定要穷举所有可行解。

### DP三要素

#### 最优子结构

即能否通过子问题的最值得到原问题的最值。（所以说dp和递归一定是可以相互转化的）

要符合最优子结构，子问题间就必须相互独立。

#### 重叠子问题

因为子问题存在重叠，所以可以通过dp table来优化穷举过程，避免暴力穷举的低效。从这个角度看，dp很像回溯法中利用备忘录对递归树进行剪枝。

#### 状态转移方程

##### base case

即问题的最简单情况，同递归中的递归基。

##### 状态

即这个问题有什么状态。

状态一般就是原问题和子问题中的“变量”，在dp table中表现为数组下标。

##### 选择

即每个状态可以做出什么选择使得状态发生改变

选择就是导致状态发生变化的行为

##### dp table(func)

dp table的功能实际上就对应递归中使用备忘录对递归树进行剪枝。

一个函数f(x)的参数x是变量，f(x)就是所求值，

对应到状态转移方程之中，dp[n]的值即为所求目标，而数组下标即为“状态”。

###### dp 数组的遍历方向

对于二维dp数组来说

```c++
int vector<vector<int> > dp(m, vector<int>(n,0));

//正向遍历
for(int i=0;i<m;i++){
    for(int j=0;j<n;j++){
        dp[i][j]=...
    }
}

//反向遍历
int vector<vector<int> > dp(m, vector<int>(n,0));
for(int i=m-1;i>=0;i--){
    for(int j=n-1;j>=n;j--){
        dp[i][j]=...
    }
}

//斜着遍历
for(int l=2;l<=n;l++){
    for(int i=0;i<=n;i++){
        int j=l+i-1;
        dp[i][j]=...
    }
}
```

确定遍历方向，需要把握两点:

1.遍历过程中，所需状态必须已经被计算出来

2.遍历的重点必须是存储结果的那个位置

### 状态压缩

#### 无后效性

为了保证计算子问题能够按照顺序、不重复地进行，动态规划要求已经求解的子问题不受后续阶段的影响。动态规划对状态空间的遍历构成一张有向无环图，遍历就是该有向无环图的一个拓扑序。有向无环图中的节点对应问题中的「状态」，图中的边则对应状态之间的「转移」，转移的选取就是动态规划中的「决策」。

### LeetCode

#### 一维DP

##### 152.乘积最大子数组

```C++
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int sz=nums.size();
        vector<int> dp1(sz,0);//以nums[i]结尾时的最大乘积
        vector<int> dp2(sz,0);//以nums[i]结尾时的最小乘积，即保存负的最大值，应对nums[i+1]<0的情况
        dp1[0]=nums[0];
        dp2[0]=nums[0];
        for(int i=1;i<sz;i++){
            dp1[i]=max({nums[i]*dp1[i-1],nums[i]*dp2[i-1],nums[i]});//std:max()可以接受一个std::initializer_list
            dp2[i]=min({nums[i]*dp1[i-1],nums[i]*dp2[i-1],nums[i]});
        }
        return *max_element(dp1.begin(),dp1.end());
    }
};
```

##### 53.最大子数组和

```c++
//标准的dp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int sz=nums.size();
        vector<int> dp(sz,0);
        dp[0]=nums[0];
        for(int i=1;i<sz;i++){
            dp[i]=max(dp[i-1]+nums[i],nums[i]);
        }
        return *max_element(dp.begin(),dp.end());
    }
};

//dp数组可以进一步压缩
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int res = INT_MIN, curSum = 0;
        for (int num : nums) 
        {
            curSum = max(curSum + num, num);
            res = max(res, curSum);
        }
        return res; 
    }
};
```

##### 918. 环形子数组的最大和

```C++
/*
1.最大子数组 不成环 --- 53题 也就是maxSum为答案
2.最大子数组 成环 ，因为total一定，而subSum=maxSum，且maxSum的两端必为正数，所以中间的不成环子数组就是最小子数组minSum，那么(total - minSum) 则为答案
*/

class Solution {
public:
    int maxSubarraySumCircular(vector<int>& nums) {
        int total=0;
        int minSum=INT_MAX;
        int maxSum=INT_MIN;
        int curMax=0,curMin=0;
        for(auto &i:nums){
            curMax=max(i,curMax+i);
            curMin=min(i,curMin+i);
            maxSum=max(maxSum,curMax);
            minSum=min(minSum,curMin);
            total+=i;
        }
        //判断一下数组全负的情况
        return maxSum>0?max(maxSum,total-minSum):maxSum;
    }
};
```

##### 198.打家劫舍（剑指 Offer II 089. 房屋偷盗)

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int sz=nums.size();
        if(sz==0)
            return 0;
        if(sz==1)
            return nums[0];
        vector<int> dp(sz,0);//dp[i]为前i个房子能盗窃到的最高金额
        dp[0]=nums[0];
        dp[1]=max(nums[0],nums[1]);
        for(int i=2;i<sz;i++){
            dp[i]=max(dp[i-2]+nums[i],dp[i-1]);
        }
        return dp[sz-1];
    }
};

//状态压缩
class Solution {
public:
    int rob(vector<int>& nums) {
        int sz=nums.size();
        int dp1=0,dp2=0;//dp前两个位置
        int dp=0;
        for(int i=0;i<sz;i++){//对原数组每一个位置进行遍历
            dp=max(dp2,nums[i]+dp1);
            dp1=dp2;
            dp2=dp;
        }
        return dp;
    }
};
```

##### 213.打家劫舍Ⅱ（剑指 Offer II 090. 环形房屋偷盗)

```C++
//环形打家劫舍
//第一间和最后一间不能同时被抢，所以分抢第一间和抢最后一间两种情况做两次dp取最大值即可

class Solution {
public:
    int robRange(vector<int>& nums,int start,int end) {
        //int sz=nums.size();
        int dp1=0,dp2=0;//dp前两个位置
        int dp=0;
        for(int i=start;i<end+1;i++){//对[start,end]每一个位置进行遍历
            dp=max(dp2,nums[i]+dp1);
            dp1=dp2;
            dp2=dp;
        }
        return dp;
    }
    int rob(vector<int>& nums) {
        int sz=nums.size();
        if(sz==0)
            return 0;
        if(sz==1)
            return nums[0];
        return max(robRange(nums,0,sz-2),robRange(nums,1,sz-1));
    }
};
```

##### 337.打家劫舍Ⅲ

```c++
//树形打家劫舍
//树形dp，状态压缩

class Solution {
public:
    int rob(TreeNode* root) {
        int l=0,r=0;
        return robTree(root,l,r);
    }
    //包含根节点则根节点的两个孩子不能偷，不包含根节点则两个孩子可以偷，两种情况取最大值
    int robTree(TreeNode* root, int &l,int &r){//l和r必须是引用，因为是作为dp依赖的两个状态，不应该在递归中被刷新
        if(!root)
            return 0;
        int ll=0,lr=0,rl=0,rr=0;//分别保存左左、左右、右左、右右孩子的最高金额
        l=robTree(root->left,ll,lr);
        r=robTree(root->right,rl,rr);
        return max(root->val+ll+lr+rl+rr,l+r);
    }
};
```

##### 300.最长递增子序列

```c++
//dp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int sz=nums.size();
        vector<int> dp(sz,1);//前i个数中的最长递增子序列长度为dp[i],每个数单独成一个序列，所以初始长度为1
        for(int i=0;i<sz;i++){
            for(int j=0;j<i;j++){//单调，可用二分查找优化
                if(nums[i]>nums[j])
                    dp[i]=max(dp[i],dp[j]+1);
            }
        }
        return *max_element(dp.begin(),dp.end());
    }
};

//二分优化，类似摸牌，摸到大的就接在后面，摸到小的就替换掉其应插入位置的牌，保持手牌单增且尽量小
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int sz=nums.size();
        vector<int> LIS(sz);//手牌
        int idx=0;
        for(int i=0;i<sz;i++){
            int poker=nums[i];//摸一张牌
            int left=0,right=idx,mid;
            while(left<right){//二分查找
                mid=left+(right-left)/2;
                if(poker<=LIS[mid])
                    right=mid;
                else
                    left=mid+1;
            }
            if(left==idx)//新牌最大
                idx++;//接在手牌后面
            LIS[left]=poker;//替换该位置的牌
        }
        return idx;
    }
};
```

##### 354.俄罗斯信封套娃问题

```c++
//并不是维护一个二维都递增的序列

bool cmpq(vector<int> &a,vector<int> &b) {
    if(a[0]==b[0])
        return a[1]>b[1];
    else
        return a[0]<b[0];
}
class Solution {
public:
    int maxEnvelopes(vector<vector<int>>& envelopes) {
        int len=envelopes.size(); 
        sort(envelopes.begin(),envelopes.end(),cmpq);
        vector<int> top(len);
        int piles=0;
        for(int i=0;i<len;i++){
            int poker=envelopes[i][1];
            int left=0,right=piles,mid;
            while(left<right){
                mid=left+(right-left)/2;
                if(poker<=top[mid])
                    right=mid;
                else
                    left=mid+1;
            }
            if(left==piles){
                piles++;
            }
            top[left]=poker;
        }
        return piles;
    }
};
```

##### 322.零钱兑换（剑指 Offer  II 103.  最少的硬币数目）

```C++
//先想dp[n]再想状态n。
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        vector<int> dp(amount+1,amount+1);//凑出金额n所需要的最少硬币数量为dp[n]
        dp[0]=0;
        for(int i=0;i<amount+1;i++){
            for(int &coin:coins){
                if(i<coin)
                    continue;
                dp[i]=min(dp[i],1+dp[i-coin]);//当前硬币面值为coin，要从凑出金额i-coin这个状态转移过来
            }
        }
        return dp[amount]>amount?-1:dp[amount];
    }
};
```

#### 二维dp

##### 10.正则表达式匹配（剑指 Offer  19.  正则表达式匹配）

```C++
//注意题意*是可以把前一个字符消除的！
class Solution {
public:
    bool isMatch(string s, string p) {
        int m=s.size();
        int n=p.size();
        //s中前m个字符和p中前n个字符能否匹配。注意明确dp数组中下标代表字符串的长度。
        vector< vector<bool> > dp(m+1,vector<bool>(n+1,false));
        //s为空串时有两种情况
        //1.s和p都为空字符串（长度为0）时可以匹配。
        //2.p为#*#*#*这种，偶数位置的*把奇数位置的#消除掉
        dp[0][0]=true;
        for(int j=1;j<n;j++){//循环中的j代表下标
            if(p[j]=='*')
                dp[0][j+1]=dp[0][j-1];
        }
        //s不为空串时
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(s[i]==p[j]||p[j]=='.'){//当前位置匹配，则继续判断p的下一个位置
                    dp[i+1][j+1]=dp[i][j];
                }else if(p[j]=='*'){
                    if(p[j-1]==s[i]||p[j-1]=='.'){
                        dp[i+1][j+1]=dp[i][j+1]||dp[i+1][j]||dp[i+1][j-1];//a*匹配多个a||匹配一个a||消除自己
                    }else{
                        dp[i+1][j+1]=dp[i+1][j-1];//前一个位置都不匹配，a*只能消除自己
                    } 
                }
            }
        }
        return dp[m][n];
    }
};
```

##### 44.通配符匹配

```c++
//这题就不存在消除的情况了
class Solution {
public:
    bool isMatch(string s, string p) {
        int m=s.size();
        int n=p.size();
        //s中前m个字符和p中前n个字符能否匹配。注意明确dp数组中下标代表字符串的长度。
        vector< vector<bool> > dp(m+1,vector<bool>(n+1,false));
        //s为空串时有两种情况
        //1.s和p都为空字符串（长度为0）时可以匹配。
        //2.p为****
        dp[0][0]=true;
        for(int j=0;j<n;j++){//循环中的j代表下标
            if(p[j]=='*'){
                dp[0][j+1]=true;
            }else{
                break;
            }
        }
        //s不为空串时
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(s[i]==p[j]||p[j]=='?'){//当前位置匹配，则继续判断p的下一个位置
                    dp[i+1][j+1]=dp[i][j];
                }else if(p[j]=='*'){
                    dp[i+1][j+1]=dp[i][j+1]||dp[i+1][j];//a*匹配多个a||匹配一个a
                }
            }
        }
        return dp[m][n];
    }
};
```

##### 62.不同路径（剑指 Offer  II 098.  路径的数目）

```c++
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector< vector<int> > dp(m, vector<int> (n,1));//到达(i,j)位置有dp[i][j]种路径
        for(int i=1;i<m;i++){
            for(int j=1;j<n;j++){
                dp[i][j]=dp[i-1][j]+dp[i][j-1];//到达一个位置的路径数=到达它上边一个格子的路径数+到达它左边一个格子的路径数
            }
        }
        return dp[m-1][n-1];
    }
};
//只依赖上边和左边格子的状态，因此可以状态压缩。
//压缩成一行
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<int> dp(n,1);//到达第一行所有位置都是一条路径，所以直接赋1
        for(int i = 1;i < m;i++){
            for(int j = 1;j < n;j++){
                dp[j] += dp[j-1];
            }
        }
        return dp[n-1];
    }
};
```

##### 63. 不同路径 II

```C++
//有障碍，进行一下判断就行了
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        int m=obstacleGrid.size();
        int n=obstacleGrid[0].size();
        vector<int> dp(n);//不能初始化为全1，因为到达某位置可能没有路径
        dp[0]=(obstacleGrid[0][0]==0);
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(obstacleGrid[i][j]){
                    dp[j]=0;
                    continue;
                }
                if(j>=1 && obstacleGrid[i][j - 1]==0)
                    dp[j]+=dp[j-1];
            }
        }
        return dp[n-1];
    }
};
```

##### 64.最小路径和（剑指 Offer II 099. 最小路径之和，剑指 Offer 47. 礼物的最大价值）

```C++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int m=grid.size();
        int n=grid[0].size();
        //到达(i,j)位置的最小路径和为dp[i][j],但此处可以原地操作，直接使用grid
        
        //base case
        for (int i=1;i<m;++i)
            grid[i][0]+=grid[i-1][0];
        for (int j=1;j<n;++j)
            grid[0][j]+=grid[0][j-1];

        for (int i=1;i<m;++i){
            for (int j=1;j<n;++j){
                grid[i][j]+=min(grid[i-1][j],grid[i][j-1]);
            }
        }
        return grid[m-1][n-1];    
    }
};
```

##### 72.编辑距离

```c++
//从两个指针移动的角度来看状态转移方程
class Solution {
public:
    int minDistance(string word1, string word2) {
        int m=word1.size();
        int n=word2.size();
        vector< vector<int> > dp(m+1,vector<int>(n+1));//将word1的前i位转换成word2的前j位需要dp[i][j]次操作。dp数组下标代表字符串长度
        dp[0][0]=0;//两个空str需要0次编辑
        for(int i=1;i<=m;i++)
            dp[i][0]=i;
        for(int j=1;j<=n;j++)
            dp[0][j]=j;
        //迭代中i和j代表数组下标
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(word1[i]==word2[j])
                    dp[i+1][j+1]=dp[i][j];//匹配，不需要操作
                else
                    dp[i+1][j+1]=1+min({dp[i][j],dp[i][j+1],dp[i+1][j]});//替换，删除，插入
            }
        }
        return dp[m][n];
    }
};
```

##### 123.买卖股票的最佳时机 III

```C++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int sz=prices.size();
        if(sz<=1)
            return 0;
        int dp[sz][3][2];//dp[天数][卖出的次数][当前是否持股]
        dp[0][0][0]=0;
        dp[0][0][1]=-prices[0];//第一天买了股票没卖出
        
        //不可能
        dp[0][1][0]=-100000;//防止溢出
        dp[0][2][0]=-100000;
        dp[0][1][1]=-100000;
        dp[0][2][1]=-100000;

        for(int i=1;i<sz;i++){
            dp[i][0][0]=0;
            dp[i][1][0]=max(dp[i-1][1][0],dp[i-1][0][1]+prices[i]);
            dp[i][2][0]=max(dp[i-1][2][0],dp[i-1][1][1]+prices[i]);
            dp[i][0][1]=max(dp[i-1][0][1],dp[i-1][0][0]-prices[i]);
            dp[i][1][1]=max(dp[i-1][1][1],dp[i-1][1][0]-prices[i]);//用INT_MIN这里会溢出
            dp[i][2][1]=-100000;
        }
        return max({dp[sz-1][1][0],dp[sz-1][2][0],0});
    }
};
```

##### 188.买卖股票的最佳时机 Ⅳ

```C++
//三维
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        int sz=prices.size();
        if(sz<=1)
            return 0;
        int dp[sz][sz][2];//dp[天数][卖出的次数][当前是否持股]
        
        //base case
        for(int i=0;i<sz;i++){
            dp[0][i][0]=0;
            dp[i][0][0]=0;//没买过也没买过；
            dp[0][i][1]=-prices[0];//第i+1天买了股票且没卖出过
            dp[i][0][i]=0;
        }

        for(int i=1;i<sz;i++){
            dp[0][1][i]=-10000;
            for(int j=1;j<sz;j++){
                if(j<i&&j<=k){
                    dp[i][0][j]=max(dp[i-1][0][j],dp[i-1][1][j-1]+prices[i]);
                    dp[i][1][j]=max(dp[i-1][1][j],dp[i-1][0][j]-prices[i]);
                }
            }
        }
        return dp[sz-1][0][k];
    }
};

//一维数组
/*
对于第 i 天的股票价格：
buy[j] 表示 ≤ i 天进行 j 次买入时的最大收益；
sell[j] 表示 ≤ i 天进行 j 次卖出时的最大收益。
*/
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        int days = prices.size();
        k = k > days/2? days/2 : k;
        vector<int> buy(k + 1, INT_MIN), sell(k + 1, 0);
        for (int i = 0; i < days; ++i) {
            for (int j = 1; j <= k; ++j) {
                buy[j] = max(buy[j], sell[j-1] - prices[i]);//不买,卖
                sell[j] = max(sell[j], buy[j] + prices[i]);//不卖,卖
            }
        }
        return sell[k];
    }
};
```

## 滑动窗口

### Leetcode

##### 209. 长度最小的子数组

```C++
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int i=0;//滑动窗口左边界
        int sum=0;//维护滑动窗口内数组和
        int len=0;//维护最短长度
        for(int j=0;j<nums.size();j++){
            sum+=nums[j];
            while(sum>=target){
                len=len==0?j-i+1:min(len,j-i+1);//炫技，为了处理掉第一次len为0的情况
                sum-=nums[i++];
            }
        }
        return len;
    }
};
```

## 模拟

#### 59. 螺旋矩阵 II

```C++
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        int t = 0;      // top
        int b = n-1;    // bottom
        int l = 0;      // left
        int r = n-1;    // right
        vector<vector<int>> ans(n,vector<int>(n));
        int k=1;
        int last=n*n;
        while(k<=last){
            for(int i=l;i<=r;++i,++k) ans[t][i] = k;
            ++t;
            for(int i=t;i<=b;++i,++k) ans[i][r] = k;
            --r;
            for(int i=r;i>=l;--i,++k) ans[b][i] = k;
            --b;
            for(int i=b;i>=t;--i,++k) ans[i][l] = k;
            ++l;
        }
        return ans;
    }
};
```

