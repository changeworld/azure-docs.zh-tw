---
title: Azure Front Door-URL 重新導向 |Microsoft Docs
description: 本文可協助您瞭解 Azure Front Door 如何針對其路由支援 URL 重新導向（若已設定）。
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: duau
ms.openlocfilehash: 41cb2343cb86d2ec756bb0a2fb690b7df886024f
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399033"
---
# <a name="url-redirect"></a>URL 重新導向
您可以使用 Azure Front Door 來重新導向流量。 您可以將多個層級的流量重新導向 (通訊協定、主機名稱、路徑、查詢字串) ，而且所有功能都可以針對個別微服務設定，因為重新導向是以路徑為基礎。 這可簡化應用程式組態、將資源使用量最佳化，並支援新的重新導向案例，包括全域和路徑式重新導向。
</br>

![Azure Front Door URL 重新導向][1]

## <a name="redirection-types"></a>重新導向類型
重新導向類型會設定用戶端的回應狀態碼，以瞭解重新導向的目的。 支援下列類型的重新導向：

- **301 (永久移動) **：表示已將新的永久 URI 指派給目標資源，而此資源的未來任何參考都應該使用其中一個包含的 uri。 使用301狀態碼進行 HTTP 至 HTTPS 重新導向。 
- **302 (找到) **：表示目標資源暫時位於不同的 URI 下。 由於重新導向可能會在某些情況下改變，因此用戶端應該繼續使用有效的要求 URI 來取得未來的要求。
- **307 (暫時性重新導向) **：表示目標資源暫時位於不同的 uri 下，而且如果它執行自動重新導向至該 uri，則使用者代理程式不得變更要求方法。 由於重新導向可能會隨著時間而改變，因此用戶端應該繼續使用原始的有效要求 URI 來取得未來的要求。
- **308 (永久重新導向) **：表示已將新的永久 URI 指派給目標資源，而此資源的未來任何參考都應該使用其中一個包含的 uri。 具有連結編輯功能的用戶端，應該在可能的情況下，自動將有效要求 URI 的參考重新連結到伺服器所傳送的一或多個新參考。

## <a name="redirection-protocol"></a>重新導向通訊協定
您可以設定將用於重新導向的通訊協定。 這允許重新導向功能最常見的其中一個使用案例，也就是將 HTTP 設定為 HTTPS 重新導向。

- **僅限 HTTPs**：如果您想要將流量從 HTTP 重新導向至 HTTPs，請將通訊協定設定為 [僅 HTTPs]。 Azure Front Door 建議您一律將重新導向設定為僅限 HTTPS。
- **僅限 HTTP**：這會將連入要求重新導向至 HTTP。 只有當您想要保留流量 HTTP （非加密）時，才使用此值。
- **符合要求**：此選項會保留連入要求所使用的通訊協定。 因此，HTTP 要求會維持 HTTP，而 HTTPS 要求會維持 HTTPS 後續重新導向。

## <a name="destination-host"></a>目的地主機
在設定重新導向路由的過程中，您也可以變更重新導向要求的主機名稱或網域。 您可以設定此欄位來變更重新導向 URL 中的主機名稱，或從傳入要求中保留主機名稱。 因此，使用此欄位，您可以重新導向傳送至的所有要求 `https://www.contoso.com/*` `https://www.fabrikam.com/*` 。

## <a name="destination-path"></a>目的地路徑
如果您想要將 URL 的路徑區段取代為重新導向的一部分，您可以使用新的路徑值來設定此欄位。 否則，您可以選擇將路徑值保留為重新導向的一部分。 因此，使用此欄位，您可以將傳送至的所有要求重新導向至 `https://www.contoso.com/\*`  `https://www.contoso.com/redirected-site` 。

## <a name="query-string-parameters"></a>查詢字串參數
您也可以取代重新導向 URL 中的查詢字串參數。 為了取代傳入要求 URL 中的任何常設查詢字串，請將此欄位設定為 [取代]，然後設定適當的值。 否則，您可以將欄位設定為 [保留]，以保留原始的查詢字串集。 例如，使用此欄位，您可以將傳送至的所有流量重新導向 `https://www.contoso.com/foo/bar` 至 `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` 。 

## <a name="destination-fragment"></a>目的地片段
目的地片段是 URL 的部分，在 ' # ' 之後，瀏覽器通常會使用它來進入頁面上的特定區段。 您可以設定此欄位將片段新增至重新導向 URL。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png