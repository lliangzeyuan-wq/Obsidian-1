---
data: 2026-02-03
---
### 解题思路
- 中序遍历是LVR，你要求倒数的第K个节点，因此我们RVL来遍历，求第K个节点

### 代码
```cpp
public:
	//求中序倒数第k个节点
	int getVal(int k) {
		Node* node = getVal(root_, k);
		if (node == NULL) {
			string err = "no No.1";
			err += k;
			throw err;
		}
		else {
			return node->data_;
		}
	}
private:
	//求中序倒数第k个节点的实现
	int i = 1; 
	Node* getVal(Node* node, int k) {
		if (node == NULL) {
			return NULL;
		}
		Node* right = getVal(node->right_, k);//R
		if (right != NULL) {
			return right;
		}
		//V
		if (i++ == k)//在RVL的顺序下，找到正数第k个元素
		{
			return node;
		}
		return getVal(node->left_, k);//L
	}
```