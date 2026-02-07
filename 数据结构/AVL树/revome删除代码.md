---
data: 2026-02-07
---
[4AVL树remove删除代码实现_ev_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1FT421k7LL?spm_id_from=333.788.videopod.episodes&vd_source=43c9de78f6e5f2b05790188e274ad943&p=122)
### 一般的删除代码(非AVL树的BSTree)
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

### AVLTree的删除代码
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
			//删除左子树，可能造成右子树太高
			if (height(node->right_) - height(node->left_) > 1) {
				//右孩子的右子树太高
				if (height(node->right_->right_) >= height(node->right_->left_)) {
					node = leftRotate(node);
				}
				//右孩子的左子树太高
				else {
					node = rightBalance(node);
				}
			}
		}
		else if (node->data_ < val) {
			node->right_ = remove(node->right_, val);
			//删除右子树，可能造成左子树太高
			if (height(node->left_) - height(node->right_) > 1) {
				//左孩子的左子树太高
				if (height(node->left_->left_) >= height(node->left_->right_)) {
					node = rightRotate(node);
				}
				//左孩子的右子树太高
				else {
					node = leftBalance(node);
				}
			}
		}
		//找到了
		else {
			//先处理有两个孩子的节点删除情况
			if (node->left_ != nullptr && node->right_ != nullptr) {
				//为了避免删除前驱或者后继节点造成节点失衡，谁高删除谁
				if (height(node->left_) >= height(node->right_)) {
					//删前驱
					Node* pre = node->left_;
					while (pre->right_ != nullptr) {
						pre = pre->right_;
					}
					node->data_ = pre->data_;
					node->left_ = remove(node->left_, pre->data_);
				}
				else {
					Node* post = node->right_;
					while (post->left_ != nullptr) {
						post = post->left_;
					}
					node->data_ = post->data_;
					node->right_ = remove(node->right_, post->data_);
				}
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