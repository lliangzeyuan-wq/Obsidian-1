- 初始化
std::map<key_type, value_type> myMap;
- 插入
>方法一
>myMap[key] = value;

>方法二
>myMap.insert( {key,value} );

- 遍历
>方法一
for (std::map<key_type, value_type>::iterator it = myMap.begin(); it != myMap.end(); ++it) {
    std::cout << it->first << " => " << it->second << std::endl;
}

>方法二
for (auto &p : m) {
    std::cout << p.first << " : " << p.second << std::endl;
}

- find
if (myMap.find(key) != myMap.end()) {
    // 键存在
}
- 删除
myMap.erase(key);
- 清空
myMap.clear();
- 求大小
size_t size = myMap.size();
- 判断为空
myMap.empty();      // 是否为空
- 是否存在
myMap.count("Bob"); // key 是否存在（返回 0 或 1）
- 默认为升序，手动设置为降序
std::map<int, std::string, std::greater<int>> m;  // 降序
f