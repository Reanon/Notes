# Trie 树

Trie 树，也叫 「字典树」，是专门处理字符串匹配的数据结构，用来解决在一组字符串集合中快速查找某个字符串的问题。

Trie 树的本质，就是==利用字符串之间的公共前缀，将重复的前缀合并在一起==。

例如：在6 个字符串how，hi，her，hello，so，see中进行查找，可以生成 Trie树结构。

- 根节点不包含任何信息
- 每个节点表示一个字符串中的字符
- 从 根节点到 **红色节点** 的一条路径表示一个字符串

![image-20210210172334805](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210210172334.png)

Trie 树构造的分解过程：

- 构造过程的每一步，都相当于往 Trie 树中插入一个字符串
- 当所有字符串都插入完成之后，Trie 树就构造好了

![image-20210210182740128](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210210182740.png)

在 Trie 树中查找一个字符串的时候，比如查找字符串“her”，那将要查找的字符串分割成单个的字符 h，e，r，然后从 Trie 树的根节点开始匹配。

![image-20210210183111652](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210210183111.png)

## 实现 Trie 树

Trie 树主要有两个操作：

1. **将字符串集合构造成 Trie 树**：过程就是一个将字符串插入到 Trie 树的过程。
2. **在 Trie 树中查询一个字符串**

### 构造 Trie 树

借助散列表的思想，通过一个下标与字符一一映射的数组，来存储子节点的指针。

- 字符串中只有从 a 到 z 这 26 个小写字母，在数组中下标为 0 的位置，存储指向子节点 a 的指针，下标为 1 的位置存储指向子节点 b 的指针
- 以此类推，下标为 25 的位置，存储的是指向的子节点 z 的指针。
- 

![image-20210210183856253](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210210183856.png)

```java
class TrieNode {
  char data;
  TrieNode children[26];
}
```

### 查询一个字符串

在 Trie 树中查找字符串的时候，就可以通过字符的 ASCII 码减去 a 的 ASCII 码，迅速找到匹配的子节点的指针

- 比如，d 的 ASCII 码减去 a 的 ASCII 码就是 3，那子节点 d 的指针就存储在数组中下标为 3 的位置中

```java
public class Trie {
    private TrieNode root = new TrieNode('/'); // 存储无意义字符

    // 往 Trie 树中插入一个字符串
    public void insert(char[] text) {
        TrieNode p = root;
        for (int i = 0; i < text.length; ++i) {
            // 记录存储的下标
            int index = text[i] - 'a';
            if (p.children[index] == null) {
                TrieNode newNode = new TrieNode(text[i]);
                p.children[index] = newNode;
            }
            p = p.children[index];
        }
        p.isEndingChar = true;
    }

    // 在 Trie 树中查找一个字符串
    public boolean find(char[] pattern) {
        TrieNode p = root;
        for (int i = 0; i < pattern.length; ++i) {
            int index = pattern[i] - 'a';
            if (p.children[index] == null) {
                return false; // 不存在 pattern
            }
            p = p.children[index];
        }
        if (p.isEndingChar == false) return false; // 不能完全匹配，只是前缀
        else return true; // 找到 pattern
    }

    public class TrieNode {
        public char data;
        public TrieNode[] children = new TrieNode[26];
        public boolean isEndingChar = false;
        public TrieNode(char data) {
            this.data = data;
        }
    }
}
```

### 时间复杂度

n 表示所有字符串的长度和，构建 Trie 树的过程，需要扫描所有的字符串，时间复杂度是 ==O(n)==。

构建好 Trie 树后，在其中查找字符串的时间复杂度是 ==O(k)==，k 表示要查找的字符串的长度

## Trie 树 内存消耗

Trie 树的本质是避免重复存储一组字符串的相同前缀子串，但是现在每个字符（对应一个节点）的存储远远大于 1 个字节。

- 用数组来存储一个节点的子节点的指针：如果字符串中包含从 a 到 z 这 26 个字符，那每个节点都要存储一个长度为 26 的数组，并且每个数组存储一个 8 字节指针（或者是 4 字节，这个大小跟 CPU、操作系统、编译器等有关）
- 即便一个节点只有很少的子节点，远小于 26 个，比如 3、4 个，我们也要维护一个长度为 26 的数组。

不可否认，Trie 树尽管有可能很浪费内存，但是确实非常高效。

### Trie 树优化

Trie 树的变体有很多，都可以在一定程度上解决内存消耗的问题比如，**缩点优化**

- 对只有一个子节点的节点，而且此节点不是一个串的结束节点，可以将此节点与子节点合并。

![image-20210210184835463](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210210184835.png)

## Trie 树与散列表、红黑树

在一组字符串中查找字符串，Trie 树实际上表现得并不好。它对要处理的字符串有及其严苛的要求：

1. 字符串中包含的字符集不能太大：如果字符集太大，那存储空间可能就会浪费很多，即便可以优化，但也要付出牺牲查询、插入效率的代价
2. 要求字符串的前缀重合比较多，不然空间消耗会变大很多
3. 如果要用 Trie 树解决问题，那就要自己从零开始实现一个 Trie 树，还要保证没有 bug，这个在工程上是将简单问题复杂化，除非必须，一般不建议这样做
4. 通过指针串起来的数据块是不连续的，而 Trie 树中用到了指针，所以对缓存并不友好，性能上会打个折扣。

故此，针对在一组字符串中查找字符串的问题，在工程中**更倾向于用散列表或者红黑树**。因为这两种数据结构，都不需要自己去实现，直接利用编程语言中提供的现成类库就行了。

简而言之：

- Trie 树比较适合的是查找前缀匹配的字符串，而不适合精确匹配查找。
- 散列表或者红黑树更适合精确匹配查找

## Trie 树 与 关键词

> 利用 Trie 树实现搜索关键词的提示功能

关键词库由用户的热门搜索关键词组成，假设词库里只有 hello、her、hi、how、so、see 这 6 个关键词，将这个词库构建成一个 Trie 树。当用户输入其中某个单词的时候，把这个词作为一个前缀子串在 Trie 树中匹配：

- 用户输入了字母 h 的时候，就把以 h 为前缀的 hello、her、hi、how 展示在搜索提示框内
- 当用户继续键入字母 e 的时候，就把以 he 为前缀的 hello、her 展示在搜索提示框内。
- 这就是搜索关键词提示的最基本的算法原理。

![image-20210210185430055](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210210185430.png)

## 敏感词过滤

### Trie 树实现敏感词过滤

1、对敏感词字典进行预处理，构建成 Trie 树结构；

- 预处理的操作只需要做一次，如果敏感词字典动态更新了，比如删除、添加了一个敏感词，那只需要动态更新一下 Trie 树就可以了。

2、敏感词 在 Trie 树中进行匹配

- 当用户输入一个文本内容后，把**用户输入的内容作为主串**，从第一个字符（假设是字符 C）开始，在 Trie 树中匹配。
- 当匹配到 Trie 树的叶子节点，或者中途遇到不匹配字符的时候，将主串的开始匹配位置后移一位，也就是从字符 C 的下一个字符开始，重新在 Trie 树中匹配。