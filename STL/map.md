- 初始化
std::map<key_type, value_type> myMap;
- 插入
>方法一
>myMap[key] = value;

>方法二
>myMap.insert( {key,value} );

- 遍历

for (std::map<key_type, value_type>::iterator it = myMap.begin(); it != myMap.end(); ++it) {
    std::cout << it->first << " => " << it->second << std::endl;
}
for (auto &p : m) {
    std::cout << p.first << " : " << p.second << std::endl;
}
if (myMap.find(key) != myMap.end()) {
    // 键存在
}
myMap.erase(key);
myMap.clear();
size_t size = myMap.size();
myMap.empty();      // 是否为空
myMap.count("Bob"); // key 是否存在（返回 0 或 1）
std::map<int, std::string, std::greater<int>> m;  // 降序
f