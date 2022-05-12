### [UVA1025 城市里的间谍 A Spy in the Metro](https://www.luogu.com.cn/problem/UVA1025)
1. DAG 上的最短路。
2. dp[i][j] 代表 i 时刻在第 j 个车站的最小总等待时间，也就是以 (i, j) 结束的最短路。初始时间为 0，初始车站为 1，所以 dp[0][1] = 0，0 时刻其他车站的等待时间为 INF，即不可达。由于时间是天然的拓扑序，所以按照时间顺序计算每个dp值即可。
3. INF 简化了判断，让我们不需要特定一个特殊值（比如 -1）来表示某个状态不可达。
4. 三种决策：从左边站点来、从右边站点来、等一分钟（等 n 分钟可由等 1 分钟转换而来）。
```cpp
int dp[201][51];
int has_train[205][51][2];
int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    
	int n; int kase = 1;
    while(cin >> n && n) {
        memset(dp, -1, sizeof(dp));
        memset(has_train, 0, sizeof(has_train));
        int T; cin >> T;
        int time_cost[50]; REP(i, 1, n-1) cin >> time_cost[i];

        {
            int M1; int station_1[51]; cin >> M1; REP(i, 1, M1) cin >> station_1[i];
            int M2; int station_n[51]; cin >> M2; REP(i, 1, M2) cin >> station_n[i];

            REP(k, 1, M1) {
                int time = station_1[k];
                if(time > T) continue;
                has_train[time][1][0] = 1;
                REP(i, 2, n) {
                    time+=time_cost[i-1];
                    if(time <= T)
                        has_train[time][i][0] = 1;
                    else break;
                }
            }
        
            REP(k, 1, M2) {
                int time = station_n[k];
                if(time > T) continue;
                has_train[time][n][1] = 1;
                REPD(i, n-1, 1) {
                    time+=time_cost[i];
                    if(time <= T)
                        has_train[time][i][1] = 1;
                    else break;
                }
            }
        }

        REP(i, 2, n) dp[0][i] = INF;
        dp[0][1] = 0;

        REP(time, 1, T) {
            REP(station_i, 1, n) {
                int &v = dp[time][station_i];
                // cout << i-1 << ' ' << j << endl;
                v = dp[time-1][station_i] + 1;
                int prev_time, prev_station;
                {
                    prev_station = station_i - 1;
                    if(prev_station >= 1) {
                        prev_time = time - time_cost[prev_station];
                        if(prev_time >= 0 && has_train[time][station_i][0]) {
                            v = min(v, dp[prev_time][prev_station]);
                        }
                    }
                }
                // if(station_i-1 >= 1 && time-time_cost[station_i-1] >= 0 && has_train[time][station_i][0])
                //     v = min(v, dp[time-time_cost[station_i-1]][station_i-1]);
                {
                    prev_station = station_i + 1;
                    if(prev_station <= n) {
                        prev_time = time - time_cost[station_i];
                        if(prev_time >= 0 && has_train[time][station_i][1]) {
                            v = min(v, dp[prev_time][prev_station]);
                        }
                    }   
                }
                // if(station_i+1 <= n && time-time_cost[station_i] >= 0 && has_train[time][station_i][1])
                //     v = min(v, dp[time-time_cost[station_i]][station_i]);
            }
        }

        cout << "Case Number " << kase++ << ": ";
        if(dp[T][n] >= INF) cout << "impossible";
        else cout << dp[T][n];
        cout << '\n';
    }
}
```
### [UVA437 巴比伦塔 The Tower of Babylon](https://www.luogu.com.cn/problem/UVA437)
1. DAG 上的最长路。
2. 与嵌套矩阵问题一样，首先需要构建 DAG，然后 DFS 按拓扑序计算 dp 值，唯二差别是点带权和需要补全所有状态（方块）。
3. dp[i] (height[i]) 代表第 i 个方块为顶部方块时的最大高度，也就是以 i 结束的最长路。
```C++
vector<tuple<int,int,int>> cubes;
vector<int> height;
int d[181];
int G[181][181];

int dp(int i, int sz) {
    int& ans = d[i];
    int h = height[i];
    if(ans > 0) return ans;
    ans = h;

    for(int j = 0; j < sz; ++j) {
        if(G[i][j]) {
            ans = max(ans, dp(j, sz)+h);
        }
    }
    return ans;
}
int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    
	int n; int kase = 1;
    while(cin >> n && n) {
        cubes.clear();
        height.clear();

        int ans = 0;

        FOR(i, 0, n) {
            int a[3]; cin >> a[0] >> a[1] >> a[2];
            sort(a, a+3);
            do {
                cubes.emplace_back(a[0], a[1], a[2]);
                height.push_back(a[2]);
            } while(next_permutation(a, a+3));
        }

        int sz = cubes.size();
        memset(G, 0, sizeof(G));
        FOR(i, 0, sz) {
            auto [x1, y1, z1] = cubes[i];
            FOR(j, 0, sz) {
                auto [x2, y2, z2] = cubes[j];
                if(x1 < x2 && y1 < y2) G[i][j] = 1;
            }
        }

        memset(d, 0, sizeof(d));
        FOR(i, 0, sz) {
            ans = max(ans, dp(i, sz));
        }

        cout << "Case " << kase++ << ": maximum height = " << ans; cout << '\n';
    
    }
}
```
### [UVA1347 旅行 Tour](https://www.luogu.com.cn/problem/UVA1347)
1. 经典题。
2. 将一个人的折返走转化为两个人一起走向终点。
3. dp[i][j] 表示一个人走到 i，另一个人走到 j 的最短路径。因为dp[i][j] = dp[j][i]，可以规定 i > j。初始状态 dp[0][0] 为 0。
4. 强制两人下一步只能走至 max(i,j)+1，也即 i+1 的位置，所以dp[i][j] 可以转化至 dp[i+1][j] 和 dp[i+1][i]（=dp[i][i+1]，依规定只能出现 i > j）。
5. 从前进顺序看，难以看出 dp[i][j] 由哪些状态转化而来，但容易知道 dp[i][j] 可以转化为哪些状态。故用刷表法按前进顺序计算 dp 值。
6. 由于 i > j，所以无法直接从 dp 的值知道路径最短总长度。但知道该长度应由 dp[n-1][0]、dp[n-1][1]、dp[n-1][2]、...、dp[n-1][n-2] 转化而来，故最后特殊计算一篇即可。
```C++
vector<pair<int,int>> points;
vector<vector<double>> dists;

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    
	int n;
    while(cin >> n) {
        points.resize(n);
        dists.resize(n);
        FOR(i, 0, n) {  
            cin >> points[i].first >> points[i].second;
        }
        FOR(i, 0, n) {
            auto [x1, y1] = points[i];
            dists[i].resize(n);
            FOR(j, 0, n) {
                auto [x2, y2] = points[j];
                dists[i][j] = sqrt((x1-x2) * (x1-x2) + (y1-y2) * (y1-y2));
            }
        }
        
        vector<vector<double>> dp(n, vector<double>(n, INF));
        
        dp[1][0] = dists[0][1];
        REP(i, 1, n-2) {
            FOR(j, 0, i) {
                double& prev = dp[i][j], &v1 = dp[i+1][j], &v2 = dp[i+1][i];
                v1 = min(v1, dists[i+1][i] + prev);
                v2 = min(v2, dists[j][i+1] + prev);
            }
        }

        double ans = INF;
        FOR(j, 0, n-1) {
            ans = min(ans, dp[n-1][j] + dists[n-1][j]);
        }
        cout << fixed << setprecision(2) << (ans) << '\n';
    }
}

```
