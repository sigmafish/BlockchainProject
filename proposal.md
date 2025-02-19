# 以資產所有權轉換實現虛擬幣的交易特性

組員: 
R13922176 張智奇、P13922007 王信璋、P12922003 陳建宏、T13902113 李駿傑、R12521616 陳俊達

---
<a href="https://docs.google.com/presentation/d/13GOQH7FGl2EIsEb6KN8DiyJmwuLhcxO-Xpqt-i-V7S0/edit#slide=id.p1" target="_blank">簡報投影片 - 1</a>
---
  
## 1. Introduction

在一個法治能被落實的政治實體中，私有資產將被一個可信的體制保護，但是體制的能力是有限的，例如政府雖然可透過法幣儲存購買力，但無法保證它最終能兌現的效益(通膨)，更何況對於一個難以被信任的政治體制，例如極權國家或動盪中的政治環境。

從穩定幣或其他虛擬貨幣的經驗看來，需求方要的是一個能保障資產或令資產增長的系統，實際上就是一個風險趨避的行為，而供給方藉由背景或財力提供保障/安全感，並將所承擔的風險轉移到實體貨幣的投資中，這樣的供需結構如同期貨，雙方藉由交換資產各取所需，但目前虛擬幣的價值也僅止於多種貨幣間交換的媒介，功能上離真正的貨幣仍有一段距離。

我們希望透過期貨思維設計商業模型，賦予虛擬幣在實體經濟中的交易特性，提供需求方確保其購買力效益(安全感)，也讓供給方能有利可圖，如需求估計(成本控制)、購買力(資源)競爭、獲得投資本金等，也希望在模型運行的過程中，活化被過度囤積的實體貨幣，並讓市場在系統設計的規則(PoW 和智能合約)中趨於自由競爭。從 <a href="#econ-policy" style="display:inline;color:var(--hmd-tw-text-default);">Reference 1</a> 的研究和實證中，我們發現這樣的市場環境(政治不一定要選用民主制度)，較有利於經濟與社會的發展。

為此，我們期望藉由區塊鏈建立一個可信的私有資產所有權交易系統，記錄包括個人擁有的虛擬貨幣及未兌現商品/服務的契約，本系統目的是活絡實體資源交易，為了讓市場活絡需要多人參與協作(拿出資本加入市場)，自由但有共識地更新交易的資訊，如此才能盡量避免作假帳和保障私有資產所有權，而其中的開放資料也可為政府政策所用，如稅收的評估依據、政策的研擬等，相關經濟觀察與論述細節請見 <a href="#7-Problems-and-notes" style="display:inline;color:var(--hmd-tw-text-default);"> 7.(1) </a>。

總結如下:<br>
**道 -- 賦予虛擬幣交易屬性，促進經濟活絡<br>
法 -- 各取所需，以利導勢<br>
術 -- 賽局設計與期貨思維<br>
器 -- 區塊鏈及相關技術**

## 2. <span id="sec2">Target audience</span>
以下是可從本區塊鏈提供的操作中，獲取利益的對象:

| 對象               | 操作 | 預期利益 |
|:----------------: | :----: | :----: |
| 生產者             | <div style="text-align: left">1.發行合約幣<br>2.發行商品/服務兌換券</div> | <div style="text-align: left">1.需求估計(成本控制)<br>2.競爭購買力(稀缺資源)的管道<br>3.獲得投資本金(風險轉移)</div> |
| 消費者             | <div style="text-align: left">1.購買商品/服務兌換券<br>2.交換商品/服務兌換券</div> | <div style="text-align: left">1.保存購買力(抗通膨)<br>2.降低購買成本<br>3.獲得交易流通性</div> |
| 政策制定者     | 取得公開資料 | <div style="text-align: left">1.稅收評估的依據<br>2.政策研擬的參考</div>  |

## 3. Technology, Service, or Product
### (1) 基本定義

- 資產類型:

| 名稱 | 說明 |
| :--------: | :--------: | 
| 原生幣(ValueFlow/VFlow coin) | <div style="text-align: left">1.OurChain 的原生幣<br>2.礦工的收入來源<br>3.加入本區塊鏈的門票</div> |
| 合約幣(contract coin) | 透過發行合約幣形成自主的商業模式 |
| 兌換券(contract token) | 透過發行兌換券將商品/服務數位化 |

- 帳戶類型:

| 名稱 | 功能 | 相關操作 |
|:----:|:--------:|:--------:|
| 一般帳戶 | 儲存私有資產，如 currency coin 及 contract token | 資產所有權轉換 |
| 合約帳戶 | 暫存私有資產 | 質押、寄賣等 |
> [註] currency coin 為 VFlow coin 和 contract coin 的通稱

- <span id="rate_def">交易類型與收益:</span><br>
系統保留原生 bitcoin 的交易手續費操作，但為了鼓勵自由競爭，需要讓各類型交易都能被平等地被確認(confirmed)，所以採取系統統一發放原生幣作為獎勵的機制，獎勵依據節點收錄進區塊的交易類型發放，初始定義如下表，附加獎勵是鼓勵節點對特定交易類型的處理，系統會視目前交易池和寄賣池中各類型的比例做調整。
> [註] 系統獎勵也可以是各節點自行依照公開公式計算，其它節點再確認(verify)的方式進行，系統可以接受(local)政策制定者的調控指導，依據共識對相關公式進行調整。
 
| 交易類型  | 基本獎勵(單位:UC) | 附加獎勵 | 相關邏輯 |
| :--------: | :--------: | :--------: | :--------: |
| 原生幣相關     | 1     |    | OurChain 原生邏輯     |
| 合約幣相關     | 1     |    | <a href="#sec3-3" style="display:inline;color:var(--hmd-tw-text-default);">3.(3)</a> |
| 兌換券相關     | 1     |    | <a href="#sec3-4" style="display:inline;color:var(--hmd-tw-text-default);">3.(4)</a> |
| 交換兌換券     | 1     | <div style="text-align: left">參與寄賣和搓合的 addresses 各可獲得 (float)1/3 UC</div> | <a href="#sec3-5" style="display:inline;color:var(--hmd-tw-text-default);">3.(5)</a> |
| 資金籌措     | 1     |    | <a href="#sec3-6" style="display:inline;color:var(--hmd-tw-text-default);">3.(6)</a> |
 
>  [註1] 暫定基本獎勵($y$)的計算公式為 $y=1+\sqrt{\text{max}\{0, x- (\text{float})\frac{2}{3}\}}$，$x$ 為交易類型佔交易池的比例 <br> 
   [註2] 1 Unit coin(UC) $\equiv \frac{1\text{ VFlow coin}}{1000\text{ transactions}}$<br>
  [註3] 依照 OurChain 原生邏輯控制 VFlow coin 的總數量，視情況做調整<br>
  [註4] 1/3 UC 是名目發放的獎勵，實際發放為 16 位小數位數的 0.3333333333333333 UC

###  <span id="sec3-2">(2) 物件結構</span> 
```c++=
#include <string> 
#include <map>
using namespace std; 

//持有者資訊
typedef struct _owner_info{
    string signature;
    string address;
    string public_key;
} OwnerInfo;

//虛擬幣(currency coin, ex.VFlow coin or contract coin)資訊
typedef struct _currency_info{
    string name;
    float amount;
} CurrencyInfo;

//商品或服務兌換券(contract token)資訊
typedef struct _token_info{
    string name;
    int amount;
} TokenInfo;

//物價對應資訊
typedef struct _currency_token_info{
    float exchange_rate; // VFlow coin 與 currency coin 的交換匯率
    map<int, float> amount_price_map;// 使用該 currency 的購買數量與單價的對應表
} CurrencyTokenInfo;

//寄賣委託單
typedef struct _consignment_order{
    string transaction_id;
    string consignor_address;
    TokenInfo token_info; // 寄賣 token
    CurrencyInfo difference; // 差價
    int remain_day; // 寄賣剩餘天數
} ConsignmentOrder;
```

###  <span id="sec3-3">(3) 發行合約幣(contract coin)的智能合約【智能合約A】</span>
- 發行者(creator)必須要質押一定量的 VFlow coin 才能發行 contract coin，並於合約到期後才能領出
- 發行者得自訂 VFlow coin 與 contract coin 的交換匯率(exchange rate)，但其宣稱的公開資訊與交換匯率，將會是購買者是否信任該合約的評估依據
- 合約運作期間，購買者可透過退款(refund)取回資金保障權益
```c++=
#include <string> 
#include <time.h>
using namespace std; 

string creator_address; // 記錄 creator 的 address
string deposit_address; // 質押 VFlow coin 的 address
string coin_name;       // contract coin 的名稱
float exchange_rate;    // VFlow coin 與 contract coin 的交換匯率
timespec end_time;      // 合約期限
bool is_available;      // 合約是否有效

/* 建構子: 
    1. 檢查 creator.address 是否已有該 contract coin 的所有權, 
       若是, 則根據交易找到運行節點並 call get_contract_info() 更新本地節點 ;
       若否, 則執行步驟 2~6
    2. 檢查 creator.address 中是否有足夠質押的 VFlow coin, 有則啟動合約 
    3. 啟動合約後，
       (1) 將質押的 VFlow coin 轉存到 deposit_address 中
       (2) 將產生的等量 currency_info.amount 的  contract coin 存到 creator 的 address 中
    4. 利用 guaranty_coin_amount 與 currency_info.amount 計算 exchange_rate
    5. 利用 period 計算 end_time
    6. 設定 is_available := true, coin_name := currency_info.name
   Inputs: 
     - OwnerInfo creator: 合約啟動者的個人資訊
     - float guaranty_coin_amount: 質押 VFlow coin 的數量
     - CurrencyInfo currency_info: 預期發行的合約幣資訊
     - int period: 合約期間, 以天為最小單位
*/
void constructor(OwnerInfo creator, float guaranty_coin_amount, CurrencyInfo currency_info, int period);

/* 檢查合約狀態:
    1. 檢查合約是否到期: 
       (1) is_available 是否為 true
       (2) end_time 是否為過去的時間 
    2. 當 1.(1) & 1.(2) 為 true 時, 設定 is_available 為 false
   Outputs: bool true/false
*/
bool is_contract_available();

/* 換匯: 
    1. 檢查: 
       (1) 執行 is_contract_available()
       (2) buyer.address 中是否有足夠的 VFlow coin 
       (3) creator_address 中是否有足夠的 contract coin
    2. 當 1.(1) & 1.(2) & 1.(3) 為 true 時, 啟動換匯轉換資產所有權, 即 buyer.address 
       取得 contract coin, creator_address 取得 VFlow coin
   Inputs: 
     - OwnerInfo buyer: buyer 的個人資訊
     - float vflow_coin_amount: buyer 預計換出的 VFlow coin 的數量
     - float contract_coin_amount: buyer 預計換入的 contract coin 的數量
*/
void exchange(OwnerInfo buyer, float vflow_coin_amount, float contract_coin_amount);

/* 退款: 
    1. 檢查: 
       (1) 執行 is_contract_available()
       (2) buyer.address 中是否有足夠的 contract coin 
       (3) deposit_address 中是否有足夠的 VFlow coin
    2. 當 1.(1) & 1.(2) & 1.(3) 為 true 時, 啟動退款轉換資產所有權, 即 buyer.address 
       取得 VFlow coin, deposit_address 取得 contract coin
   Inputs: 
     - OwnerInfo buyer: buyer 的個人資訊
     - float contract_coin_amount: buyer 預計退款的 contract coin 的數量
*/
void refund(OwnerInfo buyer, float contract_coin_amount);

/* 增資: 
    1. 檢查: 
       (1) 執行 is_contract_available()
       (2) creator.address 是否為 creator_address
       (3) creator.address 中是否有足夠的 VFlow coin 
    2. 當 1.(1) & 1.(2) & 1.(3) 為 true 時, 啟動增資轉換資產所有權, 即 creator.address 
       取得 contract coin, deposit_address 取得 VFlow coin
   Inputs: 
     - OwnerInfo creator: creator 的個人資訊
     - float contract_coin_amount: creator 預計增資的 contract coin 的數量
*/
void increase(OwnerInfo creator, float contract_coin_amount);

/* 回補: 回補 contract coin, 配合線下簽訂的具商業機密的實體合約使用
    1. 檢查: 
       (1) 執行 is_contract_available()
       (2) supporter.address 中是否有足夠的 contract coin 
    2. 當 1.(1) & 1.(2) 為 true 時, 啟動投資轉換資產所有權, 即 creator_address 
       取得 supporter.address 的 contract coin
   Inputs: 
     - OwnerInfo supporter: supporter 的個人資訊
     - float contract_coin_amount: supporter 預計投資的 contract coin 的數量
*/
void remit(OwnerInfo supporter, float contract_coin_amount);

/* 取得合約狀態:
   Outputs: (JSON string){
               (bool)is_available,
               (string)coin_name,
               (float)deposit_address 的 VFlow coin amount, 
               (float)creator_address 的 contract coin amount,
               (float)exchange_rate,
               (int)end_time(天)
            }
*/
string get_contract_info();

/* 延長合約:
    1. 檢查: 
       (1) is_available 是否為 true
       (2) creator.address 是否為 creator_address
    2. 當 1.(1) & 1.(2) 為 true 時, 執行步驟 3~4
    3. 利用 new_add_period 計算新的 end_time
    4. 將延長的訊息廣播全網
   Inputs: 
     - OwnerInfo creator: creator 的個人資訊
     - int new_add_period: 延長時間, 以天為最小單位
*/
void extend(OwnerInfo creator, int new_add_period);

/* 結束合約:
    1. 檢查: 
       (1) is_available 是否為 false
       (2) creator.address 是否為 creator_address
    2. 當 1.(1) & 1.(2) 為 true 時, 執行步驟 3~4
    3. 返回 creator 質押的 VFlow coin, 即 creator_address 取得 deposit_address 的 VFlow coin
    4. 將結束的訊息廣播全網
   Inputs: 
     - OwnerInfo creator: creator 的個人資訊
*/
void close(OwnerInfo creator);
```

###  <span id="sec3-4">(4) 發行商品/服務兌換券(contract token)的智能合約【智能合約B】</span>
- 發行者(creator)必須要質押一定量的 VFlow coin 才能發行 contract token，並於合約到期後才能領出
- 發行者得自訂質押 VFlow coin 的數量，但該數量會與退款成本直接相關
- 合約運作期間，消費者可使用合約指定的虛擬幣，如 VFlow coin 或 contract coin 購買 contract token，並可透過退款(refund)取回資金保障權益
- 合約運作期間，消費者可使用合約指定的虛擬幣或 contract token 進行消費(consume)並取得商品或服務
```c++=
#include <string> 
#include <map>
#include <time.h>
using namespace std; 

float refund_rate; // 退款(refund)比例
float total_token_value; // 記錄販售商品/服務總價值, 以 VFlow coin 計價
string creator_address;  // 記錄 creator 的 address
string deposit_address;  // 質押 VFlow coin 的 address
string token_name;       // contract token 的名稱
/* currency_price_maps 為不同 currency 的購買數量與單價的對應表, 
ex. 以不同 currency 購買 1/5/10 個 contract token 對應的單價(理論上買越多越便宜)
說明：
 {
    {"VFC" 或 "合約幣"，{ Exchange rate to VFC, 
        { { 購買數量1， 單價（Unit：“VFC” 或 “合約貨幣”） }, 
        { { 購買數量2， 單價（Unit：“VFC” 或 “合約貨幣”） },
        { ...， ... }, ...
    }
 }
 ---
 { 
    {"VFlow", { 1., { {1, 1.},{5, 1.},{10, 0.8} } } },   //用 VFlow coin
    {"ACoin", { 0.9, { {1, 0.9},{5, 0.8},{10, 0.7} } } } //用 ACoin 
 }
*/
map<string, CurrencyTokenInfo> currency_price_maps; 
timespec end_time;      // 合約期限
bool is_available;      // 合約是否有效

/* 建構子: 
    1. 檢查 creator.address 是否已有該 contract token 的所有權, 
       若是, 則根據交易找到運行節點並 call get_contract_info() 更新本地節點
       若否, 則執行步驟 2~7
    2. 啟動合約後，
       (1) 將質押的 VFlow coin 轉存到 deposit_address 中
       (2) 將產生的等量 token_info.amount 的 token 存到 creator 的 address 中 
    3. 利用 period 計算 end_time
    4. 設定 currency_price_maps["VFlow"].amount_price_map[1] := token_basic_price
    5. 設定 total_token_value := token_info.amount * token_basic_price
    6. 設定 refund_rate := _refund_rate
    7. 設定 is_available := true, token_name := token_info.name
    8. 記錄 token_basic_price 在初始 transaction 中
   Inputs: 
     - OwnerInfo creator: 啟動合約的個人資訊
     - float guaranty_coin_amount: 質押 VFlow coin 的數量
     - TokenInfo token_info: 預期發行的合約代幣資訊
     - float token_basic_price: token 以 VFlow coin 計價的單一售價
     - float _refund_rate: 退款的比例
     - int period: 合約期間, 以天為最小單位
*/
void constructor(OwnerInfo creator, float guaranty_coin_amount, TokenInfo token_info, float token_basic_price, float _refund_rate, int period);

/* 檢查合約狀態:
    1. 檢查合約是否到期: 
       (1) is_available 是否為 true
       (2) end_time 是否為過去的時間 
    2. 當 1.(1) & 1.(2) 為 true 時, 設定 is_available 為 false
   Outputs: bool true/false
*/
bool is_contract_available();

/* 購買: 
    1. 檢查: 
       (1) 執行 is_contract_available()
       (2) currency 及 token_info.amount 是否在 currency_price_maps 中被定義
       (3) buyer.address 中是否有足夠的 currency coin 
       (4) creator_address 中是否有足夠的 contract token
    2. 當 1.(1) & 1.(2) & 1.(3) & 1.(4) 為 true 時, 根據 currency_price_maps 
       中的定義計算本筆交易的價值
    3. 檢查 deposit_address 中的 VFlow coin 是否大於或等於 total_token_value 
    4. 轉換資產所有權, 即 buyer.address 取得 contract token, 
       (1) 若 3 為 true, creator_address 取得對應的 currency coin
       (2) 若 3 為 false, deposit_address 取得等價的 VFlow coin
           [當 currency coin 非 VFlow coin 時, 使用 currency_price_maps 轉換]
    5. 將以 VFlow coin 計價的購買單價, 記錄在 transaction 上
   Inputs: 
     - OwnerInfo buyer: buyer 的個人資訊
     - string currency: 預計用來購買 token 的 currency 的 name
     - TokenInfo token_info: buyer 預計購買的 contract token 資訊
*/
void purchase(OwnerInfo buyer, string currency, TokenInfo token_info);

/* 消費: 透過 currency coin
    1. 檢查: 
       (1) 執行 is_contract_available()
       (2) consumer.address 中是否有足夠的 currency coin
    2. 當 1.(1) & 1.(2) 為 true 時, 根據 currency_price_maps 中的定義轉換資產所有權, 
       即 producer.address 取得 consumer.address 的 currency coin
   Inputs: 
     - OwnerInfo consumer: consumer 的個人資訊
     - OwnerInfo producer: producer 的個人資訊
     - CurrencyInfo currency_info: consumer 消費的 currency 資訊
*/
void consume(OwnerInfo consumer, OwnerInfo producer, CurrencyInfo currency_info);

/* 消費: 透過 contract token
    1. 檢查: 
       (1) 執行 is_contract_available()
       (2) consumer.address 中是否有足夠的 contract token
    2. 當 1.(1) & 1.(2) 為 true 時, 轉換資產所有權, 即 producer.address 
       取得 consumer.address 的 contract token
   Inputs: 
     - OwnerInfo consumer: consumer 的個人資訊
     - OwnerInfo producer: producer 的個人資訊
     - TokenInfo token_info: consumer 消費的 contract token 資訊
*/
void consume(OwnerInfo consumer, OwnerInfo producer, TokenInfo token_info);

/* 退款: 憑單
    1. 檢查: 
       (1) 執行 is_contract_available()
       (2) buyer.address 中是否有剩餘的 contract token 
       (3) deposit_address 中是否有足夠的 VFlow coin
       (4) 若非募資合約, 則為 true ; 若為募資合約, 檢查 transaction 記錄的 
           end_date 是否大於 today
    2. 當 1.(1) & 1.(2) & 1.(3) & 1.(4) 為 true 時, 根據 transaction_list 取得每筆交易的 
       token_info 及購買單價。先sort完transaction_list之後，從單價高的開始計算退款, 直到退貨數 = min{amount, buyer.address 中 token 數量} 為止  
    3. 計算 max_refund := currency_price_maps["VFlow"].amount_price_map[1] * 退貨數
    4. 轉換資產所有權, 即 buyer.address 取得 VFlow coin(總退款值 * refund_rate), 
       deposit_address 取得對應的 contract token, creator_address 取得 
       max_refund - (總退款值 * refund_rate) 的 VFlow coin
   Inputs: 
     - OwnerInfo buyer: buyer 的個人資訊
     - string transaction_list[]: buyer 的購買明細
     - int amount: buyer 欲退貨數量
*/
void refund(OwnerInfo buyer, string transaction_list[], int amount);

/* 增資: 
    1. 檢查: 
       (1) 執行 is_contract_available()
       (2) deposit_address 中的 VFlow coin 是否大於或等於 total_token_value
       (3) creator.address 是否為 creator_address
       (4) creator.address 中是否有足夠的 VFlow coin 
    2. 當 1.(1) & 1.(2) & 1.(3) & 1.(4) 為 true 時, 啟動增資轉換資產所有權, 
       即 creator.address 取得 contract token, deposit_address 取得以單價
       currency_price_maps["VFlow"].amount_price_map[1] 計算的 VFlow coin
    3. 更新 total_token_value := total_token_value + contract_token_amount * 
                                 currency_price_maps["VFlow"].amount_price_map[1]  
   Inputs: 
     - OwnerInfo creator: creator 的個人資訊
     - int contract_token_amount: creator 預計增資的 contract token 的數量
*/
void increase(OwnerInfo creator, int contract_token_amount);

/* 回補: 回補 contract token, 配合線下簽訂的具商業機密的實體合約使用
    1. 檢查: 
       (1) 執行 is_contract_available()
       (2) supporter.address 中是否有足夠的 contract token 
    2. 當 1.(1) & 1.(2) 為 true 時, 啟動投資轉換資產所有權, 即 creator_address 
       取得 supporter.address 的 contract token
   Inputs: 
     - OwnerInfo supporter: supporter 的個人資訊
     - int contract_token_amount: supporter 預計投資的 contract token 的數量
*/
void remit(OwnerInfo supporter, int contract_token_amount);

/* 加盟: 等同於新增一種支付方式, 配合線下簽訂的具商業機密的實體合約使用
    1. 檢查: 
       (1) 執行 is_contract_available()
       (2) creator.address 是否為 creator_address 
    2. 當 1.(1) & 1.(2) 為 true 時, 根據 currency_token_info 擴充或更新 
       currency_price_maps, 但無法變更
       currency_price_maps["VFlow"].amount_price_map[1] 的值
    3. node 必須重新檢驗 currency_token_info 中的 exchange_rate 才能設定到 
       currency_price_maps 中
   Inputs: 
     - OwnerInfo creator: creator 的個人資訊
     - CurrencyTokenInfo currency_token_info: 以 currency coin 定義的物價對應資訊
*/
void franchise(OwnerInfo creator, CurrencyTokenInfo currency_token_info);

/* 取得合約狀態:
   Outputs: (JSON string){
               (bool)is_available,
               (string)token_name,
               (float)total_token_value,
               (float)refund_rate,
               (float)deposit_address 的 VFlow coin amount, 
               (float)creator_address 的 contract token amount,
               (JSON string)currency_price_maps,
               (int)end_time(天)
            }
*/
string get_contract_info();

/* 延長合約:
    1. 檢查: 
       (1) is_available 是否為 true
       (2) creator.address 是否為 creator_address
    2. 當 1.(1) & 1.(2) 為 true 時, 執行步驟 3~4
    3. 利用 new_add_period 計算新的 end_time
    4. 將延長的訊息廣播全網
   Inputs: 
     - OwnerInfo creator: creator 的個人資訊
     - int new_add_period: 延長時間, 以天為最小單位
*/
void extend(OwnerInfo creator, int new_add_period);

/* 結束合約:
    1. 檢查: 
       (1) is_available 是否為 false
       (2) creator.address 是否為 creator_address
    2. 當 1.(1) & 1.(2) 為 true 時, 執行步驟 3~4
    3. 返回 creator 質押的 VFlow coin, 即 creator_address 取得 deposit_address 的 VFlow coin
    4. 將結束的訊息廣播全網
   Inputs: 
     - OwnerInfo creator: creator 的個人資訊
*/
void close(OwnerInfo creator);
```

###  <span id="sec3-5">(5) 交換兌換券的智能合約【智能合約C】</span>
- 各種 token 的持有者都能透過寄賣池(consignment pool)，交易 token
- 透過交易彼此手上以較低成本(與購買數量有關)入手的 token，讓眾人都能享受到優惠的價格
- 節點經手的寄賣(consign)和搓合(matching)的交易，若最終得到確認上鏈，皆可以根據 <a href="#rate_def" style="display:inline;color:var(--hmd-tw-text-default);">「3.(1) 交易類型與收益」</a> 的定義，獲得 VFlow coin 作為報酬
```c++=
#include <string> 
#include <set>
#include <time.h>
using namespace std; 

string consignment_address; // 寄賣的 address
string node_address;        // 運行智能合約節點的 address
int list_limit;             // 過期的寄賣交易的容許筆數
set<string> expired_transaction_list; // 過期的寄賣交易明細

/* 建構子: 
    1. 產生寄賣的 address, 每個節點可以不同 
    2. 儲存節點的 address, 即 node_address := node_info.address
    3. 設定 list_limit := _list_limit
   Inputs: 
     - OwnerInfo node_info: 運行智能合約節點的個人資訊
     - int _list_limit: 過期的寄賣交易的容許筆數
*/
void constructor(OwnerInfo node_info, int _list_limit);

/* 主動交換: 
    1. 檢查: 
       (1) owner_1.address 中是否有足夠的 token 1 
       (2) owner_2.address 中是否有足夠的 token 2 
       (3) 若 difference 不為 NULL, 則檢查 owner_1.address 中是否有足夠的 
           difference.amount
    2. 當 1.(1) & 1.(2) & 1.(3) 為 true 時, 轉換資產所有權, 即 owner_1.address 取得 
       token 2, owner_2.address 取得 token 1, 若 difference 不為 NULL, 則 
       owner_2.address 再取得 currency coin
    3. 將 token 的購買單價記錄在對應的交換交易的 transaction 上
    4. 若有 transaction 為募資合約, 則將其 end_date 記錄在對應交換的 transaction 上
   Inputs: 
     - OwnerInfo owner_1: owner_1 的個人資訊
     - string transaction_id1: owner_1 擁有 token 1 的證明(含購買單價)
     - TokenInfo token_info_1: owner_1 要換出的 token 資訊
     - OwnerInfo owner_2: owner_2 的個人資訊
     - string transaction_id2: owner_2 擁有 token 2 的證明(含購買單價)
     - TokenInfo token_info_2: owner_2 要換出的 token 資訊 
     - CurrencyInfo difference: owner_1 要補給 owner_2 的差價(如果有)
*/
void exchange(OwnerInfo owner_1, string transaction_id1, TokenInfo token_info_1, OwnerInfo owner_2, string transaction_id2, TokenInfo token_info_2, CurrencyInfo difference);

/* 寄賣: 被動交換
    1. 檢查 consignor.address 中是否有足夠的 token
    2. 當 1 為 true 時, 轉換資產所有權, 即 consignment_address 在寄賣期間取得 
       consignor 的 token 的所有權
    3. 將購買單價記錄在寄賣交易的 transaction 上
   Inputs: 
     - OwnerInfo consignor: consignor 的個人資訊
     - string transaction_id: owner 擁有 token 的證明(含購買單價)
     - TokenInfo token_traded_out: consignor 寄賣的 token 資訊
     - TokenInfo token_traded_in: consignor 預計換取的 token 資訊
     - CurrencyInfo difference: consignor 要補給對方的差價(如果有)
     - int period: 寄賣期間, 以天為最小單位
*/
void consign(OwnerInfo consignor, string transaction_id1, TokenInfo token_traded_out, TokenInfo token_traded_in, CurrencyInfo difference, int period);

/* 搓合: 
    1. 檢查:
        (1) transaction_id_1 中的資產所有權是否還未轉換
        (2) transaction_id_2 中的資產所有權是否還未轉換
    2. 轉換資產所有權, 即 
        (1) transaction_id_1 中的 consignor 取得 transaction_id_2 中的
            consignment_address 擁有的 token 及 difference(如果有)
        (2) transaction_id_2 中的 consignor 取得 transaction_id_1 中的 
            consignment_address 擁有的 token 及 difference(如果有)
    3. 將 token 的購買單價記錄在對應交換交易的 transaction 上
    4. 若有 transaction 為募資合約, 則將其 end_date 記錄在對應交換的 transaction 上
    5. 記錄 transaction_id_1、transaction_id_2 中的 consignment_address, 
       以及搓合人(node)的 consignment_address 於搓合交易中
   Inputs: 
     - string transaction_id_1: 寄賣交易 1
     - string transaction_id_2: 寄賣交易 2  
*/
void matching(string transaction_id_1, string transaction_id_2);

/* 取得節點寄賣資產: 回傳未過期的寄賣資產
    1. 檢查 consignment_address 寄賣的資產是否過期
    2. 若過期, 則加入 expired_transaction_list 中 ; 若未過期, 則加入 outputs 清單中
   Inputs: 
     - string token_name: 搜尋特定 token(如果有)
     - int order_amount: 回傳筆數(如果有)
   Outputs: (JSON string){
               (string)consignment_address,
               (JSON string)ConsignmentOrder[]
            }
*/
string get_consignment_info(string token_name, int order_amount);

/* 取消寄賣: 主動, 由寄賣所有權人呼叫
    1. 檢查:
        (1) owner.address 是否與 transaction_list 中每筆交易的 consignor.address 相同
        (2) consignment_address 是否還有 transaction_list 中每筆交易的 token 的所有權
    2. 若有 1.(1) & 1.(2) 的檢查結果為 true 的 transaction, 則轉換其資產所有權, 
       即 consignor 取得 consignment_address 寄賣的 token 的所有權
   Inputs: 
     - OwnerInfo owner: owner 的個人資訊
     - string transaction_list[]: 寄賣交易明細
*/
void cancel(OwnerInfo owner, string transaction_list[]);

/* 取消寄賣: 被動, 當節點發現寄賣的 tokens 過期時呼叫
    1. 檢查: 
        (1) node.address 是否為 node_address 
        (2) expired_transaction_list.size() >= list_limit
    2. 當 1.(1) & 1.(2) 為 true 時, 轉換 expired_transaction_list 中每筆 
       transaction 的資產所有權, 即 consignor 取得 consignment_address 寄賣的 
       token 的所有權
   Inputs: 
     - OwnerInfo node: node 的個人資訊
*/
void cancel(OwnerInfo node);

/* 取得寄賣收入: 
    1. 檢查 node.address 是否為 node_address 
    2. 當 1 為 true 時, 轉換資產所有權, 即 node_address 取得 consignment_address 
       的 VFlow coin 
   Inputs: 
     - OwnerInfo node: node 的個人資訊
*/
void withdraw(OwnerInfo node);
```

###  <span id="sec3-6">(6) 資金籌措的智能合約【智能合約D】</span>
- 使用者可以透過此智能合約進行專案籌資(fundraise/invest)，營運 contract token 的商品/服務
- 參與本區塊鏈的初期開發團隊可透過結合 (4) 及 (6) 兩種智能合約，開啟募資並提供系統更新、技術及法規諮詢、客製化開發及系統架設等服務，從 <a href="#sec2" style="display:inline;color:var(--hmd-tw-text-default);">「2. Target audience」</a> 所定義的對象處取得對應的收益
```c++=
#include <string>
#include <time.h>
using namespace std; 

float basic_investment_amount; // 基礎投資額, 以 VFlow 計價
float token_basic_price; // token 以 VFlow coin 計價的單一售價
float return_rate;       // 投資回報率
int return_token_amount; // 募資回報 token 數
int active_period;       // 投資期間, 投資人只能於運行結束後才能提領本金與報酬
string creator_address;  // 記錄 creator 的 address
string deposit_address;  // 記錄 contract token 的 deposit address

/* 建構子: 
    1. 檢查 token_transaction_id 的 creator 的 address 是否為目前 creator 所有, 
       若是則執行步驟 2~8
    2. 設定 creator_address := 發行 token 的 transaction 上記錄的 creator 的 address
    3. 設定 deposit_address := 發行 token 的 transaction 上記錄的 deposit_address
    4. 設定 token_basic_price := 發行 token 的 transaction 上記錄的 token_basic_price
    5. 設定 basic_investment_amount := _basic_investment_amount
    6. 設定 return_rate := _return_rate
    7. 設定 return_token_amount := _return_token_amount
    8. 設定 active_period := _active_period
   Inputs: 
     - OwnerInfo creator: 啟動合約的個人資訊
     - string token_transaction_id: creator 發行 token 時的第一筆交易 id, 
                                    即由該 constructor 建立的 transaction 的 id
     - float _basic_investment_amount: 基礎投資額
     - float _return_rate: creator 承諾的投資回報率
     - int _return_token_amount: 承諾的募資回報 token 數
     - int _active_period: 投資期間, 以天為最小單位
*/
void constructor(OwnerInfo creator, string token_transaction_id, float _basic_investment_amount, float _return_rate, int _return_token_amount, int _active_period);

/* 募資: 以 token 作為回報, 只能透過 matching 釋出, 無法使用 refund 功能
    1. 計算投資額(investment_amount) := investment_unit * basic_investment_amount
    2. 檢查:
       (1) 該 contract token 的智能合約是否為有效(available)
       (2) creator_address 中是否有足夠 return_token_amount 的 token
       (3) supporter.address 中是否有足夠投資額的 VFlow coin
    3. 當 2.(1) & 2.(2) & 2.(3) 為 true 時, 轉換資產所有權, 即 deposit_address 取得 
       supporter.address 的 VFlow coin, supporter.address 從 creator_address 
       取得等量 return_token_amount 的 token
    4. 計算 end_date := today + active_period
    5. 將 end_date、token_basic_price(視為購買單價), 記錄在 transaction 上
   Inputs: 
     - OwnerInfo supporter: supporter 的個人資訊
     - int investment_unit: supporter 預計投資單位數
*/
void fund(OwnerInfo supporter, int investment_unit);

/* 投資: 以 return rate 作為回報
    1. 計算投資額(investment_amount) := investment_unit * basic_investment_amount
    2. 計算 return_value := 投資額 * (1 + return_rate)
    3. 檢查:
       (1) 該 contract token 的智能合約是否為有效(available)
       (2) deposit_address + creator_address 擁有的 VFlow coin 總量是否大於或等於 return_value
       (3) supporter.address 中是否有足夠投資額的 VFlow coin
       當 (1) & (2) & (3) 為 true 時, 執行步驟 4~6
    4. 計算 end_date := today + active_period
    5. 轉換資產所有權, 即 deposit_address 取得 supporter.address 的 VFlow coin 
    6. 將 return_rate、return_value、end_date 也一併記錄在 transaction 中
   Inputs: 
     - OwnerInfo supporter: supporter 的個人資訊
     - int investment_unit: supporter 預計投資單位數
*/
void invest(OwnerInfo supporter, int investment_unit);

/* 退場: 只有 invest 產生的 transaction 才能使用
    1. 檢查:
       (1) transaction_list 中每筆交易是否為 invest 產生的 transaction
       (2) 該筆 transaction 記錄的 end_date 是否大於 today
       (3) deposit_address + creator_address 擁有的 VFlow coin 總量是否大於或等於, 
           單筆 transaction 中的 return_value
    2. 當一筆 transaction 滿足 1.(1) & 1.(2) & 1.(3) 為 true 時, 執行步驟 3
    3. 轉換資產所有權, 即 investor.address 依次從 deposit_address, creator_address 
       取得等量 return_value 的 VFlow coin
   Inputs: 
     - OwnerInfo investor: investor 的個人資訊
     - string transaction_list[]: 投資的交易明細
*/
void exit(OwnerInfo investor, string transaction_list[]);
```

### (7) 不同種類的幣之間的轉換與交換
![不同種類的幣之間的轉換與交換-min](https://hackmd.io/_uploads/ByjPDndQ1e.png)


## 4. Business model (商業模型)
| 關鍵要素 | 詳情 |
|:----:|:----:|
| **客戶細分** |<div style="text-align: left">- **生產者**：可以發行合約幣、商品/服務兌換券。預期收益包括需求預估（成本控制）、獲取競爭購買力（稀缺資源）的途徑以及獲得投資本金（風險轉移）。<br> - **消費者**：能夠購買和交換商品/服務兌換券。預期好處是保存購買力（抗通脹）、降低購買成本（接近成本價）以及獲得交易流通性。<br> - **政策制定者**：可以獲取公開數據，用作稅收評估和政策制定的依據。</div> |
| **價值主張** | <div style="text-align: left">- **對生產者而言**：通過發行合約幣等方式提供一種新的商業運營模式，能夠更好地控製成本、獲取投資並參與市場資源競爭。<br> - **對消費者而言**：保障購買力，使其能以更低成本獲得商品和服務，並享受交易流通性，以此應對通脹風險。<br> - **對政策制定者而言**：公開數據可輔助進行稅收評估和政策制定，使政策更貼合實際經濟情況。</div> |
| **渠道** | <div style="text-align: left">- **區塊鏈平台**：作為整個系統的基礎設施，通過智能合約實現諸如發行合約幣、兌換券以及開展交易、籌資等各種交易和操作。不同類型的智能合約（用於發行合約幣、發行兌換券、交換兌換券、籌資等）為用戶提供多樣化的操作渠道，以滿足不同需求。<br> - **線上節點**：參與交易處理的節點在整個系統中起著關鍵作用，負責驗證、記錄交易，並根據交易類型獲得相應獎勵，以此激勵節點積極參與系統運營，確保系統順暢運行。</div> |
| **客戶關係** | <div style="text-align: left">- **基於智能合約的保障**：智能合約在各種交易活動（如發行、購買、交換、退款、增資、投資等）中為生產者和消費者提供規則和保障，明確各方權益和操作流程，增強用戶對系統的信任。<br> - **數據共享與政策關聯（針對政策制定者）**：通過提供公開數據，與政策制定者建立起間接的合作關係，使其能夠利用這些數據開展政策相關工作，實現一定程度的互利共贏。</div> |
| **收入來源** | <div style="text-align: left">- **交易獎勵**：系統根據節點收錄進區塊的交易類型發放相應獎勵，不同交易類型有不同的獎勵機制（如與原生幣相關、合約幣相關、兌換券相關等交易類型）。獎勵以VFlow幣為基礎進行計算和發放，激勵節點積極處理各類交易，這是系統維持運營和激勵參與者的一種重要收入分配方式。<br> - **潛在增值收益（針對生產者和投資者）**：若市場表現良好，生產者通過發行合約幣、兌換券等操作可能獲得資產增值帶來的收益；參與籌資等活動的投資者也有望根據投資回報機制獲得相應的增值收益。</div> |
| **關鍵資源** | <div style="text-align: left">- **區塊鏈技術及相關設施**：包括區塊鏈自身架構、智能合約技術等，是實現整個商業模式的核心技術基礎，確保交易記錄、驗證、執行等功能得以實現。<br> - **VFlow幣及其他虛擬資產**：作為系統內的原生幣以及各類合約幣、兌換券等虛擬資產，是交易的媒介和價值承載物，其穩定運行和價值認可對系統至關重要。<br> - **專業的開發與運營團隊**：負責系統的開發、維護、升級以及智能合約的編寫和管理等工作，確保系統能持續適應市場需求和技術發展。</div> |
| **關鍵活動** | <div style="text-align: left">- **系統開發與維護**：不斷完善區塊鏈系統的功能，優化智能合約的性能，確保系統的穩定性、安全性和可擴展性，以滿足不斷增長的用戶需求和日益複雜的交易場景。<br> - **交易處理與監管**：節點負責處理各類交易，包括驗證交易的真實性、合法性，將交易信息記錄到區塊鏈上，同時對交易活動進行監管，防止欺詐、違規等行為發生。<br> - **市場推廣與用戶教育**：向生產者、消費者、政策制定者等目標客戶群體宣傳推廣該系統的優勢和使用方法，提高系統的知名度和用戶參與度，同時對用戶進行必要的教育，使其能夠正確理解和運用系統的各項功能。</div> |
| **關鍵合作夥伴** | <div style="text-align: left">- **硬件供應商（可能涉及）**：為系統運行提供必要的硬件支持，如服務器等設備，確保系統能夠穩定運行，處理大量的交易數據。<br> - **相關行業機構（可能涉及）**：與金融、商業等相關行業的機構建立合作關係，有助於推廣系統的應用，拓展業務範圍，同時獲取行業最新動態和需求信息，以便更好地優化系統。</div> |
| **成本結構** | <div style="text-align: left">- **技術研發成本**：包括區塊鏈系統開發、智能合約編寫、測試等方面的人力、物力投入，以及相關技術設備和軟件工具的採購費用。<br> - **運營成本**：涉及服務器維護、網絡帶寬租賃、節點運營等方面的費用，以保障系統的日常穩定運行。<br> - **市場推廣成本**：用於宣傳推廣該系統，提高其知名度和影響力，吸引更多用戶參與，包括廣告投放、參加行業展會、舉辦推廣活動等方面的費用。</div> |

## 5. What makes this system superior, or better than current alternatives, or competitors?
- 提供退款保障確保消費者權益，可避免出現如 <a href="#cama" style="display:inline;color:var(--hmd-tw-text-default);">Reference 2</a> 的消費爭議，導致私有資產蒙受損失
- 目前密碼貨幣大多是用在純金融交易上，藉由加入能夠交易實體資源的操作，可以讓區塊鏈變成一套更能融入人類生活的制度
- 從實體資源層次處理經濟問題，相比於純粹的貨幣政策來說，更能達到預期的功效
- 比起單純為了謀求個人資產增值的密碼貨幣體系，本系統因具有穩定經濟、社會等效用，具有與政府談判的籌碼

## 6. Expected outcomes
- 透過活化被過度囤積的實體貨幣並賦予虛擬幣交易特性等操作，增加實體資源的交易效率，進而改善經濟環境
- 藉由區塊鏈保障私有資產和提供一個相對自由競爭的市場，將有機會使得財富分配能夠被後繼有能者參與，減緩財富分配不均導致的政治及社會問題

## 7. Problems and notes
### (1) 經濟觀察與系統設計

- 經濟學是一門研究如何有效利用有限資源，如天然資源、工具或人力等，滿足人類欲望的學科，供需問題是其中最核心的研究課題，例如，一個住在山中的獵戶跟海邊的漁夫，可以因為要滿足對方的對於不同類型資源的需求，而達成一筆以物易物的交易，如用 3 塊山豬肉交換 1 瓶鯨魚油，他們同時是對方的生產者也是消費者，這筆交易的價值在他們相互受到滿足的當下得以被實踐。
> [註] 交易的價值在於，能將個別生產者手中重複的財貨(效用遞減)，去換取其它稀缺性財貨，例如漁夫可能認為山豬肉相較鯨魚油更值錢，反之對獵戶也是如此。

- 這時候某位漁夫，洞見到獵戶對鯨魚油的需求似乎很旺盛，他為了盡可能獲得所有的山豬肉，可能會盡其所能地拉攏獵戶，並透過擴建自己的船隻(增加資本支出)、研究能航行更遠更安全的相關技術(創新研發)或雇傭更多的漁夫(增加人力成本)，來確保鯨魚的捕獲量和鯨魚油的產量，這便是生產力基於需求的提升，但是漁夫必須為這些尚未被實踐的交易提前支付代價(投資)，例如要給予船匠、航海技術員和漁夫提供住宿和伙食照料等。
- 當然該名漁夫也不希望自己的付出是無償的，他計畫在成為壟斷山豬肉的供應商後，能以滿足他慾望的形式與其他漁夫交換不同資源，可以是 1 塊山豬肉換 2 隻螃蟹，也可以是 2 塊山豬肉換 1 瓶鯨魚油。
> [註] 從這個例子可以看到，生產力的提升並沒有實踐交易的價值，因為供需雙方的慾望尚未被滿足，漁夫的風險是 "到時候" 獵戶或其他漁夫對他的計畫不買單，這便是為何會有景氣循環的原因，因為財貨的製造到實際被交易之間存在時間差(風險)，而這個風險出現的原因在於，生產者是透過觀察取得對未來需求的預估，是尚未落實的交易，所以其價值也是不確定的。

- 因為鯨魚油的生意被寡占，而且該名漁夫重新訂立了對其他漁夫不利(相較過去)的交換規則，讓善於捕鯨的漁夫相當不滿，使得山豬肉在這邊的流通性降低，交易也較難被成立。但是對於擅長捕撈螃蟹的漁夫而言是筆新生意，而且他也早就想嘗嘗山豬肉了，無奈之前獵戶只想要鯨魚油，所以他很開心有新類型的交易發生，讓 "舊的" 慾望終於被滿足了。
> [註] 從這邊我們發現財貨的流通性與需求呈正相關，流通性越高則交易被成立的機會也越高。有些需求不是不存在，而是在等待被滿足，它在等待一個可以做這類型交易的市場。如果在該名漁夫的操作下，總體財貨流通性和交易被成立的總量是上升的，則我們或可稱該經濟體獲得了成長。

- 考量到漁夫在沒有學習到獵戶的醃肉技術的情況下，山豬肉保質期不長而且不易攜帶，所以他抵押了一些鯨魚油發行了一種以深海貝類計價的貝殼幣，他這邊的交易也通通使用貝殼幣計價，因為貝殼幣可作為中間交易媒介、容易攜帶跟容易辨認等特質，很快就獲得了大量的流通性，即便是那些排斥他的捕鯨漁夫，同時市場上也出現了更多種類型的交易，讓各式各樣的欲望都能被很快地滿足(媒合供給與需求)。
> [註] 貨幣的流通性跟實體財貨的流通性本質上是相同的，只是虛擬一層媒介讓交易的類型更多元了，例如之前獵戶不需要螃蟹，但換成貝殼計價他們就願意將山豬肉賣給捕蟹的漁夫，在這樣的貨幣系統下，整體市場中交易被成立的效率(慾望被滿足的效率)也被提高了，這便是為何各國通常會把貨幣流通性跟經濟成長掛勾的原因。

- 分析市面上樂此不疲的咖啡寄杯業務，可以發現它在本質上或多或少帶著一種期貨性質，消費者透過提前消費以較低成本購入未來的需求，咖啡店則能透過預先收到貨款，去做一些投資或大量購買原物料以降低成本等風險趨避行為，並且透過這些購買資訊還能較為精準地 "掌握" 需求的數量，剩下的問題就是去實踐這些待落實的交易(契約)，而因販賣期貨所製造的時間差，或能正好彌補生產到交易落實中間的時間差風險，而正因為這項業務可能有這些利益可圖，所以才會被各大商家採用。
- 觀察到寄杯業務除了對商家有利外，也因為消費者的提前消費行為，將預計轉作存款的貨幣變成購買力保證契約(喜歡的樣子)，兩種都是消費者個人對未來風險的趨避行為，但是前者降低了貨幣的流通性，後者則相反。這是一種基於需求產生的主動式投入，而非被基準利率脅迫的，且投入的是消費財貨的市場，而非純粹為賺取差價的投機市場，根據前面論述，前者才有助於實體經濟的發展。
- 本系統宣稱能促進經濟活絡的原因在於，我們推廣並強化了寄杯業務，如以質押機制加強消費者對該業務的信心、通過虛擬契約的轉移讓商家得以回收票券重複利用(流通性的再釋放)，及開設寄賣池增加契約/財貨的流通性等，在區塊鏈的保障和基於商家維護自身商譽是有利的假設前提下，不同類型的供給與需求能被安全、快速和方便地在本系統中被媒合，這不異於發行第一顆貝殼幣帶出的效益。
>[註] 虛擬化契約除了帶來交易便利性和提升流通性外，也能提供各類型的交易資料，而因為這些資料描述的是未來需求，或可視為領先指標，幫助政策制定者以更為前瞻的視角制定相應政策，並指導系統(依據共識)做適當的調控，又因為本系統是數位化的，比起不確定因素較多和較難掌控的宏觀貨幣政策來說，本系統的調控能更快地反應到底層並且收斂出結果。

### (2) 如何設計適當的經濟模型與匯率?
參考 <a href="#enc-model" style="display:inline;color:var(--hmd-tw-text-default);">Reference 3</a> 中的第二部分、第四部份及第八部分。

- 系統內實行以 VFlow coin 為黃金，contract coins 為各類不同貨幣的金本位制度，目的是確保不同的 contract coins 間的匯率，能由<a href="https://zh.wikipedia.org/zh-tw/%E8%B4%AD%E4%B9%B0%E5%8A%9B%E5%B9%B3%E4%BB%B7">購買力平價</a>機制計算。因為系統容許生產者透過建立自主的 contract coin 來滿足特定的操作(如商業模式、制定彈性價格區間等)，但消費行為通常是局域性的，表示一項商品的供給價格應該要落在一個以需求價格為平均值的區間內，所以我們需要一個穩定的基礎貨幣作為快速衡量的標準，幫助消費者及生產者做成本和調整價格的評估。
>[註1] 請注意，contract token 的買賣完全可以只用原生幣來計價，只是考慮到資本家有營運自己商業模式的需求，所以才提供 contract coin 的彈性，token 的發行方可自行決定是否要加入特定 coin 的計價體系，這與日常生活中實體店家必然接受現金支付、支援 Line pay 但不支援街口支付的經營模式沒有差別，contract token 只是將該模式數位化而已。<br>
>[註2] Contract token 是提供流通性的關鍵要素，所以希望它是穩定的，不同 contract coin 可以對 VFlow coin 有不同匯率，但是在對 token 計價上應該趨近於相同(假設此時沒有任何 coin 發行方在做推廣促銷)，因為訂價權在於提供商品/服務的商家，不是發行 coin 的人，而因為商品/服務的價格穩定，所以 coin 匯差最終將體現在，購買該商品時支付的 coin 數量。

- 研究指出，成功促成區塊鏈系統和實體經濟的關聯，與如何有效控制虛擬幣的稀缺性和流通性有關，參考資料中建議成立基金會、聘請專業人士管理，和使用公開市場操作來做定期調控。
- 因為我們在設計上，統一使用質押 VFlow coin 來作為權益保障，某種程度上會降低流通性。除此之外，透過資金籌措的智能合約，如籌募天使投資基金(contract token 是天使投資)或虛擬幣銷毀基金(為保持稀缺性以確保個人資產價值)也能降低流通性，而這些都是基於自身利益的自主行為。但是，黃金的例子也告訴我們，流通性不足將限制經濟發展的速度，如何取得有效平衡將是一大課題。

### (3) 如何記錄智能合約購買者白名單?
- 可以參考以太坊，使用 Patricia Trie 記錄購買者名單，如經 Hash 過後的 public key 或 wallet id，以便之後確認身分。
- 因為以太坊要維持狀態的一致性，採用 single tasking，讓名單跟著合約帳戶的狀態一起被維護和保存(?，待 7.(3) 實作確認)；而 Ourchain 採用 multitasking，雖然可以提供較佳的 scalability，但合約資料的同步上可能需要藉由其他技術達成，如 IPFS 等。

### (4) 如何進行實作? 
#### Note:
參考 <a href="#blkchain-tech" style="display:inline;color:var(--hmd-tw-text-default);">Reference 4~5</a>
- 把 transaction 看成多個 instructions 組成的 script，所以不管是操作一般轉帳或合約，都是送出一個 transaction
- 定義並實作 API 的 inputs 跟 outputs
- UTXO(Unspent Transaction Output)描述各自獨立的資產；account 描述狀態(state)，狀態可以包含除了資產外的其他資訊，如以太坊的合約帳戶可以擁有記錄在全網的 storage
#### Test:
參考 <a href="#sol-tech" style="display:inline;color:var(--hmd-tw-text-default);">Reference 6~7</a>，實驗結果見<a href="https://docs.google.com/presentation/d/13GOQH7FGl2EIsEb6KN8DiyJmwuLhcxO-Xpqt-i-V7S0/edit#slide=id.p1" target="_blank">簡報投影片 - 1</a>。
- 先透過 ERC20、ERC1155 及 ERC4626，實作授權、代幣鎖、時間鎖和代理合約等功能，看 inputs 跟 outputs 如何設計，和區塊鏈上的資料如何變化

### (5) Contract D: To support paying back dividend in the future?
Right now, Contract D supports proactive calls from investors to exit from an investment. However, for long-term investors, it may not be sufficient, as they expect dividends during the time they hold the investment. So, we may need to support a function for the `creator` or `investee` to call to pay dividends to investors.

## References
<a id="econ-policy" href="#econ-policy">1. 關於私有資產保護與自由競爭制度，對經濟環境影響的研究和實證</a><br>
<a href="https://global.udn.com/global_vision/story/8663/8292959">&emsp;&nbsp;(1) 國家為何失敗？世界為何不平等？2024諾貝爾經濟學獎的當代啟示</a><br>
<a href="https://zh.m.wikipedia.org/zh-tw/%E5%AE%8B%E6%9C%9D#%E7%B6%93%E6%BF%9F">&emsp;&nbsp;(2) 宋朝的經濟政策</a><br>
<a href="https://zh.m.wikipedia.org/zh-tw/%E7%B9%94%E7%94%B0%E4%BF%A1%E9%95%B7#%E5%95%86%E6%A5%AD%E6%94%BF%E7%AD%96">&emsp;&nbsp;(3) 織田信長的商業政策</a><br>
<a id="cama" href="https://news.tvbs.com.tw/life/2631882" target="_blank">2. 我的咖啡勒！寄杯「剩362杯」　加盟店8月推買百送百半月後歇業</a><br>
<a id="enc-model" href="https://buidlerdao.notion.site/0c1c683e29af48c0a3134fbea9ded822">3. 区块链经济模型</a><br>
<a id="blkchain-tech" href="https://en.bitcoin.it/wiki/Script#Standard_Transaction_to_Bitcoin_address_(pay-to-pubkey-hash)" target="_blank">4. Standard Transaction to Bitcoin address</a><br>
<a href="https://tomni.notion.site/Buidler-DAO-89472b07caff4a5b9807d1e54117181f" target="_blank">5. Buidler DAO区块链底层技术系列课程    - -图灵完备与以太坊</a><br>
<a id="#sol-tech" href="https://www.wtf.academy/docs/solidity-103" target="_blank">6. Solidity 103 应用</a><br>
<a href="https://www.youtube.com/watch?v=fpA2yxLKU5o&list=PLHmOMPRfmOxQYDnXAc1hKY6ra4WDU8ZlM" target="_blank">7. 在 2022 年，我們該如何寫智能合約</a><br>
<a href="https://www.books.com.tw/products/E050031639?sloc=main" target="_blank">8. 實戰區塊鏈技術｜加密貨幣與密碼學, 王毅丞, 碁峰, 2021</a><br>
<a href="https://www.books.com.tw/products/0010803367?sloc=main" target="_blank">9. 白話區塊鏈, 蔣勇, 碁峰 2018</a><br>
<a href="https://www.books.com.tw/products/CN11399011?sloc=main" target="_blank">10. 區塊鏈技術指南, 鄒均, 機械工業, 2016</a>