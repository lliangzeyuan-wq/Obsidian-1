---
data: 2026-02-07
---
### 一般的删除代码
```cpp
public:
	void remove(const T& val) {
		root_ = remove(root_, val);
	}
private:
	Node* remove(Node* node, const T& val) {
		if (node == nullptr) {
			return nullptr;
		}
		if (node->data_ > val) {
			node->left_ = remove(node->left_, val);
		}
		else if (node->data_ < val) {
			node->right_ = remove(node->right_, val);
		}
		//找到了
		else {
			//先处理有两个孩子的节点删除情况
			if (node->left_ != nullptr && node->right_ != nullptr) {
				//删前驱
				Node* pre = node->left_;
				while (pre->right_ != nullptr) {
					pre = pre->right_;
				}
				node->data_ = pre->data_;
				node->left_ = remove(node->left_, pre->data_);
			}
			//删除节点最多有一个孩子
			else {
				if (node->left_ != nullptr) {
					Node* left = node->left_;
					delete node;
					return left;
				}
				else if (node->right_ != NULL) {
					Node* right = node->right_;
					delete node;
					return right;
				}
				else {
					return nullptr;
				}
			}
		}
		
		//更新节点高度
		node->height_ = max(height(node->left_), height(node->right_)) + 1;
		return node;//递归回溯过程中，把当前节点给父节点返回
	}
```