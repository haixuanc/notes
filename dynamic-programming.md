## 1. Assembly-line scheduling

f(i, j) = ei, if j = 1

f(1, j) = min(f(1, j - 1) + a(1, j), f(2, j - 1) + t(2, j - 1) + a(1, j))

f* = min(f(1, n) + x1, f(2, n) + x2)

Top-down approach will take O(2^n) time:

f(i, n) = referenced 1 time
f(i, j) = refereced 2 ^ (n - j) times
sum(f(i, j)) = 2^n - 1

Bottom-up approach will take only O(n) time since each cell is calculated exactly once.

``` java
public class Solution {
  public void assemblyLine(int[] a1, int[] a2, int[] t1, int[] t2, int enter1, int enter2, int exit1, int exit2) {
    int[][] f = new int[2][a1.length];
    int ft = 0;
    int[][] l = new int[2][a1.length];
    int lt = 0;
    f[0][0] = enter1 + a1[0];
    f[1][0] = enter2 + a2[0];
    l[0][0] = 1;
    1[1][0] = 2;
    for (int i = 1; i < a1.length; i++) {
      int 1to1 = f[0][i-1] + a1[i];
      int 2to1 = f[1][i-1] + t2[i-1] + a1[i];
      if (1to1 < 2to1) {
        f[0][i] = 1to1;
        l[0][i] = 1;
      } else {
        f[0][i] = 2to1;
        l[0][i] = 2;
      }
      int 2to2 = f[1][i-1] + a2[i];
      int 1to2 = f[0][i-1] + t1[i-1] + a2[i];
      if (2to2 < 1to2) {
        f[1][i] = 2to2;
        l[1][i] = 2;
      } else {
        f[1][i] = 1to2;
        l[1][i] = 1;
      }
    }
    int n = al.length;
    int 1toEnd = f[0][n-1] + exit1;
    int 2ToEnd = f[1][n-1] + exit2;
    if (1ToEnd < 2ToEnd) {
      ft = 1ToEnd;
      lt = 1;
    } else {
      ft = 2ToEnd;
      lt = 2;
    }
  }
}
```