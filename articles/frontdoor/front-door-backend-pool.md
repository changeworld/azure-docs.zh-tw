---
title: Azure 前門中的後端和後端池*微軟文檔
description: 本文介紹了前門配置中的後端池和後端池。
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 18b165d83bfa154348842542bd8323a40330aa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293460"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Azure 前門的後端和後端池
本文介紹有關如何使用 Azure 前門映射應用部署的概念。 它還解釋了前門配置中有關應用後端的不同術語。

## <a name="backends"></a>後端
後端等於應用中在區域中的部署實例。 前門同時支援 Azure 和非 Azure 後端，因此該區域不僅僅限於 Azure 區域。 此外，它可以是本地資料中心或另一個雲中的應用實例。

前門後端是指應用程式的主機名稱或公共 IP，它可以為用戶端請求提供服務。 後端不應與資料庫層、存儲層等混淆。 後端應被視為應用後端的公共終結點。 在前門後端池中添加後端時，還必須添加以下內容：

- **後端主機類型**。 要添加的資源類型。 前門支援自動探索應用服務、雲服務或存儲的應用後端。 如果想要 Azure 中的不同資源，甚至需要非 Azure 後端，請選擇 **"自訂主機**"。

    >[!IMPORTANT]
    >在配置期間，API 不會驗證後端是否從前門環境無法訪問。 確保前門可以到達您的後端。

- **訂閱和後端主機名稱**。 如果尚未為後端主機類型選擇**自訂主機**，請在 UI 中選擇相應的訂閱和相應的後端主機名稱來選擇後端。

- **後端主機標頭**。 為每個要求傳送到後端的主機標頭值。 有關詳細資訊，請參閱[後端主機標頭](#hostheader)。

- **優先順序**。 當您要對所有流量使用主服務後端時，將優先順序分配給不同的後端。 此外，如果主後端或備份後端不可用，請提供備份。 有關詳細資訊，請參閱[優先順序](front-door-routing-methods.md#priority)。

- **重量**. 將權重分配給不同的後端，以便均勻或根據權重係數在一組後端分配流量。 有關詳細資訊，請參閱[權重](front-door-routing-methods.md#weighted)。

### <a name="backend-host-header"></a><a name = "hostheader"></a>後端主機標頭

前門轉發到後端的請求包括後端用於檢索目標資源的主機標頭欄位。 此欄位的值通常來自後端 URI，並且具有主機和連接埠。

例如，為發出`www.contoso.com`的請求將具有主機標頭www.contoso.com。 如果使用 Azure 門戶配置後端，則此欄位的預設值是後端的主機名稱。 如果後端contoso-westus.azurewebsites.net，則在 Azure 門戶中，後端主機標頭的自動填滿值將contoso-westus.azurewebsites.net。 但是，如果使用 Azure 資源管理器範本或其他方法而不顯式設置此欄位，前門將發送傳入主機名稱作為主機標頭的值。 如果請求是 contoso.com 的\.www contoso.com，並且後端contoso-westus.azurewebsites.net具有空標頭欄位，則前門將主機標頭設置為 www\.contoso.com。

大多數應用後端（Azure Web 應用、Blob 存儲和雲服務）都需要主機標頭與後端的域匹配。 但是，路由到後端的前端主機將使用其他主機名稱，如www.contoso.net。

如果後端需要主機標頭與後端主機名稱匹配，請確保後端主機標頭包含主機名稱後端。

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>設定後端的後端主機標頭

要為後端池部分中的後端配置**後端主機頭**欄位，請進行以下工作：

1. 打開前門資源，選擇後端池，然後配置後端池。

2. 如果尚未添加後端，請添加後端，或編輯現有後端。

3. 將後端主機標頭欄位設置為自訂值或將其留空。 傳入請求的主機名稱將用作主機標頭值。

## <a name="backend-pools"></a>後端池
前門的後端池是指為其應用接收類似流量的後端集。 換句話說，它是全世界應用實例的邏輯分組，這些實例接收相同的流量，並回應預期的行為。 這些後端部署在不同區域或同一區域內。 所有後端都可以處於主動/主動部署模式或定義為主動/被動配置。

後端池定義如何通過運行狀況探測器評估不同的後端。 它還定義了它們之間的負載平衡方式。

### <a name="health-probes"></a>健康狀態探查
前門定期向每個配置的後端發送 HTTP/HTTPS 探測請求。 探測請求確定每個後端的接近性和運行狀況，以平衡最終使用者請求的負載。 後端池的運行狀況探測設置定義我們如何輪詢應用後端的運行狀況。 以下設置可用於負載平衡配置：

- **路徑**：用於後臺池中所有後端的探測請求的 URL。 例如，如果其中一個後端contoso-westus.azurewebsites.net並且路徑設置為 /probe/test.aspx，則前門環境（假設協定設置為 HTTP）將向 HTTP\://contoso-westus.azurewebsites.net/probe/test.aspx發送運行狀況探測請求。

- **協定**：定義是否使用 HTTP 或 HTTPS 協定將運行狀況探測請求從前門發送到後端。

- **方法**：用於發送運行狀況探測器的 HTTP 方法。 選項包括 GET 或 HEAD（預設）。
    > [!NOTE]
    > 為了降低後端的負載和成本，前門建議使用 HEAD 請求進行運行狀況探頭。

- **間隔（秒）：** 定義運行狀況探測器到後端的頻率，或每個前門環境發送探測器的間隔。

    >[!NOTE]
    >要加快容錯移轉速度，將間隔設置為較低的值。 值越低，後端接收的運行狀況探測卷就越高。 例如，如果間隔設置為 30 秒，假設為 100 個前門持久性有機污染物，則每個後端每分鐘將收到大約 200 個探測請求。

有關詳細資訊，請參閱[運行狀況探測器](front-door-health-probes.md)。

### <a name="load-balancing-settings"></a>負載平衡設置
後端池的負載平衡設置定義我們如何評估運行狀況探測器。 這些設置確定後端是正常還是不正常。 它們還會檢查如何在後端池中的不同後端之間負載平衡流量。 以下設置可用於負載平衡配置：

- **樣本大小**。 確定需要考慮多少健康探測樣本進行後端運行狀況評估。

- **成功的樣本大小**。 定義如前面提到的樣本大小，調用後端正常運行所需的成功樣本數。 例如，假設前門運行狀況探測間隔為 30 秒，樣本大小為 5，成功樣本大小為 3。 每次評估後端的健康探測器時，我們都會查看最後 5 個樣本，超過 150 秒（5 x 30）。 至少需要三個成功的探測器才能聲明後端為健康。

- **延遲敏感度（額外延遲）。** 定義是希望前門將要求傳送到延遲測量敏感範圍內的後端，還是將請求轉發到最近的後端。

有關詳細資訊，請參閱[基於延遲的最小路由方法](front-door-routing-methods.md#latency)。

## <a name="next-steps"></a>後續步驟

- [建立 Front Door 設定檔](quickstart-create-front-door.md)
- [前門的工作原理](front-door-routing-architecture.md)