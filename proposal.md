# 以資產所有權轉換實現虛擬幣的交易特性

組員: 
R13922176 張智奇、P13922007 王信璋、P12922003 陳建宏、T13902113 李駿傑、R12521616 陳俊達

---
  
## 1. Introduction

在一個法治能被落實的政治實體中，私有資產將被一個可信的體制保護，但是體制的能力是有限的，例如政府雖然可透過法幣儲存購買力，但無法保證它最終能兌現的效益(通膨)，更何況對於一個難以被信任的政治體制，例如極權國家或動盪中的政治環境。

從穩定幣或其他虛擬貨幣的經驗看來，需求方要的是一個能保障資產或令資產增長的系統，實際上就是一個風險趨避的行為，而供給方藉由背景或財力提供保障/安全感，並將所承擔的風險轉移到實體貨幣的投資中，這樣的供需結構如同期貨，雙方藉由交換資產各取所需，但目前虛擬幣的價值也僅止於多種貨幣間交換的媒介，功能上離真正的貨幣仍有一段距離。

我們希望透過期貨思維設計商業模型，賦予虛擬幣在實體經濟中的交易特性，提供需求方確保其購買力效益(安全感)，也讓供給方能有利可圖，如需求估計(成本控制)、購買力(資源)競爭、獲得投資本金等，也希望在模型運行的過程中，活化被過度囤積的實體貨幣，改善經濟環境和財富分配。

為此，我們期望藉由區塊鏈建立一個可信的私有資產所有權交易系統，記錄包括個人擁有的虛擬貨幣及未兌現商品/服務的契約，本系統目的是活絡實體資源交易，為了讓市場活絡需要多人參與協作(拿出資本加入市場)更新交易的資訊，而其中的開放資料也可為政府政策所用，如稅收的評估依據、政策的研擬等。

總結如下:<br>
**道 -- 賦予虛擬幣交易屬性，促進經濟活絡<br>
法 -- 各取所需，以利導勢<br>
術 -- 賽局設計與期貨思維<br>
器 -- 區塊鏈及相關技術**

## 2. Target audience

以下是可從本區塊鏈提供的操作中，獲取利益的對象:

| 對象               | 操作 | 預期利益 |
|:----------------: | :----: | :----: |
| 生產者             | <div style="text-align: left">1.發行合約幣<br>2.發行商品/服務兌換券</div> | <div style="text-align: left">1.需求估計(成本控制)<br>2.競爭購買力(稀缺資源)的管道<br>3.獲得投資本金(風險轉移)</div> |
| 消費者             | <div style="text-align: left">1.購買商品/服務兌換券<br>2.交換商品/服務兌換券</div> | <div style="text-align: left">1.保存購買力(抗通膨)<br>2.降低購買成本(接近成本價)<br>3.獲得交易流通性</div> |
| 政策制定者     | 取得公開資料 | <div style="text-align: left">1.稅收評估的依據<br>2.政策研擬的參考</div>  |


## 3. Technology, Service, or Product

### (1) 基本定義

- 資產類型:

| 名稱 | 說明 |
| :--------: | :--------: | 
| 原生幣(ValueFlow/VFlow coin) | <div style="text-align: left">1.OurChain 的原生幣<br>2.礦工的收入來源<br>3.加入本區塊鏈的門票</div> |
| 合約幣(contract coin) | 透過發行合約幣形成自主的商業模式 |
| 兌換券(contract token) | 透過發行兌換券將商品/服務數位化 |

- <span id="rate_def">交易類型與收益:</span>
系統會依據節點收錄進區塊的交易類型發放相應的獎勵，初始定義如下表，目的是鼓勵節點對特定交易類型的處理，系統會視目前交易池中各類型的比例做調整。
 
| 交易類型  | 基本獎勵(單位:UC) | 附加獎勵 | 相關邏輯 |
| :--------: | :--------: | :--------: | :--------: |
| 原生幣相關     | 1     |    | OurChain 原生邏輯     |
| 合約幣相關     | 1     |    | <a href="#sec3-3" style="display:inline;color:var(--hmd-tw-text-default);">3.(3)</a> |
| 兌換券相關     | 1     |    | <a href="#sec3-4" style="display:inline;color:var(--hmd-tw-text-default);">3.(4)</a> |
| 交換兌換券     | 1     | <div style="text-align: left">參與寄賣和搓合的 addresses 各可獲得 1/3 UC</div> | <a href="#sec3-5" style="display:inline;color:var(--hmd-tw-text-default);">3.(5)</a> |
 
> [註1] 1 Unit coin(UC) $\equiv \frac{1\text{ VFlow coin}}{1000\text{ transactions}}$<br>
  [註2] 依照 OurChain 原生邏輯控制 VFlow coin 的總數量，視情況做調整

###  <span id="sec3-2">(2) 物件結構</span> 
```c++=
#include <string> 
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

###  <span id="sec3-3">(3) 發行合約幣(contract coin)的智能合約</span>
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
       (2) 將產生的 contract coin 存到 creator 的 address 中
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
       取得 VFlow coin, creator_address 取得 contract coin
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

/* 投資: 配合線下簽訂的具商業機密的實體合約使用
    1. 檢查: 
       (1) 執行 is_contract_available()
       (2) supporter.address 中是否有足夠的 contract coin 
    2. 當 1.(1) & 1.(2) 為 true 時, 啟動投資轉換資產所有權, 即 creator_address 
       取得 supporter.address 的 contract coin
   Inputs: 
     - OwnerInfo supporter: supporter 的個人資訊
     - float contract_coin_amount: supporter 預計投資的 contract coin 的數量
*/
void invest(OwnerInfo supporter, float contract_coin_amount);

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
    1. 檢查 
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
    1. 檢查 
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

###  <span id="sec3-4">(4) 發行商品/服務兌換券(contract token)的智能合約</span>
- 發行者(creator)必須要質押一定量的 VFlow coin 才能發行 contract token，並於合約到期後才能領出
- 發行者得自訂質押 VFlow coin 的數量，但該數量會與退款成本直接相關
- 合約運作期間，消費者可使用合約指定的虛擬幣，如 VFlow coin 或 contract coin 購買 contract token，並可透過退款(refund)取回資金保障權益
- 合約運作期間，消費者可使用合約指定的虛擬幣或 contract token 進行消費(consume)並取得商品或服務
```c++=
#include <string> 
#include <map>
#include <time.h>
using namespace std; 

string creator_address; // 記錄 creator 的 address
string deposit_address; // 質押 VFlow coin 的 address
string token_name;      // contract token 的名稱
/* currency_price_maps 為不同 currency 的購買數量與單價的對應表, 
ex. 以不同 currency 購買 1/5/10 個 contract token 對應的單價
 { 
    {"VFlow", { 1., { {1, 1.},{5, 1.},{10, 0.8} } } },   //用 VFlow coin
    {"ACoin": { 0.9, { {1, 0.9},{5, 0.8},{10, 0.7} } } } //用 ACoin 
 }
*/
map<string, CurrencyTokenInfo> currency_price_maps; 
timespec end_time;      // 合約期限
bool is_available;      // 合約是否有效

/* 建構子: 
    1. 檢查 creator.address 是否已有該 contract token 的所有權, 
       若是, 則根據交易找到運行節點並 call get_contract_info() 更新本地節點
       若否, 則執行步驟 2~6
    2. 檢查 creator.address 中是否有足夠質押的 VFlow coin, 有則啟動合約 
    3. 啟動合約後，
       (1) 將質押的 VFlow coin 轉存到 deposit_address 中
       (2) 將產生的 contract token 存到 creator 的 address 中 
    4. 利用 guaranty_coin_amount 與 token_info.amount 計算 
       currency_price_maps["VFlow"].amount_price_map[1]
    5. 利用 period 計算 end_time
    6. 設定 is_available := true, token_name := token_info.name
   Inputs: 
     - OwnerInfo creator: 啟動合約的個人資訊
     - float guaranty_coin_amount: 質押 VFlow coin 的數量
     - TokenInfo token_info: 預期發行的合約代幣資訊
     - int period: 合約期間, 以天為最小單位
*/
void constructor(OwnerInfo creator, float guaranty_coin_amount, TokenInfo token_info, int period);

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
    2. 當 1.(1) & 1.(2) & 1.(3) & 1.(4) 為 true 時, 根據 currency_price_maps 中的定義
       轉換資產所有權, 即 buyer.address 取得 contract token, creator_address 
       取得對應的 currency coin
   Inputs: 
     - OwnerInfo buyer: buyer 的個人資訊
     - string currency: 預計用來購買 token 的 coin name
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

/* 退款: 單筆
    1. 檢查: 
       (1) 執行 is_contract_available()
       (2) buyer.address 中是否有 contract token 
       (3) deposit_address 中是否有足夠的 VFlow coin
    2. 當 1.(1) & 1.(2) & 1.(3) 為 true 時, 以 
       currency_price_maps["VFlow"].amount_price_map[1] 為單價計算退款, 
       直到退貨數 = min{amount, buyer.address 中 token 數量} 為止  
    3. 轉換資產所有權, 即 buyer.address 取得 VFlow coin(退款), creator_address 
       取得對應的 contract token
   Inputs: 
     - OwnerInfo buyer: buyer 的個人資訊
     - int amount: buyer 欲退貨數量
*/
void refund(OwnerInfo buyer, int amount);

/* 退款: 憑單
    1. 檢查: 
       (1) 執行 is_contract_available()
       (2) buyer.address 中是否有剩餘的 contract token 
       (3) deposit_address 中是否有足夠的 VFlow coin
    2. 當 1.(1) & 1.(2) & 1.(3) 為 true 時, 根據 transaction_list 取得每筆交易的 
       token_info 及對應 currency_price_maps 的購買單價(轉換為 VFlow 計價), 
       從單價高的開始計算退款, 直到退貨數 = min{amount, buyer.address 中 token 數量} 為止  
    3. 轉換資產所有權, 即 buyer.address 取得 VFlow coin(退款), creator_address 
       取得對應的 contract token
   Inputs: 
     - OwnerInfo buyer: buyer 的個人資訊
     - string transaction_list[]: buyer 的購買明細
     - int amount: buyer 欲退貨數量
*/
void refund(OwnerInfo buyer, string transaction_list[], int amount);

/* 增資: 
    1. 檢查: 
       (1) 執行 is_contract_available()
       (2) creator.address 是否為 creator_address
       (3) creator.address 中是否有足夠的 VFlow coin 
    2. 當 1.(1) & 1.(2) & 1.(3) 為 true 時, 啟動增資轉換資產所有權, 即 creator.address 
       取得 contract token, deposit_address 取得以單價
       currency_price_maps["VFlow"].amount_price_map[1] 計算的 VFlow coin
   Inputs: 
     - OwnerInfo creator: creator 的個人資訊
     - int contract_token_amount: creator 預計增資的 contract token 的數量
*/
void increase(OwnerInfo creator, int contract_token_amount);

/* 投資: 配合線下簽訂的具商業機密的實體合約使用
    1. 檢查: 
       (1) 執行 is_contract_available()
       (2) supporter.address 中是否有足夠的 contract token 
    2. 當 1.(1) & 1.(2) 為 true 時, 啟動投資轉換資產所有權, 即 creator_address 
       取得 supporter.address 的 contract token
   Inputs: 
     - OwnerInfo supporter: supporter 的個人資訊
     - int contract_token_amount: supporter 預計投資的 contract token 的數量
*/
void invest(OwnerInfo supporter, int contract_token_amount);

/* 加盟: 等同於新增一種支付方式, 配合線下簽訂的具商業機密的實體合約使用
    1. 檢查: 
       (1) 執行 is_contract_available()
       (2) creator.address 是否為 creator_address 
    2. 當 1.(1) & 1.(2) 為 true 時, 根據 currency_token_info 擴充或更新 
       currency_price_maps, 但無法變更
       currency_price_maps["VFlow"].amount_price_map[1] 的值
   Inputs: 
     - OwnerInfo creator: creator 的個人資訊
     - CurrencyTokenInfo currency_token_info: 
*/
void franchise(OwnerInfo creator, CurrencyTokenInfo currency_token_info);

/* 取得合約狀態:
   Outputs: (JSON string){
               (bool)is_available,
               (string)token_name,
               (float)deposit_address 的 VFlow coin amount, 
               (float)creator_address 的 contract token amount,
               (JSON string)currency_price_maps,
               (int)end_time(天)
            }
*/
string get_contract_info();

/* 延長合約:
    1. 檢查 
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
    1. 檢查 
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

###  <span id="sec3-5">(5) 交換兌換券的智能合約</span>
- 各種 token 的持有者都能透過寄賣池(consignment pool)，交易 token
- 節點經手的寄賣(consign)和搓合(matching)的交易，若最終得到確認上鏈，皆可以根據 <a href="#rate_def" style="display:inline;color:var(--hmd-tw-text-default);">「3.(1) 交易類型與收益」</a> 的定義，獲得 VFlow coin 的報酬
```c++=
#include <string> 
#include <time.h>
using namespace std; 

string consignment_address; // 寄賣的 address
string node_address;        // 運行智能合約節點的 address

/* 建構子: 
    1. 產生寄賣的 address, 每個節點可以不同 
    2. 儲存節點的 address, 即 node_address := node_info.address
   Inputs: 
     - OwnerInfo node_info: 運行智能合約節點的個人資訊
*/
void constructor(OwnerInfo node_info);

/* 主動交換: 
    1. 檢查: 
       (1) owner_1.address 中是否有足夠的 token 1 
       (2) owner_2.address 中是否有足夠的 token 2 
       (3) 若 difference 不為 NULL, 則檢查 owner_1.address 中是否有足夠的 
           difference.amount
    2. 當 1.(1) & 1.(2) & 1.(3) 為 true 時, 轉換資產所有權, 即 owner_1.address 取得 
       token 2, owner_2.address 取得 token 1, 若 difference 不為 NULL, 則 
       owner_2.address 再取得 currency coin
   Inputs: 
     - OwnerInfo owner_1: owner_1 的個人資訊
     - TokenInfo token_info_1: owner_1 要換出的 token 資訊
     - OwnerInfo owner_2: owner_2 的個人資訊
     - TokenInfo token_info_2: owner_2 要換出的 token 資訊 
     - CurrencyInfo difference: owner_1 要補給 owner_2 的差價(如果有)
*/
void exchange(OwnerInfo owner_1, TokenInfo token_info_1, OwnerInfo owner_2, TokenInfo token_info_2, CurrencyInfo difference);

/* 寄賣: 被動交換
    1. 檢查 consignor.address 中是否有足夠的 token
    2. 當 1 為 true 時, 轉換資產所有權, 即 consignment_address 在寄賣期間取得 
       consignor 的 token 的所有權
   Inputs: 
     - OwnerInfo consignor: consignor 的個人資訊
     - TokenInfo token_info: consignor 寄賣的 token 資訊
     - CurrencyInfo difference: consignor 要補給對方的差價(如果有)
     - int period: 寄賣期間, 以天為最小單位
*/
void consign(OwnerInfo consignor, TokenInfo token_info, CurrencyInfo difference, int period);

/* 搓合: 
    1. 檢查:
        (1) transaction_id_1 中的資產所有權是否還未轉換
        (2) transaction_id_2 中的資產所有權是否還未轉換
    2. 轉換資產所有權, 即 
        (1) transaction_id_1 中的 consignor 取得 transaction_id_2 中的
            consignment_address 擁有的 token 及 difference(如果有)
        (2) transaction_id_2 中的 consignor 取得 transaction_id_1 中的 
            consignment_address 擁有的 token 及 difference(如果有)
    3. 記錄 transaction_id_1、transaction_id_2 中的 consignment_address, 
       以及搓合人(node)的 consignment_address 於搓合交易中
   Inputs: 
     - string transaction_id_1: 寄賣交易 1
     - string transaction_id_2: 寄賣交易 2  
*/
void matching(string transaction_id_1, string transaction_id_2);

/* 取得節點寄賣資產: 回傳未過期的寄賣資產
    1. 檢查 consignment_address 寄賣的資產是否過期
    2. 若過期, 則執行 cancel() ; 若未過期, 則加入 outputs 清單中
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
    1. 檢查 owner.address 是否與 transaction_list 中每筆交易的 
       consignor.address 相同
    2. 若有檢查結果為 true 的 transaction, 則轉換其資產所有權, 即 consignor 取得 
       consignment_address 寄賣的 token 的所有權
   Inputs: 
     - OwnerInfo owner: owner 的個人資訊
     - string transaction_list[]: 寄賣交易明細
*/
void cancel(OwnerInfo owner, string transaction_list[]);

/* 取消寄賣: 被動, 當節點發現寄賣的 tokens 過期時呼叫
    1. 檢查 node.address 是否為 node_address 
    2. 當 1 為 true 時, 轉換 transaction_list 中每筆 transaction 的資產所有權, 
       即 consignor 取得 consignment_address 寄賣的 token 的所有權
   Inputs: 
     - OwnerInfo node: node 的個人資訊
     - string expired_transaction_list[]: 過期的寄賣交易明細
*/
void cancel(OwnerInfo node, string expired_transaction_list[]);

/* 取得寄賣收入: 
    1. 檢查 node.address 是否為 node_address 
    2. 當 1 為 true 時, 轉換資產所有權, 即 node_address 取得 consignment_address 
       的 VFlow coin 
   Inputs: 
     - OwnerInfo node: node 的個人資訊
*/
void withdraw(OwnerInfo node);
```

## 4. What makes this system superior, or better than current alternatives, or competitors?

- 提供退款保障確保消費者權益，可避免出現如 <a href="#cama" style="display:inline;color:var(--hmd-tw-text-default);">Reference 1</a> 的消費爭議，導致私有資產蒙受損失
- 目前密碼貨幣大多是用在純金融交易上，藉由加入能夠交易實體資源的操作，可以讓區塊鏈變成一套更能融入人類生活的制度
- 從實體資源層次處理經濟問題，相比於純粹的貨幣政策來說，更能達到預期的功效
- 比起單純為了謀求個人資產增值的密碼貨幣體系，本系統因具有穩定經濟、社會等效用，具有與政府談判的籌碼

## 5. Expected outcomes

- 透過活化被過度囤積的實體貨幣並賦予虛擬幣交易特性等操作，增加實體資源的交易效率，進而改善經濟環境
- 藉由區塊鏈開闢一個相對自由競爭的市場，使財富分配能夠被後繼有能者參與，減緩財富分配不均導致的政治及社會問題

## References
<a id="cama" href="https://www.ctwant.com/article/365562/" target="_blank"><span>1. cama加盟店推「咖啡寄杯優惠」半個月後突歇業！他剩362杯求償無門</span></a><br>
<a href="https://www.books.com.tw/products/E050031639?sloc=main" target="_blank"><span>2. 實戰區塊鏈技術｜加密貨幣與密碼學, 王毅丞, 碁峰, 2021</span></a><br>
<a href="https://www.books.com.tw/products/0010803367?sloc=main" target="_blank"><span>3. 白話區塊鏈, 蔣勇, 碁峰 2018</span></a><br>
<a href="https://www.books.com.tw/products/CN11399011?sloc=main" target="_blank"><span>4. 區塊鏈技術指南, 鄒均, 機械工業, 2016</span></a>