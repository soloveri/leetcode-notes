---
title: Morris
mathjax: true
data: 2020-03-26 00:12:47
updated:
tags:
- morris
categories:
- algorithm
---
Morris遍历，应该就是线索二叉树的遍历。
cur为空时，则遍历停止
如果一个节点有左子树，那么一定会来到该节点两次，第一次是第一次遍历，第二次是通过线索来到。

如果一个节点有左子树，则找到**左子树的最右节点**并且为null，将其指向当前节点，并向左移动。
如果左子树最右节点指向当前节点，则将其置为空，并向右移动
如果一个节点没有左子树，则向右移动

``` c++ "Morris"
void Morris(TreeNode* head){
    if(head==nullptr){
        return;
    }
    TreeNode* cur{head};
    TreeNode* most_right{nullptr};
    while(cur!=nullptr){
        //来到左节点
        most_right=cur->left;
        if(most_right!=nullptr){
            //有左子树，则找到最右节点，记得一定是左子树，most_right->right!=cur是为了防止跳出左子树进入右子树
            while(most_right->right!=nullptr && most_right->right!=cur){
                most_right=most_right->right;
            }
            //第一次来到该节点
            if(most_right->right == nullptr){
                most_right->right=cur;
                cur=cur->left;
                //向左移动，直接进入下一次循环
                continue;
            }
            else{
                //第二次来到该节点，将most_right置空，然后向右移动
                most_right->right=nullptr;
                //cur=cur->right;
            }

        }
        //向右移动
        cur=cur->right;
    }
}

```

没有左子树的点都只会遍历一次，有左子树的点都会遍历两次，但是增加的遍历次数是常数级的，并且没有重复的。所以总的来说总的复杂度仍是`O(N)`,空间为`O(1)`

### Morris先序遍历

对于能够到达两次的节点，在第一次遇见时进行打印，对于只能够到达一次的节点，则直接打印。

``` c++ "Morris"
void MorrisPreOrder(TreeNode* head){
    if(head==nullptr){
        return;
    }
    TreeNode* cur{head};
    TreeNode* most_right{nullptr};
    while(cur!=nullptr){
        //来到左节点
        most_right=cur->left;
        if(most_right!=nullptr){
            //进入到这个分支，就说明能来到该节点两次

            //有左子树，则找到最右节点，记得一定是左子树，most_right->right!=cur是为了防止跳出左子树进入右子树
            while(most_right->right!=nullptr && most_right->right!=cur){
                most_right=most_right->right;
            }
            //第一次来到该节点
            if(most_right->right == nullptr){
                most_right->right=cur;
                cur=cur->left;
                cout<<cur->val<<endl;
                //向左移动，直接进入下一次循环
                continue;
            }
            else{
                //第二次来到该节点，将most_right置空，然后向右移动
                most_right->right=nullptr;
                //cur=cur->right;
            }

        }
        //执行到这，要么第二次来到，要么第一次来到，如果不加else分支，第二次来到cur时也会打印

        //能进入这个分支，说明只能来到cur一次
        else{
            cout<<cur->val<<endl;
        }
        //向右移动
        cur=cur->right;
    }
}

```

中序遍历时对于能来到自己两次的节点，第二次打印，对于只能到达自己一次的节点，直接打印

``` c++ "Morris"
void MorrisPreOrder(TreeNode* head){
    if(head==nullptr){
        return;
    }
    TreeNode* cur{head};
    TreeNode* most_right{nullptr};
    while(cur!=nullptr){
        //来到左节点
        most_right=cur->left;
        if(most_right!=nullptr){
            //有左子树，则找到最右节点，记得一定是左子树，most_right->right!=cur是为了防止跳出左子树进入右子树
            while(most_right->right!=nullptr && most_right->right!=cur){
                most_right=most_right->right;
            }
            //第一次来到该节点
            if(most_right->right == nullptr){
                most_right->right=cur;
                cur=cur->left;
                //向左移动，直接进入下一次循环
                continue;
            }
            else{
                //第二次来到该节点，将most_right置空，然后向右移动
                most_right->right=nullptr;
                //cur=cur->right;
            }

        }
        //执行到这，要么第二次来到，要么第一次来到，如果不加else分支，第二次来到cur时也会打印
        cout<<cur->val<<endl;

        //向右移动
        cur=cur->right;
    }
}

```

后序遍历：对于只能来到自己一次的节点，什么也不做。对于能来到自己两次的节点，当第二次来到时，**逆序打印** 左子树 的右边界节点。最后**逆序**打印整棵树的右边界(即包含头节点的那一斜线右边界)。

那么如何逆序打印右边界？
逆序就相当于把右边界做链表反转，然后打印链表，然后再反转回来即可。


``` c++ "Morris Post"
void MorrisPostOrder(TreeNode* head){
    if(head==nullptr){
        return;
    }
    TreeNode* cur{head};
    TreeNode* most_right{nullptr};
    while(cur!=nullptr){
        //来到左节点
        most_right=cur->left;
        if(most_right!=nullptr){
            //有左子树，则找到最右节点，记得一定是左子树，most_right->right!=cur是为了防止跳出左子树进入右子树
            while(most_right->right != nullptr && most_right->right!=cur){
                most_right=most_right->right;
            }
            //第一次来到该节点
            if(most_right->right == nullptr){
                most_right->right=cur;
                cur=cur->left;
                //向左移动，直接进入下一次循环
                continue;
            }
            else{
                //第二次来到该节点，将most_right置空，然后向右移动
                most_right->right=nullptr;
                //逆序打印该节点的左数右边界
                PrintEdge(cur->left);
                //cur=cur->right;
            }

        }
        //执行到这，要么第二次来到，要么第一次来到，如果不加else分支，第二次来到cur时也会打印
        

        //向右移动
        cur=cur->right;
    }
    PrintEdge(head);
}

//逆序打印以head为头的右边界
void PrintEdge(TreeNode* head){
    TreeNode* pre{nullptr},* next{nullptr};
    while(head!=nullptr){
        next=head->right;
        head->right=pre;
        pre=head;
        head=next;
    }
    TreeNode* tail{pre};
    head=pre;
    pre=nullptr;
    while(head!=nullptr){
        cout<<head->val;
        head=head->right;
    }
    //再次反转链表
    while(tail!=nullptr){
        next=tail->right;
        tail->right=pre;
        pre=tail;
        tail=next;
    }
}
```

## 什么时候用递归套路什么时候用Morris?

如果需要同时获得左右子树的信息并重新整合时只能使用递归套路。否则就可以使用Morris遍历完成相关的遍历题目。