---
title: Azure Front Door 中的後端和後端集區 |Microsoft Docs
description: 本文說明 Front Door 設定中的後端和後端集區。
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 66767d4329a0a757de99308e1f586b56b327a515
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399917"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Azure Front Door 中的後端和後端集區
本文說明如何將您的應用程式部署對應至 Azure Front Door 的概念。 此外也會說明在應用程式後端方面 Front Door 設定的不同詞彙。

## <a name="backends"></a>後端
後端等於區域中的應用程式部署實例。 Front Door 支援 Azure 和非 Azure 後端，因此區域不只限於 Azure 區域。 此外，它可以是您的內部部署資料中心或另一個雲端中的應用程式實例。

Front Door 後端指的是應用程式的主機名稱或公用 IP，可提供用戶端要求。 後端不應該與您的資料庫層、儲存層等混淆。 後端應該視為應用程式後端的公用端點。 當您在 Front Door 後端集區中新增後端時，您也必須新增下列各項：

- **後端主機類型**。 您要新增的資源類型。 Front Door 支援從 app service、雲端服務或儲存體後端應用程式的自動探索。 如果您想要在 Azure 或甚至非 Azure 後端中使用不同的資源，請選取 [ **自訂主機**]。

    >[!IMPORTANT]
    >在設定期間，Api 不會驗證後端是否可從 Front Door 環境中存取。 請確定 Front Door 可以觸及您的後端。

- **訂用帳戶和後端主機名稱**。 如果您尚未針對後端主機類型選取 **自訂主機** ，請在 UI 中選擇適當的訂用帳戶和對應的後端主機名稱，以選取您的後端。

- **後端主機標頭**。 針對每個要求傳送至後端的主機標頭值。 如需詳細資訊，請參閱 [後端主機標頭](#hostheader)。

- **優先權**。 當您想要針對所有流量使用主要服務後端時，請將優先順序指派給不同的後端。 此外，如果主要或備份後端無法使用，請提供備份。 如需詳細資訊，請參閱 [Priority](front-door-routing-methods.md#priority)。

- **權數**。 將權數指派給不同的後端，以平均或根據加權係數，將流量分散到一組後端上。 如需詳細資訊，請參閱 [加權](front-door-routing-methods.md#weighted)。

### <a name="backend-host-header"></a><a name = "hostheader"></a>後端主機標頭

Front Door 轉送至後端的要求包含主機標頭欄位，後端會使用該欄位來取得目標資源。 此欄位的值通常來自後端 URI，並且具有主機和連接埠。

例如，對提出的要求 `www.contoso.com` 將會有主機標頭 www.contoso.com。 如果您使用 Azure 入口網站設定您的後端，此欄位的預設值是後端的主機名稱。 如果您的後端是 contoso-westus.azurewebsites.net，則在 Azure 入口網站中，後端主機標頭的自動填入值將會是 contoso-westus.azurewebsites.net。 但是，如果您使用 Azure Resource Manager 範本或其他方法，但未明確設定此欄位，Front Door 會將傳入的主機名稱傳送為主機標頭的值。 如果對 www contoso.com 提出要求 \. ，而您的後端是具有空白標頭欄位的 contoso-westus.azurewebsites.net，Front Door 會將主機標頭設定為 www \. contoso.com。

大部分的應用程式後端 (Azure Web Apps、Blob 儲存體及雲端服務) 需要主機標頭以符合後端的網域。 不過，路由至後端的前端主機會使用不同的主機名稱，例如 www.contoso.net。

如果您的後端需要主機標頭符合後端主機名稱，請確定後端主機標頭包含主機名稱後端。

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>設定後端的後端主機標頭

若要在後端集區區段中設定後端的 **後端主機標頭** 欄位：

1. 開啟您的 Front Door 資源，然後選取後端要設定的後端集區。

2. 如果您未這麼做，請新增後端，或編輯現有的後端。

3. 將 [後端主機標頭] 欄位設定為自訂值，或將其保留空白。 傳入要求的主機名稱將會用來作為主機標頭值。

## <a name="backend-pools"></a>後端集區
Front Door 中的後端集區是指接收其應用程式類似流量的後端集。 換句話說，它是世界各地您的應用程式實例的邏輯群組，可接收相同的流量並回應預期的行為。 這些後端會部署在不同的區域或相同的區域內。 所有後端都可以處於主動/主動部署模式，或定義為主動/被動設定。

後端集區會定義如何透過健康情況探查來評估不同的後端。 它也會定義負載平衡在兩者之間的發生方式。

### <a name="health-probes"></a>健康狀態探查
Front Door 將定期 HTTP/HTTPS 探查要求傳送至每個已設定的後端。 探查要求會決定每個後端的鄰近性和健康情況，以平衡終端使用者要求的負載。 後端集區的健康情況探查設定會定義如何輪詢應用程式後端的健康情況狀態。 下列設定適用于負載平衡設定：

- **路徑**：用於對後端集區中的所有後端進行探查要求的 URL。 例如，如果您的其中一個後端是 contoso-westus.azurewebsites.net，而且路徑設定為/probe/test.aspx，則 Front Door 環境（假設通訊協定設定為 HTTP）會將健康情況探查要求傳送至 HTTP \: //contoso-westus.azurewebsites.net/probe/test.aspx。

- **通訊協定**：定義是否使用 HTTP 或 HTTPS 通訊協定，將健康情況探查要求從 Front Door 傳送至您的後端。

- **方法**：用於傳送健康情況探查的 HTTP 方法。 選項包括 GET 或 HEAD (預設) 。
    > [!NOTE]
    > 針對較低的負載和後端成本，Front Door 建議使用健康情況探查的 HEAD 要求。

- **間隔 (秒) **：定義健全狀況探查後端的頻率，或每個 Front Door 環境傳送探查的間隔。

    >[!NOTE]
    >若要更快速容錯移轉，請將間隔設定為較低的值。 值越低，後端所收到的健康情況探查磁片區愈高。 例如，如果將間隔設定為30秒，而在全域顯示 100 Front Door，每個後端會收到大約每分鐘200個探查要求。

如需詳細資訊，請參閱 [健康情況探查](front-door-health-probes.md)。

### <a name="load-balancing-settings"></a>負載平衡設定
後端集區的負載平衡設定會定義我們評估健康情況探查的方式。 這些設定會決定後端是否狀況良好或狀況不良。 它們也會檢查如何針對後端集區中不同後端之間的流量進行負載平衡。 下列設定適用于負載平衡設定：

- **樣本大小**。 識別針對後端健康情況評估需要考慮的健康情況探查樣本數。

- **成功的取樣大小**。 定義如先前所述的範例大小，呼叫後端狀況良好所需的成功樣本數。 例如，假設 Front Door 健康情況探查間隔為30秒、樣本大小為5，而成功樣本大小為3。 每次我們評估您後端的健康情況探查時，我們會查看過去五個樣本150秒 (5 x 30) 。 至少需要三個成功的探查，才能將後端聲明為狀況良好。

- **延遲敏感度 (額外的延遲) **。 定義您是否要 Front Door 將要求傳送至延遲測量敏感度範圍內的後端，或將要求轉送至最接近的後端。

如需詳細資訊，請參閱 [最低延遲型路由方法](front-door-routing-methods.md#latency)。

## <a name="next-steps"></a>接下來的步驟

- [建立 Front Door 設定檔](quickstart-create-front-door.md)
- [Front Door 的運作方式](front-door-routing-architecture.md)