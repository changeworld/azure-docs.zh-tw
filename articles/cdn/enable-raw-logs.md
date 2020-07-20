---
title: Azure CDN HTTP 原始記錄
description: 本文說明 Azure CDN HTTP 原始記錄。
services: cdn
author: sohamnchatterjee
manager: danielgi
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/23/2020
ms.author: sohamnc
ms.openlocfilehash: a2522eba17574246ab99a0d47a42f128d5f61ace
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84888645"
---
# <a name="azure-cdn-http-raw-logs"></a>Azure CDN HTTP 原始記錄
原始記錄可提供豐富的作業與錯誤資訊，這些資訊對於稽核和疑難排解來說很重要。 原始記錄與活動記錄不同。 活動記錄可讓您看見在 Azure 資源上完成的作業。 原始記錄則提供您資源的作業記錄。

> [!IMPORTANT]
> HTTP 原始記錄功能適用於 Microsoft 的 Azure CDN。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="configuration"></a>組態

若要為 Microsoft 設定檔中的 Azure CDN 設定原始記錄： 

1. 從 [Azure 入口網站] 功能表中，選取 [**所有資源**]  >>  **\<your-CDN-profile>** 。

2. 在 [監視] 下方選取 [診斷設定]。

3. 選取 [+新增診斷設定]。

    ![CDN 診斷設定](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > 原始記錄僅適用於設定檔層級，而彙總的 HTTP 狀態碼記錄則適用於端點層級。

4. 在 [診斷設定] 下方，於 [診斷設定名稱] 底下輸入診斷設定的名稱。

5. 選取 [記錄] 並設定保留天數。

6. 選取 [目的地詳細資料]。 目的地選項有：
    * **傳送至 Log Analytics**
        * 選取 [訂用帳戶] 和 [Log Analytics 工作區]。
    * **封存至儲存體帳戶**
        * 選取 [訂用帳戶] 和 [儲存體帳戶]。
    * **串流至事件中樞**
        * 選取 [訂用帳戶]、[事件中樞命名空間]、\[事件中樞名稱 \(選擇性\)\] 及 [事件中樞原則名稱]。

    ![CDN 診斷設定](./media/cdn-raw-logs/raw-logs-02.png)

7. 選取 [儲存]。

## <a name="raw-logs-properties"></a>原始記錄屬性

來自 Microsoft 服務的 Azure CDN 目前提供原始記錄。 原始記錄會提供個別的 API 要求，其中每個項目都具有下列結構描述： 

| 屬性              | 描述                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | 這項唯一的參考字串可識別 Front Door 所提供的要求，也會以 X-Azure Ref 標頭的形式傳送給用戶端， 這是在特定要求的存取記錄中搜尋詳細資料時的必要項目。 |
| HttpMethod            | 要求使用的 HTTP 方法。                                                                                                                                                                     |
| HttpVersion           | 要求或連線的類型。                                                                                                                                                                   |
| RequestUri            | 接收之要求的 URI。                                                                                                                                                                         |
| RequestBytes          | HTTP 要求訊息的大小 (以位元組為單位)，包括要求標頭和要求本文。                                                                                                   |
| ResponseBytes         | 後端伺服器以回應形式傳回的位元組。                                                                                                                                                    |
| UserAgent             | 用戶端使用的瀏覽器類型。                                                                                                                                                               |
| ClientIp              | 發出要求之用戶端的 IP 位址。                                                                                                                                                  |
| TimeTaken             | 動作所花費的時間長度 (毫秒)。                                                                                                                                            |
| SecurityProtocol      | 要求所使用的 TLS/SSL 通訊協定版本，如果沒有加密則為 null。                                                                                                                           |
| 端點              | CDN 端點主機已在父系 CDN 設定檔底下設定。                                                                                                                                   |
| 後端主機名稱     | 寄出要求的後端主機或來源名稱。                                                                                                                                |
| 傳送至來源保護盾 | 若為 true，則表示是從來源保護盾快取回答要求，而非邊緣 Pop。 原始保護盾是用來改善快取點擊率的父代快取。                                       |
| HttpStatusCode        | 從 Proxy 傳回的 HTTP 狀態碼。                                                                                                                                                        |
| HttpStatusDetails     | 對要求產生的狀態。 此字串值的意義可以在狀態參考資料表中找到。                                                                                              |
| POP                   | 邊緣 Pop 會回應使用者要求。 POP 的縮寫是其各自都市的機場代碼。                                                                                   |
| 快取狀態          | 表示物件是從快取傳回，還是來自來源。                                                                                                             |
> [!IMPORTANT]
> 系統會自動為 **2020 年 2 月 25 日**之後建立或更新的任何設定檔提供 HTTP 原始記錄功能。 對於之前建立的 CDN 設定檔，應於設定記錄之後更新 CDN 端點。 例如，您可以瀏覽至 CDN 端點底下的地區篩選，並封鎖任何與工作負載無關的國家/地區，然後點擊 [儲存]。 

> [!NOTE]
> 您可以藉由執行查詢，以在 Log Analytics 設定檔下方檢視記錄。 範例查詢如下所示：              AzureDiagnostics | where Category == "AzureCdnAccessLog"

## <a name="next-steps"></a>後續步驟
在本文中，您已啟用 Microsoft CDN 服務的 HTTP 原始記錄。

如需有關 Azure CDN 和本文所述其他 Azure 服務的詳細資訊，請參閱：

* [分析](cdn-log-analysis.md) Azure CDN 使用模式。

* 深入了解 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)。

* 設定 [Azure 監視器中的 Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。
