# <center>目录</center>

[TOC]

## 二叉树

### [重建二叉树](https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof)

#### 我的题解

~~~cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
private:
    unordered_map<int, int> hm;
    int preidx;
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        preidx = 0;
        //将中序序列存进哈希map
        int n = inorder.size();
        for(int i = 0; i < n; i++){
            hm[inorder[i]] = i;
        }
        
        return buildTree(preorder, inorder, 0, n - 1);
    }
    
    //通过中序遍历的begin 到 end 生成二叉树 并返回根节点 
    TreeNode* buildTree(vector<int>& pre, vector<int>& in, int begin, int end){
        if(begin > end){
            return nullptr;
        }
        TreeNode* root = new TreeNode(pre[preidx]);//先序序列的值为根节点
        int idx = hm[pre[preidx++]];//根节点在中序序列中的索引
        root->left = buildTree(pre, in, begin, idx - 1);//中序序列的begin ~ end
        root->right = buildTree(pre, in, idx + 1, end);
        return root;
    }
    
};
~~~

### [二叉搜索树与双向(循环)链表](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof)

#### 我的题解

~~~cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* left;
    Node* right;

    Node() {}

    Node(int _val) {
        val = _val;
        left = NULL;
        right = NULL;
    }

    Node(int _val, Node* _left, Node* _right) {
        val = _val;
        left = _left;
        right = _right;
    }
};
*/
class Solution {
private:
    vector<Node*> inorder;
public:
    //先中序遍历得到bst的有序数组
    void inorderTraverse(Node* root){
        if(!root)
            return;
        inorderTraverse(root->left);
        inorder.push_back(root);
        inorderTraverse(root->right);
    }
    //再遍历该有序数组 修改左右指针即可得到双向链表
    Node* treeToDoublyList(Node* root) {
        if(!root)
            return NULL;
        inorderTraverse(root);
        int n = inorder.size();
        Node *pRoot = inorder[0];
        for(int i = 0; i < n; i++){
            Node *curr = inorder[i];
            if(i == 0)
                curr->left = inorder[n - 1];//双向链表无需此步骤
            if(i > 0)
                curr->left = inorder[i - 1];
            if(i < n - 1)
                curr->right = inorder[i + 1];
            if(i == n - 1)
                curr->right = inorder[0];//双向链表无需此步骤
        }
        
        return pRoot;
    }
};
~~~

### [树的子结构](https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof)

#### 我的题解

~~~cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool isSubStructure(TreeNode* A, TreeNode* B) {
        return (A && B) && (AincludeB(A, B) || isSubStructure(A->left, B) || isSubStructure(A->right, B));
        //A包含B(B和A的根一样) 或者 A的左子树包含B 或者 A的右子树包含B 
    }
    
    bool AincludeB(TreeNode* A, TreeNode* B){
        if(!B) return true;//B遍历到叶结点 说明 包含
        if(!A) return false;//A遍历到叶结点 不包含 
        if(A->val != B->val) return false; //值不相同 也不包含
        return AincludeB(A->left, B->left) && AincludeB(A->right, B->right);
    }
};
~~~

### [二叉树的第k大节点](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof)

#### 我的题解

~~~cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int kthLargest(TreeNode* root, int k) {
        int ans = 0;
        kthLargest1(root, ans, k);
        return ans;
    }
    
    //逆中序遍历
    void kthLargest1(TreeNode* root,int &ans, int& k){//此处k必须是引用 否则每回溯到父节点 k进入左孩子结点递归的时候没变
        if(!root){
            return;
        }
        kthLargest1(root->right, ans, k);
        // cout << root->val<<endl;
        --k;
        if(k == 0){
            ans = root->val;
            return;
        }
        kthLargest1(root->left, ans, k);
    }
};
~~~

### [二叉树的锯齿形层次遍历](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

#### 我的题解

~~~cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };b 
 */
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        if(!root)
            return {};
        queue<TreeNode*> q;
        vector<vector<int>> ans;
        q.push(root);
        bool lr = true;
        while(!q.empty()){
            int size = q.size();
            vector<int> temp(size, 0);
            //遍历某一层
            for(int i = 0; i < size; i++){
                TreeNode* e = q.front();
                q.pop();
                temp[lr ? i : size - i - 1] = e->val;
                
                if(e->left)
                    q.push(e->left);
                if(e->right)
                    q.push(e->right);
            }
            //一层结束
            ans.push_back(temp);
            lr = !lr;//lr需要交替变换
        }
        
        return ans;
    }
};
~~~

### [平衡二叉树](https://leetcode-cn.com/problems/ping-heng-er-cha-shu-lcof/)

#### 我的题解

~~~cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool isBalanced(TreeNode* root) {
        if(maxDepth(root) == -1)
            return false;
        return true;
    }

    //求最大深度
    int maxDepth(TreeNode* root){
        if(!root)
            return 0;
        int depthl = maxDepth(root->left);
        if(depthl == -1)
            return -1;//重要判断
        int depthr = maxDepth(root->right);
        if(depthr == -1)//重要
            return -1;
        int diff = abs(depthl - depthr);
        // cout <<"root"<<root->val<<"-"<<diff<<endl;
        // cout << "depthl:" <<depthl<<endl;
        // cout << "depthr:" <<depthr<<endl;
        if(diff <= 1)
            return max(depthl, depthr) + 1;
        return -1;//左右子树的深度之差大于1
    }
};
~~~

### [二叉树的深度](https://leetcode-cn.com/problems/er-cha-shu-de-shen-du-lcof/)

#### 我的题解

~~~cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(!root)
            return 0;
        return max(maxDepth(root->left), maxDepth(root->right)) + 1;
    }
};
~~~

### [二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

#### 我的题解

~~~cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root || root == p || root == q)
            return root; //退出递归 返回上一层递归
        
        //每次新建左右节点  因为每次递归的根节点是不一样的  左右节点也是不一样的  每一层递归逐层考察pleft pright
        TreeNode *pleft = lowestCommonAncestor(root->left, p, q);//递归左子树
        TreeNode *pright = lowestCommonAncestor(root->right, p, q);//递归右子树
        
        //某结点的左右子树都考察完了
        if(!pleft && !pright) return nullptr;//左右子树都没找到p q
        if(!pleft && pright) return pright;//在右子树上找到了p 或者 q
        if(pleft && !pright) return pleft;//在左子树上找到了p 或 q
        return root;//在左右子树上都找到了 说明当前根节点是其最近公共祖先
        //找到之后传递到其根节点 一直向上传递直到根节点 退出递归
    }
};
~~~

### [二叉树的镜像](https://leetcode-cn.com/problems/er-cha-shu-de-jing-xiang-lcof/)

#### 我的题解

~~~cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    /*
    将左右孩子节点进行
    交换，然后在利用先序递归，那么就将所有结点的左右孩子都进行了交换。
    */
    TreeNode* mirrorTree(TreeNode* root) {
        exchange(root);
        return root;
    }

    void exchange(TreeNode *root){
        if(!root) return;
        swap(root->left, root->right);
        exchange(root->left);
        exchange(root->right);
    }

};
~~~



## 链表

### [反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)

#### 题解1

> 不设哑结点

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if(!head) return NULL;//空头结点  返回空
        
        ListNode* pre = NULL;//相当于哑结点
        ListNode* cur = head;
        while(cur){
            ListNode* q = cur->next;
            cur->next = pre;
            pre = cur;
            cur = q;
        }
        
        return pre;
    }
};
~~~

#### 题解2

> 设置哑结点 类似头插法创建链表

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if(!head) return NULL;//空头结点  返回空
        
        ListNode *dummy = new ListNode(0);//新建头结点
        dummy->next = head;
        ListNode *p = dummy->next;
        dummy->next = NULL;
        while(p)
        {
            ListNode *q = p; //临时指针q
            p = p->next; //p是遍历原始链表的指针
            q->next = dummy->next;
            dummy->next = q;//类似于头插法创建链表
        }
        
        return dummy->next;//返回head
    }
};
~~~

### [k个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

#### 我的题解

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    pair<ListNode*, ListNode*> reverseList(ListNode* head, ListNode* tail){        
        ListNode* pre = tail->next;
        ListNode* cur = head;
        while(pre != tail){
            ListNode* q = cur->next;
            cur->next = pre;
            pre = cur;
            cur = q;
        }
        //pre == tail
        return {tail, head};
    }
    ListNode* reverseKGroup(ListNode* head, int k) {
        //新建哑结点
        ListNode* dummy = new ListNode(0);
        dummy->next = head;
        ListNode* pre = dummy;
        
        while(head){
            ListNode* tail = pre;
            for(int i = 0; i < k; i++){
                tail = tail->next;
                if(!tail)
                    return dummy->next;
            }//tail走到一个子链的尾部
            ListNode* nexthead = tail->next;//记录下一个子链的头部
            pair<ListNode*, ListNode*> ht = reverseList(head, tail);
            head = ht.first;//新的头结点
            tail = ht.second;//新的尾结点
            pre->next = head;//pre和新头相连
            tail->next = nexthead;//新尾和nxt相连 和下一个子链的头部相连
            pre = tail;//pre重置为该子链的尾
            head = tail->next;//头结点重置为下一个子链的头
        }
        
        return dummy->next;
    }
};
~~~

### [链表中倒数第k个节点](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

#### 我的题解

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        ListNode* first = head;
        ListNode* second = head;
        while(k){
            first = first->next;
            k--;
        }
        while(first){
            second = second->next;
            first = first->next;
        }
        return second;
    }
};
~~~

### [删除链表的倒数第N个节点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

#### 我的题解

> 两个指针巧解此题

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        if(!head)
            return NULL;
        ListNode* dummy = new ListNode(0);
        dummy->next = head;
        ListNode* first = dummy;
        ListNode* second = dummy;//删除节点和寻找结点不一样 需要新建一个哑结点 因为有可能删除头结点 这样可以简化代码 
        
        while(n){
            first = first->next;
            n--;
        }
        
        while(first->next){//此处需要注意 必须是first->next 因为我们要找的是第k个的前一个结点 
            first = first->next;
            second = second->next;
        }
        ListNode* q = second->next;
        second->next = q->next;
        delete q;
        
        return dummy->next;
    }
};
~~~

### [删除链表的节点](https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/)

#### 我的题解

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* deleteNode(ListNode* head, int val) {
        ListNode* dummy = new ListNode(-1);
        dummy->next = head;
        ListNode* p = dummy;
        while(p->next){
            if(p->next->val == val){//找到要删除节点的前一个节点
                ListNode* q = p->next;
                p->next = q->next;
                break;//删除之后就直接break就行了
            }
            p = p->next;
        }
        
        return dummy->next;
    }
};
~~~

### [合并两个排序的链表](https://leetcode-cn.com/problems/he-bing-liang-ge-pai-xu-de-lian-biao-lcof/)

#### 我的题解

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode *p1 = l1;
        ListNode *p2 = l2;
        ListNode *h = new ListNode;//新建头结点
        ListNode *p3 = h;
        
        while(p1 && p2){
            if(p1->val <= p2->val){
                p3->next = p1;
                p3 = p1;
                p1 = p1->next;  
            }
            else{
                p3->next = p2;
                p3 = p2;
                p2 = p2->next;
            }
        }
        p3->next = p1? p1 : p2;
        return h->next;
    }
};
~~~



### 合并k个升序链表

#### 我的题解

> 直接顺序两两合并($TC-O(k^2n)$)

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        ListNode *pa = nullptr;
        int n = lists.size();
        for(int i = 0; i < n; i++){
            ListNode* pb = lists[i];
            pa = mergeLists(pa, pb);
        }
        return pa;
    }
    
    ListNode *mergeLists(ListNode *headA, ListNode *headB){
        ListNode *pa = headA;
        ListNode *pb = headB;
        ListNode *head = new ListNode(0);//新建一个哑结点
        ListNode *pc = head;
        
        while(pa && pb){
            if(pa->val <= pb->val){
                pc->next = pa;
                pa = pa->next;
            }
            else{
                pc->next = pb;
                pb = pb->next;
            }
            pc = pc->next;
        }
        
        pc->next = pa ? pa : pb;
        
        return head->next;
    }
};
~~~

#### [官方题解](https://leetcode-cn.com/problems/merge-k-sorted-lists/solution/he-bing-kge-pai-xu-lian-biao-by-leetcode-solutio-2/)

> $O(nk*\log k)$

~~~cpp
class Solution {
public:
    struct Status {
        int val;
        ListNode *ptr;
        bool operator < (const Status &rhs) const {
            return val > rhs.val;
        }
    };

    priority_queue <Status> q;

    ListNode* mergeKLists(vector<ListNode*>& lists) {
        for (auto node: lists) {
            if (node) q.push({node->val, node});
        }
        ListNode head, *tail = &head;
        while (!q.empty()) {
            auto f = q.top(); q.pop();
            tail->next = f.ptr; 
            tail = tail->next;
            if (f.ptr->next) q.push({f.ptr->next->val, f.ptr->next});
        }
        return head.next;
    }
};
~~~

### [环形链表(双指针)](https://leetcode-cn.com/problems/linked-list-cycle/)

#### 我的题解

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    //快慢指针法  额外空间开销是O(1) 快指针落后于慢指针 且
    //快指针反过来追上慢指针  则说明链表有环
    bool hasCycle(ListNode *head) {
        if(!head || !head->next)
            return false;//没结点或者只有一个结点  没有环
        
        ListNode *slow = head;
        ListNode *fast = head->next;
        while(fast && fast->next){
            fast = fast->next->next;
            slow = slow->next;
            if(fast == slow)//快慢指针相遇
                return true;
        }
        //到达链表尾部
        return false;
    }
};
~~~

### [环形链表 II(双指针)](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

#### 我的题解

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        if(!head || !head->next) return NULL;
        ListNode *fast = head;
        ListNode *slow = head;
        ListNode *p = head;
        
        while(fast->next && fast->next->next){
            fast = fast->next->next;
            slow = slow->next;
            if(fast == slow){//快慢指针相遇
                //新建一个指针指向头指针，直到slow和head相遇 相遇点即为入环点
                while(p != slow){
                    p = p->next;
                    slow = slow->next;
                }
                return p;
            }
        }
        //fast经过的路程是slow的两倍
        return NULL;
    }
};
~~~

### [相交链表](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)

#### 我的题解

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode *pa = headA;
        ListNode *pb = headB;
        
        while(pa != pb){
            pa = pa == NULL ? headB : pa->next;//pa到链表A末尾 指向headB
            pb = pb == NULL ? headA : pb->next;//pb到链表B末尾 指向headA
        }//pa pb相遇时 走过的路程是一样的
        return pa;
    }
};
~~~

### [排序链表(双指针)](https://leetcode-cn.com/problems/sort-list/)

#### 我的题解

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    /*归并排序  是链表排序的最佳选择*/
    ListNode* sortList(ListNode* head) {
        if(head == NULL || head->next == NULL)
            return head;
        else{ //快慢指针找到中间节点
            // ListNode *fast = head->next;
            // ListNode *slow = head;
            // while(fast && fast->next){//判断fast指针是否即将到达末尾
            //     fast = fast->next->next;//指向第偶数个节点 步长为2
            //     slow = slow->next;//顺序指向结点 步长为1
            // }
            //上面的情况fast总是指向第偶数个结点
            
            ListNode *fast = head;
            ListNode *slow = head;
            while(fast->next && fast->next->next){//判断fast指针是否即将到达末尾
                fast = fast->next->next;//指向第奇数个节点 步长为2
                slow = slow->next;//顺序指向结点 步长为1
            }
            //上面的情况fast总是指向第奇数个节点
            
            //但是两种情况都可以找到链表的中点  因此可以选用任何一种方法
            //而且快指针总是慢指针经过结点的2倍  也就是快指针的路程是慢指针的2倍
            
            
            //退出循环后 
            //slow指向中间结点  
            //fast指向最后一个结点(节点数为奇数) 或者倒数第二个结点(节点数为偶数)
            fast = slow;//fast指向中间结点
            slow = slow->next;//slow指向中间结点的下一个节点
            fast->next = NULL;
            
            fast = sortList(head);//对前半段排序
            slow = sortList(slow);//对后半段排序
            return mergeLists(fast, slow);//合并排序之后的
        }
    }
    
    /*合并有序链表*/
    ListNode* mergeLists(ListNode *head1, ListNode *head2){
        if(!head1) return head2;
        if(!head2) return head1;
        ListNode *head = new ListNode(0);//新建头节点 用于指示合并的有序链表
        ListNode *p = head;//指向头结点
        
        while(head1 && head2){
            if(head1->val <= head2->val){
                p->next = head1;//指向head1
                head1 = head1->next;
            }
            else{
                p->next = head2;//指向head2
                head2 = head2->next;
            }
            p = p->next; //赋值为 head1 或者 head2
        }
        p->next = head1 ? head1 : head2;//某链表空了 指向另一个链表
        
        return head->next;
    }
};
~~~

### [两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

#### 我的题解

~~~cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        ListNode * hh = new ListNode(0, head);//头结点 不存储数据 指向首元结点
        ListNode *p = hh->next;//指针p指向结点hh
        ListNode *r = hh;

        while(p){
            ListNode *temp = p;
            if(p->next){//奇数的时候需要判断next是否为空
                ListNode *q = p->next;
                p = q->next;
                temp->next = q->next;
                q->next = temp;
                r->next = q;
                r = temp;
            }
            else{
                r->next = temp;
                p = p->next;
            }
        }
        
        return hh->next;
    }
};
~~~



## 栈和队列

### [柱状图中最大的矩形(单调栈)](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)

#### 我的题解

~~~cpp
class Solution {
public:
        int largestRectangleArea(vector<int>& height) {
            stack<int> si;//用栈存放直方图高度所在的索引
            int rectArea = 0; //矩形面积
            int len = height.size();
            for (int i = 0; i < len; i++)
            {
                if (si.empty() || height[si.top()] < height[i])
                {
                    si.push(i);//栈空 或者 当前元素比栈顶元素大 压栈
                }
                else //栈不空 并且 当前元素比栈顶元素小  出栈
                {
                    //只要栈不空  并且当前元素小于栈顶元素 一直出栈  并更新最大矩形面积
                    while (!si.empty() && height[si.top()] > height[i])
                    {
                        int idx = si.top();//当前栈顶元素
                        si.pop();//当前栈顶元素出栈
                        //计算矩形面积 并更新
                        rectArea = max(rectArea, height[idx] * (si.empty() ? i : i - si.top() - 1));
                    }
                    si.push(i);//退出循环  si.top()< height[i] 或者栈空 也需要压栈i
                }
            }
            //所有元素都处理结束(压栈 或者 出栈了)
            while (!si.empty())//栈中还有剩余元素没有出栈 此时需要注意计算矩形长度时 要用height.size()
            {
                int idx = si.top();
                si.pop();
                rectArea = max(rectArea, height[idx] * (si.empty() ? len : len - si.top() - 1));
                //else
                //{
                //    rectArea = max(rectArea, height[idx] * len);//此时栈中只剩最小的一个了 因此需要乘以所有柱状图个数
                //}
            }
            return rectArea;
                }
};
~~~

### [最大矩形(单调栈)](https://leetcode-cn.com/problems/maximal-rectangle/)

#### 我的题解

~~~cpp
class Solution {
public:
    int largestRectangleArea(vector<int>& height) {
        stack<int> si;//用栈存放直方图高度所在的索引
        int rectArea = 0; //矩形面积
        int len = height.size();
        for (int i = 0; i < len; i++) {
            if (si.empty() || height[si.top()] < height[i]) {
                si.push(i);//栈空 或者 当前元素比栈顶元素大 压栈
            }
            else { //栈不空 并且 当前元素比栈顶元素小  出栈
             //只要栈不空  并且当前元素小于栈顶元素 一直出栈  并更新最大矩形面积
                while (!si.empty() && height[si.top()] > height[i]) {
                    int idx = si.top();//当前栈顶元素
                    si.pop();//当前栈顶元素出栈  有可能出栈之后栈空
                    //计算矩形面积 并更新
                    rectArea = max(rectArea, height[idx] * (si.empty() ? i : i - si.top() - 1));
                }
                si.push(i);//退出循环  si.top()< height[i] 或者栈空 也需要压栈i
            }
        }
        //所有元素都处理结束(压栈 或者 出栈了)
        while (!si.empty()) { //栈中还有剩余元素没有出栈 此时需要注意计算矩形长度时 要用height.size()
            int idx = si.top();
            si.pop();
            rectArea = max(rectArea, height[idx] * (si.empty() ? len : len - si.top() - 1));
            //rectArea = max(rectArea, height[idx] * len);
            //此时栈中只剩最小的一个了 因此需要乘以所有柱状图个数
        }
        return rectArea;
    }

    int maximalRectangle(vector<vector<char>>& matrix) {
        int rown = matrix.size();//行数
        int coln = rown > 0 ? matrix[0].size() : 0;//列数
        vector<vector<int>> m(rown, vector<int>(coln, 0));//记录连续的1的个数
        //逐列求连续的1的个数
        for (int j = 0; j < coln; j++)
        {
            for (int i = 0; i < rown; i++)
            {
                if (matrix[i][j] == '0')
                {
                    m[i][j] = 0;
                }
                else if (matrix[i][j] == '1')
                {
                    if (i > 0 && matrix[i - 1][j] == '1')
                    {
                        m[i][j] = m[i - 1][j] + 1;
                    }
                    else 
                    {
                        m[i][j] = 1;
                    }
                }
            }
        }

        int rectArea = 0;
        //逐行按照求柱状图的最大面积进行计算
        for (int i = 0; i < rown; i++)
        {
            rectArea = max(rectArea, largestRectangleArea(m[i]));//m[i]表示第0行到第i行形成的柱状图
        }
        return rectArea;
    }
};
~~~

### [接雨水(单调栈)](https://leetcode-cn.com/problems/trapping-rain-water/)

#### 我的题解

~~~cpp
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size();
        //用vector模拟栈
        vector<int> si;//index stack 高度索引栈  单调递减栈
        int ans = 0;
        for (int i = 0; i < n; i++) {
            int cur = height[i];
            while (!si.empty() && cur > height[si.back()]) {
                int idx = si.back();
                si.pop_back();
                //需要判断栈空
                if (!si.empty()) {
                    //此处必须要做比较  用较小的数
                    //因为cur > height[idx] 但是不一定大于 此时栈顶的值
                    int h = min(height[si.back()], cur) - height[idx];//可以盛雨水的高度
                    int w = i - si.back() - 1;
                    ans += h * w;
                }
            }
            //cur <= height[si.top()] 需要将cur压栈  || 栈空
            si.push_back(i);
        }
        return ans;
    }
};
/*
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size();
        stack<int> si;//index stack 高度索引栈
        int ans = 0;
        for (int i = 0; i < n; i++) {
            int cur = height[i];
            while (!si.empty() && cur > height[si.top()]) {
                int idx = si.top();
                si.pop();
                //需要判断栈空
                if (!si.empty()) {
                    //此处必须要做比较  用较小的数
                    //因为cur > height[idx] 但是不一定大于 此时栈顶的值
                    int h = min(height[si.top()], cur) - height[idx];//可以盛雨水的高度
                    int w = i - si.top() - 1;
                    ans += h * w;
                }
            }
            //cur <= height[si.top()] 需要将cur压栈  || 栈空
            si.push(i);
        }
        return ans;
    }
};
*/
~~~

### [用两个栈实现队列](https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)

#### 我的题解1

~~~cpp
class CQueue {
    stack<int> s1;//插入
    stack<int> s2;//用于删除
public:
//插入和删除操作，时间复杂度均为 O(1)
    CQueue() {
        while (!s1.empty()) {
            s1.pop();
        }
        while (!s2.empty()) {
            s2.pop();
        }
    }
    
    void appendTail(int value) {
        s1.push(value);
    }
    
    int deleteHead() {
        // if(!s2.empty()){
        //     int top = s2.top();
        //     s2.pop();
        //     return top;
        // }//s2不空 就在s2中删除
        // //s2空
        // //删除元素首先将s1中的元素放到s2中  然后在s2中删除  s2中顺序存放队头元素 s1中存放后来进入队尾的元素
        // while(!s1.empty()){
        //     s2.push(s1.top());
        //     s1.pop();
        // }
        // if(!s2.empty()){
        //     int top = s2.top();
        //     s2.pop();
        //     return top;
        // }
        // //s2空 返回-1 说明队列空
        // return -1;
        
		
		//每个元素至多插入和弹出s2一次
        if(s2.empty()){
            while(!s1.empty()){
                s2.push(s1.top());
                s1.pop();
            }
        }
        //如果s2还为空
        if(s2.empty())
            return -1;//说明队列空
        //s2不空
        int top = s2.top();
        s2.pop();
        return top;
    }
};

/**
 * Your CQueue object will be instantiated and called as such:
 * CQueue* obj = new CQueue();
 * obj->appendTail(value);
 * int param_2 = obj->deleteHead();
 */
~~~

#### 我的题解2

~~~cpp
//插入O(n) 删除O(1)
class CQueue {
private:
    stack<int> s1;//插入栈 删除栈
    stack<int> s2;//辅助插入栈
public:
    CQueue() {
    }
    
    void appendTail(int value) {
        //每个元素至少被插入 和 弹出 栈s1，s2一次  插入时间复杂度是O(n)
		while(!s1.empty()){
            //栈不空
            s2.push(s1.top());
            s1.pop();
        }//将s1中的元素都push到s2
        s1.push(value);//s1插入当前值 这样该值在s1栈底 相当于插入到队尾 栈顶是队首
        while(!s2.empty()){
            s1.push(s2.top());
            s2.pop();
        }
    }
    
    int deleteHead() {
        if(!s1.empty()){
            //删除元素直接删除队首元素  即当前栈顶元素
            int top = s1.top();
            s1.pop();
            return top;
        }
        return -1;
    }
};

/**
 * Your CQueue object will be instantiated and called as such:
 * CQueue* obj = new CQueue();
 * obj->appendTail(value);
 * int param_2 = obj->deleteHead();
 */
~~~

### [最小栈](https://leetcode-cn.com/problems/min-stack/)

#### 我的题解

> 双栈实现

~~~cpp
class MinStack {
private:
    stack<int> s;
    stack<int> ts;//辅助栈 记录压入当前值时栈中的最小值
public:
    /** initialize your data structure here. */
    MinStack() {
        ts.push(INT_MAX);
    }
    
    void push(int x) {
        s.push(x);
        ts.push(min(x, ts.top()));//辅助栈记录的是 栈中压入当前数字时的最小值 两者同时增长
    }
    
    void pop() {
        s.pop();
        ts.pop();
    }
    
    int top() {
        return s.top();
    }
    
    int getMin() {
        return ts.top();
    }
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack* obj = new MinStack();
 * obj->push(x);
 * obj->pop();
 * int param_3 = obj->top();
 * int param_4 = obj->getMin();
 */
~~~

### [滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)

#### 我的题解

~~~cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        deque<int> dq;
        vector<int> ans;
        int n = nums.size();
        //单调队列实现 单调递减队列 front()即为最大元素
        for(int i = 0; i < k; i++)
        {
            while(!dq.empty() && nums[i] > dq.back()){
                dq.pop_back();
            }
            //nums[i] < back() || dq.empty()
            dq.push_back(nums[i]);
        }
        ans.push_back(dq.front());//最大值
        for(int i = k; i < n; i++){
            if(nums[i - k] == dq.front())
                dq.pop_front();//只有窗口划过的元素是队列头的元素 才弹出队头元素
            while(!dq.empty() && nums[i] > dq.back()){
                dq.pop_back();
            }
            dq.push_back(nums[i]);
            ans.push_back(dq.front());//最大值
        }
        return ans;
    }
};
~~~

### [简化路径](https://leetcode-cn.com/problems/simplify-path/)

#### 我的题解

> **stringstream的字符串分割应用，相当于split**

~~~cpp
class Solution {
public:
    string simplifyPath(string path) {
        int n = path.size();
        vector<string> strs;
        stringstream ss(path);
        string temp = "";
        string ans = "";
        char del = '/';
        while(getline(ss, temp, del)){
            //如果是连在一起的//或者/./则继续 continue
            if(temp == "" || temp == "."){
                continue;
            }
            //如果是/../则从栈中弹出栈顶元素
            if(temp == ".." && !strs.empty()){
                strs.pop_back();
            }
            else if(temp != ".."){//是文件夹名
                strs.push_back(temp);
            }
        }
        
        for(auto str:strs){
            ans += "/" + str;
        }
        if(ans.empty()){
            ans += "/";
        }
        return ans;
    }
};
~~~



## 搜索

### 深度优先搜索(DFS)

#### [岛屿的最大面积](https://leetcode-cn.com/problems/max-area-of-island)

##### 我的题解

~~~cpp
class Solution {
public:
    int maxAreaOfIsland(vector<vector<int>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        int ans = 0;

        for (int i = 0; i < m; i++)
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    ans = max(ans, dfs(i, j, grid));
                }
            }
        return ans;
    }

    int dfs(int x, int y, vector<vector<int>>& grid) {
        if (x < 0 || x > grid.size() - 1 || y < 0 || y > grid[0].size() - 1 || grid[x][y] != 1) {//超出边界
            return 0;
        }
        //只考虑格子值为1的 并且没被访问过的
        grid[x][y] = 0;//访问过了 置0
        int area = 1;
        area += dfs(x, y - 1, grid);//左
        area += dfs(x, y + 1, grid);//右
        area += dfs(x - 1, y, grid);//上
        area += dfs(x + 1, y, grid);//下
        //int dx[4] = {0, 0, 1, -1};
        //int dy[4] = {1, -1, 0, 0};
        //grid[x][y] = 0;//访问过了
        //int area = 1;
        //for(int i = 0; i < 4; i++){
        //    area += dfs(x + dx[i], y + dy[i], grid);//向四个方向走
        //}
        
        //上面每次都int area = 1表示这个方格算面积1 然后每次从一个方格出发之后 直到不满足要求返回时 area才会累加 正向dfs的时候area += 0 = 1 反向(朝递归由上一节点进来时方向的反方向)回溯的时候area += return的area
        return area;
    }
};
~~~

#### [机器人的运动范围](https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof)

##### 我的题解

~~~cpp
class Solution {
public:
    //计算位之和
    int sum(int num){
        int s = 0;
        while (num) {
            int y = num % 10;
            s += y;
            num = num / 10;
        }
        return s;
    }
    
    int movingCount(int m, int n, int k) {
        vector<vector<int>> vis(m, vector<int>(n, 0));
        return dfs(0, 0, k, vis);
    }
    
    int dfs(int x, int y, int k, vector<vector<int>>& vis){
        if(x < 0 || y < 0 || x >= vis.size() || y >= vis[0].size() || vis[x][y] != 0){
            return 0;
        }
        if(sum(x) + sum(y) > k)
            return 0;
        
        int dx[4] = {0, 0, 1, -1};
        int dy[4] = {1, -1, 0, 0};
        vis[x][y] = 1;//访问过了
        int range = 1;
        for(int i = 0; i < 4; i++){
            range += dfs(x + dx[i], y + dy[i], k, vis);//向四个方向走
        }
        
        return range;
    }
};
~~~

### 广度优先搜索(BFS)

#### [机器人的运动范围](https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof)

##### 我的题解

~~~cpp
class Solution {
public:
    //计算位之和
    int sum(int num){
        int s = 0;
        while (num) {
            int y = num % 10;
            s += y;
            num = num / 10;
        }
        return s;
    }
    
    int movingCount(int m, int n, int k) {
        vector<vector<int>> vis(m, vector<int>(n, 0));
        using pos = pair<int, int>;//定义新的结构体 pos
        queue<pos> q;//队列 存储横纵坐标值
        int dx[4] = {0, 0, 1, -1};
        int dy[4] = {1, -1, 0, 0};
        int range = 0;
        
        //将0,0存进q
        pair<int, int> pos0(0, 0);
        q.push(pos0);
        vis[0][0] = 1;
        while(!q.empty()){
            pair<int, int> front = q.front();
            q.pop();
            range++;//q里面的都是满足题意的
            for(int i = 0; i < 4; i++){
                int x = front.first + dx[i];
                int y = front.second + dy[i];
                if(x < 0 || x >= m || y < 0 || y >= n || vis[x][y] != 0)
                    continue;
                if(sum(x) + sum(y) > k)
                    continue;
                vis[x][y] = 1;
                q.push({x, y});
            }
        }
        
        return range;
    }
};
~~~

### 回溯

#### [矩阵中的路径](https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/)

##### 我的题解

~~~cpp
class Solution {
private:
    string word;
public:
    bool exist(vector<vector<char>>& board, string word) {
        this->word = word;
        int m = board.size();
        int n = board[0].size();
        for(int i = 0; i < m; i++){
            for(int j = 0; j < n; j++){
                if(board[i][j] == word[0]){//从矩阵中找到和word第一个字符相同的字符 从此处开始dfs
                    vector<vector<int>> vis(m, vector<int>(n, 0));
                    if(dfs(i, j, vis, board, 0))
                        return true;
                }
            }
        }
        return false;
    }
    
    bool dfs(int x, int y, vector<vector<int>>& vis,vector<vector<char>>& board, int idx){//idx:word中字符索引 vis访问数组
        if(idx == word.size()){
            return true;//存在该字符串 退出 后面不需要考虑了
        }
        
        if(x < 0 || y < 0 || x >= board.size() || y >= board[0].size() || vis[x][y] != 0 || board[x][y] != word[idx]){
            return false;
        }
        
        int dx[4] = {0, 0, 1, -1};
        int dy[4] = {1, -1, 0, 0};
        //右左下上
        idx++;
        vis[x][y] = 1;//访问过了 必定在正确路径上
        for(int i = 0; i < 4; i++){//四个方向分别dfs
            if(dfs(x + dx[i], y + dy[i], vis, board, idx))
                return true;//在正确的搜索方向上 返回true
        }
        vis[x][y] = 0;//这个方向不能搜索到整个字符串 只搜到一部分相同的 但是到某个字符向四个方向都返回了false 可能前面的字符从下一个不同的方向到该字符会成功 因此需要置零 在下个方向可能会考虑 需要回溯
        return false;//一个字符的四个方向都没有得到正确的搜索方向 说明这条路不通 返回false
    }
};
~~~

#### [复原IP地址](https://leetcode-cn.com/problems/restore-ip-addresses/)

##### 我的题解

~~~cpp
class Solution {
private:
    vector<string>ans;
public:
    vector<string> restoreIpAddresses(string s) {
        vector<string> subRes;
        backtrack(0, subRes, s);
        
        return ans;
    }
    string join(vector<string> vec, string del){
        string ret = vec[0];
        for(int i = 1; i < vec.size(); i++){
            ret += del + vec[i];
        }
        return ret;
    }
    /*一共4段  每段的长度不超过3 每段大小不超过255 最短长度是4  最长长度是12*/
    void backtrack(int start,vector<string> subRes, string s) {
        if (subRes.size() == 4 && start == s.size() ) {
            //满4段 或者 遍历完了字符串 加入结果集
            string subAns = join(subRes, ".");
            ans.push_back(subAns);
            return;
        }
        
        //满4段 但是没有遍历完字符串
        if(subRes.size() == 4 && start < s.size())
            return;
        
        //一共有三种长度 1 2 3
        for(int len = 1; len <=3; len++){
            if(start + len - 1  >= s.size())
                return;//长度超过字符串长度
            
            if(len != 1 && s[start] == '0')
                return;//是0x 0xx
            
            //切出当前片段
            string ss = s.substr(start, len);
            
            if(len == 3 && atoi(ss.c_str()) > 255)
                return;//长度为3时不能大于255
            
            subRes.push_back(ss);//压入子结果集
            backtrack(start + len, subRes, s);
            subRes.pop_back();//撤销状态 恢复之前的状态
        }
    }
};
~~~

#### [全排列](https://leetcode-cn.com/problems/permutations/)

##### 我的题解

~~~cpp
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        int n = nums.size();
        vector<vector<int>> ans;
        vector<int> temp;
        vector<int> vis(n, 0);
        //回溯
        backtrack(nums, ans, temp, vis);
        
        return ans;
    }
    
    void backtrack(vector<int>& nums, vector<vector<int>> & ans, vector<int>& temp, vector<int>& vis){
        if(temp.size() == nums.size()){
            ans.push_back(temp);
            return;
        }
        for(int i = 0; i < nums.size(); i++){
            if(vis[i]){
                continue;//已经访问了 剪枝
            }
            temp.push_back(nums[i]);//选择该数字
            vis[i] = 1;
            backtrack(nums, ans, temp, vis);//执行搜索
            temp.pop_back();//撤销选择该数字
            vis[i] = 0;
        }
    }
};
~~~

#### [无重复字符串的排列组合](https://leetcode-cn.com/problems/permutation-i-lcci/)

> **题解1和题解2得到的全排列的顺序是不同的**

##### 我的题解1

> 此处使用的是**交换**。

~~~cpp
class Solution {
private:
    vector<string> ans;//存放解
public:
    vector<string> permutation(string s) {
        if(!s.size())
            return {};
        backtrack(0, s);//从第0个字符开始考察
        
        return ans;
    }
    
    void backtrack(int startIdx, string s){
        if(startIdx == s.size() - 1){
            ans.push_back(s);
            return;
        }
        unordered_set<char> hashset;//存放字符
        for(int i = startIdx; i < s.size(); i++){
            if(hashset.count(s[i]) == 1){
                //存在该元素 剪枝 不考虑该元素
                continue;
            }
            hashset.insert(s[i]);
            swap(s[startIdx], s[i]);//交换start和其他元素的位置
            backtrack(startIdx + 1, s);//考虑下一个元素
            swap(s[startIdx], s[i]);//恢复交换
        }
    }
    
    /*void swap(char *p, char *q){
        char temp = *p;
        *p = *q;
        *q = temp;
    }*/
};
~~~

##### 我的题解2

~~~cpp
class Solution {
private:
    vector<string> ans;//存放解
public:
    vector<string> permutation(string s) {
        if(!s.size())
            return {};
        // vector<bool>used(s.size(), false);//访问数组
        string temp = "";
        backtrack(s, temp);//从第0个字符开始考察
        
        return ans;
    }
    
    void backtrack(string s, string temp){
        if(temp.size() == s.size()){
            ans.push_back(temp);
            return;
        }
        for(int i = 0; i < s.size(); i++){
            if(temp.find(s[i]) != string::npos){
                //存在该元素 剪枝 不考虑该元素
                continue;
            }
            temp.push_back(s[i]);
            backtrack(s, temp);
            temp.pop_back();
        }
    }
};
~~~

#### [有重复字符串的排列组合](https://leetcode-cn.com/problems/permutation-ii-lcci/)

> **此处不能使用上一题(无重复字符串的排列组合)中“我的题解2”那种方式求解，但是用“我的题解1”是完全没有问题的，因为它的求解方法中使用的是集合，这样就避免了重复元素。**

##### 我的题解

~~~cpp
class Solution {
private:
    vector<string> ans;//存放解
public:
    vector<string> permutation(string s) {
        if(!s.size())
            return {};
        backtrack(0, s);//从第0个字符开始考察
        
        return ans;
    }
    
    void backtrack(int startIdx, string s){
        if(startIdx == s.size() - 1){
            ans.push_back(s);
            return;
        }
        unordered_set<char> hashset;//存放字符
        for(int i = startIdx; i < s.size(); i++){
            if(hashset.count(s[i]) == 1){
                //存在该元素 剪枝 不考虑该元素
                continue;
            }
            hashset.insert(s[i]);
            swap(s[startIdx], s[i]);//交换start和其他元素的位置
            backtrack(startIdx + 1, s);//考虑下一个元素
            swap(s[startIdx], s[i]);//恢复交换
        }
    }
    
    /*void swap(char *p, char *q){
        char temp = *p;
        *p = *q;
        *q = temp;
    }*/
};
~~~



#### [第k个排列序列](https://leetcode-cn.com/problems/permutation-sequence/)

> 给出集合 `[1,2,3,...,n]`，其所有元素共有 `n!` 种排列。

##### 我的题解

~~~cpp
class Solution {
private:
    int g_n;
    int g_k;
public:
    string getPermutation(int n, int k) {
        g_n = n;
        g_k = k;
        string ans = "";
        vector<int> factor = getFactor(n);//阶乘数组
        vector<int> vis(n + 1, 0);//访问数组
        dfs(0, ans, factor, vis);//深度优先搜索
        return ans;
    }

    /*计算阶乘*/
    vector<int> getFactor(int n)
    {
        vector<int>factor(n + 1, 0);
        factor[0] = 1;
        for (int i = 1; i <= n; i++) {
            factor[i] = factor[i - 1] * i;
        }

        return factor;
    }

    /*深度优先搜索 直接到达目的结点 通过剪枝 忽略不需要考虑的结点*/
    void dfs(int start, string& ans, vector<int>& factor, vector<int>& vis) {
        if (ans.size() == g_n) {
            return;
        }
        int cnt = factor[g_n - 1 - start];
        //第一次搜索的时候其含义为
        //以1开头，剩余字符串形成的全排列个数=(n - 1)!
        //cout <<"cnt: "<< cnt << endl;
        for (int i = 1; i <= g_n; i++) {
            if (vis[i]) {//被访问过了 剪枝
                continue;
            }
            if (cnt < g_k) {//当前字符串的全排列个数 < k 剪枝
                //k需要减去上一轮要剪枝的叶子结点个数 即上一轮字符串形成的全排列个数
                g_k -= cnt;
                //cout << "k :" << g_k << endl;
                continue;
            }
            //如果cnt >= k 说明第k个全排列在当前字符串的全排列(个数)中
            vis[i] = 1;
            ans.push_back(i + '0');
            dfs(start + 1, ans, factor, vis);
            //此处不需要回溯  直接深度优先搜索即可
            //设计的是一下子访问到目标(第k个)叶子结点因此不需要重置vis数组  
            return;
            //此处需要return, 后面的数没有必要去尝试了
        }
    }
};
~~~



## 动态规划(DP)

### [剪绳子|整数拆分](https://leetcode-cn.com/problems/jian-sheng-zi-ii-lcof)

#### 不涉及大数取余

> 不涉及到大数取余，动态规划($TC-O(n^2)$) 

~~~cpp
class Solution {
public:
    int cuttingRope(int n) {
        vector<int> dp(n + 1);//将n划分成至少2个正整数的乘积最大值
        //n >= 2
        dp[0] = 0;
        dp[1] = 0;
        //将i划分成j和i - j 且i- j不再拆分成正整数之和 此时dp[i] = j * (i - j)
        //将i划分成j和i - j 且i-j 拆分成正整数之和  此时dp[i] = j * dp[i - j]
        for(int i = 2; i <= n; i++){
            for(int j = 1; j < i; j++){
                dp[i] = max(dp[i], max(j * (i - j), j * dp[i - j]));
            }
        }
        
        return dp[n];
    }
};
~~~

> 贪心算法 $TC-O(n)$

~~~cpp
class Solution {
public:
    //贪心算法 O(n/3)
    int cuttingRope(int n) {
        if(n <= 3)
            return n - 1;
        int ans = 1;
        while(n > 4){
            n -= 3;//尽可能多的切成长度为3的段 结论
            ans *= 3;
        }
        
        return n*ans;
    }
};
~~~

#### 涉及大数取余

> 涉及大数取余，使用**快速幂取余**，贪心算法

~~~cpp
class Solution {
public:
    int cuttingRope(int n) {
        if(n <= 3)
            return n - 1;
        int a = n / 3 - 1;
        int b = n % 3;
        int p = 1000000007;
        long int rem = 1;
        long int x = 3;
        //快速幂求余
        while(a > 0){
            if(a & 1) rem = (rem * x) % p;
            x = (x * x) % p;
            a /= 2;
        }
        if(b == 0) return (rem * 3) % p;
        if(b == 1) return (rem * 4) % p;
        return (rem * 6) % p;
    }
};
~~~

### [单词拆分](https://leetcode-cn.com/problems/word-break)

#### 我的题解

~~~cpp
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
		unordered_set<string> ws;
        for(auto word: wordDict){
            ws.insert(word);
        }//存入哈希表
		
        int n = s.size();
        vector<int> dp(n + 1, 0);
        dp[0] = 1;//串为空初始化为1
        //dp[i] = 1 表示s[0:i-1]能够被拆分成单词 i表示字符串长度i-1-0 + 1
        //dp[j] = dp[i] && s[i:j-1]在wordDict中
        //leetcode [leet code]
        for(int i = 0; i <= n; i++){
            for(int j = i + 1; j <= n; j++){
                //cout << s.substr(i, j - i) << endl; 
                if(dp[i] && ws.find(s.substr(i, j - i)) != ws.end()){
                    dp[j] = 1;
                }
            }
        }
        
        if(dp[n])
            return true;
        return false;
    }
};

//这个好理解
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> ws;
        for(auto word: wordDict){
            ws.insert(word);
        }//存入哈希表
        
        int n = s.size();
        vector<int> dp(n + 1, 0);
        dp[0] = 1;//串为空初始化为1
        //dp[i] = 1 表示s[0:i-1]能够被拆分成单词 i表示字符串长度
        //dp[i] = dp[j] && s[j:i]在wordDict中
        
        for(int i = 1; i <= n; i++){//长度1~n
            for(int j = 0; j <= i; j++){
                if(dp[j] && ws.find(s.substr(j, i - j)) != ws.end()){
                    dp[i] = 1;
                    break;//长度为i的字符串可以被拆分成单词 后面的不需要考虑了
                }
            }
        }
        
        if(dp[n])
            return true;
        return false;
    }
};
~~~

### [*买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

#### 我的题解(贪心)

~~~cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int minPrice = INT_MAX;
        int maxprofit = 0;
        int n = prices.size();
        for(int i = 0; i< n; i++){
            if(prices[i] < minPrice)
            {
                minPrice = prices[i];//最小买入价格
            }
            else//维护一个最大利润 根据最小购买价格
            {
                maxprofit = max(maxprofit, prices[i] - minPrice);
            }
        }
        
        return maxprofit;
    }
};
~~~

#### 我的题解(动规)

> 只能完成一笔交易

~~~cpp
//昨天持股和今天持股有很大的关系 因此需要将是否持股这个
//状态设计到状态数组中
/*
令 dp[i][0]表示第i天结束的时候，手上不持股 状态为0 获得的最大利润 
令 dp[i][1]表示第i天结束的时候，手上持股 状态为1 获得的最大利润 
因此最后计算出的dp[n-1][0]为最终的结果 即到最后一天结束的时候，不持股获得的最大利润 
*/ 
class Solution {
public:
    int maxProfit(vector<int>& prices) {
    	int n = prices.size();
		if(n < 2)
			return 0;
        vector<vector<int>> dp(n, vector<int>(2, 0));
		dp[0][0] = 0;//第0天不持股显然获得的利润为0
		dp[0][1] = -prices[0];//第0天持股，获得的利润为当天股价的相反数
		
		//主要是要理清今天持股、不持股 和 昨天持股和不持股的关联 
		for(int i = 1; i < n; i++){
			//今天不持股 1.昨天不持股，今天啥都不做 2.昨天持股，今天卖出 
			dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
			//第i天不持股最大的利润是  第i-1天不持股和第i-1天持股第i天卖出获得利润的较大值
			//今天持股 1.昨天持股，今天啥都不做 2. 昨天不持股，今天买入 
			dp[i][1] = max(dp[i - 1][1], -prices[i]);
			//第i天持股最大的利润是 第i-1天持股和 第i天买入获得利润 的较大者 
			//由于只能交易一次，因此今天买入获得的利润只能是当天股价的相反数 
		} 
		
		return dp[n - 1][0]; 
    }
}; 

//空间优化之后
//从上面的代码中可以看出 循环中更新dp数组只与前一天的是否持股有关 因此可以优化到一维
//========================================================================
//昨天持股和今天持股有很大的关系 因此需要将是否持股这个
//状态设计到状态数组中
/*
令 dp[i][0]表示第i天结束的时候，手上不持股 状态为0 获得的最大利润 
令 dp[i][1]表示第i天结束的时候，手上持股 状态为1 获得的最大利润 
因此最后计算出的dp[n-1][0]为最终的结果 即到最后一天结束的时候，不持股获得的最大利润 
*/ 
class Solution {
public:
    int maxProfit(vector<int>& prices) {
    	int n = prices.size();
		if(n < 2)
			return 0;
        vector<int> dp(2, 0);
		dp[0]= 0;//第0天不持股显然获得的利润为0
		dp[1] = -prices[0];//第0天持股，获得的利润为当天股价的相反数
		
		//主要是要理清今天持股、不持股 和 昨天持股和不持股的关联 
		for(int i = 1; i < n; i++){
			//今天不持股 1.昨天不持股，今天啥都不做 2.昨天持股，今天卖出 
			dp[0] = max(dp[0], dp[1] + prices[i]);
			//第i天不持股最大的利润是  第i-1天不持股和第i-1天持股第i天卖出获得利润的较大值
			//今天持股 1.昨天持股，今天啥都不做 2. 昨天不持股，今天买入 
			dp[1] = max(dp[1], -prices[i]);
			//第i天持股最大的利润是 第i-1天持股和 第i天买入获得利润 的较大者 
			//由于只能交易一次，因此今天买入获得的利润只能是当天股价的相反数 
		} 
		
		return dp[0]; 
    }
}; 
~~~

### [*买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

#### 我的题解(贪心)

> 尽可能完成更多的交易

~~~cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        int max_profit = 0;
        //所有上升阶段距离的和 > 最低到最高的距离  画折线图可以清晰看出
        for(int i = 1; i < n; i++){
            if(prices[i - 1] < prices[i])
                max_profit += prices[i] - prices[i - 1];
        }
        return max_profit;
    }
};
~~~

#### 我的题解(动规)

~~~cpp
//昨天持股和今天持股有很大的关系 因此需要将是否持股这个
//状态设计到状态数组中
/*
令 dp[i][0]表示第i天结束的时候，手上不持股 状态为0 获得的最大利润 
令 dp[i][1]表示第i天结束的时候，手上持股 状态为1 获得的最大利润 
因此最后计算出的dp[n-1][0]为最终的结果 即到最后一天结束的时候，不持股获得的最大利润 
*/ 
class Solution {
public:
    int maxProfit(vector<int>& prices) {
    	int n = prices.size();
		if(n < 2)
			return 0;
        vector<vector<int>> dp(n, vector<int>(2, 0));
		dp[0][0] = 0;//第0天不持股显然获得的利润为0
		dp[0][1] = -prices[0];//第0天持股，获得的利润为当天股价的相反数
		
		//主要是要理清今天持股、不持股 和 昨天持股和不持股的关联 
		for(int i = 1; i < n; i++){
			//今天不持股 1.昨天不持股，今天啥都不做 2.昨天持股，今天卖出 
			dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
			//第i天不持股最大的利润是  第i-1天不持股和第i-1天持股第i天卖出获得利润的较大值
			//今天持股 1.昨天持股，今天啥都不做 2. 昨天不持股，今天买入 
			dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
			//第i天持股最大的利润是 第i-1天持股和 第i天买入获得利润 的较大者 
			//由于可以交易多次，因此今天买入获得的利润是 前一天不持股 今天买入 获得的利润 
		} 
		
		return dp[n - 1][0]; 
    }
}; 

//空间优化到一维 同上题

//昨天持股和今天持股有很大的关系 因此需要将是否持股这个
//状态设计到状态数组中
/*
令 dp[i][0]表示第i天结束的时候，手上不持股 状态为0 获得的最大利润 
令 dp[i][1]表示第i天结束的时候，手上持股 状态为1 获得的最大利润 
因此最后计算出的dp[n-1][0]为最终的结果 即到最后一天结束的时候，不持股获得的最大利润 
*/ 
class Solution {
public:
    int maxProfit(vector<int>& prices) {
    	int n = prices.size();
		if(n < 2)
			return 0;
        vector<int> dp(2, 0);
		dp[0] = 0;//第0天不持股显然获得的利润为0
		dp[1] = -prices[0];//第0天持股，获得的利润为当天股价的相反数
		
		//主要是要理清今天持股、不持股 和 昨天持股和不持股的关联 
		for(int i = 1; i < n; i++){
			//今天不持股 1.昨天不持股，今天啥都不做 2.昨天持股，今天卖出 
			dp[0] = max(dp[0], dp[1] + prices[i]);
			//第i天不持股最大的利润是  第i-1天不持股和第i-1天持股第i天卖出获得利润的较大值
			//今天持股 1.昨天持股，今天啥都不做 2. 昨天不持股，今天买入 
			dp[1] = max(dp[1], dp[0] - prices[i]);
			//第i天持股最大的利润是 第i-1天持股和 第i天买入获得利润 的较大者 
			//由于可以交易多次，因此今天买入获得的利润是 前一天不持股 今天买入 获得的利润 
		} 
		
		return dp[0]; 
    }
}; 

//空间优化到一维 同上题

~~~



### [*买卖股票的最佳时机 III(困难)](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/)

#### 我的题解(动规)

> 最多可以完成2笔交易

~~~cpp
/*
dp[i][j][k]表示
在[0,i]的区间内(截止第i天 i>=0) 
进行了第j次交易
此时状态为k时(持股和不持股)
得到的最大利润;
j: 表示进行了第几次交易 也就是第几次买入股票
k: 0表示不持股  1表示持股
初始化下列值:
dp[0][0][0] = 0          表示进行了第0次交易并且不持股 当然等于0
dp[0][1][0] = 0          表示进行了第一次交易并且不持股 显然不成立 等于0无妨
dp[0][1][1] = -prices[0] 表示进行了第一次交易并且持股 "所得的利润"是当天价钱的相反数 即负数
dp[0][2][0] = 0          表示进行了第二次交易并且不持股 显然不成立 等于0无妨
dp[0][2][1] = -inf       表示进行了第二次交易并且持股  此处等于负无穷 第二次交易肯定没发生

当k等于1 表示交易在当天或之前就已经发生了并且此时持股 那么初始化第0天的时候 需要注意：
dp[0][2][1] = -inf 表示第0天必定不可能进行第2次交易 由于该值dp[0][2][1]需要与dp[0][1][0] - prices[1] <= 0
(即第1天进行第1次交易时作比较) 初始化为0 显然不对
*/
//先持股 再卖出
/*
for (int i = 1; i < n; i++)
{
    //截止第i天进行了1次交易 并且此时持股 也就是第1股还没卖出
    //需要得出第几天买进股票才能获得最大利润 也就是说买入的时候尽可能的小
    //因此需要确定截至第i天若进行第1次交易，持股时能够获得的最大利润

    dp[i][1][1] = max(dp[i - 1][1][1], -prices[i]);
    
    //截至第i天 进行了1次交易 并且卖出第1股能够获得的最大利润
    //今天(第i天)卖出第1股 和 前一天(第i - 1天)不持股时候 比较(可能已经在前一天或之前就卖出了)
    //计算得出截止 今天(第i天)卖出第1股 获得的最大利润

    dp[i][1][0] = max(dp[i - 1][1][0], dp[i - 1][1][1] + prices[i]);

    //截止第i天 进行了2次交易 此时持股(第1股已经卖出)
    //今天进行第2次交易 和 前一天持股时(前一天或者之前就进行了第2次交易) 得到的利润进行比较
    //得出截止第i天进行了2次交易持股时获得的最大利润

    dp[i][2][1] = max(dp[i - 1][2][1], dp[i - 1][1][0] - prices[i]);

    //截止第i天 进行了2次交易 并且卖出第2股获得的最大利润
    //今天卖出第2股 和 前一天不持股时 比较(可能在前一天或之前就卖出了)
    //计算得出 截止今天卖出第2股 获得的最大利润

    dp[i][2][0] = max(dp[i - 1][2][0], dp[i - 1][2][1] + prices[i]);
    
    //总是和 截止前一天 作比较得到 截止今天 的最大利润
}
*/
//进行了空间优化O(1) 因为每次比较总是今天 和 前一比较 因此只需要通过循环来记录前一天的值即可 在新的循环中更新
//时间复杂度是O(n)
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        vector<vector<int>> dp(3, vector<int>(2,0));

        
        dp[1][1] = -prices[0];
        dp[2][1] = INT_MIN;
        
        for (int i = 1; i < n; i++)
        {
            //截止第i天进行了1次交易 并且此时持股 也就是第1股还没卖出
            //需要得出第几天买进股票才能获得最大利润 也就是说买入的时候尽可能的小
            //因此需要确定截至第i天若进行第1次交易，持股时能够获得的最大利润

            dp[1][1] = max(dp[1][1], -prices[i]);

            //截至第i天 进行了1次交易 并且卖出第1股能够获得的最大利润
            //今天(第i天)卖出第1股 和 前一天(第i - 1天)不持股时候 比较(可能已经在前一天或之前就卖出了)
            //计算得出截止 今天(第i天)卖出第1股 获得的最大利润

            dp[1][0] = max(dp[1][0], dp[1][1] + prices[i]);

            //截止第i天 进行了2次交易 此时持股(第1股已经卖出)
            //今天进行第2次交易 和 前一天持股时(前一天或者之前就进行了第2次交易) 得到的利润进行比较
            //得出截止第i天进行了2次交易持股时获得的最大利润

            dp[2][1] = max(dp[2][1], dp[1][0] - prices[i]);

            //截止第i天 进行了2次交易 并且卖出第2股获得的最大利润
            //今天卖出第2股 和 前一天不持股时 比较(可能在前一天或之前就卖出了)
            //计算得出 截止今天卖出第2股 获得的最大利润

            dp[2][0] = max(dp[2][0], dp[2][1] + prices[i]);

            //总是和 截止前一天 作比较得到 截止今天 的最大利润
        }
        
        return max(dp[1][0], dp[2][0]);
        //返回截止第n-1天 进行1次交易和进行2次交易 得到利润的最大值
    }
};
~~~

### [*最长上升子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

#### 我的题解1

> $TC-O(n\log n)$   二分搜索

~~~cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        if(n == 0)
            return 0;
        vector<int> dp(n + 1, 0);//dp[i]表示长度为i的上升序列的最小末尾元素
        //末尾元素越小，那么它的后续发展潜力更大
        //即在后面追加更大元素以形成更长上升序列的几率更大
        int len = 1;
        dp[len] = nums[0];
        for(int i = 1; i < n; i++){
            if(nums[i] > dp[len]){//大于末尾最小数字 加入即可
                dp[++len] = nums[i];
            }
            else{//nums[i] <= nums[i - 1]
                int l = 1;
                int r = len;
                int pos = 0;//要是没有找到比它小的 说明都大于它 因此直接替换0 + 1处即可
                //二分法在dp中找到比nums[i]小的第一个元素
                // while(l <= r){//细节  必须等于
                //     int mid = (l + r) / 2;// /2
                //     if(nums[i] > dp[mid]){
                //         pos = mid;
                //         l = mid + 1;
                //     }
                //     else{
                //         r = mid - 1;
                //     }
                // }
                //dp[pos + 1] = nums[i];//并将其替换
                while (l < r) {//循环条件是有2个元素
                    int mid = l + (r - l) / 2;
                    if (dp[mid] < nums[i])
                        l = mid + 1;
                    else//当前值也有可能是比target大的第一个元素
                        //因此需要包含进去
                        r = mid;
                }//退出循环条件是l == r
                dp[l] = nums[i];
            }
        }
        return  len;
    }
};
~~~

#### 我的题解2

> $TC-O(n^2)$

~~~cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int> dp(n, 1);//初始化为全1 每个单独的数字长度为1
        //dp[i] 表示以nums[i]结尾的序列(包含nums[i]) 最长上升子序列长度
        int ans = 1;
        for(int i = 0; i < n; i++){
            for(int j = 0; j < i; j++){
                if(nums[i] > nums[j]){
                    //dp[i] = max(dp[j]) + 1
                    dp[i] = max(dp[i], dp[j] + 1);
                    //初始状态dp[i] = 1 因此要找出max(dp[j]) 则需要每次作比较才可
                }
            }
            //cout<<dp[i]<<endl;
            ans = max(ans, dp[i]);//以nums[i]结尾的序列中 最长上升子序列长度
            // cout << ans <<endl;
        }
        
        return n ? ans : 0;
    }
};
~~~

### [*最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

#### 我的题解

~~~cpp
class Solution {
    /*
    dp[i][j] = true; i == j  length = 1;
    dp[i][i+1] = true; s[i] = s[i+1] length = 2;
    dp[i][j] = dp[i+1][j-1] && (s[i] == s[j])  
    */
public:
    string longestPalindrome(string s) {
        int n = s.size();
        vector<vector<int>> dp(n, vector<int>(n));//二维向量
        string ans = "";
        
        for(int len = 0; len < n; len++)//每种长度都需要考虑
            for(int i = 0; i + len < n; i++)//以字符串中的第i个字符开始
            {
                int j = i + len;
                if(len == 0){
                    dp[i][j] = 1;
                }
                else if(len == 1)
                {
                    dp[i][j] = (s[i] == s[j]);
                }
                else{
                    dp[i][j] = (dp[i+1][j-1] && s[i] == s[j]);
                }
                if(dp[i][j] && len + 1 > ans.size()){//当前回文子串的长度大于已计算的回文子串
                    ans = s.substr(i, len + 1);
                }
            }
        return ans;
    }
};
~~~

### [*最大子序和](https://leetcode-cn.com/problems/maximum-subarray/)

#### 我的题解

> 最大连续子序列和，最大子段和

~~~cpp
class Solution {
public:
    //TC-O(n)
    int maxSubArray(vector<int>& nums) {
        //动态规划
        //dp[i] 表示以nums[i]结尾的序列(该最大子序和序列中包含nums[i]) 最大子序和  (最大连续子序列和)
        int n = nums.size();
        vector<int> dp(n, 0);
        dp[0] = nums[0];
        int ans = dp[0];
        for(int i = 1; i < n; i++){
            // if(dp[i - 1] > 0){
            //     dp[i] = dp[i - 1] + nums[i];//若dp[i - 1] >0 说明加上 nums[i] 必定会使得最大子段和dp[i]增加
            // }
            // else{
            //     dp[i] = nums[i];//dp[i - 1] <= 0 说明加上nums[i]必定不能使得 dp[i]增大 此时dp[i] = nums[i]
            // }
            dp[i] = max(dp[i - 1], 0) + nums[i];
            ans = max(dp[i], ans);
            //cout << dp[i]<<endl;
        }
        return ans;
    }
};

//空间优化之后
class Solution {
public:
    //空间复杂度降到O(1)
    int maxSubArray(vector<int>& nums) {
        int ans = nums[0];
        int pre = 0;
        int n = nums.size();
        for(int i = 0; i < n; i++)
        {
            pre = max(pre, 0) + nums[i];
            ans = max(pre, ans);
        }
        
        return ans;
    }
};
~~~

### [*三角形最小路径和](https://leetcode-cn.com/problems/triangle/)

#### 我的题解

~~~cpp
class Solution {
public:
    //SC-O(n)
    int minimumTotal(vector<vector<int>>& triangle) {
        int n = triangle.size();
        vector<int> dp(n + 1, 0);//多一层 第n+1层初始化为0 那么第n层就是 三角形第n层的数字了
        for(int i = n - 1; i >= 0; i--){//从倒数第二层开始
            for(int j = 0; j < triangle[i].size(); j++){
                dp[j] = min(dp[j], dp[j + 1]) + triangle[i][j];//下一行的左右最小值 + 当前行的值
            }//因为比较只与下一层有关 因此可以优化到一维数组
        }
        
        return dp[0];
    }
};
/*
class Solution {
public:
    //原地进行dp 空间复杂度降为0
    int minimumTotal(vector<vector<int>>& triangle) {
        int n = triangle.size();
        for(int i = n - 2; i >= 0; i--){//从倒数第二层开始
            for(int j = 0; j < triangle[i].size(); j++){
                triangle[i][j] += min(triangle[i+1][j], triangle[i+1][j + 1]);//下一行的左右最小值 + 当前行的值
            }//因为比较只与下一层有关 因此可以优化到一维数组
        }
        
        return triangle[0][0];
    }
};
*/
~~~

### [统计全为 1 的正方形子矩阵](https://leetcode-cn.com/problems/count-square-submatrices-with-all-ones/)

#### 我的题解

> 可以使用动态规划降低时间复杂度。我们用 **$dp(i, j)$ 表示以$ (i, j)$为右下角，且只包含 1 的正方形的边长最大值**。如果我们能计算出所有 $dp(i, j)$ 的值，那么其中的最大值即为矩阵中只包含 1 的正方形的边长最大值，其平方即为最大正方形的面积。
>
> 那么如何计算 $dp$ 中的每个元素值呢？对于每个位置$ (i, j)$，检查在矩阵中该位置的值：
>
> 如果该位置的值是 0，则 $dp(i, j) = 0$，因为当前位置不可能在由 1组成的正方形中；
>
> 如果该位置的值是 1，则 $dp(i, j) $的值由其上方、左方和左上方的三个相邻位置的 $dp $值决定。具体而言，当前位置的元素值等于三个相邻位置的元素中的最小值加 1，状态转移方程如下：
>
> $dp(i, j)=min(dp(i−1, j), dp(i−1, j−1), dp(i, j−1))+1$
>
> ![fig1](https://assets.leetcode-cn.com/solution-static/221/221_fig1.png)

~~~cpp
class Solution {
public:
    int countSquares(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = m > 0 ? matrix[0].size() : 0;
        int ans = 0;
        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));//初始化dp数组为 m+1 n+1
        for(int i = 1; i < m + 1; i++){
            for(int j = 1; j < n + 1; j++){
                if(matrix[i - 1][j - 1] == 1){
                    dp[i][j] = min(dp[i - 1][j], min(dp[i - 1][j - 1], dp[i][j - 1])) + 1;
                    ans += dp[i][j];
                }
            }
        }
        
        return ans;
    }
};
~~~



### [最大正方形](https://leetcode-cn.com/problems/maximal-square/)

#### 我的题解1(动规)

~~~cpp
class Solution {
public:
//TC-O(mn)
    int maximalSquare(vector<vector<char>>& matrix) {
        int m = matrix.size();
        int n = m > 0 ? matrix[0].size() : 0;
        int max_area = 0;
        int max_side = 0;
        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));//初始化dp数组为 m+1 n+1
        for(int i = 1; i < m + 1; i++){
            for(int j = 1; j < n + 1; j++){
                if(matrix[i - 1][j - 1] == '1'){
                    dp[i][j] = min(dp[i - 1][j], min(dp[i - 1][j - 1], dp[i][j - 1])) + 1;
                    max_side = max(dp[i][j], max_side);
                }
            }
        }
        max_area = max_side * max_side;
        return max_area;
    }
};
~~~

#### 我的题解2

~~~cpp
class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        int m = matrix.size();
        int n = m > 0 ? matrix[0].size() : 0;
        vector<int> left_bound(n, 0);//每个点的左边界
        vector<int> right_bound(n, n);//每个点的右边界
        vector<int> height(n, 0);//每个点的高度
        int max_area = 0;

        /*先找到某点出发的高度  然后向左右寻找边界即可*/
        /*TC  O(mn)  SC  O(n)*/
        for(int i = 0; i < m; i++){
            int left = 0;
            int right = n;
            //更新高度
            for(int j = 0; j < n; j++){
                if(matrix[i][j] == '1'){
                    height[j]++;
                }
                else{
                    height[j] = 0;
                }
            }
            
            //更新左边界
            for(int j = 0; j < n; j++){
                if(matrix[i][j] == '1'){
                    left_bound[j] = max(left_bound[j], left);
                }
                else{
                    left = j + 1;//是0 左边界向右移动
                    left_bound[j] = 0;
                }
            }
            
            //更新右边界 从右边开始考察
            for(int j = n - 1; j >= 0; j--){
                if(matrix[i][j] == '1'){
                    right_bound[j] = min(right_bound[j], right);
                }
                else{
                    right = j;
                    right_bound[j] = n;
                }
            }
            
            
            for(int j = 0; j < n; j++){
                //cout << "h = "<<height[j] << endl;
                int horizon = right_bound[j] - left_bound[j];
                //cout <<"l = "<<left_bound[j] <<", r= " <<right_bound[j] << endl;
                int area = height[j] >= horizon ? horizon * horizon : height[j] * height[j];
                //cout << "area = "<<area<<endl;
                max_area = max(max_area, area);
            }
            //cout <<"--------------------"<<endl;
        }
       
        return max_area;
    }
};
~~~

### [俄罗斯套娃信封问题(sort+LIS)](https://leetcode-cn.com/problems/russian-doll-envelopes/)

#### 我的题解

~~~cpp
class Solution {
public:
    //在class中必须是静态的 在类外作为普通函数则不需要
    static bool cmp(vector<int>& vec1, vector<int>& vec2){
        if(vec1[0] != vec2[0])//第一个元素不相同
             return vec1[0] < vec2[0];//按照第一个元素升序排列
        else//第一个元素相同
             return vec1[1] > vec2[1];//按照第二个元素降序排列
    }
    //TC-O(nlogn)
    int maxEnvelopes(vector<vector<int>>& envelopes) {
        //将数组按照w自小到大排序 w相同 按照h自大到小排序
        sort(envelopes.begin(), envelopes.end(),cmp);
        
        //利用LIS算法求出h的最长递增子序列
        return lengthOfLIS(envelopes);
    }
    
     int lengthOfLIS(vector<vector<int>>& nums) {
        int n = nums.size();
        if(n == 0)
            return 0;
        vector<int> dp(n + 1, 0);//dp[i]表示长度为i的上升序列的最小末尾元素
        //末尾元素越小，那么它的后续发展潜力更大
        //即在后面追加更大元素以形成更长上升序列的几率更大
        int len = 1;
        dp[len] = nums[0][1];
        for(int i = 1; i < n; i++){
            if(nums[i][1] > dp[len]){//大于末尾最小数字 加入即可
                dp[++len] = nums[i][1];
            }
            else{//nums[i] <= nums[i - 1]
                int l = 1;
                int r = len;
                int pos = 0;//要是没有找到比它小的 说明都大于它 因此直接替换0 + 1处即可
                //二分法在dp中找到比nums[i]小的第一个元素
                while(l <= r){//细节  必须等于
                    int mid = (l + r) / 2;// /2
                    if(nums[i][1] > dp[mid]){
                        pos = mid;
                        l = mid + 1;
                    }
                    else{
                        r = mid - 1;
                    }
                }
                dp[pos + 1] = nums[i][1];//并将其替换
            }
        }
        return  len;
    }
};
~~~



## 双指针

### [三数之和](https://leetcode-cn.com/problems/3sum/)

#### 我的题解

~~~cpp
class Solution {
public:
    //不适合用哈希法做
    //left<right
    //nums[left] + nums[right] + a < 0 left++;
    //nums[left] + nums[right] + a > 0 right--;
    vector<vector<int>> threeSum(vector<int>& nums) {
        int n = nums.size();
        vector<vector<int>> ans;  
        sort(nums.begin(), nums.end());//对原数组排序 升序排列  nlogn

        for(int i = 0; i < n; i++){//a
            if(i > 0 && nums[i - 1] == nums[i]){//枚举的数必须不一样
                continue;
            }
            int target = -1*nums[i];//-a
            //双指针法
            int right = n - 1;//右指针从大到小 右->左
            int left = i + 1; //左指针由小到大移动 左->右
            while(left < right){
                if(nums[left] + nums[right] < target)
                    left++;
                else if(nums[left] + nums[right] > target)
                    right--;
                else{ //==0
                    ans.push_back(vector<int>{-target, nums[left], nums[right]});
                    left++;
                    right--;//同时收缩左右指针
                    //去重左右指针处值  必须和上一个指的元素不一样
                    while(left < right && nums[left] == nums[left - 1])
                        left++;
                    while(left < right && nums[right] == nums[right + 1])
                        right--;
                }
            }//退出循环
        }
        return ans;
    }
};
~~~

### [无重复字符的最长子串(双指针+哈希)](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

#### 我的题解

~~~cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        //双指针操作
        unordered_set<char> hashset;//用哈希集合来判断是否有重复的字符
        int n = s.size();
        int rp = 0;//右指针 移动
        int ans = 0;//最大长度
        for(int lp = 0; lp < n; lp++){//遍历字符串
           //每次循环左指针右移1
            if(lp != 0){
                hashset.erase(s[lp - 1]);
            }
            while(rp < n && hashset.count(s[rp]) == 0){//哈希集合中没有找到该元素
                hashset.insert(s[rp]);//元素插入哈希集合
                ++rp;//右指针向右移动
            }
            ans = max(ans, rp - lp);
        }
        return ans;
    }
};
~~~



## 哈希表

> unordered_map unordered_set

### [LRU缓存机制(哈希+双向链表)](https://leetcode-cn.com/problems/lru-cache/)

#### 我的题解

~~~cpp
/*构建双向链表*/
struct DLinkedList{
    int key;
    int val;
    DLinkedList *prev;
    DLinkedList *next;
    DLinkedList():key(0), val(0){}
    DLinkedList(int _key, int _val):key(_key), val(_val), prev(nullptr), next(nullptr){}   
};

class LRUCache {
private:
    DLinkedList *head;
    DLinkedList *tail;
    unordered_map<int, DLinkedList*> cache;
    int capacity;
    int curCap;
    
public:
    LRUCache(int capacity) {
        head = new DLinkedList();
        tail = new DLinkedList();
        head->next = tail;
        tail->prev = head;
        this->capacity = capacity;
        this->curCap = 0;//当前缓存容量
    }
    
    void push_front(DLinkedList *p){
        p->prev = head;
        p->next = head->next;
        head->next->prev = p;
        head->next = p;
    }
    
    DLinkedList *getFront(){
        return head->next;
    }
    
    void push_back(DLinkedList *p){
        p->next = tail;
        p->prev = tail->prev;
        tail->prev->next = p;
        tail->prev = p;
    }
    
    void erase(DLinkedList *p){
        p->prev->next = p->next;
        p->next->prev = p->prev;
    }
    
    void pop_front(){
        erase(head->next);
    }
    
    void move_back(DLinkedList *p){//移动到尾部
        erase(p);//删除该结点
        push_back(p);//插入到尾部
    }
    
    
    int get(int key) {
        if(cache.count(key)){//存在该键
            DLinkedList *p = cache[key];
            move_back(p);//移动到双向链表尾部
            
            return cache[key]->val;
        }
        return -1;
    }
    
    void put(int key, int value) {
        if(cache.count(key)){//该键已经在缓存中了
            DLinkedList *p = cache[key];
            move_back(p);//移动到链表尾部
            p->val = value;//修改该键的值为新值
        }
        else{
            if(curCap >= capacity){//超过容量了
                DLinkedList *f = getFront();
                cache.erase(f->key);//在缓存中将该键移除
                pop_front();//移除头部节点 头部是最久未使用的结点 尾部是最近刚使用的结点
            }
            else{
                curCap++;
            }
            DLinkedList *node = new DLinkedList(key, value);//新建结点
            cache[key] = node;
            push_back(node);
        }
    }
};

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache* obj = new LRUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
~~~

### 两数之和

#### 我的题解

~~~cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
       //建立哈希表  用map
        unordered_map<int, int> hashtable;//key = nums[i] value = i
        for(int i = 0; i < nums.size(); i++)
        {
            auto it = hashtable.find(target - nums[i]);//寻找target - nums[i]
            if(it != hashtable.end())//找到了
            {
                return {it->second, i};
            }
            
            hashtable[nums[i]] = i;
        }
        return {};
    }
};
~~~

### [数组中重复的数字](https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)

#### 我的题解

~~~cpp
class Solution {
public:
    int findRepeatNumber(vector<int>& nums) {
        unordered_set<int> hs;
        int n = nums.size();
        for(int i = 0 ; i < n; i++){
            if(hs.find(nums[i]) == hs.end())
                hs.insert(nums[i]);
            else{
                return nums[i];
            }
        }
        return -1;
    }
};
~~~

### [包含字符串的排列(哈希+滑动窗口)](https://leetcode-cn.com/problems/permutation-in-string/)

> 判断 **s2** 是否包含 **s1** 的排列。

#### 我的题解

~~~cpp
class Solution {
public:
    bool checkInclusion(string s1, string s2) {
//滑动窗口算法  窗口大小为s1的长度
        if (s1.size() > s2.size())
            return false;

        int ws = s1.size();//ws = window size

        //建立两个哈希表(用数组模拟)
        //map1记录模式串的字典  记录每个字母的频率分布
        vector<int> hashmap1(26, 0);//26个小写字母
        //map2记录匹配串中窗口大小的子串的字母频率分布
        vector<int> hashmap2(26, 0);

        //初始化两个哈希表
        for (int i = 0; i < s1.size(); i++) {
            int idx1 = s1[i] - 'a';
            int idx2 = s2[i] - 'a';
            hashmap1[idx1]++;
            hashmap2[idx2]++;
        }
        //动态更新匹配串的窗口大小子串的字母频率分布
        //哈希表2始终记录的是在当前窗口范围内出现的字母的频率
        for (int i = ws; i < s2.size(); i++) {
            if (hashmap1 == hashmap2)
                return true;//两个频率分布一样 说明s1的一个排列是s2的子串
            //否则  更新  窗口在s2上向右滑动
            int idx_ = s2[i - ws] - 'a';//更新上一个窗口中的频率 --
            hashmap2[idx_]--;
            int idx = s2[i] - 'a';//更新当前窗口中的频率 ++
            hashmap2[idx]++;//该字母在s2中出现一次

        }

        return hashmap1 == hashmap2;//两个相等则说明true
    }
};
~~~

### [最长连续序列](https://leetcode-cn.com/problems/longest-consecutive-sequence/)

#### 我的题解

~~~cpp
class Solution {
private:
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_set<int> hs;
        int n = nums.size();
        if(!n)
            return 0;
        //先插入哈希集合
        for(int i = 0; i < n; i++){
            hs.insert(nums[i]);
        }
        
        int ans = 1;
        for(int i = 0; i < n; i++){
            if(hs.count(nums[i]) == 1){//那些被擦除的元素必定在最大序列里面包含着
                //存在该元素 
                hs.erase(nums[i]);//从集合中删除
                
                //找到左右邻居
                int left = nums[i] > INT_MIN ? nums[i] - 1 : nums[i];
                int right = nums[i] < INT_MAX ? nums[i] + 1 : nums[i];
                
                //从左右邻居开始向左向右分别寻找 一次可以找到以该元素扩散的最大连续数字序列
                while(hs.count(left) == 1){
                    hs.erase(left--);
                }
                while(hs.count(right) == 1){
                    hs.erase(right++);
                }

                ans = max(ans, right - left - 1);
            }    
        }
        return ans;

    }
    
};
~~~



## 位运算

### [只出现一次的数字](https://leetcode-cn.com/problems/single-number/)

#### 我的题解

> $TC-O(n) SC-O(1)$

~~~cpp
class Solution {
public:
    
    int singleNumber(vector<int>& nums) {
        int n = nums.size();
        //任何数和0异或不变
        int onceNum = 0;
        for(int i = 0; i < n; i++){
            onceNum ^= nums[i];
        }
        return onceNum;
    }
};
~~~

### [只出现一次的数字 III](https://leetcode-cn.com/problems/single-number-iii/)

#### 我的题解

~~~cpp
class Solution {
public:
    //TC O(n)  SC-O(1)
    vector<int> singleNumber(vector<int>& nums) {
        int n = nums.size();
        int ans = 0;
        for(int i = 0; i < n ; i++){
            ans ^= nums[i];
        }
        
        //ans是两个不同的数字ab异或之后的结果；
        int x = 1;
        while((x & ans) == 0){
            x <<= 1;//x左移一位
        }//直到找到ans为1的位
        
        int a = 0;
        int b = 0;
        //分组 
        //nums[i]与x相与=1 表示nums[i]该位不为0 而原数组中两个不重复的数在该位一定不同 所以必定分到两个不同的组中  两个组中的元素数目不一定相同 这取决于原数组元素的分布 而相同的数必定会分到同一组中
        for(int i = 0; i < n; i++){
            if(x & nums[i])
                a ^= nums[i];
            else
                b ^= nums[i];
        }
        
        return vector<int>{a,b};
    }
};
~~~



### [二进制中1的个数](https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/)

#### 我的题解

~~~cpp
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int num = 0;
        while(n){
            n &= (n - 1);
            num++;
        }
        return num;
    }
    /*
    int hammingWeight(uint32_t n) {
        int num = 0;
        while(n){
            num += n & 0x01;//得出最后一位是0还是1
            n >>= 1;
        }
        return num;
    }
    */
};
~~~

### [UTF-8 编码验证](https://leetcode-cn.com/problems/utf-8-validation/)

#### 我的题解

~~~cpp
class Solution {
public:
    bool validUtf8(vector<int>& data) {
        int n = data.size();
        int flag = 0;
        int cnt = 0;
        for(int i = 0; i < n; i++){
            if(cnt == 0){
                if(data[i] >> 5 == 0b110) cnt = 1;
                else if(data[i] >> 4 == 0b1110) cnt = 2;
                else if(data[i] >> 3 == 0b11110) cnt = 3;//剩余需要考虑的数字
                else if(data[i] >> 7) return false;
            }
            else {
                if(data[i] >> 6 != 0b10) return false;
                --cnt;
            }
        }
        //cnt != 0说明还没考察结束 就退出循环了 说明10xxxx不够 不符合题意
        return cnt == 0;
    }
};
~~~



## 分治

### [数组中的第k个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)

#### 我的题解

~~~cpp
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        int low = 0;
        int low0 = low;
        int high = nums.size() - 1;
        int n = nums.size();
        int high0 = high;
        int flag = 1;
        
        //加快执行速度  随机选择数
        int j = rand() % (high - low + 1) + low;
        swap(nums[j], nums[low]);
        
        int pivot = nums[low];
        while(flag){
            if(low < high){
                //随机选择枢轴位置
                int j = rand() % (high - low + 1) + low;
                swap(nums[j], nums[low]);
            }
            pivot = nums[low];
            while(low < high){
                while(low < high && nums[high] >= pivot) high--;
                if(low != high) nums[low] = nums[high];
                while(low < high && nums[low] <= pivot) low++;
                if(low != high) nums[high] = nums[low];
            }
            nums[low] = pivot;
            if(low == n - k)
                flag = 0;
            else{
                if(low < n - k){
                    low0 = ++low;
                    high = high0;
                }
                else{
                    low = low0;
                    high0 = --high;
                }
            }
        }
        return pivot;
    }
};
~~~

### [最小的k个数](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/)

#### 我的题解



## 数组

### [最长连续递增序列](https://leetcode-cn.com/problems/longest-continuous-increasing-subsequence/)

#### 我的题解

~~~cpp
class Solution {
public:
    int findLengthOfLCIS(vector<int>& nums) {
        int n = nums.size();
        int ans = 0;
        int j = 0;
        for(int i = 0; i < n; i++){
           if(i > 0 && nums[i] <= nums[i - 1]) j = i;
            ans = max(ans, i - j + 1);
        }
        return ans;
    }
};
~~~



## 字符串

### [最长公共前缀](https://leetcode-cn.com/problems/longest-common-prefix/)

#### 我的题解

~~~cpp
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
         if(strs.size() == 0)
            return "";
        //纵向扫描 以第0个字符串为参考对象
        int n = strs.size();
        int len0 = strs[0].size();
        for(int i = 0; i < len0; i++){//遍历第0个字符串
            char c = strs[0][i];
            for(int j = 1; j < n; j++){//第1个到第n-1个字符串顺序考虑
                if(i == strs[j].length() || strs[j][i] != c)
                    return strs[0].substr(0, i);
            }
        }
        return strs[0];
    }
};
~~~

## 数字问题

### [整数反转](https://leetcode-cn.com/problems/reverse-integer/)

#### 我的题解

~~~cpp
class Solution {
public:
    int reverse(int x) {
       int rev_x = 0;
        while(x)
        {
            int y = x % 10;
            x /= 10;
            if(rev_x > INT_MAX / 10 || rev_x == INT_MAX / 10 && y > 7 ) return 0;
            if(rev_x < INT_MIN / 10 || rev_x == INT_MIN / 10 && y < -8) return 0;
            //都是溢出
            rev_x = rev_x * 10 + y;//在溢出之前判断
        }
        return rev_x;
    }
};
~~~

### [回文数](https://leetcode-cn.com/problems/palindrome-number/)

#### 我的题解

~~~cpp
class Solution {
public:
    bool isPalindrome(int x) {
        //负数 不是回文数
        if(x < 0)
            return false;
        //0开头0结尾的非零数字不是回文数
        if(x % 10 == 0 && x != 0)
            return false;
        
        int rev_x = 0;
        while(x > rev_x)
        {
            int y = x % 10;//个位数
            x /= 10;//除了个位数之后的数字
            rev_x = rev_x * 10 + y;
        }
        if(x < rev_x && x == rev_x / 10) return true;
        if(x == rev_x) return true;
        return false;
    }
};
~~~

### [字符串相加](https://leetcode-cn.com/problems/add-strings/)

#### 我的题解

~~~cpp
 class Solution {
public:
    string addStrings(string num1, string num2) {
        //模拟竖式加法
        int i = num1.size() - 1;
        int j = num2.size() - 1;

        string ans = "";
        int carry = 0;//进位
        int res = 0;
        while(i >= 0 || j >= 0){
            int x = i >= 0 ? num1[i] - '0' : 0;
            int y = j >= 0 ? num2[j] - '0' : 0;
            res = (x + y + carry) % 10;
            carry = (x + y +carry) / 10;
            ans.push_back(res + '0');
            i--;
            j--;
        }
        if(carry != 0){
            ans.push_back(carry + '0');
        }
        reverse(ans.begin(), ans.end());//翻转
        return ans;
    }
};
~~~

### [字符串相乘](https://leetcode-cn.com/problems/multiply-strings/)

#### 我的题解

~~~cpp
class Solution {
public:
    string multiply(string num1, string num2) {
        string ans = "0";
        if(num1 == "0" || num2 == "0")
            return ans;
        int m = num1.size();
        int n = num2.size();

        for(int i = n - 1; i >= 0; i--){
            string num;
            for(int k = n - 1; k > i; k--){
                num.push_back('0');
            }//补零
            int carry = 0;//进位位
            int res = 0;//结果位
            for(int j = m - 1; j >= 0; j--){
                int x = num2[i] - '0';
                int y = num1[j] - '0'; 
                res = (x * y + carry) % 10;
                carry = (x * y +carry) / 10;
                num.push_back(res + '0');
            }
            if(carry != 0)
                num.push_back(carry + '0');
            reverse(num.begin(), num.end());//翻转数字
            
            ans = addStrings(ans, num);
        }
        return ans;
    }
    
    string addStrings(string num1, string num2) {
        //模拟竖式加法
        int i = num1.size() - 1;
        int j = num2.size() - 1;

        string ans = "";
        int carry = 0;//进位
        int res = 0;
        while(i >= 0 || j >= 0){
            int x = i >= 0 ? num1[i] - '0' : 0;
            int y = j >= 0 ? num2[j] - '0' : 0;
            res = (x + y + carry) % 10;
            carry = (x + y +carry) / 10;
            ans.push_back(res + '0');
            i--;
            j--;
        }
        if(carry != 0){
            ans.push_back(carry + '0');
        }
        reverse(ans.begin(), ans.end());//翻转
        return ans;
    }
};
~~~

### [字符串转换整数 (atoi)](https://leetcode-cn.com/problems/string-to-integer-atoi/)

#### 我的题解

~~~cpp
class Solution {
public:
    bool is_sign(char sign)
    {
        if (sign == '-' || sign == '+')
            return true;
        return false;
    }
    
    int myAtoi(string s) {
        int i = 0;
        int num = 0;
        
        while (isblank(s[i]))//跳过空白字符
        {
            i++;
        }
        //第一个有效字符
        if(!isdigit(s[i]) && !is_sign(s[i]))
            return num;
        
        int sign = 1;
        if (is_sign(s[i])) //如果是符号
        {
            if (s[i] == '-')
                sign = -1;
            i++;
        }
        //如果是数字
        if(isdigit(s[i]))
        {
            while (isdigit(s[i])) {
                //视正负号而定
                int y = sign == 1 ? s[i] - '0' : -1 * (s[i] - '0');//转换成数字
                if (num > INT_MAX / 10 || num == INT_MAX / 10 && y > 7 ) return INT_MAX; 
                if (num < INT_MIN / 10 || num == INT_MIN / 10 && y < -8) return INT_MIN;
                num = (num * 10 + y);
                i++;
            }
        }

        return num;
    }
};
~~~

## 排序

### C++STL中sort工作原理

~~~
STL中的sort()，在数据量大时，采用快排quicksort，分段递归；一旦分段后的数量小于某个门限值，改用插入排
序Insertion sort，避免quicksort深度递归带来的过大的额外负担，如果递归层次过深，还会改用
heapsort(堆排序)。
~~~

#### sort & qsort

~~~cpp
/*
qsort的自定义比较函数 规则：
compar 参数指向一个比较两个元素的函数。
比较函数的原型应该像下面这样。注意两个形参必须是 const void * 型，
同时在调用 compar 函数（compar 实质为函数指针，这里称它所指向的函
数也为 compar）时，传入的实参也必须转换成const void * 型。在 compar
函数内部会将 const void * 型转换成实际类型，见下。

int compar(const void *p1, const void *p2);

如果 compar 返回值小于 0（< 0），那么 p1 所指向元素会被排在p2所指
向元素的前面;如果 compar 返回值等于 0（= 0），那么 p1 所指向元素与
 p2 所指向元素的顺序不确定;如果 compar 返回值大于 0（> 0），那么 p1
  所指向元素会被排在 p2 所指向元素的后面。
*/
#include<iostream>
#include<cstdlib>
#include<algorithm>
using namespace std;

int compar(const void* p1, const void* p2) {
	return *(int*)p1 - *(int*)p2;
}

bool compar1(int p1, int p2) {
	return p1 < p2;//升序排列
	//return p1 > p2; //降序排列 
}

class Student {
public:
	int id;
	string name;
	double grade;

	/*友元函数内部重载可以多个参数*/
	friend bool operator<(const Student& s1, const Student& s2)
	{
		return s1.id < s2.id;
	}

	/*（非友元函数）内部重载只能一个参数*/
//	bool operator<(const Student& s) {
//		//return id > s.id;//降序排列
//		return id < s.id;//升序排列

//	}
};

//bool operator<(const Student& s1, const Student& s2)
//{
//	return s1.id < s2.id;
//}
int main() {
	const int maxn = 100;
	int r[] = { 81, 68, 33, 40 ,89, 72, 75, 21, 75, 3 ,63, 15, 54 };

	//C库函数
	//qsort(r, sizeof(r)/sizeof(int), sizeof(int),compar);

	//C++ algorithm库函数 默认升序排列
	sort(r, r + 13, compar1);

	for (int i = 0; i < 13; i++) {
		cout << r[i] << " ";
	}
	cout << endl;

	Student stu[4];
	stu[0] = { 1, "aaa", 120.22 };
	stu[1] = { 2, "bbb", 221.11 };
	stu[2] = { 3, "ccc", 232.11 };
	stu[3] = { 4, "ddd", 333.11 };
	sort(stu, stu + 4);
	for (int i = 0; i < 4; i++) {
		cout << stu[i].name << endl;
	}
	system("pause");
	return 0;
}
~~~



### [内部排序算法(8种)](https://blog.csdn.net/qq_41139677/article/details/108912742)

#### 快速排序

~~~cpp
/*排列高低子表元素 最后确定枢轴位置*/
int  Partition(int r[], int low, int high)
{
    int pivotkey = r[low];//枢轴记录为r[low]

    while (low < high) {
        while (low < high && r[high] >= pivotkey) {
            high--;
        }//r[high] < pivotkey

        //交换high和low指向的记录
        Swap1(&r[high], &r[low]);
        //Swap2(r[high], r[low]);

        while (low < high && r[low] <= pivotkey) {
            low++;
        }//r[low] > pivotkey

        //交换low和high指向的记录
        Swap1(&r[low], &r[high]);
    }
    return low;
}

/*第一种方法 快速排序*/ 
void QuickSort(int r[], int low, int high) {
    if (low < high)
    {
        int pivotloc = Partition(r, low, high);
        QuickSort(r, low, pivotloc - 1); //对低子表进行递归排序
        QuickSort(r, pivotloc + 1, high);//对高子表进行递归排序
    }
}

/*第二种 快排方法*/
int QuickSort_(int r[], int low, int high)
{
	if(low < high)
	{
		int pivotkey = r[low];//枢轴记录为r[low]
		int pl = low;
	    int ph = high;
	    
	    while (low < high) 
		{	
	        while (low < high && r[high] >= pivotkey) {
	            high--;
	        }//r[high] < pivotkey
	        
	        if(low < high)
	        	r[low++] = r[high];
	
	        while (low < high && r[low] <= pivotkey) {
	            low++;
	        }//r[low] > pivotkey
	        
	        if(low < high)
	        	r[high--] = r[low];
    	}//完成一次快排 
    	
		int pivotloc = low;//确定枢轴位置为low 
    	
		//重要!!!!!! 
    	r[pivotloc] = pivotkey; //一次快排结束之后 以low为分界线 r[low]处为上次快排的枢轴记录 
    	
		QuickSort_(r, pl, pivotloc - 1);//对低字表进行递归排序
		QuickSort_(r, pivotloc + 1, ph);//对高子表进行递归排序 
	}
}

~~~

#### 归并排序

~~~cpp
/*合并子表*/ 
void Merge(int r[], int left, int mid, int right)
{
	//9个元素  left = 1; right = 9+1; mid = (1+9+1)/2 = 5;  10个元素 left = 1; right = 10; mid = 5; 
	int n1 = mid - left;//5 - 1 = 4 
	for(int i = 0; i < n1; i++)
	{
		L[i] = r[left + i]; //1 2 3 4 ... 
	} 
	int n2 = right - mid; //9+1 - 5 = 5
	for(int j = 0; j < n2; j++)
	{
		R[j] = r[mid + j];
	}
	L[n1] = inf;
	R[n2] = inf;//末尾元素初始化为无穷大
	
	int i = 0;//L[]元素指针
	int j = 0;//R[]元素指针 
	for(int k = left; k < right; k++)
	{
		if(L[i] <= R[j])
			r[k] = L[i++];
		else
			r[k] = R[j++];	
	} 
} 

/*归并排序*/
void MergeSort(int r[], int left, int right)
{
	if(left < right - 1)//right = n + 1 ; left = 1; 
	{
		//下标是1开始所以需要right = n + 1,这样分成左右子表时的元素下标才正确 
		int mid = (left + right) / 2; 
		MergeSort(r, left, mid);//对左子表进行递归合并排序 
		MergeSort(r, mid, right);//对右子表进行递归合并排序 
		Merge(r, left, mid, right);//合并 左右子表 
	}
}
~~~

#### 堆排序

~~~cpp
/*
调整为大顶堆
子节点下标需要注意：
1. 若是待排序序列下标是0开始的 
	则父节点的下标是<序列长度/2 = j>,
		左孩子结点的下标是<2*j>,
		右孩子结点的下标是<2*j+1>
2. 若是待排序序列下标是1开始的 
	则父节点的下标是<序列长度/2-1 = j>, 
		左孩子结点的下标是<2*j+1>,
		右孩子结点的下标是<2*j+2> 
*/
void HeapAdjust(int r[], int parent, int n)
{
	// 调整r[parent ... n]为大顶堆 parent是父节点  
	// n下标最大的子节点 也就是待排序序列的长度 
	// 其实调整的值为 以parent为根节点的二叉树 满足大/小顶堆的定义 
	int temp = r[parent];//父节点的值暂存 
	
	for(int j = 2 * parent; j <= n; j++)
	{
		//右孩子是最大的结点 否则 左孩子是最大的节点 
		if(j < n && r[j] < r[j + 1])  ++j; 
		
		//父节点值>=右孩子结点值
		if(temp >= r[j]) break;
		
		//父节点值小于左/右孩子结点值
		r[parent] = r[j];//父节点值替换为左/右孩子结点值 
		parent = j;//parent暂存子节点下标 
	}
	//若break，父节点值无需右孩子结点交换值
	//否则，子节点的值替换为父节点的值 
	r[parent] = temp;	
} 

/*堆排序*/
void HeapSort(int r[])
{
	//从第一个非叶子结点n/2开始进行初始大顶堆构建 
	//元素个数为n个 下标从1开始的序列 非叶子节点下标为 n/2 ~ 1 
	for(int i = n / 2; i > 0; i--)
	{
		HeapAdjust(r, i, n); 
	} 
	
	for(int i = n; i > 1; i--)
	{
		//将大顶堆第一个值（最大）与最后一个值交换 
		//最后一个值变成最大的 第一个值变成堆顶元素 
		int t = r[1];
		r[1] = r[i];
		r[i] = t; 
		
		//此时还需要调整堆 将1~i-1调整为大顶堆 r[1]为整个堆的根节点 调整即可 
		HeapAdjust(r, 1, i - 1); 
	} 
}
~~~

#### 插入排序

~~~cpp
void InsertSort(int r[])
{

    //gap = 1的shell sort
    for (int i = 2; i <= n; i++)
    {
        if (r[i] < r[i - 1])
        {
            r[0] = r[i];
            r[i] = r[i - 1];//向后移动

            int j = i - 2;
            while (r[0] < r[j]) //若是r[0]比r[j]小
            {
                r[j + 1] = r[j];//r[j]向后移动
                --j;//向前找比r[0]大的记录
            }//r[0] >= r[j]
            r[j + 1] = r[0];
        }
    }
}
~~~

#### 折半插入排序

~~~cpp
void BiInsertSort(int r[])
{
    for (int i = 2; i <= n; i++)
    {
        r[0] = r[i];
        int low = 1;
        int high = i - 1;
        while (low <= high)
        {
            //int mid = (high + low) / 2;
            //防止溢出
            int  mid = high - (high - low) / 2;
            if (r[0] < r[mid])
                high = mid - 1;
            else low = mid + 1;
        }

        int j = i - 1;
        while (j >= high + 1)
        {
            r[j + 1] = r[j];
            --j;
        }//j < hight + 1  j == high
        r[j + 1] = r[0];
    }
}
~~~

#### 选择排序

~~~cpp
//选择排序
void SelectSort(int r[])
{
	for(int i = 1; i < n; i++)// n-1趟 
	{
		int k = i;
		for(int j = i + 1; j <= n; j++)//选择出剩余元素中的最小值 
		{
			if(r[j] < r[k])
				k = j; 
		}
		if(k != i)//将第i个元素和剩余元素中的最小值交换 
		{
			Swap1(&r[k], &r[i]);
			//int t = r[i]; 
			//r[i] = r[k];
			//r[k] = t;
		}
	}
} 
~~~

#### 希尔排序

~~~cpp
void ShellInsert(int r[], int gap)
{
    for (int i = gap + 1; i <= n; i++)
    {
        if (r[i] < r[i - gap])
        {
            r[0] = r[i];
            int j = i - gap;
            while (j > 0 && r[0] < r[j])
            {
                //向后移动元素
                r[j + gap] = r[j];
                j -= gap;
            }
            r[j + gap] = r[0];
        }
    }

}

/*
希尔排序
delta数组是间隔数组 每个元素代表间隔gap大小
t表示delta数组的元素个数
*/
void ShellSort(int r[], int delta[], int t)
{
    for (int i = 0; i < t; i++)
    {
        ShellInsert(r, delta[i]);//对每一组子序列进行插入排序
    }
}
~~~

#### 冒泡排序

~~~cpp
/*冒泡排序*/
void BubbleSort(int r[])
{
    for (int i = 1; i < n; i++) //n - 1趟 
    {
        for (int j = 1; j <= n - i; j++) //每趟排序会将一个最大值浮到最后面
        {
            if (r[j + 1] < r[j])//交换 将大数放在后面 
            {
                int t = r[j + 1];
                r[j + 1] = r[j];
                r[j] = t;
            }
        }
    }
}
~~~

### [合并区间](https://leetcode-cn.com/problems/merge-intervals/)

#### 我的题解

~~~cpp
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        //先对vector进行排序
        sort(intervals.begin(), intervals.end());
        int n = intervals.size();
        vector<vector<int>> merged;
        int flag = 0;
        int max_ = 0;
        int min_ = 0;
        for(int i = 0; i < n;){
            max_ = intervals[i][1];
            min_ = intervals[i][0];
            int j = i + 1;
            while(j < n && intervals[j][0] <= max_){
                max_ = max(intervals[j][1], max_);
                j++;
            }
            merged.push_back({min_, max_});
            i = j;
        }
        return merged;
    }
};
~~~

## 二分查找

### 基础模板及讲解

#### 4种基本情况

##### 查找第一个值等于给定值的元素

~~~cpp
// 查找第一个值等于给定值的元素
int firstEquals(vector<int>arr, int target) {
    int l = 0, r = arr.size() - 1;
    while (l < r) {
        int mid = l + ((r - l) >> 1);
        if (arr[mid] < target) l = mid + 1;
        else r = mid; // 收缩右边界不影响 first equals
    }
    if (arr[l] == target && (l == 0 || arr[l - 1] < target)) return l;
    return -1;
}
~~~

##### 查找最后一个值等于给定值的元素

~~~cpp
// 查找最后一个值等于给定值的元素
int lastEquals(vector<int>arr, int target) {
    int l = 0, r = arr.size() - 1;
    while (l < r) {
        int mid = l + ((r - l + 1) >> 1);
        if (arr[mid] > target) r = mid - 1;
        else l = mid; // 收缩左边界不影响 last equals
    }
    if (arr[l] == target && (l == arr.size() - 1 || arr[l + 1] > target)) return l;
    return -1;
}
~~~

##### 查找第一个大于等于给定值的元素

~~~cpp
// 查找第一个大于等于给定值的元素
int firstLargeOrEquals(vector<int>arr, int target) {
    int l = 0, r = arr.size() - 1;
    while (l < r) {
        int mid = l + ((r - l) >> 1);
        if (arr[mid] < target) l = mid + 1;
        else r = mid; // 收缩右边界不影响 first equals
    }
    if (arr[l] >= target && (l == 0 || arr[l - 1] < target)) return l; // >=
    return -1;
}
~~~

##### 查找最后一个小于等于给定值的元素

~~~cpp
// 查找最后一个小于等于给定值的元素
int lastLessOrEquals(vector<int>arr, int target) {
    int l = 0, r = arr.size() - 1;
    while (l < r) {
        int mid = l + ((r - l + 1) >> 1);
        if (arr[mid] > target) r = mid - 1;
        else l = mid; // 收缩左边界不影响 last equals
    }
    if (arr[l] <= target && (l == arr.size() - 1 || arr[l + 1] > target)) return l; // <=
    return -1;
}
~~~

##### 具体说明

~~~
从一个元素什么时候不是解开始考虑下一轮搜索区间是什么 
	一部分肯定不存在解 另一部分可能存在解

while(left <= right) 这种写法表示在循环体内部直接查找元素；
退出循环的时候 left 和 right 不重合，区间 [left, right] 是空区间。

while(left < right) 这种写法表示在循环体内部排除元素；
退出循环的时候 left 和 right 重合，区间 [left, right] 只剩下成 1个元素
这个元素 有可能 就是我们要找的元素。
vector<int> vec1{ 1,1,1,1,1,2,3,4,5,6,7 };
int l = 0;
int r = vec1.size() - 1;
int mid = l + ((r - l) >> 1);//向下取整 奇数个元素 向下向上取整都一样
int mid = l + ((r - l + 1) >> 1); //向上取整
//偶数个元素 向下取整和向上取整不一样
//向上向下取整的区别在于 在剩余两个元素的时候 此时l = x; r = x + 1; 那么l+r/2向下取整是x 向上取整是x+1
//此时收缩边界的判断条件中 若mid被分到左半部分，说明right = mid 需要向下取整 下次l = mid+1 = r 跳出循环
//若mid被分到右半部分， 说明l = mid, 需要向上取整，下次r = mid - 1 = l 跳出循环 
//上述情况都是可以缩小区间 进而可以跳出循环 否则一旦进入的分支不能缩小区间 会陷入死循环
//cout << mid << endl;
cout << firstLargeOrEquals(vec1,9) << endl;
    

~~~

#### 模板1

~~~cpp
int binarySearch(vector<int>& nums, int target){
  if(nums.size() == 0)
    return -1;

  int left = 0, right = nums.size() - 1;
  while(left <= right){
    // Prevent (left + right) overflow
    int mid = left + (right - left) / 2;
    if(nums[mid] == target){ return mid; }
    else if(nums[mid] < target) { left = mid + 1; }
    else { right = mid - 1; }
  }

  // End Condition: left > right
  return -1;
}
~~~

##### 关键属性

- 二分查找的最基础和最基本的形式。
- 查找条件可以在不与元素的两侧进行比较的情况下确定（或使用它周围的特定元素）。
- 不需要后处理，因为每一步中，你都在检查是否找到了元素。如果到达末尾，则知道未找到该元素。

##### 区分语法

- 初始条件：`left = 0, right = length-1`
- 终止：`left > right`
- 向左查找：`right = mid-1`
- 向右查找：`left = mid+1`

#### 模板2

~~~cpp
int binarySearch(vector<int>& nums, int target){
  if(nums.size() == 0)
    return -1;

  int left = 0, right = nums.size();
  while(left < right){
    // Prevent (left + right) overflow
    int mid = left + (right - left) / 2;
    if(nums[mid] == target){ return mid; }
    else if(nums[mid] < target) { left = mid + 1; }
    else { right = mid; }
  }

  // Post-processing:
  // End Condition: left == right
  if(left != nums.size() && nums[left] == target) return left;
  return -1;
}
~~~

##### 关键属性

- 一种实现二分查找的高级方法。
- 查找条件需要访问元素的直接右邻居。
- 使用元素的右邻居来确定是否满足条件，并决定是向左还是向右。
- 保证查找空间在每一步中至少有 2 个元素。
- 需要进行后处理。 当你剩下 1 个元素时，循环 / 递归结束。 需要评估剩余元素是否符合条件。

##### 区分语法

- 初始条件：`left = 0, right = length`
- 终止：`left == right`
- 向左查找：`right = mid`
- 向右查找：`left = mid+1`

#### 模板3

~~~cpp
int binarySearch(vector<int>& nums, int target){
    if (nums.size() == 0)
        return -1;

    int left = 0, right = nums.size() - 1;
    while (left + 1 < right){
        // Prevent (left + right) overflow
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            left = mid;
        } else {
            right = mid;
        }
    }

    // Post-processing:
    // End Condition: left + 1 == right
    if(nums[left] == target) return left;
    if(nums[right] == target) return right;
    return -1;
}
~~~

##### 关键属性

- 实现二分查找的另一种方法。
- 搜索条件需要访问元素的直接左右邻居。
- 使用元素的邻居来确定它是向右还是向左。
- 保证查找空间在每个步骤中至少有 3 个元素。
- 需要进行后处理。 当剩下 2 个元素时，循环 / 递归结束。 需要评估其余元素是否符合条件。

##### 区分语法

- 初始条件：`left = 0, right = length-1`
- 终止：`left + 1 == right`
- 向左查找：`right = mid`
- 向右查找：`left = mid`

### [x 的平方根](https://leetcode-cn.com/problems/sqrtx/)

#### 我的题解

~~~cpp
class Solution {
public:
    int mySqrt(int x) {
        int l = 0;
        int r = x;
        int ans = 0;
        while(l <= r){
            int mid = l + (r - l) / 2;
            if((long long)mid * mid <= x){
                l = mid + 1;
                ans = mid;
            }else{
                r = mid - 1;
            }
        }
        return ans;
    }
};
~~~

### [搜索插入位置(简单)](https://leetcode-cn.com/problems/search-insert-position/)

#### 我的题解

~~~cpp
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int n = nums.size();
        int l = 0;
        int r = n; //因为插入的元素位置可能是最后一个元素之后 因此r可以初始化为n
//         int ans = n;
//         while(l <= r){
//             int mid = l + ((r - l)>> 1);
//             if(nums[mid] < target){
//                 l = mid + 1;
//             }
//             else{
//                 r = mid - 1;
//                 ans = mid;
//             }
//         }
        
//         return ans;
        //模板解题
        while(l < r){
            int mid = l + (r - l) / 2;
            if(nums[mid] < target)
                l = mid + 1;
            else
                r = mid;
        }
        
        return l;
    }
};
~~~



### [搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

> 无重复数字

#### 我的题解

~~~cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int n = nums.size();
        int l = 0;
        int r = n - 1;
        while(l <= r){
            int mid = l + (r - l) / 2;
            if(nums[mid] == target) return mid;
            if(nums[mid] >= nums[0]){ //nums[0]是分界点
                if(nums[mid] > target && target >= nums[0])
                    r = mid - 1;
                else
                    l = mid + 1;
            }
            else{
                if(nums[mid] < target && target <= nums[n - 1])
                    l = mid + 1;
                else
                    r = mid - 1;
            }
            //两种方法都可 前一种好理解
            /*
             if(nums[mid] >= nums[l]){ //nums[0]是分界点
                if(nums[mid] > target && target >= nums[l])
                    r = mid - 1;
                else
                    l = mid + 1;
            }
            else{
                if(nums[mid] < target && target <= nums[r])
                    l = mid + 1;
                else
                    r = mid - 1;
            }
            */
        }
        
        return -1;
    }
};
~~~

### [搜索旋转排序数组 II](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/)

> 有重复数字

#### 我的题解

~~~cpp
class Solution {
public:
    bool search(vector<int>& nums, int target) {
        int n = nums.size();
        int l = 0;
        int r = n - 1;

        if(!n) return false;
        int l0 = 0;
        int r0 = n - 1;
        
        while(l <= r){
            int mid = l + (r - l) / 2;
            if(nums[mid] == target) return true;
            if(nums[l] != nums[r]){
                if(nums[mid] >= nums[l0]){
                    if(target >= nums[0] && target < nums[mid])
                        r = mid - 1;
                    else
                        l = mid + 1;
                }
                else {
                    if(target > nums[mid] && target <= nums[r0])
                        l = mid + 1;
                    else
                        r = mid - 1;
                }
            }
            else{//nums[l] == nums[r]
                if(nums[l] == target)
                    return true;
                //将左右端相同的数字剔除 问题就会转化成不含重复数字
                //或者剔除一端的数字也可
                l0 = ++l;
                r0 = --r;
            }
        }
        return false;
    }
};
/*
public class Solution {

    public boolean search(int[] nums, int target) {
        int len = nums.length;
        if (len == 0) {
            return false;
        }

        int left = 0;
        int right = len - 1;

        while (left < right) {
            int mid = (left + right) >>> 1;
            if (nums[mid] > nums[left]) {
                if (nums[left] <= target && target <= nums[mid]) {
                    // 落在前有序数组里
                    right = mid;
                } else {
                    left = mid + 1;
                }
            } else if (nums[mid] < nums[left]) {
                // 让分支和上面分支一样
                if (nums[mid] < target && target <= nums[right]) {
                    left = mid + 1;
                } else {
                    right = mid;
                }
            } else {
                // 要排除掉左边界之前，先看一看左边界可以不可以排除
                if (nums[left] == target) {
                    return true;
                } else {
                    left = left + 1;
                }
            }

        }
        // 后处理，夹逼以后，还要判断一下，是不是 target
        return nums[left] == target;
    }
}

作者：liweiwei1419
链接：https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/solution/er-fen-cha-zhao-by-liweiwei1419/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
*/
~~~

### [寻找旋转排序数组中的最小值](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)

> 无重复数字

#### 我的题解

~~~cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        int n = nums.size();
        int l = 0;
        int r = n - 1;
        
        while(l < r){
            int mid = l + (r - l) / 2;
            if(nums[mid] >= nums[0] && nums[mid] > nums[n - 1]){
                //递增序列需要考虑nums[n-1]
                l = mid + 1;
            }//mid处必定不是最小值
            else{
                r = mid;//mid比0处值要小||mid处必n-1处要小 mid有可能是最小值 因此需要包含mid
            }
        }
        //l==r退出循环 此时l指向最小值
        return nums[l];
    }
};
~~~

### [寻找旋转排序数组中的最小值 II](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)

> 有重复数字

#### 我的题解

~~~cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        int n = nums.size();
        int l = 0;
        int r = n - 1;
        int l0 = l;
        
        //其实l0可以替换为l
        //n-1可以替换为r
        while(l < r){
            int mid = l + (r - l) / 2;
            if(nums[l] != nums[r]){
                //该情况是必定旋转的情况 最小值一定在nums[l0]之后 故中间值>等于左边值的时候向右收缩区间
                if(nums[mid] >= nums[l0] && nums[mid] > nums[n - 1]){//中间值严格大于右边值才能保证旋转
                    l = mid + 1;
                }
                else{//中间值小于右边值 或者中间值小于左边值 该中间值可能是最小值
                    r = mid;
                }
            }
            else{
                l0 = ++l;
            }
        }
        
        return nums[l];
    }
};
~~~

### [ 在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

#### 我的题解

~~~cpp
class Solution {
public:
    //在寻找左右边界的时候 需要注意 因为mid有可能成为target的左右边界
    //所以在左右部分寻找时需要考虑左右边界处的值
    //l + 1 < r 保证在每个步骤中有至少三个元素 l+1 == r 时还有2个元素  退出循环
    int getLeftBound(vector<int>& nums, int l, int r, int target){
        while(l + 1 < r){
            int mid = l + (r - l) / 2;
            if(target <= nums[mid])//找到左边界
                r = mid;
            else 
                l = mid;
        }
        int left_bound = -1;
        if(nums[r] == target) 
            left_bound = r;
        if(nums[l] == target) 
            left_bound = l;
        //这两个if不可以调换顺序 因为l r处都有可能是等于target 所以左边界最后的值肯定是l
        //若l r处只有一个位target 那么这两个的顺序是无所谓的
        
        return left_bound;
    }
    
    int getRightBound(vector<int>& nums, int l, int r, int target){
         while(l + 1 < r){
            int mid = l + (r - l) / 2;
            if(target >= nums[mid])//找到you边界
                l = mid;
            else 
                r = mid;
        }
        
        int right_bound = -1;
        if(nums[l] == target) 
            right_bound = l;
        if(nums[r] == target) 
            right_bound = r;
        //这两个if不可以调换顺序 因为l r处都有可能是等于target 所以右边界最后的值肯定是r
        //若l r处只有一个位target 那么这两个的顺序是无所谓的
        
        return right_bound;
    }
    vector<int> searchRange(vector<int>& nums, int target) {
        int n = nums.size();            
        if(n == 0)
            return {-1, -1};
        int l = 0;
        int r = n - 1;
        
        int lb = getLeftBound(nums, l, r, target);
        int rb = getRightBound(nums, l, r, target);
        
        return {lb, rb};
    }
 
};
~~~

### [山脉数组的峰顶索引](https://leetcode-cn.com/problems/peak-index-in-a-mountain-array/)

#### 我的题解

~~~cpp
class Solution {
public:
    //TC-O(n)
    int findPeakElement(vector<int>& nums) {
        int n = nums.size();
        // for(int i = 0; i + 1 < n; i++){
        //     if(nums[i] > nums[i + 1])
        //         return i;//元素降序 或者 先升序后降序
        // }
        // return n - 1;//元素升序排列
        
        return search(0, n - 1, nums);
    }
    //TC-O(logn)
    int search(int l, int r, vector<int>& nums){
        if(l == r)
            return l;
        int mid = l + (r - l) / 2;
        if(nums[mid] > nums[mid + 1])//处在下降序列中 峰值在mid及左边 所以在左半段中搜索
            return search(l, mid, nums);
        return search(mid + 1, r, nums);//处在上升序列中 峰值必定在mid右边 所以在右半段中搜索
    }
};
~~~

### [山脉数组中查找目标值](https://leetcode-cn.com/problems/find-in-mountain-array/)

#### 我的题解

> 结合峰值索引求解  还有匿名函数的使用

~~~cpp
/**
 * // This is the MountainArray's API interface.
 * // You should not implement it, or speculate about its implementation
 * class MountainArray {
 *   public:
 *     int get(int index);
 *     int length();
 * };
 */

class Solution {
public:
    int bsearch(int l, int r,  MountainArray& nums, int target, int key(int)){
        target = key(target);
        while (l <= r) {
            int mid = l + (r - l) / 2;
            int mm = key(nums.get(mid));
            if (mm == target) {
                return mid;
            }
            else if (mm > target)
                r = mid - 1;
            else
                l = mid + 1;
        }
        return -1;
    }
    //貌似只有一个峰就能得到正确答案 多个峰并不行
    int findInMountainArray(int target, MountainArray& mountainArr) {
        int n = mountainArr.length();
        int l = 0;
        int r = n - 1;

        int peakIdx = binarySearch(l, r, mountainArr);//找到峰值索引
        cout << peakIdx<<endl;
        //在峰值左边找目标值 二分查找 升序序列
        l = 0;
        r = peakIdx;
        //匿名函数  lambda表达式应用
        int idx = bsearch(l, r, mountainArr, target, [](int x) -> int{return x;});
        if(idx != -1)
            return idx;
        
        //在峰值右边找目标值 降序序列 数组值取负数 然后target也取负数 这样就和升序序列二分查找一样了
        l = peakIdx + 1;
        r = n - 1;
        return bsearch(l, r, mountainArr, target, [](int x) -> int{return -x;});
    }

    int binarySearch(int l, int r, MountainArray& nums) {
        if (l == r)
            return l;
        int mid = l + (r - l) / 2;
        int mm = nums.get(mid);
        if (mm > nums.get(mid + 1))//处在下降序列中 峰值在mid及左边 所以在左半段中搜索
            return binarySearch(l, mid, nums);
        return binarySearch(mid + 1, r, nums);//处在上升序列中 峰值必定在mid右边 所以在右半段中搜索
    }
};
~~~

### [找到 K 个最接近的元素(困难)](https://leetcode-cn.com/problems/find-k-closest-elements/)

#### 我的题解([refer](https://leetcode-cn.com/problems/find-k-closest-elements/solution/pai-chu-fa-shuang-zhi-zhen-er-fen-fa-python-dai-ma/))

> $TC-O(\log n)$

~~~cpp
class Solution {
public:
    vector<int> findClosestElements(vector<int>& arr, int k, int x) {
        int n = arr.size();
        int l = 0;
        int r = n - k;
        
        while(l < r){
            //int mid = (l + r) / 2;
            int mid = l + (r - l) / 2;
            cout << mid << endl;
            //[mid, mid+k]共k+1个数字
            if(x - arr[mid] > arr[mid + k] - x)
                l = mid + 1;
            else//如果相等 删除右边界处数值
                r = mid;
        }
        
        return vector<int>(arr.begin() + l, arr.begin() + l + k);
    }
};
~~~

### [寻找两个正序数组的中位数(困难)](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

#### 我的题解([refer](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xun-zhao-liang-ge-you-xu-shu-zu-de-zhong-wei-s-114/))

> $TC-O(\log(m+n))$
>

~~~cpp
class Solution {
public:
   /* 主要思路：要找到第 k (k>1) 小的元素，那么就取 pivot1 = nums1[k/2-1] 和 pivot2 = nums2[k/2-1] 进行比较
    * 这里的 "/" 表示整除
    * nums1 中小于等于 pivot1 的元素有 nums1[0 .. k/2-2] 共计 k/2-1 个
    * nums2 中小于等于 pivot2 的元素有 nums2[0 .. k/2-2] 共计 k/2-1 个
    * 取 pivot = min(pivot1, pivot2)，两个数组中小于等于 pivot 的元素共计不会超过 (k/2-1) + (k/2-1) <= k-2 个
    * 这样 pivot 本身最大也只能是第 k-1 小的元素
    * 如果 pivot = pivot1，那么 nums1[0 .. k/2-1] 都不可能是第 k 小的元素。把这些元素全部 "删除"，剩下的作为新的 nums1 数组
    * 如果 pivot = pivot2，那么 nums2[0 .. k/2-1] 都不可能是第 k 小的元素。把这些元素全部 "删除"，剩下的作为新的 nums2 数组
    * 由于我们 "删除" 了一些元素（这些元素都比第 k 小的元素要小），因此需要修改 k 的值，减去删除的数的个数
    */
    int getKthElement(vector<int>& nums1, vector<int> nums2, int k){
        int m = nums1.size();
        int n = nums2.size();
        int idx1 = 0;
        int idx2 = 0;
        
        while(true){
            if(idx1 == m)
                return nums2[idx2 + k - 1];
            if(idx2 == n)
                return nums1[idx1 + k - 1];
            if(k == 1)
                return min(nums1[idx1], nums2[idx2]);
            
            int idx11 = min(idx1 + k / 2 - 1, m - 1);
            int idx22 = min(idx2 + k / 2 - 1, n - 1);
            int pivot1 = nums1[idx11];
            int pivot2 = nums2[idx22];
            if(pivot1 <= pivot2){
                k -= idx11 - idx1 + 1;
                idx1 = idx11 + 1;
            }
            else{
                k -= idx22 - idx2 + 1;
                idx2 = idx22 + 1;
            }
        }
    }
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int m = nums1.size();
        int n = nums2.size();
        int len = m + n;
        if(len & 1){//奇数
            return getKthElement(nums1,nums2, (len + 1)/2);
        }
        else{
            return (getKthElement(nums1,nums2, len/2) + getKthElement(nums1,nums2, len/2 + 1)) / 2.0;
        }
    }
};
~~~
