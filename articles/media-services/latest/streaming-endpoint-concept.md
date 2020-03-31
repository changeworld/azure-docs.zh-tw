---
title: 流式處理終結點（原點）
titleSuffix: Azure Media Services
description: 瞭解流式處理端點 （原點），這是一種動態打包和流式處理服務，可直接將內容傳遞到用戶端播放機應用或內容交付網路 （CDN）。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: 1a2a370ac92ea3edf925d97af6f5e721c79d0d2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529691"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Azure 媒體服務中的流式處理終結點（原點）

在 Microsoft Azure 媒體服務中，[流式處理終結點](https://docs.microsoft.com/rest/api/media/streamingendpoints)表示動態（即時）打包和源服務，該服務可以使用一種常見的流媒體協議 （HLS 或 DASH） 將即時和按需內容直接傳遞到用戶端播放機應用。 此外，**流式處理終結點**為行業領先的 DRM 提供動態（及時）加密。 

當您建立媒體服務帳戶時，系統會為您建立**預設**串流端點 (處於已停止狀態)。 無法刪除**預設**的流式處理終結點。 可以在帳戶下創建更多流式處理終結點（請參閱[配額和限制](limits-quotas-constraints.md)）。

> [!NOTE]
> 若要開始串流處理視訊，您必須啟動要作為視訊串流處理來源的**串流端點**。
>
> 僅當流式處理終結點處於運行狀態時，才會對您收費。

請確保還要查看[動態打包](dynamic-packaging-overview.md)主題。 

## <a name="naming-convention"></a>命名慣例

流 URL 的主機名稱格式是： `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`，`servicename`其中 = 流式處理終結點名稱或即時事件名稱。

使用預設流式處理終結點時`servicename`，省略了 URL，因此`{accountname}-{regionname}.streaming.azure.net`URL 為： 。

### <a name="limitations"></a>限制

* 流式處理終結點名稱的最大值為 24 個字元。
* 名稱應遵循此[RegEx](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference)模式： `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`。

## <a name="types"></a>型別

有兩種**流式處理終結點**類型：**標準**（預覽）和**高級**。 類型會根據您為串流端點配置的縮放單位數 (`scaleUnits`) 來定義。

類型描述如下表所示：

|類型|縮放單位|描述|
|--------|--------|--------|  
|**標準**|0|預設流式處理終結點是**標準**類型，可以通過調整`scaleUnits`將其更改為高級類型。|
|**溢價**|>0|**高級**流式處理端點適用于高級工作負載，並提供專用且可擴展的頻寬容量。 通過調整`scaleUnits`（流式處理單位）移動到**高級**類型。 `scaleUnits` 提供您專用的輸出容量，您可以透過每次增量 200 Mbps 的方式來購買。 使用**高級**類型時，每個啟用的單元都會為應用提供額外的頻寬容量。 |

> [!NOTE]
> 對於希望向大型互聯網受眾提供內容的客戶，我們建議您在流式處理終結點上啟用 CDN。

有關 SLA 資訊，請參閱[定價和 SLA](https://azure.microsoft.com/pricing/details/media-services/)。

## <a name="comparing-streaming-types"></a>比較串流類型

功能|標準|Premium
---|---|---
Throughput |高達 600 Mbps，在使用 CDN 時可提供更高的有效輸送量。|每個串流單位 (SU) 200 Mbps。 在使用 CDN 時，可以提供更高的有效輸送量。
CDN|Azure CDN、協力廠商 CDN 或無 CDN。|Azure CDN、協力廠商 CDN 或無 CDN。
按比例計費| 每日|每日
動態加密|是|是
動態封裝|是|是
調整|自動相應增加至目標輸送量。|其他 S
IP 過濾/G20/自訂主機<sup>1</sup>|是|是
漸進式下載|是|是
建議用法 |建議用於絕大多數的串流案例。|專業用法。

<sup>1</sup>僅在終結點上未啟用 CDN 時直接在流式處理終結點上使用。<br/>

## <a name="streaming-endpoint-properties"></a>流式處理終結點屬性

本節提供有關流式處理終結點的一些屬性的詳細資訊。 如需如何建立新串流端點的範例及所有屬性的說明，請參閱[串流端點](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)。

- `accessControl`： 用於為此流式處理終結點配置以下安全設置：允許連接到此終結點的 Akamai 簽名標頭身份驗證金鑰和 IP 位址。 此屬性只能在設置為 false 時`cdnEnabled`設置。

- `cdnEnabled`： 指示是否啟用了此流式處理終結點的 Azure CDN 集成（預設情況下禁用）。 如果您將 `cdnEnabled` 設為 True，下列設定就會停用：`customHostNames` 和 `accessControl`。

    並非所有資料中心都支援 Azure CDN 整合。 要檢查資料中心是否具有可用的 Azure CDN 集成，請執行以下步驟：

  - 嘗試設定`cdnEnabled`設為 true。
  - 檢查返回的結果（`HTTP Error Code 412`預置失敗）與消息"流終結點 CdnEnabled 屬性不能設置為 true，因為 CDN 功能在目前範圍不可用。

    如果收到此錯誤，資料中心不支援它。 嘗試其他資料中心。

- `cdnProfile`：`cdnEnabled`當設置為 true 時，還可以傳遞`cdnProfile`值。 `cdnProfile` 是 CDN 設定檔的名稱，而該設定檔就是要建立 CDN 端點的位置。 您可以提供現有的 cdnProfile，或使用新的。 如果值為 NULL，而且 `cdnEnabled` 是 True，則會使用預設值 "AzureMediaStreamingPlatformCdnProfile"。 如果提供的 `cdnProfile` 已經存在，則會在其下方建立端點。 如果設定檔不存在，將自動創建新設定檔。
- `cdnProvider`：啟用 CDN 後，還可以傳遞`cdnProvider`值。 `cdnProvider` 會控制將使用哪一個提供者。 目前支援三個值："StandardVerizon"、"PremiumVerizon" 和 "StandardAkamai"。 如果未提供任何值且`cdnEnabled`為 true，則使用"標準Verizon"（這是預設值）。
- `crossSiteAccessPolicies`：用於為各種用戶端指定跨網站訪問策略。 如需詳細資訊，請參閱[跨網域原則檔案規格](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)和[使服務可跨網域界限使用](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx)。 這些設置僅適用于平滑流式處理。
- `customHostNames`： 用於配置流式處理終結點以接受定向到自訂主機名稱的流量。 此屬性對標準和高級流式處理終結點有效，可在 ： false`cdnEnabled`時設置。

    功能變數名稱的擁有權必須由媒體服務確認。 媒體服務通過要求包含媒體服務帳戶 ID`CName`的記錄作為要添加到正在使用的域的元件來驗證功能變數名稱擁有權。 例如，若要將 "sports.contoso.com" 作為串流端點的自訂主機名稱，`<accountId>.contoso.com` 的記錄就必須設定為指向其中一個媒體服務驗證名稱。 驗證主機名稱會由 verifydns.\<mediaservices-dns-zone> 組成。

    以下是將在不同 Azure 區域的驗證記錄中使用的預期 DNS 區域。
  
  - 北美、歐洲、新加坡、香港特別行政區、日本：

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - 中國：

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    例如，將`CName`"945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com"映射到"verifydns.media.azure.net"的記錄證明，媒體服務 ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad 擁有contoso.com域的擁有權，從而使contoso.com下的任何名稱都可用作該帳戶下的流端的自訂主機名稱。 若要尋找媒體服務識別碼的值，請前往 [Azure 入口網站](https://portal.azure.com/)，然後選取您的媒體服務帳戶。 **帳戶 ID**顯示在頁面的右上角。

    如果嘗試在未正確驗證`CName`記錄的情況下設置自訂主機名稱，DNS 回應將失敗，然後緩存一段時間。 當適當的記錄就緒後，可能需要等待一段時間，才會重新驗證快取的回應。 根據自訂域的 DNS 提供程式，重新驗證記錄需要幾分鐘到一個小時。

    除了映射到 之外`CName`，`<accountId>.<parent domain>``verifydns.<mediaservices-dns-zone>`還必須創建另一個`CName`將自訂主機名稱（例如`sports.contoso.com`）映射到媒體服務流終結點的主機名稱（例如。 `amstest-usea.streaming.media.azure.net`

    > [!NOTE]
    > 位於同一資料中心的流式處理終結點不能共用相同的自訂主機名稱。

    目前媒體服務不支援將 SSL 用於自訂網域。

- `maxCacheAge`- 覆蓋媒體片段和按需清單上的流式處理終結點設置的預設最長期限 HTTP 緩存控制標頭。 此值的設定會以秒為單位。
- `resourceState` -

    - 已停止：創建後流式處理終結點的初始狀態
    - 開始：正在過渡到運行狀態
    - 正在運行：能夠將內容資料流到用戶端
    - 縮放：規模單位正在增加或減少
    - 停止：正在過渡到停止狀態
    - 刪除：正在刪除

- `scaleUnits`：為您提供專用出口容量，以 200 Mbps 的增量購買。 如果您需要移至**進階**類型，請調整 `scaleUnits`。

## <a name="why-use-multiple-streaming-endpoints"></a>為什麼要使用多個流式處理終結點？

單個流式處理終結點可以同時資料流即時和點播視頻，大多數客戶僅使用一個流式處理終結點。 本節給出了一些示例，說明為什麼您可能需要使用多個流式處理終結點。

* 每個預留單元允許 200 Mbps 的頻寬。 如果您需要超過 2，000 Mbps （2 Gbps） 的頻寬，則可以使用第二個流式處理終結點和負載平衡來為您提供額外的頻寬。

    但是，CDN 是實現流式處理內容橫向擴展的最佳方式，但如果提供的內容太多，CDN 的拉取量超過 2 Gbps，則可以添加其他流式處理終結點（原點）。 在這種情況下，您需要分發跨兩個流式處理終結點平衡的內容 URL。 此方法提供比嘗試隨機向每個源發送請求（例如，通過流量管理器）更好的緩存。 
    
    > [!TIP]
    > 通常，如果 CDN 的拉拉超過 2 Gbps，則某些內容可能配置錯誤（例如，沒有源遮罩）。
    
* 負載平衡不同的 CDN 提供程式。 例如，您可以將預設流式處理終結點設置為使用 Verizon CDN，並創建第二個流式處理終結點以使用 Akamai。 然後，在兩者之間添加一些負載平衡，以實現多 CDN 平衡。 

    但是，客戶通常使用單個源在多個 CDN 提供程式之間進行負載平衡。
* 資料流混合內容：即時和視頻點播。 

    即時和按需內容的訪問模式非常不同。 即時內容往往同時獲得對相同內容的大量需求。 視頻點播內容（例如長尾存檔內容）對同一內容的用法較低。 因此，緩存在即時內容上效果很好，但在長尾內容上效果不是很好。

    考慮一個方案，您的客戶主要觀看即時內容，但只是偶爾觀看點播內容，並且從同一流終結點提供服務。 按需內容使用率低將佔用緩存空間，以更好地為即時內容保存。 在這種情況下，我們建議從一個流式處理終結點提供即時內容，另一個流終結點提供長尾內容。 這將提高即時事件內容的性能。
    
## <a name="scaling-streaming-with-cdn"></a>使用 CDN 調整串流作業的規模

查看下列文章：

- [CDN 概述](../../cdn/cdn-overview.md)
- [使用 CDN 調整串流作業的規模](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>提出問題並獲取更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="see-also"></a>另請參閱

[動態封裝](dynamic-packaging-overview.md)

## <a name="next-steps"></a>後續步驟

[管理串流端點](manage-streaming-endpoints-howto.md)
