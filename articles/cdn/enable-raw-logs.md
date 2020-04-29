---
title: Azure CDN HTTP 原始記錄
description: 本文說明 Azure CDN HTTP 原始記錄檔。
services: cdn
author: sohamnchatterjee
manager: danielgi
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2020
ms.author: sohamnc
ms.openlocfilehash: c6e8570746ae3dd0051dbec084c89d90580d28b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80371640"
---
# <a name="azure-cdn-http-raw-logs"></a>Azure CDN HTTP 原始記錄
原始記錄檔提供有關進行審核和疑難排解的重要作業和錯誤的豐富資訊。 原始記錄與活動記錄不同。 活動記錄可讓您查看在 Azure 資源上完成的作業。 原始記錄會提供您資源的作業記錄。

> [!IMPORTANT]
> HTTP 原始記錄功能適用于來自 Microsoft 的 Azure CDN。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="configuration"></a>設定

若要為來自 Microsoft 的 Azure CDN 設定檔設定原始記錄： 

1. 從 [Azure 入口網站] 功能表中，選取 [**所有資源** >> **\<]-[CDN-設定檔**] [>]。

2. 在 [**監視**] 底下，選取 [**診斷設定**]。

3. 選取 [ **+ 新增診斷設定**]。

    ![CDN 診斷設定](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > 原始記錄僅適用于設定檔層級，而匯總的 HTTP 狀態碼記錄則可在端點層級中取得。

4. 在 [**診斷**設定] 下的 [**診斷設定名稱**] 底下，輸入診斷設定的名稱。

5. 選取**記錄**並設定 [保留天數]。

6. 選取**目的地詳細資料**。 目的地選項包括：
    * **傳送至 Log Analytics**
        * 選取**訂**用帳戶和**Log Analytics 工作區**。
    * **封存至儲存體帳戶**
        * 選取**訂**用帳戶和**儲存體帳戶**。
    * **串流至事件中樞**
        * 選取 [**訂**用帳戶]、[**事件中樞命名空間**]、[**事件中樞名稱] （選擇性）** 和 [**事件中樞原則名稱**]。

    ![CDN 診斷設定](./media/cdn-raw-logs/raw-logs-02.png)

7. 選取 [儲存]  。

## <a name="raw-logs-properties"></a>原始記錄屬性

來自 Microsoft 服務的 Azure CDN 目前提供原始記錄。 原始記錄檔會提供個別的 API 要求，其中每個專案都具有下列架構： 

| 屬性              | 描述                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | 唯一的參考字串，可識別 Front 門所提供的要求，也會以 X-Azure Ref 標頭的形式傳送給用戶端。 在特定要求的存取記錄中搜尋詳細資料時的必要項。 |
| HttpMethod            | 要求使用的 HTTP 方法。                                                                                                                                                                     |
| HttpVersion           | 要求或連接的類型。                                                                                                                                                                   |
| RequestUri            | 接收之要求的 URI。                                                                                                                                                                         |
| RequestBytes          | HTTP 要求訊息的大小（以位元組為單位），包括要求標頭和要求主體。                                                                                                   |
| ResponseBytes         | 後端伺服器傳送的位元組作為回應。                                                                                                                                                    |
| UserAgent             | 用戶端使用的瀏覽器類型。                                                                                                                                                               |
| ClientIp              | 發出要求之用戶端的 IP 位址。                                                                                                                                                  |
| TimeTaken             | 動作所花費的時間長度（以毫秒為單位）。                                                                                                                                            |
| SecurityProtocol      | 要求所使用的 TLS/SSL 通訊協定版本，如果沒有加密，則為 null。                                                                                                                           |
| 端點              | CDN 端點主機已在父 CDN 設定檔底下設定。                                                                                                                                   |
| 後端主機名稱     | 要在其中傳送要求的後端主機或來源的名稱。                                                                                                                                |
| 已傳送至原始防護板 | 若為 true，表示已從原始防護快取（而不是邊緣 pop）回應要求。 原始防護是用來改善快取點擊率的父快取。                                       |
| HttpStatusCode        | 從 proxy 傳回的 HTTP 狀態碼。                                                                                                                                                        |
| HttpStatusDetails     | 要求的結果狀態。 此字串值的意義可以在狀態參照資料表中找到。                                                                                              |
| 提示                   | 邊緣 pop，它會回應使用者要求。 Pop 的縮寫是其各自 metros 的機場代碼。                                                                                   |
| 快取狀態          | 表示物件是否從快取傳回，或來自來源。                                                                                                             |
> [!IMPORTANT]
> 在**2020 年2月25日**之後建立或更新的任何設定檔，都會自動提供 HTTP 原始記錄功能。 針對稍早建立的 CDN 設定檔，您應該在設定記錄之後更新 CDN 端點。 例如，您可以流覽至 [CDN 端點] 底下的 [地區篩選]，並封鎖與工作負載無關的任何國家/地區，然後點擊 [儲存]。 

> [!NOTE]
> 您可以藉由執行查詢，在 Log Analytics 設定檔下查看記錄。 範例查詢看起來會像 AzureDiagnostics |where Category = = "AzureCdnAccessLog"

## <a name="next-steps"></a>後續步驟
在本文中，您已啟用 Microsoft CDN 服務的 HTTP 原始記錄檔。

如需有關 Azure CDN 和本文所述其他 Azure 服務的詳細資訊，請參閱：

* [分析](cdn-log-analysis.md)Azure CDN 使用模式。

* 深入瞭解[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)。

* [在 Azure 監視器中設定 Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。
