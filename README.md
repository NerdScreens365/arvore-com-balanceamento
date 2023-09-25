#include <iostream>
using namespace std;

struct TreeNode {
    int key;
    TreeNode* left;
    TreeNode* right;
    int height;
    TreeNode(int k) : key(k), left(nullptr), right(nullptr), height(1) {}
};

class AVLTree {
public:
    TreeNode* insert(TreeNode* root, int key);
    TreeNode* remove(TreeNode* root, int key);
    int getBalance(TreeNode* root);
    int getHeight(TreeNode* root);
    TreeNode* rotateLeft(TreeNode* z);
    TreeNode* rotateRight(TreeNode* y);
    TreeNode* findMinNode(TreeNode* node);
    void inorderTraversal(TreeNode* root);
};

int max(int a, int b) {
    return (a > b) ? a : b;
}

int AVLTree::getHeight(TreeNode* root) {
    if (!root)
        return 0;
    return root->height;
}

int AVLTree::getBalance(TreeNode* root) {
    if (!root)
        return 0;
    return getHeight(root->left) - getHeight(root->right);
}

TreeNode* AVLTree::rotateLeft(TreeNode* z) {
    TreeNode* y = z->right;
    TreeNode* T2 = y->left;

    y->left = z;
    z->right = T2;

    z->height = max(getHeight(z->left), getHeight(z->right)) + 1;
    y->height = max(getHeight(y->left), getHeight(y->right)) + 1;

    return y;
}

TreeNode* AVLTree::rotateRight(TreeNode* y) {
    TreeNode* x = y->left;
    TreeNode* T2 = x->right;

    x->right = y;
    y->left = T2;

    y->height = max(getHeight(y->left), getHeight(y->right)) + 1;
    x->height = max(getHeight(x->left), getHeight(x->right)) + 1;

    return x;
}

TreeNode* AVLTree::insert(TreeNode* root, int key) {
    if (!root)
        return new TreeNode(key);

    if (key < root->key)
        root->left = insert(root->left, key);
    else if (key > root->key)
        root->right = insert(root->right, key);
    else
        return root; // Duplicatas não são permitidas na árvore AVL

    root->height = 1 + max(getHeight(root->left), getHeight(root->right));

    int balance = getBalance(root);

    if (balance > 1) {
        if (key < root->left->key)
            return rotateRight(root);
        else {
            root->left = rotateLeft(root->left);
            return rotateRight(root);
        }
    }

    if (balance < -1) {
        if (key > root->right->key)
            return rotateLeft(root);
        else {
            root->right = rotateRight(root->right);
            return rotateLeft(root);
        }
    }

    return root;
}

TreeNode* AVLTree::remove(TreeNode* root, int key) {
    if (!root)
        return root;

    if (key < root->key)
        root->left = remove(root->left, key);
    else if (key > root->key)
        root->right = remove(root->right, key);
    else {
        if (!root->left || !root->right) {
            TreeNode* temp = root->left ? root->left : root->right;
            if (!temp) {
                temp = root;
                root = nullptr;
            } else {
                *root = *temp;
            }
            delete temp;
        } else {
            TreeNode* temp = findMinNode(root->right);
            root->key = temp->key;
            root->right = remove(root->right, temp->key);
        }
    }

    if (!root)
        return root;

    root->height = 1 + max(getHeight(root->left), getHeight(root->right));

    int balance = getBalance(root);

    if (balance > 1) {
        if (getBalance(root->left) >= 0)
            return rotateRight(root);
        else {
            root->left = rotateLeft(root->left);
            return rotateRight(root);
        }
    }

    if (balance < -1) {
        if (getBalance(root->right) <= 0)
            return rotateLeft(root);
        else {
            root->right = rotateRight(root->right);
            return rotateLeft(root);
        }
    }

    return root;
}

TreeNode* AVLTree::findMinNode(TreeNode* node) {
    TreeNode* current = node;
    while (current->left)
        current = current->left;
    return current;
}

void AVLTree::inorderTraversal(TreeNode* root) {
    if (root) {
        inorderTraversal(root->left);
        cout << root->key << " ";
        inorderTraversal(root->right);
    }
}

int main() {
    AVLTree avlTree;
    TreeNode* root = nullptr;

    int keys[] = {10, 20, 30, 40, 50, 25};
    int n = sizeof(keys) / sizeof(keys[0]);

    for (int i = 0; i < n; i++) {
        root = avlTree.insert(root, keys[i]);
    }

    cout << "Árvore AVL após a inserção: ";
    avlTree.inorderTraversal(root);
    cout << endl;

    root = avlTree.remove(root, 30);
    cout << "Árvore AVL após a remoção de 30: ";
    avlTree.inorderTraversal(root);
    cout << endl;

    return 0;
}
