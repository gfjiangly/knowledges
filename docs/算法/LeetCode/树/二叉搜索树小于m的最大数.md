

```C++
int min_node(TreeNode* root) {
    if (root == NULL) return INT_MIN;
    while (root->left != NULL) root = root->left;
    return root->val;
}

int max_node(TreeNode* root) {
    if (root == NULL) return INT_MAX;
    while (root->right != NULL) root = root->right;
    return root->val;
}

int find_not_greater_m_max_node(TreeNode* root) {
    if (root == NULL) return INT_MIN;  // 未找到符合条件的值
    
    if (root->val < m) {
        if (root->right == NULL || min_node(root->right) >= m) return root->val;
        find_not_greater_m_max_node(root->right);
    }
    else {
        if (root->left == NULL) return INT_MIN;  // 未找到符合条件的值
        if (max_node(root->left) < m) return max_node(root->left);
        find_not_greater_m_max_node(root->left);
    }
}
```

