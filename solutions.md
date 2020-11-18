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

### [柱状图中最大的矩形](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)

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

### [最大矩形](https://leetcode-cn.com/problems/maximal-rectangle/)

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

### [接雨水](https://leetcode-cn.com/problems/trapping-rain-water/)

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

### [买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

#### 我的题解

> 只能完成一笔交易

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

### [买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

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

### [买卖股票的最佳时机 III(困难)](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/)

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

### [字符串的排列(哈希+滑动窗口)](https://leetcode-cn.com/problems/permutation-in-string/)

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

### [数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)

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

