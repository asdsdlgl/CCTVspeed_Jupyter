**步驟

重頭開始: retrieve_CCTV -> retrieve_VD -> video2npy -> create_VD_target -> frame_analysis -> pretrain_nn -> 手動編輯frame_index.txt (用於之後快速訓練)
已跑過pretrain_nn: 可直接執行searched_nn

**詳細說明

-----抓取CCTV-----
檔案: retrieve_CCTV.py
變數:
cctvs: 共抓取14支CCTVs，可自行調整
urls: 需與cctvs對應之url
num_of_days: 設定天數
備註: 電腦時間秒數為20時抓一次CCTV，每分鐘抓一次

-----抓取VD-----
檔案: retrieve_VD.py
變數:
vds: 與cctvs對應，共14個，可自行調整
day1: 抓取的第一天
num_of_days: 總共抓幾天的VD
備註: 只抓06:00~24:00的VD

-----CCTV前處理-----
檔案: video2npy.py
變數:
cctvs: 要前處理的CCTVs，可自行調整
day1: 起始日期
dayLast: 結束日期
dont_care_start, dont_care_end: 不需要的時間區段 (目前為06:00~24:00)
備註: 讀取CCTV後轉換為.npy，並將每個影片的frame數存在frame_num.txt(training 需使用)

-----產生VD真值-----
檔案: create_VD_target.py
變數:
vds: 共幾支VD
day1, dayLast: 資料區間
說明: 會生成三種真值, Spd_Max, Spd_Min, Spd_Avg

-----分析CCTV-----
檔案: frame_analysis.py
變數:
cctvs: 要分析的CCTVs，可自行調整
說明: 讀取frame_num.txt，分析frame數區間(以十個frame為區間)，儲存於frame_interval.txt(training 需使用)
並會將frame數分布圖存在frame.png

-----包含搜尋的訓練-----
檔案: pretrain_nn.py
變數:
day1, train_dayLast: 訓練時間
test_day1, dayLast: 測試時間
val_day1, val_dayLast: 驗證時間
feature: 選擇真值(Spd_Max, Spd_Min, Spd_Avg)
model: 要訓練的神經網路模型
filename: 選擇vd真值的csv檔
備註: 此程式會將frame數分為兩個model去訓練，並分別向左右延伸，找到最佳的訓練與測試集

-----已搜尋完畢的訓練-----
檔案: searched_nn.py
變數:
許多與pretrain_nn類似
models: 要訓練哪些模型(會依序訓練)
備註: 此程式會依據frame_index.txt來決定要使用哪些frame數的CCTV來訓練與測試

frame_index.txt欄位說明:
[CCTV,peak1 train, peak1 test, peak2 train, peak2 test]
*此檔案須根據pretrain_nn的結果來手動編輯

-----其他檔案-----
test_diff_cctv.py: 以其中一支CCTV的模型來測試別支CCTV
kernel_visualize.py: 將feature maps or kernel視覺化，可自行更改
