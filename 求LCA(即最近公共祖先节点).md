---
data: 2026-02-02
---
[5二叉树镜像翻转问题_ev_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1FT421k7LL?spm_id_from=333.788.player.switch&vd_source=43c9de78f6e5f2b05790188e274ad943&p=113)

### 代码实现
```
public:
	//最近公共祖先节点
	int getLCA(int val1, int val2) {
		Node* node = getLCA(root_, val1, val2);
		if (node == NULL) {
			throw "no LCA!";
		}
		else {
			return node->data_;
		}
	}

private:
	//最近公共祖先节点实现
	Node*getLCA(Node* node, int val1, int val2) {
		if (node == NULL) {
			return NULL;
		}
		if (comp_(node->data_, val1) && comp_(node->data_, val2)) {
			return getLCA(node->right_, val1, val2);
		}
		else if (comp_(val1,node->data_) && comp_(val2,node->data_)) {
			return getLCA(node->left_, val1, val2);
		}
		else {
			return node;
		}
	}
```