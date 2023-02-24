# gzy02.github.io

### 在 vscode 中调试 LeetCode 二叉树类型题目

-   首先给出二叉树节点定义
-   利用 297 题序列化及反序列化二叉树的方法存储/转化二叉树
-   然后做对应的题，以 LeetCode 题目序号 897 为例：

```
#include <bits/stdc++.h>
using namespace std;
// 二叉树节点定义
struct TreeNode
{
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode() : val(0), left(nullptr), right(nullptr) {}
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
};
// 序列化和反序列化二叉树
class Codec
{
public:
    // Encodes a tree to a single string.
    static string serialize(TreeNode *root)
    {
        queue<TreeNode *> node_queue; // 层序遍历
        string ans;
        node_queue.push(root);
        while (!node_queue.empty())
        {
            TreeNode *cur_node = node_queue.front();
            node_queue.pop();
            if (cur_node)
            {
                ans += to_string(cur_node->val);
                ans.push_back(',');
                node_queue.push(cur_node->left);
                node_queue.push(cur_node->right);
            }
            else
                ans += "null,";
        }
        return ans;
    }

    // Decodes your encoded data to tree.
    static TreeNode *deserialize(string data)
    {
        data.push_back(','); // 给结尾补个逗号，方便处理
        string tep_string;
        TreeNode *root = nullptr;
        queue<TreeNode **> node_queue; // 层序遍历
        node_queue.push(&root);        // 利用指针的指针，去修改指针TreeNode*的值
        for (char ch : data)
        {
            if (ch == ',')
            {
                if (tep_string == "null")
                    *node_queue.front() = nullptr;
                else
                {
                    TreeNode *new_node = new TreeNode(stoi(tep_string));
                    *node_queue.front() = new_node;
                    node_queue.push(&(new_node->left));
                    node_queue.push(&(new_node->right));
                }
                node_queue.pop();
                tep_string.clear();
            }
            else
                tep_string.push_back(ch);
        }
        return root;
    }
};

void DestroyTree(TreeNode *root)
{
    if (root)
    {
        DestroyTree(root->left);
        DestroyTree(root->right);
        delete root;
    }
}

class Solution
{
    void InOrder(TreeNode *root, TreeNode *&prev)
    {
        if (root)
        {
            TreeNode *left = root->left, *right = root->right;
            InOrder(left, prev);
            prev->right = root;
            prev->left = nullptr;
            prev = root;
            InOrder(right, prev); // 下面的节点可能会修改上面节点的right
        }
    }

public:
    TreeNode *increasingBST(TreeNode *root)
    {
        TreeNode dummyNode, *prev = &dummyNode;
        InOrder(root, prev);
        prev->left = nullptr;
        // prev->right = nullptr;//最后一个节点可能有左孩子，但一定没右孩子
        return dummyNode.right; // 最初节点的右孩子
    }
};
int main()
{
    TreeNode *testTree = Codec::deserialize("2,1,4,null,null,3");

    {
        Solution sol;
        testTree = sol.increasingBST(testTree);
    }

    DestroyTree(testTree);
    return 0;
}
```

### 参考

-   https://leetcode.cn/problems/serialize-and-deserialize-binary-tree/
-   https://leetcode.cn/problems/increasing-order-search-tree/
