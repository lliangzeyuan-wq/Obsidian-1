---
data: 2026-02-06
---
[3AVL树insert插入代码实现_ev_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1FT421k7LL?spm_id_from=333.788.videopod.episodes&vd_source=43c9de78f6e5f2b05790188e274ad943&p=121)
### 非AVL树的一般BSTree的插入代码
```cpp
public:
	//AVL树的插入操作
	void insert(const T& val) {
		root_ = insert(root_, val);
	}
private:
	Node* insert(Node* node, const T& val) {
		if (node == NULL) {
			return new Node(val);
		}
		if (node->data_ == val) {
			;//找到相同元素了，不用再往下递归找位置插入了，已经有了
		}
		else if (node->data_ > val) {
			insert(node->left_, val);
		}
		else {
			insert(node->right_, val);
		}
		return node;
	}
```

### AVL树的insert插入代码实现
- 注释很详细，看注释
```cpp
	//AVL树的插入操作实现
	Node* insert(Node* node, const T& val) {
		if (node == NULL) {
			return new Node(val);
		}
		if (node->data_ == val) {
			;//找到相同元素了，不用再往下递归找位置插入了，已经有了
		}
		else if (node->data_ > val) {
			insert(node->left_, val);
			//添加1   在递归回溯时判断节点是否失衡
			if (height(node->left_) - height(node->right_) > 1) {//前提：一定是平衡树。往左插，则失衡的时候一定是左边高
				//因为是失衡的情况，因此node->left_一定不为空
				if (height(node->left_->left_) >= height(node->left_->right_)) {
					//左孩子的左子树太高
					node = rightRotate(node);
				}
				else {
					//节点失衡，由于左孩子的右子树太高
					node = leftBalance(node);
				}
			}
		}
		else {
			insert(node->right_, val);
			//添加2   在递归回溯时判断节点是否失衡
			if (height(node->right_) - height(node->left_) > 1) {//前提：一定是平衡树。往左插，则失衡的时候一定是左边高
				//因为是失衡的情况，因此node->right_一定不为空
				if (height(node->right_->right_) >= height(node->right_->left_)) {
					//右孩子的右子树太高
					node = leftRotate(node);
				}
				else {
					//节点失衡，由于右孩子的左子树太高
					node = rightBalance(node);
				}
			}
		}

		//添加3  更新节点高度
		node->height_ = max(height(node->left_), height(node->right_)) + 1;
		return node;
	}
```



### 过程可视化

- 插入1 2 3 4 5 6 7 8 9 10
#### 失衡1
![[Pasted image 20260206230318.png]]
#### 1
![[Pasted image 20260206230327.png]]

#### 失衡2
![[Pasted image 20260206230417.png]]
#### 2
![[Pasted image 20260206230440.png]]
####