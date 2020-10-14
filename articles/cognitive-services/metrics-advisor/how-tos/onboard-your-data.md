---
title: 將您的資料摘要上線至計量 Advisor
titleSuffix: Azure Cognitive Services
description: 如何開始將您的資料摘要上線至計量 Advisor。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: fe3b87c733f54d8bd52c4d973977e3c8cbfefe19
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043188"
---
# <a name="how-to-onboard-your-metric-data-to-metrics-advisor"></a>How to：將您的計量資料上架到計量 Advisor

您可以使用此文章來瞭解如何將您的資料上架到計量審查程式。 

## <a name="data-schema-requirements-and-configuration"></a>資料結構描述需求和組態

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

## <a name="avoid-loading-partial-data"></a>避免載入部分資料

部分資料是由於計量建議程式和資料來源中儲存的資料不一致所造成。 當資料來源在計量審查程式完成提取資料之後更新時，就會發生這種情況。 計量審查程式只會從指定的資料來源提取資料一次。

例如，如果計量已上線至計量審查程式以進行監視。 計量審查程式成功在時間戳記 A 抓取計量資料，並對其執行異常偵測。 但是，如果該特定時間戳記 A 的度量資料在內嵌資料之後已重新整理。 不會取出新的資料值。

您可以嘗試 [回填](manage-data-feeds.md#backfill-your-data-feed) 歷程記錄資料 (稍後所述) 來減少不一致的情況，但如果已觸發這些時間點的警示，則不會觸發新的異常警示。 此程式可能會在系統中新增額外的工作負載，且不會自動進行。

為了避免載入部分資料，我們建議兩種方法：

* 在一個交易中產生資料：

    確定在相同時間戳記上，所有維度組合的計量值都會儲存在一個交易中的資料來源。 在上述範例中，等到所有資料來源的資料都已就緒，然後將它載入至一個交易中的計量 Advisor。 計量審查程式可以定期輪詢資料摘要，直到資料成功 (或部分) 抓取為止。

* 針對內嵌 **時間位移** 參數設定適當的值，以延遲資料內嵌：

    設定資料摘要的內嵌 **時間位移** 參數，以延遲內嵌，直到資料完全備妥為止。 對於不支援諸如 Azure 資料表儲存體等交易的某些資料來源，這會很有用。 如需詳細資料，請參閱 [advanced settings](manage-data-feeds.md#advanced-settings) 。

## <a name="add-a-data-feed-using-the-web-based-workspace"></a>使用 web 工作區新增資料摘要

登入您的計量 Advisor 入口網站並選擇您的工作區之後，請按一下 [ **開始**使用]。 然後，在工作區的主頁面上，按一下左側功能表中的 [ **加入資料** 摘要]。

### <a name="add-connection-settings"></a>新增連接設定

接下來，您將輸入一組參數來連接您的時間序列資料來源。 
* **來源類型**：儲存時間序列資料的資料來源類型。
* **細微性**：時間序列資料中連續資料點之間的間隔。 計量顧問目前支援：每年、每月、每週、每日、每小時和自訂。 自訂選項支援的最小間隔為60秒。
  * **秒**： *GranularityName* 設定為 *自訂*的秒數。
* 在** (UTC) 之後內嵌資料**：資料內嵌的基準開始時間。 *startOffsetInSeconds* 通常用來新增位移，以協助資料一致性。

接下來，您必須指定資料來源的連接資訊，以及用來將資料轉換成所需架構的自訂查詢。 如需其他欄位和連接不同資料來源類型的詳細資訊，請參閱 [從不同的資料來源加入資料](../data-feeds-from-different-sources.md)摘要。

[!INCLUDE [query requirements](../includes/query-requirements.md)]

### <a name="verify-and-get-schema"></a>驗證並取得架構

設定連接字串和查詢字串之後，請選取 [ **驗證] 並取得架構** 以驗證連接，然後執行查詢以從資料來源取得資料架構。 此作業通常需要幾秒鐘，視您的資料來源連線而定。 如果此步驟發生錯誤，請確認：

* 您的連接字串和查詢是正確的。
* 如果有防火牆設定，您的 Metrics Advisor 執行個體就能夠連線到資料來源。

### <a name="schema-configuration"></a>結構描述組態

載入資料架構之後，請選取適當的欄位。

如果省略資料點的時間戳記，則當資料點改為內嵌時，計量審查程式將會使用時間戳。 針對每個資料摘要，您最多可以指定一個資料行做為時間戳記。 如果您收到訊息，指出無法將資料行指定為時間戳記，請檢查您的查詢或資料來源，以及查詢結果中是否有多個時間戳記，而不只是預覽資料。 執行資料內嵌時，計量審查程式每次只能使用一個區塊 (例如一天、一小時-根據特定來源的時間序列資料的資料細微性) 。

|選取項目  |描述  |附註  |
|---------|---------|---------|
| **顯示名稱** | 要在您的工作區中顯示的名稱，而不是原始資料行名稱。 | |
|**Timestamp**     | 資料點的時間戳記。 如果省略，則在內嵌資料點時，Metrics Advisor 會使用時間戳記。 針對每個資料摘要，您最多可以指定一個資料行做為時間戳記。        | 選擇性。 最多只能指定一個資料行。 如果您 **無法將資料行指定為時間戳記** 錯誤，請檢查您的查詢或資料來源是否有重複的時間戳記。      |
|**Measure**     |  資料摘要中的數值。 您可以針對每個資料摘要指定多個量值，但至少應選取一個資料行做為量值。        | 應指定至少一個資料行。        |
|**維度**     | 類別值。 不同值的組合會識別特定的單一維度時間序列，例如：國家/地區、語言、租用戶。 您可以選取零個或多個資料行做為維度。 注意：選取非字串資料行做為維度時，請務必謹慎。 | 選擇性。        |
|**忽略**     | 略過選取的資料行。        | 選擇性。 請參閱以下文字。       |

如果您想要忽略資料行，我們建議您更新查詢或資料來源，以排除這些資料行。 您也可以使用 [略過資料 **行** ] 來略過資料行，然後在特定資料行上 **略** 過。 如果資料行應該是維度，並錯誤地設定為 *忽略*，則計量審查程式最後可能會擷取部分資料。 例如，假設查詢中的資料如下所示：

| 資料列識別碼 | 時間戳記 | 國家/地區 | 語言 | Income |
| --- | --- | --- | --- | --- |
| 1 | 2019/11/10 | 中國 | ZH-CN | 10000 |
| 2 | 2019/11/10 | 中國 | ZH-TW | 1000 |
| 3 | 2019/11/10 | 美國 | ZH-CN | 12000 |
| 4 | 2019/11/11 | 美國 | ZH-TW | 23000 |
| ... | ...| ... | ... | ... |

如果 *Country* 是維度且 *語言* 設定為 *忽略*，則第一個和第二個數據列將會有相同的維度。 計量審查程式將會任意使用兩個數據列中的一個值。 在此情況下，度量 Advisor 不會匯總資料列。

### <a name="automatic-roll-up-settings"></a>自動積存設定

> [!IMPORTANT]
> 如果您想要啟用根本原因分析和其他診斷功能，則必須設定「 **自動匯總」設定** 。 啟用之後，就無法變更自動匯總設定。

計量建議程式可以在內嵌期間，自動執行每個維度之 SUM、MAX、MIN) 的匯總 (，然後建立將用於根案例分析和其他診斷功能的階層。 

請考慮下列案例：

* *我不需要包含資料的匯總分析。*

    您不需要使用計量建議程式匯總。

* *我的資料已經匯總，維度值是以下列專案表示： Null 或空白 (預設值) 、僅限 Null、其他。*

    此選項表示計量建議程式不需要匯總資料，因為資料列已加總。 例如，如果您只選取 [ *Null*]，則下列範例中的第二個數據列會顯示為所有國家/地區和語言 *en-us*的匯總。但是，第四個數據列的 *國家/地區* 有空白值，則會顯示為一般資料列，這可能表示未完成的資料。
    
    | 國家/地區 | 語言 | Income |
    |---------|----------|--------|
    | 中國   | ZH-CN    | 10000  |
    |  (Null)   | ZH-TW    | 999999 |
    | 美國      | ZH-TW    | 12000  |
    |         | ZH-TW    | 5000   |

* *我需要計量建議程式，藉由計算總和/最大/最小/平均/計數來匯總我的資料，並將其代表 <some string>*

    某些資料來源（例如 Cosmos DB 或 Azure Blob 儲存體）不支援特定的計算，例如 *group by* 或 *cube*。 計量建議程式會提供匯總選項，以在內嵌期間自動產生資料 cube。
    此選項表示您需要計量建議程式使用您所選取的演算法來計算匯總，並使用指定的字串表示計量建議程式中的匯總。 這不會變更您資料來源中的任何資料。
    例如，假設您有一組時間序列，代表維度 (Country，Region) 的銷售計量。 針對指定的時間戳記，它看起來可能如下所示：


    | Country       | 區域           | Sales |
    |---------------|------------------|-------|
    | Canada        | Alberta          | 100   |
    | Canada        | British Columbia | 500   |
    | 美國 | Montana          | 100   |


    啟用 with *Sum*的自動匯總之後，計量建議程式會計算維度組合，並在資料內嵌期間加總度量。 結果可能是：

    | Country       | 區域           | Sales |
    | ------------ | --------------- | ---- |
    | Canada        | Alberta          | 100   |
    | NULL          | Alberta          | 100   |
    | Canada        | British Columbia | 500   |
    | NULL          | British Columbia | 500   |
    | 美國 | Montana          | 100   |
    | NULL          | Montana          | 100   |
    | NULL          | NULL             | 700   |
    | Canada        | NULL             | 600   |
    | 美國 | NULL             | 100   |

    `(Country=Canada, Region=NULL, Sales=600)` 表示加拿大 (所有區域的銷售總和) 為600。

    以下是 SQL 語言的轉換。

    ```mssql
    SELECT
        dimension_1,
        dimension_2,
        ...
        dimension_n,
        sum (metrics_1) AS metrics_1,
        sum (metrics_2) AS metrics_2,
        ...
        sum (metrics_n) AS metrics_n
    FROM
        each_timestamp_data
    GROUP BY
        CUBE (dimension_1, dimension_2, ..., dimension_n);
    ```

    使用「自動匯總」功能之前，請先考慮下列事項：

    * 如果您想要使用 *SUM* 來匯總您的資料，請確定您的計量在每個維度中都是加法。 以下是 *非加法* 類計量的一些範例：
      * 以分數為基礎的度量。 這包括比率、百分比等等。例如，您不應該新增每個狀態的失業率來計算整個國家/地區的失業率。
      * 維度中的重迭。 例如，您不應該將中的人員人數新增至每個運動，以計算喜歡運動的人數，因為兩者之間的重迭，一個人可以喜歡多個運動。
    * 為了確保整個系統的健康狀態，cube 的大小受到限制。 目前的限制為1000000。 如果您的資料超過該限制，該時間戳記的內嵌將會失敗。

## <a name="advanced-settings"></a>進階設定

有幾個 advanced 設定可讓您以自訂的方式啟用資料內嵌，例如指定內嵌位移或平行存取。 如需詳細資訊，請參閱資料摘要管理文章中的「 [advanced 設定](manage-data-feeds.md#advanced-settings) 」一節。

## <a name="specify-a-name-for-the-data-feed-and-check-the-ingestion-progress"></a>指定資料摘要的名稱，並檢查內嵌進度
 
為資料摘要提供自訂名稱，這會顯示在您的工作區中。 然後按一下 [ **提交**]。 在 [資料摘要詳細資料] 頁面中，您可以使用內嵌進度列來查看狀態資訊。

:::image type="content" source="../media/datafeeds/ingestion-progress.png" alt-text="內嵌進度列" lightbox="../media/datafeeds/ingestion-progress.png":::


若要檢查內嵌失敗的詳細資料： 

1. 按一下 [ **顯示詳細資料**]。
2. 按一下 [ **狀態** ]，然後選擇 [ **失敗** ] 或 [ **錯誤**]。
3. 將滑鼠停留在失敗的內嵌上方，並查看顯示的詳細資料訊息。

:::image type="content" source="../media/datafeeds/check-failed-ingestion.png" alt-text="內嵌進度列":::

*失敗*狀態表示此資料來源的內嵌將于稍後重試。
*錯誤*狀態表示「計量顧問」不會重試資料來源。 若要重載資料，您需要手動觸發回填/重載。

您也可以按一下 [重新整理 **進度**]，重載內嵌的進度。 資料內嵌完成之後，您就可以按一下 [計量]，並檢查異常偵測結果。

## <a name="next-steps"></a>後續步驟
- [管理您的資料摘要](manage-data-feeds.md)
- [不同資料來源的組態](../data-feeds-from-different-sources.md)
- [設定計量和微調偵測組態](configure-metrics.md)
