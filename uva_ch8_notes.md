### [UVA1152	和为 0 的 4 个值 4 Values whose Sum is 0](https://www.luogu.com.cn/problem/UVA120)
#### 题意
4 个集合，求从每个集合各选一个数，使得四数之和为 0 的选法。
#### 思路
1. 枚举：O(n^4)
2. leetcode 四数之和做法：O(n^3logn)
3. O(n^2logn) 做法：设四数为 a,b,c,d，将 a+b 的所有可能放入集合 umap，枚举所有 c+d，查询 -(c+d) 是否在 umap 中。
```cpp
int a[4][4000]; int n;
unordered_map<int, int> umap;
void solve() {
	umap.clear();
	FOR(i, 0, n) {
		FOR(j, 0, n) {
			umap[a[0][i] + a[1][j]]++;
		}
	}
	int ans = 0;
	FOR(i, 0, n) {
		FOR(j, 0, n) {
			int n = umap[-a[3][i] - a[2][j]];
			if(n) {
				ans += n;
			}
		}
	}
	cout << ans << "\n";
}

int main()
{
  ios_base::sync_with_stdio(false);
  cin.tie(NULL);
    
	int t; cin >> t;
	while(t--) {
		cin >> n; FOR(i, 0, n) FOR(j, 0, 4) cin >> a[j][i];
		solve();
		if(t) cout << '\n';
	}
	
}
```

### [UVA11134 传说中的车 Fabled Rooks](https://www.luogu.com.cn/problem/UVA11134)
#### 题意
在 n*n 棋盘上放 n 个车，给定 n 个车的 x 轴和 y 轴区间范围，问是否存在一种放置方式，使得任意两车之间互不能攻击，若有，输出这 n 个车的坐标。
#### 思路
1. 棋子在 x 轴上的坐标和在 y 轴的坐标相互独立，互不影响。所以求每个棋子的坐标 (x,y) 这一个大问题，可以看作是求每个棋子的 x 坐标和 y 坐标这两个独立的子问题。
2. 从 1 到 n，共有从 n 个需要放置的位置，依次计算他们应放置哪个棋子。假设当前放置的位置为 x_i，对棋子区间的右端点排序，则第一个能包含 x_i 的区间所对应的棋子就应该放在 x_i（当然该棋子还未被放在[0, x_i)中的任意位置）。

#### 代码
```cpp
#define MAXN 5000
int n;
array<int,5> p[MAXN];
int ans[MAXN][2];
int vis[MAXN];

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    
next_round:
    while(cin >> n && n) {
    
        FOR(i, 0, n) {
            p[i][4] = i;
            FOR(j, 0, 4) cin >> p[i][j]; // x:p[i][0]...p[i][2] y:p[i][1]...p[i][3]
        }
        sort(p, p+n, [](array<int,5> a, array<int,5> b){
            if(a[2] != b[2]) return a[2] < b[2];
            if(a[0] != b[0]) return a[0] < b[0];
            return a[4] < b[4];
        });

        memset(vis, 0, sizeof(vis));
        REP(x, 1, n) {
            int i = 0;
            while(i < n) {
                if(!vis[i] && p[i][0] <= x && x <= p[i][2]) {
                    int no = p[i][4]; ans[no][0] = x; 
                    vis[i] = 1;
                    break;
                }
                ++i;
            }
            if(i == n) {
                cout << "IMPOSSIBLE\n";
                goto next_round;
            }
        }

        sort(p, p+n, [](array<int,5> a, array<int,5> b){
            if(a[3] != b[3]) return a[3] < b[3];
            if(a[1] != b[1]) return a[1] < b[1];
            return a[4] < b[4];
        });

        memset(vis, 0, sizeof(vis));
        REP(x, 1, n) {
            int i = 0;
            while(i < n) {
                if(!vis[i] && p[i][1] <= x && x <= p[i][3]) {
                    int no = p[i][4]; ans[no][1] = x; 
                    vis[i] = 1;
                    break;
                }
                ++i;
            }
            if(i == n) {
                cout << "IMPOSSIBLE\n";
                goto next_round;
            }
        }

        FOR(i, 0, n) {
            cout << ans[i][0] << ' ' << ans[i][1] << '\n';
        }
    }
    
    return 0;
}
```
### [UVA11134 传说中的车 Fabled Rooks](https://www.luogu.com.cn/problem/UVA11134)
#### 题意
#### 思路
#### 代码
