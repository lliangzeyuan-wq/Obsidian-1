---
data: 2026-03-03
---
https://www.bilibili.com/video/BV1FT421k7LL?spm_id_from=333.788.player.switch&vd_source=a1738e65b6790850448f77a5620aaa98&p=185

- 每一个树的枝条都相当于一个单词，典型的空间换时间
- Trie树处理串的应用：有比较多的公共前缀，效率高，否则内存占用量比较大
- 结果的输出：按照字典序输出插入的单词,即实现了**串排序**
### 代码
```cpp
public:
	//前序遍历字典树
	void preOrder()
	{
		string word;
		vector<string>wordList;
		preOrder(root_, word, wordList);
		for (auto word : wordList)
		{
			cout << word << endl;
		}
		cout << endl;
	}
	
	
private:
	void preOrder(TrieNode* cur, string word, vector<string>& wordList)
	{
		//前序遍历 VLR
		if (cur != root_)//V
		{
			word.push_back(cur->ch_);
			if (cur->freqs_ > 0)
			{
				//已经遍历到一个有效的单词
				wordList.emplace_back(word);
			}
		}
		//递归处理孩子节点
		for (auto pair : cur->nodeMap_)
		{
			preOrder(pair.second, word, wordList);
		}
	}


```

### 完整代码
```cpp
#include<iostream>
#include<string>
#include<map>
#include<vector>
using namespace std;

//Trie字典树
class TrieTree
{
public:
	TrieTree()
	{
		root_ = new TrieNode('\0', 0);
	}

	//添加单词     O(m),m为word.size()
	void add(const string& word)
	{
		TrieNode* cur = root_;
		for (int i = 0; i < word.size(); ++i)
		{
			auto childIt = cur->nodeMap_.find(word[i]);
			if (childIt == cur->nodeMap_.end())
			{
				//相应字符的节点没有，创建他
				TrieNode* child = new TrieNode(word[i], 0);
				cur->nodeMap_.emplace(word[i], child);
				cur = child;
			}
			else
			{
				//相应的字符节点已经存在，移动cur指向对应的字符节点
				cur = childIt->second;
			}
		}    
		//cur指向了word单词的最后一个节点
		cur->freqs_++;
	}

	//查询单词       O(m),m为word.size()
	int query(const string& word)
	{
		TrieNode* cur = root_;
		for (int i = 0; i < word.size(); ++i)
		{
			auto childIt = cur->nodeMap_.find(word[i]);
			if (childIt == cur->nodeMap_.end())
			{
				return 0;
			}
			//移动cur指向下一个单词的字符节点上
			cur = childIt->second;
		}
		return cur->freqs_;  
	}

	//前序遍历字典树
	void preOrder()
	{
		string word;
		vector<string>wordList;
		preOrder(root_, word, wordList);
		for (auto word : wordList)
		{
			cout << word << endl;
		}
		cout << endl;
	}
private:
	struct TrieNode
	{
		TrieNode(char ch,int freqs)
			:ch_(ch)
			,freqs_(freqs)
		{ }
		//节点存储的字符数据
		char ch_;
		//单词的末尾字符存储单词的数量（频率）
		int freqs_;
		//存储孩子节点字符数据和节点指针的对应关系
		map<char, TrieNode*>nodeMap_;
	};
private:
	void preOrder(TrieNode* cur, string word, vector<string>& wordList)
	{
		//前序遍历 VLR
		if (cur != root_)//V
		{
			word.push_back(cur->ch_);
			if (cur->freqs_ > 0)
			{
				//已经遍历到一个有效的单词
				wordList.emplace_back(word);
			}
		}
		//递归处理孩子节点
		for (auto pair : cur->nodeMap_)
		{
			preOrder(pair.second, word, wordList);
		}
	}

private:
	TrieNode* root_;//根节点
};
int main()
{
	TrieTree trie;
	trie.add("hello");
	trie.add("hello");
	trie.add("hel");
	trie.add("hel");
	trie.add("hel");
	trie.add("china");
	trie.add("ch");
	trie.add("ch");
	trie.add("heword");
	trie.add("hellw");

	cout << trie.query("hel") << endl;

	cout << "====================" << endl;
	trie.preOrder();
	return 0;
}



```