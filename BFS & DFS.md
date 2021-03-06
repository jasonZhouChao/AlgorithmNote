##BFS & DFS
<a name="AnchorIndex" id="AnchorIndex"></a>
Index:  
-[N-Queens II](#Anchor1)  

-------
<a name="Anchor1" id="Anchor1"></a>
-**[N-Queens II](http://oj.leetcode.com/problems/n-queens-ii/)**([Back to Index](#AnchorIndex))   

八皇后是递归和回溯的经典问题，递归解法思路比较简明：每次递归选择处在新一行、未被使用列的点，且保证该点与已选择的点斜率绝对值不为1，使用一个Vector来记录已选择的棋子所在列即可，这种做法是有效且正确的。  

这里介绍求N-queens可行解个数最快的算法[引自Matrix67]。代码如下，思路见注释：
```cpp
class Solution {
public:
    int result, upperLim;
    int totalNQueens(int n) {
        if(n <= 0) return 0;
        result = 0;
        upperLim = (1 << n) - 1;//掩码，用于过滤掉高于n位的无用bits
        recursion(0, 0, 0);//递归初始条件(0x00000000,0x00000000,0x00000000)
        return result;
    }
    
    int recursion(int colInfo, int leftDiagonal, int rightDiagonal){
        //3个参数均表示禁位，即之前所选节点对于本次递归所在行选择的限制，来自3方面
        //colInfo表示列禁位，leftDiagonal表示左斜线禁位，rightDiagonal表示右斜线禁位
        //如colInfo == 0x00000003表示最后两列已被选择，不可再次选择
        //leftDiagonal == 0x00000001表示前面选择的点使得本次递归所在行不能选择最后一列
        int pos, p;
        //pos表示本次递归所在行可用位置，如0x00000003表示最右两列可用
        //p表示从pos中取出的、只包含“最靠右”的1的数
        if(colInfo == upperLim){
            result++;//所有列已被选择，产生一个可行解
        }else{
            pos = upperLim & ~(colInfo | leftDiagonal | rightDiagonal);
            //三个输入参数的或操作是对列、左斜线和右斜线禁位效果合成，取反则是得到可用位
            //与掩码操作过滤掉高于n位的无用bits
            while(pos){//不为0表示还有可用位置
                p = pos & -pos;//取出“最靠右”1操作
                pos = pos - p;//消除已选的1
                recursion(colInfo | p, (leftDiagonal | p) << 1, (rightDiagonal | p) >> 1);
                //对p的或操作表示该位已选择，对下一层递归起到禁位效果
                //位移的起到表示斜线禁位的效果，举例如下
                //位移前:leftDiagonal == 0001000(7bits), rightDiagonal == 0001000(7bits)
                //位移后:leftDiagonal == 0010000(7bits), rightDiagonal == 0000100(7bits)
                //显然本次选中第四列会对下一层递归起到第3和第5列禁位效果
            }
        }
    }
};
```
