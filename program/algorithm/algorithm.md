<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [1. 字符串](#1-字符串)
  - [1.1. 字符串 KMP 算法](#11-字符串-kmp-算法)
- [2. 双指针思想](#2-双指针思想)
  - [2.1. 快慢指针](#21-快慢指针)
  - [2.2. 碰撞指针](#22-碰撞指针)
  - [2.3. 滑动窗口指针](#23-滑动窗口指针)
  - [2.4. 固定窗口指针](#24-固定窗口指针)
- [4. TOP k 问题、最小 K 个数问题](#4-top-k-问题-最小-k-个数问题)
- [5. 递归思想](#5-递归思想)
- [6. 附录](#6-附录)

<!-- /code_chunk_output -->

## 1. 字符串

### 1.1. 字符串 KMP 算法

字符串 S 和模式串 P 匹配，先根据模式串 P 计算出 next 数组，再匹配

next 数组的意义：next 数组是对于模式串而言的。P 的 next 数组定义为：next[i] 表示 P[0] ~ P[i] 这一个子串，使得 前 k 个字符恰等于后 k 个字符 的最大的 k.

```c++
// 计算next数组
void GetNextval(char* p, int next[])
{
	int pLen = strlen(p);
	next[0] = -1;
	int k = -1;
	int j = 0;
	while (j < pLen - 1)
	{
		//p[k]表示前缀，p[j]表示后缀
		if (k == -1 || p[j] == p[k])
		{
			++j;
			++k;
			//较之前next数组求法，改动在下面4行
			if (p[j] != p[k])
				next[j] = k;   //之前只有这一行
			else
				//因为不能出现p[j] = p[ next[j ]]，所以当出现时需要继续递归，k = next[k] = next[next[k]]
				next[j] = next[k];
		}
		else
		{
			k = next[k];
		}
	}
}

// kmp搜索
int KmpSearch(char* s, char* p)
{
	int i = 0;
	int j = 0;
	int sLen = strlen(s);
	int pLen = strlen(p);
	while (i < sLen && j < pLen)
	{
		//①如果j = -1，或者当前字符匹配成功（即S[i] == P[j]），都令i++，j++
		if (j == -1 || s[i] == p[j])
		{
			i++;
			j++;
		}
		else
		{
			//②如果j != -1，且当前字符匹配失败（即S[i] != P[j]），则令 i 不变，j = next[j]
			//next[j]即为j所对应的next值
			j = next[j];
		}
	}
	if (j == pLen)
		return i - j;
	else
		return -1;
}

```

## 2. 双指针思想

双指针，指的是在遍历对象的过程中，不是普通的使用单个指针进行访问，而是使用两个相同方向（快慢指针）或者相反方向（对撞指针）的指针进行扫描，从而达到相应的目的。

双指针一般针对数组和链表，数组和链表要先排序。

https://www.cnblogs.com/huansky/p/13508533.html
https://www.cnblogs.com/huansky/p/13488234.html

### 2.1. 快慢指针

快慢指针也是双指针，但是两个指针从同一侧开始遍历数组，将这两个指针分别定义为快指针（fast）和慢指针（slow），两个指针以不同的策略移动，直到两个指针的值相等（或其他特殊条件）为止，如 fast 每次增长两个，slow 每次增长一个。

1. 计算链表的中点：快慢指针从头节点出发，每轮迭代中，快指针向前移动两个节点，慢指针向前移动一个节点，最终当快指针到达终点的时候，慢指针刚好在中间的节点。
2. 判断链表是否有环：如果链表中存在环，则在链表上不断前进的指针会一直在环里绕圈子，且不能知道链表是否有环。使用快慢指针，当链表中存在环时，两个指针最终会在环中相遇。
3. 求链表中环的长度：只要相遇后一个不动，另一个前进直到相遇算一下走了多少步就好了
4. 求链表倒数第 k 个元素：先让其中一个指针向前走 k 步，接着两个指针以同样的速度一起向前进，直到前面的指针走到尽头了，则后面的指针即为倒数第 k 个元素。（严格来说应该叫先后指针而非快慢指针）

### 2.2. 碰撞指针

对撞指针是指在数组中，将指向最左侧的索引定义为左指针(left)，最右侧的定义为右指针(right)，然后从两头向中间进行数组遍历。

1. 二分查找问题
2. n 数之和问题：比如两数之和问题，先对数组排序然后左右指针找到满足条件的两个数。如果是三数问题就转化为一个数和另外两个数的两数问题。以此类推。

### 2.3. 滑动窗口指针

指针 left, right，初始化 left = right = 0，把索引闭区间 [left, right] 称为一个「窗口」。
先不断地增加 right 指针扩大窗口 [left, right]，直到窗口中的字符串符合要求（包含了 T 中的所有字符）
我们停止增加 right，转而不断增加 left 指针缩小窗口 [left, right]，直到窗口中的字符串不再符合要求（不包含 T 中的所有字符了）。同时，每次增加 left，我们都要更新一轮结果。

1. 字符串匹配问题
2. 子数组问题

例如：“找到满足 xx 的最 x 的区间（子串、子数组）的 xx ”这类问题

```c++

string s, t;
// 在 s 中寻找 t 的「最小覆盖子串」
int left = 0, right = 0;
string res = s;

while(right < s.size()) {
    window.add(s[right]);
    right++;
    // 如果符合要求，说明窗口构造完成，移动 left 缩小窗口
    while (window 符合要求) {
        // 如果这个窗口的子串更短，则更新 res
        res = minLen(res, window);
        window.remove(s[left]);
        left++;
    }
}
return res;
```

### 2.4. 固定窗口指针

```c++

// 固定窗口大小为 k
string s;
// 在 s 中寻找窗口大小为 k 时的所包含最大元音字母个数
int  right = 0;

while(right < s.size()) {
	window.add(s[right]);
	right++;
	// 如果符合要求，说明窗口构造完成，
	if (right>=k) {
		// 这是已经是一个窗口了，根据条件做一些事情
		// ... 可以计算窗口最大值等
		// 最后不要忘记把 right -k 位置元素从窗口里面移除
	}
}
return res;
```

## 4. TOP k 问题、最小 K 个数问题

最小堆 和 最大堆可以处理 topk 问题，java 里面的 PriorityQueue 可以实现大堆和小堆

## 5. 递归思想

递归 3 要素

1. 递归函数功能
2. 递归结束条件
3. 函数的等价关系式

例如：

```c++
int fn(int n) {
	// 结束条件
	if (n == 1) {
		return 1;
	}
	//等价关系式
	return n * fn(n-1);
}
```

```c++
// 求二叉树的最近公共父节点
TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
	//结束条件：发现目标节点则通过返回值标记该子树发现了某个目标结点
	if(root == null || root == p || root == q) return root;
	//查看左子树中是否有目标结点，没有为null
	TreeNode left = lowestCommonAncestor(root.left, p, q);
	//查看右子树是否有目标节点，没有为null
	TreeNode right = lowestCommonAncestor(root.right, p, q);
	//都不为空，说明做右子树都有目标结点，则公共祖先就是本身
	if(left!=null&&right!=null) return root;
	//如果发现了目标节点，则继续向上标记为该目标节点
	return left == null ? right : left;
}
```

## 6. 附录

https://www.geekxh.com/
