# Single-Depot BCC – bcc_compare.py

此程式以單一檔案（`bcc_compare.py`）整合 **單一補給點的預算迴路覆蓋 (Single-Depot BCC)** 之整個流程，並支援：

1. **隨機圖生成**：  
   - 可產生一般隨機度量圖，或  
   - 產生「\(\beta\)-triangle (Delta_\beta)」圖形（可對邊長進行修正，使滿足 \(\beta\)-三角不等式）。  
2. **演算法執行**：  
   - **Algorithm S**（作者提出的分層法），或  
   - **Beta 前處理 (BetaPre)** 先放寬路徑至 \(\beta B\) 再拆分到 \(B\)。  
3. **檢查可行性**：  
   - 若某節點往返距離都 > \(B\)，則輸出無解。  
   - 若最終產生的迴路超過 \(B\) 也會輸出無解。  
4. **輸出結果**：  
   - 迴路數目、每條迴路長度、覆蓋多少非補給點。

---

## 一、檔案說明

- **`bcc_compare.py`**：  
  - `generate_random_graph(n)`：一般隨機圖生成  
  - `generate_beta_metric_graph(n, betam)`：嘗試修正三角形，構造 \(\beta\)-triangle  
  - `floyd_warshall(n, dist)`：做最短路，確保度量  
  - `algorithm_S_single_depot(...)`：作者原法 S  
  - `beta_method_single_depot(...)`：Beta 預算放寬  
  - 主程式 `main()` 負責解析參數、產生/讀取實例並執行演算法

---

## 二、使用方式

### 1. 產生隨機圖

```bash
# (A) 產生一般度量完整圖
python3 bcc_compare.py --generate --n 10 --b 3000 --outfile normal.txt

# (B) 產生 \beta-triangle 圖 (e.g. \beta=0.8)
python3 bcc_compare.py --generate --n 10 --b 3000 --betametric 0.8 --outfile beta_tsp.txt
```
- --n <int>：頂點數
- --b <float>：迴路預算
- --betametric <float>：若 >0，則嘗試修正三角，構造 β-triangle。若 ≤ 0，則使用一般亂數圖。
- --outfile <path>：輸出檔案路徑。若不指定，印到終端。


# 產生的檔案格式：
1. 第 1 行：n m r B
2. 接著 m 行：u v w，其中 r 為隨機選到的補給點。

### 2. 執行演算法

```bash
# 使用作者原法 (Algorithm S)
python3 bcc_compare.py --mode AUTHOR --instance normal.txt

# 使用Beta前處理 (BetaPre)
python3 bcc_compare.py --mode BETAPRE --beta 2.0 --instance normal.txt
```
- --mode {AUTHOR|BETAPRE}：指定演算法
- --instance <path>：讀取檔案
- --beta <float>：在 BetaPre 模式下表示放寬倍數 (預設=2.0)
若程式發現無解，會輸出 No feasible solution: ...

## 三、執行結果
程式執行後會顯示：
- === [Method] ===：顯示使用哪種演算法
- Number of cycles = k：生成的迴路數量
- Cycle lengths = [...]：各迴路長度
- Covered X/(n-1) nodes (excluding depot).
   - 若 X=(n-1) 代表覆蓋全部非補給點成功。
