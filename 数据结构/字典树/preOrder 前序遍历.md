---
data: 2026-03-03
---
https://www.bilibili.com/video/BV1FT421k7LL?spm_id_from=333.788.player.switch&vd_source=a1738e65b6790850448f77a5620aaa98&p=185

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