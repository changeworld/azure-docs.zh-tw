---
title: Azure CDN HTTP 原始日誌
description: 本文介紹 Azure CDN HTTP 原始日誌。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371640"
---
# <a name="azure-cdn-http-raw-logs"></a>Azure CDN HTTP 原始日誌
原始日誌提供有關對審核和故障排除非常重要的操作和錯誤的豐富資訊。 原始日誌與活動日誌不同。 活動日誌提供對 Azure 資源上執行的操作的可見度。 原始日誌提供資源的操作記錄。

> [!IMPORTANT]
> HTTP 原始日誌功能可用於 Microsoft 的 Azure CDN。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="sign-in-to-azure"></a>登入 Azure

登錄到 中的[https://portal.azure.com](https://portal.azure.com)Azure 門戶。

## <a name="configuration"></a>組態

要從 Microsoft 設定檔配置 Azure CDN 的原始日誌，請執行以下規定： 

1. 在 Azure 門戶功能表中，選擇**\<CDN 設定檔>****的所有資源** >> 。

2. 在 **"監視"** 下，選擇 **"診斷"設置**。

3. 選擇 **= 添加診斷設置**。

    ![CDN 診斷設置](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > 原始日誌僅在設定檔級別中可用，而聚合的 HTTP 狀態碼日誌在終結點級別中可用。

4. 在**診斷設置**下，在診斷設置名稱 下輸入**診斷設置的名稱**。

5. 選擇**日誌**並設置保留天數。

6. 選擇**目標詳細資訊**。 目的地選項包括：
    * **傳送至 Log Analytics**
        * 選擇**訂閱**和**日誌分析工作區**。
    * **存檔到存儲帳戶**
        * 選擇**訂閱**和**存儲帳戶**。
    * **資料流到事件中心**
        * 選擇**訂閱**、**事件中心命名空間**、**事件中心名稱（可選）** 和**事件中心策略名稱**。

    ![CDN 診斷設置](./media/cdn-raw-logs/raw-logs-02.png)

7. 選取 [儲存]****。

## <a name="raw-logs-properties"></a>原始日誌屬性

來自 Microsoft 服務的 Azure CDN 當前提供原始日誌。 原始日誌提供單個 API 請求，每個條目具有以下架構： 

| 屬性              | 描述                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 跟蹤參考     | 標識前門提供的請求的唯一引用字串，也作為 X-Azure-Ref 標頭髮送到用戶端。 需要搜索特定請求的訪問日誌中的詳細資訊。 |
| HttpMethod            | 要求使用的 HTTP 方法。                                                                                                                                                                     |
| HttpVersion           | 請求或連接的類型。                                                                                                                                                                   |
| 請求庫裡            | 接收之要求的 URI。                                                                                                                                                                         |
| 請求位元組          | HTTP 要求消息的大小（以位元組為單位），包括請求標頭和請求正文。                                                                                                   |
| 回應位元組         | 後端伺服器作為回應發送的位元組。                                                                                                                                                    |
| UserAgent             | 用戶端使用的瀏覽器類型。                                                                                                                                                               |
| ClientIp              | 發出要求之用戶端的 IP 位址。                                                                                                                                                  |
| TimeTaken             | 操作所花的時間長度（以毫秒為單位）。                                                                                                                                            |
| 安全協定      | 請求使用的 TLS/SSL 協定版本，如果沒有加密，則為 null。                                                                                                                           |
| 端點              | CDN 終結點主機已在父 CDN 設定檔下配置。                                                                                                                                   |
| 後端主機名稱稱     | 發送請求的後端主機或源的名稱。                                                                                                                                |
| 發送到源遮罩 | 如果為 true，則表示請求是從源遮罩緩存而不是邊緣彈出應答的。 原點遮罩是用於提高緩存命中率的父緩存。                                       |
| HttpStatusCode        | 從代理返回的 HTTP 狀態碼。                                                                                                                                                        |
| HttpStatus 詳細資訊     | 在請求上生成狀態。 可在狀態引用表中找到此字串值的含義。                                                                                              |
| 流行                   | 回應使用者請求的邊緣彈出。 持久性有機污染物的縮寫是各自地鐵的機場代碼。                                                                                   |
| 緩存狀態          | 表示物件是從緩存返回的還是來自源的。                                                                                                             |
> [!IMPORTANT]
> HTTP Raw 日誌功能可自動用於**2020 年 2 月 25**日之後創建或更新的任何設定檔。 對於之前創建的 CDN 設定檔，應在設置日誌記錄後更新 CDN 終結點。 例如，可以在 CDN 終結點下導航到地理篩選，並阻止與其工作負載無關的任何國家/地區並命中保存。 

> [!NOTE]
> 可以通過執行查詢在日誌分析設定檔下查看日誌。 依例查詢將類似于 Azure 診斷 |其中類別 = "AzureCdn 訪問日誌"

## <a name="next-steps"></a>後續步驟
在本文中，您為 Microsoft CDN 服務啟用了 HTTP 原始日誌。

有關 Azure CDN 和本文中提到的其他 Azure 服務的詳細資訊，請參閱：

* [分析](cdn-log-analysis.md)Azure CDN 使用模式。

* 瞭解有關[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)的更多。

* [在 Azure 監視器 中配置日誌分析](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。
