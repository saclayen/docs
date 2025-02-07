---

copyright:
  years: 2015, 2016

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen:.screen}
{:codeblock:.codeblock}

# 使用 Insights for Weather REST API
{: #rest_apis}

*前次更新：2016 年 4 月 6 日*

您可以使用 [Insights for Weather REST API](https://twcservice.{APPDomain}/rest-api/){:new_window} 來擷取氣候資料。您可以測試 API 作業並立即檢視結果，以協助您更快速地建置應用程式。

從參考文件中，按一下**清單作業**以檢視每一個作業的詳細資料。在您指定參數並按一下**試試看！**後，系統可能會要求您提供認證。您必須提供來自 `VCAP_SERVICES` 環境變數的使用者名稱及密碼。開啟應用程式並按一下目錄中的**環境變數**，即可找到此資訊。

**附註：**每個區域各自獨立。您無法將在某個區域佈建給您的服務認證，在另一個區域中用來鑑別服務。若無法輸入適當的認證，則會導致回應內文中出現「未獲授權」訊息。

利用 Insights for Weather API，您可以透過緯度及經度座標提供地理定位來擷取天氣資料。您可以使用下列 API。

|**API**                                  |**說明**              |
|-----------------------------------------|-----------------------------|
|`GET /v2/forecast/daily/10day`           |傳回某個地理定位當天及未來 9 天的天氣預測。每日預測每個都包含白天預測及晚間預測。這些區段是 JSON 回應中的個別物件。在當地時間下午 3:00 之後，每日預測的白天預測資料就無法再使用。在當地時間下午 3:00 點時，您的應用程式應該不再顯示白天預測。|
|`GET /v2/forecast/hourly/24hour`         |傳回某個地理定位當天及未來 24 個小時的每小時預測。每小時預測資料可以包含每個位置的最多 24 小時預測。收到新資料時，您必須捨棄某個位置的所有先前每小時預測|
|`GET /v2/observations/current`           |傳回某個地理定位的目前天氣狀況。這些最近的觀測資料會保留在資料庫中，特定報告工作站最多保留 10 分鐘的資料，而每個工作站則最多保留 24 小時的觀測資料。會根據觀測資料的日期/時間戳記，以先進/先出方法（以最新的觀測資料輪換資料，並將最舊的觀測資料移至保存儲存體）不斷更新及取代最近的觀測資料。|
|`GET /v2/observations/timeseries/24hour` |傳回某個地理定位從目前日期和時間開始的目前觀測資料，以及最多 24 小時的過去觀測資料。天氣觀測資料收集自世界各地所部署的實際裝置，以及目前天氣觀測資料。|
*表 1. Insights for Weather API 摘要*

## 時間序列觀測資料
{: #time_series}

時間序列天氣觀測資料提供溫度、降雨、風、氣壓、能見度、紫外線 (UV) 以及其他觀測元素的相關資訊，包括觀測站、觀測日期/時間、天氣圖示代碼和詞組。時間序列觀測資料和目前觀測資料之間的不同之處在於觀測時段，這個導致產生一個以上的觀測資料集。

目前狀況是未使用時間參數下所要求之位置的最新觀測資料。會傳回一個資料集。時間序列觀測資料包含發生的過去觀測資料，最多包括所要求位置的前 24 小時資料。

最新的觀測資料會保留在主要資料庫中，特定報告工作站最多保留 48 小時（2 天）的資料。會根據觀測資料的日期/時間戳記，以先進/先出方法（以最新的觀測資料輪換資料，並將最舊的觀測資料移至保存儲存體）不斷更新及取代最近的觀測資料。任何工作站保留及提供的資料量可以多於 24 個個別觀測報告。觀測資料數目取決於其觀測資料類型。

## URL 建構
{: #url_construction}

Insights for Weather API 會使用一般 URL 結構和查詢參數來要求及過濾天氣資料。傳遞到 Insights for Weather API 的 URL 建構如下：

```
https://twcservice.mybluemix.net/api/weather/v2/<product group>/&format={format type}&geocode={latitude,longitude}&language={language code}&units={units code}

```

|**屬性**     |**說明**                                    |
|------------------|---------------------------------------------------|
|`hostname`        |受管理的 URL 路徑（例如 `https://twcservice.mybluemix.net:443/api/weather`）|
|`version`         |目前反覆運算值（例如 "v2"）|
|`product group`   |產品（例如 "observations" 或 "forecast"）|
|`geocode`         |選用的緯度和經度（例如 "45.4214,75.6919" 代表加拿大渥太華）。如果您提供地理編碼座標，則 API 會傳回最接近的可用位置的資料。句點用來作為小數點，而逗點則用來區隔緯度值和經度值。如果提供地理編碼，則會在回應的 meta 資料中傳回實際使用的緯度值和經度值。|
|`language`        |用來傳回回應的語言。預設值為 en-US。在回應的 meta 資料的 language 參數中，會傳回預設或所要求的翻譯語言。|
|`units`           |用來傳回回應的選用單位。API 支援 English (e)、Metric (m) 及 UK-Hybrid (h) 度量單位。如果您提供度量單位但未提供值，則 API 會以對應於語言碼的度量單位傳回資料。在回應的 meta 資料的 units 參數中，會傳回預設或所要求的度量單位。|
*表 2. URL 詳細資料*

## 度量單位
{: #units_measure}

當您使用 Insights for Weather API 時，不需要明確傳遞度量單位。這些 Insights for Weather API 在 URL 中預設為使用與語言碼相關聯的度量單位。不過，如果您想要提供不同於預設值的度量單位，則可以傳入度量單位以置換預設值。

* 若為 en-US 或 en，預設度量單位代碼為 English/Imperial。單位代碼為 "e"。
* 若為 en-GB，預設度量單位為 Hybrid-UK。單位代碼為 "h"。
* 若為其他項目，預設度量單位為 Metric。單位代碼為 "m"。

## 語言翻譯
{: #language_translation}

Insights for Weather API 翻譯了一些詞組和度量單位。當您格式化要求 URL 時，必須提供有效的語言。下列欄位已翻譯：

|**欄位**           |**說明**                                    |
|--------------------|---------------------------------------------------|
|`narrative`         |24 小時期間的敘述性預測|
|`dow`               |星期幾|
|`wind_phrase`       |說明 12 小時白天部分的風向和風速的詞組|
|`wdir_cardinal`     |以紅色標記表示的白天平均風向|
|`daypart_name`      |12 小時白天部分的名稱（不包含最初 48 小時中的白天名稱）|
|`temp_phrase`       |包含 12 小時預測期間預測到的高溫或低溫的簡短詞組|
|`shortcast`         |敘述性預測的可感測天氣縮寫部分|
|`long_daypart_name` |使用擴充格式的有效天氣預測的指定時間範圍。指定的時間範圍可以是 12 小時期間或 24 小時期間。|
|`golf_category`     |以適用於打高爾夫球之天氣狀況的詞組表示的高爾夫球指數種類|
|`phrase_nnchar`     |白天可感測天氣詞組|
|`lunar_phrase`      |月相的三個字元簡短代碼|
|`uv_desc`           |UV 指數說明，其提供與因曝露而造成皮膚傷害相關聯的風險等級來補充說明 UV 指數值|
|`sky_cover`         | 根據雲層遮蔽度的百分比說明天空遮蔽度|
|`ptend_desc`        | 過去 3 小時的氣壓趨勢敘述性文字|
*表 3. 翻譯的回應欄位*

## 處理 API 回應中的空值或遺漏資料欄位
{: #handling_null_or_missing}

如果資料欄位因為資料無法使用而是空值，則 Insights for Weather API 會傳回以「空值」一詞標記的適當欄位，或根本不會傳回欄位。

## 處理錯誤
{: #handling_errors}

下列錯誤碼適用於所有 API：

|**錯誤** |**說明**                                    |
|----------|---------------------------------------------------|
|400       |錯誤的要求。由於語法形態異常，伺服器無法瞭解要求。這個錯誤碼是針對所有 API 實作。如果提供的任何參數無效，API 會拒絕此要求。|
|401       |未獲授權。要求需要鑑別。|
|403       |已禁止。伺服器已瞭解要求，但拒絕完成它。|
|404       |找不到。如果 API 要求中沒有必要的參數，則會傳回包含 404 錯誤碼的 MissingParameterException 錯誤。|
|405       |不容許方法。例如，傳送 POST 而非 GET。|
|406       |無法接受。例如，不接受 gzip 壓縮回應。|
|408       |要求逾時。用戶端未在伺服器願意等待的時間內產生要求。|
|500       |內部伺服器錯誤。由於伺服器遭遇到非預期的狀況，所以無法完成此要求。|
|502-504   |「服務無法使用」或「閘道」問題。如果服務暫時無法使用，則會傳回這些錯誤碼。|
*表 4. 錯誤回應碼*

發生錯誤時的回應一律相同。可以在一個回應中傳回多個錯誤碼。例如，可能傳回的 JSON 錯誤回應如下：

```
{
  metadata: {
    transaction_id: "1411496413365:-1880721071"
  },
  success: false,
  errors: [
    {
      error: {
        code: "NDF-0001",
        message: "There was no data found for your product query."
      }
    }
}
```
