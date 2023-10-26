
###buildpartialMatchTable


  *用于构建 next 数组 计算第i个字符的最长前后缀长度但不包括从0-i的本身这个字符串长度*
  **前后缀指：前缀从0开始，后缀从当前位置往前，举例：abab,则next数组为|0|0|1|2|**
  *如果next数组当前位置j=3指向next数组的2，则当我们主串和next数组对应的匹配串不符时，令j=next[j-1],这样j=1,从匹配串来说abab的最后一个b不匹配，那么我们就回退到上一个匹配字符a(此时j=2)的最长匹配前缀(即第一个a(j=0))的下一个字符(第一个b(j=1))开始匹配*

###strStr

  1.先构建子匹配串的next数组
  2.两个指向 i,j 分别指向 主串和字串
  3.KMP的时间复杂度是O(n+m) 其中n为主串长度，m为子串长度，原因是i永远是往前，j不匹配和到末尾时都执行一次 j= next[j-1].
  -找匹配子串
    -如果 s[i]==p[j] 那么 i++ j++ 
    -判断 j==p.size() 如果是，则找到匹配的串了，压入栈。 否则，i没有越界且此时的s[i]!=p[j], j为0就i++往后接着匹配,否则j=next[j-1]进行下一次的匹配.
```
#include <iostream>
#include <vector>
using namespace std;
class Solution {
public:
    vector<int> buildpartialMatchTable(string pattern) {
        int n = pattern.size();
        vector<int> table(n, 0);
        int length = 0;
        for (int i = 1; i < n;) {
            if (pattern[i] == pattern[length]) {
                length++;
                table[i] = length;
                i++;
            }else {
                if (length != 0) {
                    length = table[length - 1];
                }
                else {
                    i++;
                    length=0;
                }
            }
        }
        return table;
    }
    int strStr(string haystack, string needle) {
        vector<int> matches;
        auto partialMatchesTables = buildpartialMatchTable(needle);
        int i = 0;  // 指向两个字符串
        int j = 0;
        while (i < haystack.size()) {
            if (haystack[i] == needle[j]) {
                i++; j++;
            }
            if (j == needle.size()) {
                matches.emplace_back(i - j); // 压入匹配点的起始位置
                j = partialMatchesTables[j - 1];
            }else if (i < haystack.size() && needle[j] != haystack[i]) {
                if (j != 0) {
                    j = partialMatchesTables[j - 1];
                }
                else {
                    i++;
                }
            }
        }
        if (matches.size() == 0) {
            return -1;
        }
        return matches[0];
    }
};
```
