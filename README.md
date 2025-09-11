# 情境與時段參數

1. scenario: '現況'、'全線拆除'、'部分拆除' --新生高架橋拆除情境路網
2. case: ''(空字串表示原始OD)、'士林北投三重'、'全區域' --兩種北士科未來發展情境OD
3. rush_hour: '晨峰'、'昏峰'

# 主要程式檔說明
## Data Pre /

`Net Pre.ipynb`  路網資料

`OD Pre.ipynb`  旅次資料

`no_left_turn Pre.ipynb`  路口轉向限制資料

用以將資料轉換為 TNTP 格式 (.dat)

## ta /

Traffic Assignment

`ta - VehType - auto.ipynb` 

指派主程式

## ta / 指派結果 /

`Correction for TRTS-4S.ipynb`

指派結果校估，

與實際值、CUBE 模擬值相比較

`result comparison.ipynb`

抓取新生高替代路廊流量資料


