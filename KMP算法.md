> 起因是因为自己课上无聊刷视频，碰到老师讲得一个很有趣的 KMP 算法，在听的稀里糊涂以及 B 站视频也看得稀里糊涂的情况下
> 认为很有挑战性，才更新的这篇算法
> <span style="background:#fff88f">这里只讲 next 数组开头初始化为 0 的情况，初始化-1 的情况，请根据本文自行推导</span>
# 用来干什么的
在这个算法出来之前，用于解决最长字串的算法是朴实无华的暴力穷举算法(BF)，时间复杂度是沟槽的 `O(n*m)`，其中 n 是主串长度，m 是字串长度，因此由 [D.E.Knuth](https://zhida.zhihu.com/search?content_id=108106464&content_type=Article&match_order=1&q=D.E.Knuth&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzYyMzk3MzYsInEiOiJELkUuS251dGgiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoxMDgxMDY0NjQsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.QIp2NWurCJr7KePsgd4semMli385ydxz3Atd_Df9uBA&zhida_source=entity)，[J.H.Morris](https://zhida.zhihu.com/search?content_id=108106464&content_type=Article&match_order=1&q=J.H.Morris&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzYyMzk3MzYsInEiOiJKLkguTW9ycmlzIiwiemhpZGFfc291cmNlIjoiZW50aXR5IiwiY29udGVudF9pZCI6MTA4MTA2NDY0LCJjb250ZW50X3R5cGUiOiJBcnRpY2xlIiwibWF0Y2hfb3JkZXIiOjEsInpkX3Rva2VuIjpudWxsfQ.rx-GzSEakqxXyglHJAVD9P61dHvDsLwfvmHzKnLw1Po&zhida_source=entity)，[V.R.Pratt](https://zhida.zhihu.com/search?content_id=108106464&content_type=Article&match_order=1&q=V.R.Pratt&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NzYyMzk3MzYsInEiOiJWLlIuUHJhdHQiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoxMDgxMDY0NjQsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.YmewPsajGoXXaFroLjd3I9nqJQJ2HihSd69TUeonWO0&zhida_source=entity) 这三位大佬想出来的 KMP 算法
至少在大部分情况下，时间复杂度优于暴力穷举法，KMP 的时间复杂度为 `O(n+m)` 其中 n 是主串长度，m 是字串长度

> 为什么说大部分情况下？
> 暴力算法的理念就是，对于主串和字串进行一个字符一个字符的比对，一旦不对，主串的指针回到上一步指针初始位置+1 的地方，字串指针回到原点，进行比对，直到字串全部匹配或者主串走到末尾
> 但大部分的字串长度很长，且大概率会重复几个字符，那么这就是 KMP 的优势，它利用前后缀一致的理念，主串指针不动，动字串的指针回到前缀的末尾，再次进行比对，直到字串全部匹配或者主串走到末尾
> 但如果字串很短，且无重复片段，那其实这两个算法没什么区别，甚至搞笑的是 KMP 还要略慢与 BF 算法


# 例子
假设 text='abaacababcac' pattern='ababc'
# 什么是前缀后缀
首先我们需要知道字符串的前缀和后缀：  
对于字符串 `ababc` 来说，它的前缀有 `[a,ab,aba,abab]`，也就是以字符串第一个字符作为开头，<span style="background:#ff4d4f">同时不包括最后一个字符的所有子串</span>，同理它的后缀有 `[c,bc,abc,babc]`，也就是以字符串最后一个字符作为结尾，<span style="background:#ff4d4f">同时不包括第一个字符的所有字串。</span>

# 最长公共前后缀
我们再来说什么是字符串的**最长公共前后缀**，说白了，也就是前缀和后缀这 2 个集合中的相同部分，同时取最长的那个，就是这个字符串的**最长公共前后缀**。显然，在这个例子中，`ababc` 是没有公共前后缀的。但是对于 `abab`，它的前缀和后缀分别是 `[a,ab,aba]` 和 `[b,ab,bab]`，那么它的**最长公共前后缀**就是 `ab`。

# 核心
那我们回到一开始，主串字串的指针走到 3 这个位置，发现不匹配，此时 KMP 算法开始工作
![[Pasted image 20260413162527.png]]
我们注意到，主串的前缀以及字串的前缀都拥有公共部分 `aba`，且最长公共前后缀是 `a`，那么直接将主串的后缀与字串的前缀的最长公共前后缀进行对齐即可，此时主串的指针保持不变，字串指针移动到重复部分的后一位
![[Pasted image 20260413162932.png]]
> 为什么能这么移动，我们来分析一下
> 1. 首先既然 i，j 能移动到这里，就说明之前的主串和字串的字串肯定是一样的，且得到字串为 `aba`
> 2. 通过 next 数组告诉我们，aba 字串的最长前后公共字串的长度为 1<span style="background:#b1ffff">(这个要是看不懂，其实比较建议先看下面的 next 数组计算，毕竟一开始我也挺懵的</span>)
> 3. 通过 1，2 两点我们得知，i 之前的公共子串，主串的后缀，以及字串的前缀肯定是相同的
> 4. 进而我们可以让相同部分对齐，也就是让 `j=next[j-1]` (<span style="background:#b1ffff">回到 next 数组中已经计算出来的相同部分时记录的长度，并根据长度跳转到相应 next 数组的位置</span>)

那么接下来就会出现一个新的问题，`j=next[j-1]` 是什么？
> <span style="background:#ff4d4f">我们这里讲的 next 数组版本是"next 开头初始化为 0，并非-1"</span>
# Next 数组计算
![[Pasted image 20260413165057.png]]
我给你这么一个字串，我来一步步叫你怎么计算 next 数组，<span style="background:#ff4d4f">你要记住next 数组记录的是匹配最长字串的长度</span>
初始化 `next[0] = 0`，`i = 1`，`j = 0`。  
每次比较 `p[i]` 和 `p[j]`：
- 相等 → `next[i] = j + 1`，然后 `i++, j++`
> 为什么 j+1?
> 大部分人其实想说为什么不是 `next[i]=j`, 但你想想你把 j 初始化成了 0，那么假设一开始就匹配上了，那第一个匹配上的前缀位置的 next 数组记录的不就是 0 了嘛？
> 换句话说，就是在进入到相等这个判断的时候，本来就有 j 个长度匹配上了，现在又来一个，是不是 j+1 个长度呢？
- 不相等且 `j > 0` → `j = next[j-1]`（不回退 i）
> 这里我怕有人也有问题(其实是我有问题 XD)，所以也得说一嘴
> 为什么要让 j 回退到上一个最长字串长度的地方，（因为 next 数组存储的是最长字串的长度）, 首先你要搞清楚一件事，你走判断的永远是 j+1 的长度进行匹配，理清楚这个概念了，对于这个问题，我再问你一个问题，既然 j+1 的长度字串不匹配了，那万一 j 个长度字串万一真的匹配上了呢？，也就是说，这个判断要确保，无论任何长度的字串，都走遍了整个判断逻辑
- 不相等且 `j == 0` → `next[i] = 0`，`i++`