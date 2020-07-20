---
title: Azure Front 中的後端和後端集區 |Microsoft Docs
description: 本文說明哪些後端和後端集區是在 Front configuration 中。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80293460"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Azure Front 中的後端和後端集區
本文說明如何將您的應用程式部署與 Azure 前門對應的概念。 它也會說明應用程式後端前後的 Front 門板設定中的不同詞彙。

## <a name="backends"></a>後端
後端等於某個區域中的應用程式部署實例。 Front 門板同時支援 Azure 和非 Azure 後端，因此區域不只限於 Azure 區域。 此外，它可以是您的內部部署資料中心或另一個雲端中的應用程式實例。

Front 後端指的是應用程式的主機名稱或公用 IP，可以服務用戶端要求。 後端不應該與您的資料庫層、儲存層等混淆。 後端應該視為應用程式後端的公用端點。 當您在 Front 大門後端集區中新增後端時，您也必須新增下列各項：

- **後端主機類型**。 您想要新增的資源類型。 Front 門支援從 app service、雲端服務或儲存體後端應用程式的自動搜索。 如果您想要在 Azure 中使用不同的資源，或甚至是非 Azure 後端，請選取 [**自訂主機**]。

    >[!IMPORTANT]
    >在設定期間，Api 不會驗證後端是否可從前端環境存取。 請確定 Front 門可以觸達您的後端。

- **訂用帳戶和後端主機名稱**。 如果您尚未選取 [**自訂主機**] 作為後端主機類型，請在 UI 中選擇適當的訂用帳戶和對應的後端主機名稱，以選取您的後端。

- **後端主機標頭**。 針對每個要求傳送至後端的主機標頭值。 如需詳細資訊，請參閱[後端主機標頭](#hostheader)。

- **優先順序**。 當您想要針對所有流量使用主要服務後端時，請將優先順序指派給不同的後端。 此外，如果主要或備份後端無法使用，請提供備份。 如需詳細資訊，請參閱[Priority](front-door-routing-methods.md#priority)。

- **權數**。 將權數指派給不同的後端，以平均或根據權數係數將流量分散到一組後端上。 如需詳細資訊，[請參閱權數](front-door-routing-methods.md#weighted)。

### <a name="backend-host-header"></a><a name = "hostheader"></a>後端主機標頭

由 Front 門轉寄至後端的要求，包括後端用來抓取目標資源的主機標頭欄位。 此欄位的值通常來自後端 URI，並且具有主機和連接埠。

例如，針對提出的要求 `www.contoso.com` 將會有主機標頭 www.contoso.com。 如果您使用 Azure 入口網站設定後端，此欄位的預設值會是後端的主機名稱。 如果您的後端已 contoso-westus.azurewebsites.net，則在 Azure 入口網站中，後端主機標頭的自動填入值會是 contoso-westus.azurewebsites.net。 不過，如果您使用 Azure Resource Manager 範本或其他方法，但未明確設定此欄位，則 Front 門會將傳入的主機名稱傳送為主機標頭的值。 如果是針對 www contoso.com 提出要求 \. ，而且您的後端是 contoso-westus.azurewebsites.net，其標頭欄位為空白，則 Front 門板會將主機標頭設定為 www \. contoso.com。

大部分的應用程式後端（Azure Web Apps、Blob 儲存體和雲端服務）都需要主機標頭符合後端的網域。 不過，路由至後端的前端主機將會使用不同的主機名稱，例如 www.contoso.net。

如果您的後端需要主機標頭符合後端主機名稱，請確定後端主機標頭包含主機名稱後端。

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>設定後端的後端主機標頭

若要在後端集區區段中設定後端的**後端主機標頭**欄位：

1. 開啟您的 Front 門板資源，然後選取後端集區與後端以進行設定。

2. 如果您還沒這麼做，請加入後端，或編輯現有的一個。

3. 將 [後端主機標頭] 欄位設為自訂值，或保留空白。 傳入要求的主機名稱會當做主機標頭值使用。

## <a name="backend-pools"></a>後端集區
Front 中的後端集區是指接收其應用程式類似流量的一組後端。 換句話說，它是全球應用程式實例的邏輯群組，可接收相同的流量並回應預期的行為。 這些後端會部署在不同區域或相同區域內。 所有後端都可以處於主動/主動部署模式，或定義為主動/被動設定。

後端集區會定義應該如何透過健康狀態探查評估不同的後端。 它也會定義它們之間的負載平衡。

### <a name="health-probes"></a>健康狀態探查
Front 門板會將定期 HTTP/HTTPS 探查要求傳送至您所設定的每個後端。 探查要求會判斷每個後端的鄰近性和健康狀態，以對您的終端使用者要求進行負載平衡。 後端集區的健康情況探查設定會定義我們如何輪詢應用程式後端的健康狀態。 下列設定適用于負載平衡設定：

- **路徑**：用於後端集區中所有後端的探查要求的 URL。 例如，如果您的其中一個後端是 contoso-westus.azurewebsites.net，且路徑設定為/probe/test.aspx，而 Front 門板環境（假設通訊協定設定為 HTTP）會將健康情況探查要求傳送至 HTTP \: //contoso-westus.azurewebsites.net/probe/test.aspx。

- **通訊協定**：定義是否要使用 HTTP 或 HTTPS 通訊協定，將健康情況探查要求從 Front 門傳送到您的後端。

- **方法**：用來傳送健康情況探查的 HTTP 方法。 選項包括 GET 或 HEAD （預設值）。
    > [!NOTE]
    > 針對較低的後端負載和成本，Front 門建議使用健康狀態探查的 HEAD 要求。

- **間隔（秒）**：定義健全狀況探查至您的後端的頻率，或每個 Front 門環境傳送探查的間隔。

    >[!NOTE]
    >如需更快速的容錯移轉，請將間隔設定為較低的值。 值越低，後端接收的健康情況探查磁片區就愈高。 例如，如果間隔設定為30秒，則會全域彈出 100 Front 門板，每個後端每分鐘會收到大約200個探查要求。

如需詳細資訊，請參閱[健康情況探查](front-door-health-probes.md)。

### <a name="load-balancing-settings"></a>負載平衡設定
後端集區的負載平衡設定會定義我們評估健康狀態探查的方式。 這些設定會判斷後端的狀況是否良好或狀況不良。 同時也會檢查後端集區中不同後端之間的流量負載平衡。 下列設定適用于負載平衡設定：

- **樣本大小**。 識別在後端健康狀態評估中，我們需要考慮的健康狀態探查樣本數目。

- **成功的樣本大小**。 定義先前所述的樣本大小，呼叫後端狀況良好所需的成功樣本數。 例如，假設 Front 門板健全狀況探查間隔為30秒、取樣大小為5，且成功的樣本大小為3。 每次評估後端的健康情況探查時，我們會查看過去五個樣本150秒（5 x 30）。 至少需要三個成功的探查，才能將後端宣告為狀況良好。

- **延遲敏感度（額外延遲）**。 定義您是否要讓 Front 大門將要求傳送至延遲測量敏感度範圍內的後端，或將要求轉寄到最接近的後端。

如需詳細資訊，請參閱以[延遲為基礎的最小路由方法](front-door-routing-methods.md#latency)。

## <a name="next-steps"></a>後續步驟

- [建立 Front Door 設定檔](quickstart-create-front-door.md)
- [Front 門板的運作方式](front-door-routing-architecture.md)