---
data: 2026-02-06
---
[1AVL树的节点平衡旋转理论讲解_ev_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1FT421k7LL?spm_id_from=333.788.videopod.episodes&vd_source=43c9de78f6e5f2b05790188e274ad943&p=119)
AVL树    BST树+节点平衡操作（节点平衡：任意节点左右子树的高度差不超过1）
![[Pasted image 20260206111924.png]]
当BST树不是平衡树的时候，他的增删改查的时间复杂度会提升，因此要想办法把他变成平衡树 
### 理论
- 右旋转：左子树太高
![[Pasted image 20260206113259.png]]
- 左旋转：右子树太高
![[Pasted image 20260206113331.png]]
- 左平衡（先以根节点的左孩子为轴做左旋转，再以根节点为轴做右旋转）：左孩子的右子树太高了
![[Pasted image 20260206113657.png]]

- 右平衡（先以根节点的右孩子为轴做右旋转，再以根节点为轴做左旋转）右孩子的左子树太高了
![[Pasted image 20260206113745.png]]

### 代码
#### 定义AVL树
```cpp
private:
	//定义AVL树
	struct Node
	{
		Node(T data=T())
			:data_(data)
			,left_(NULL)
			,right(NULL)
			,height(1)
		{ }
		T data_;
		Node* left_;
		Node* right_;
		int height_;//记录节点的高度值
	};
	Node* root_;

```

#### 返回节点的高度值
- 当为NULL的时候，高度返回0
```cpp
private:
	//返回节点的高度值
	int height(Node* node)
	{
		return node == NULL ? 0 : node->height_;
	}
```
#### 右旋转
```cpp
private:
	//右旋转操作 以参数node为轴做右旋转操作，并把新的根节点返回
	Node* rightRotate(Node* node)
	{
		Node* child = node->left_;
		node->left_ = child->right_;
		child->right_ = node;
		//高度更新
		node->height_ = max(height(node->left_),height(node->right_) + 1;//node节点现在是child的右孩子，因此要先求
		child->height_ = max(height(child->left_), height(node->right_)) + 1;
		//返回旋转后的子树新的根节点
		return child;
	}
```

#### 左旋转
```cpp
private:
	//左旋转操作 以参数node为轴做左 旋转操作，并把新的根节点返回
	Node* leftRotate(Node* node)
	{
		Node* child = node->left_;
		node->right_ = child->left_;
		child->left_ = node;
		//高度更新
		node->height_ = max(height(node->left_), height(node->right_)) + 1;
		child->height_ = max(height(child->left_), height(node->right_)) + 1;
		//返回旋转后的子树新的根节点
		return child;
	}
```

#### 左平衡：左孩子的右子树太高了
```cpp
private:
	//左平衡操作 以参数node为轴做左-右旋转操作，并把新的根节点返回
	Node* leftBalance(Node* node)
	{
		node->left_ = leftRotate(node->left_);
		return rightRotate(node);
	}
```

#### 右平衡：右孩子的左子树太高了
```cpp
private:
	//右平衡操作 以参数node为轴做右-左旋转操作，并把新的根节点返回
	Node* rightBalance(Node* node)
	{
		node->right_ = rightRotate(node->left_);
		return leftRotate(node);
	}
```
