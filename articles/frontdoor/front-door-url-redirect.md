---
title: Azure Front 門板-URL 重新導向 |Microsoft Docs
description: 這篇文章可協助您瞭解 Azure Front 大門如何支援其路由的 URL 重新導向（若已設定）。
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 5e3e44c4aee84fe9e2e21174a1d65fdf26b765a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295466"
---
# <a name="url-redirect"></a>URL 重新導向
您可以使用 Azure Front 門板來重新導向流量。 您可以重新導向多個層級的流量（通訊協定、主機名稱、路徑、查詢字串），而且所有功能都可以針對個別微服務進行設定，因為重新導向是以路徑為基礎。 這可簡化應用程式組態、將資源使用量最佳化，並支援新的重新導向案例，包括全域和路徑式重新導向。
</br>

![Azure Front 入口 URL 重新導向][1]

## <a name="redirection-types"></a>重新導向類型
重新導向類型會設定用戶端的回應狀態碼，以瞭解重新導向的目的。 支援下列類型的重新導向：

- **301 （永久移動）**：表示已將新的永久 URI 指派給目標資源，而且任何未來對此資源的參考，都應該使用其中一個包含的 uri。 使用301狀態碼來進行 HTTP 至 HTTPS 重新導向。 
- **302 （已找到）**：表示目標資源暫時位於不同的 URI 之下。 由於重新導向有時可能會改變，因此用戶端應該繼續針對未來的要求使用有效的要求 URI。
- **307 （暫時重新導向）**：表示目標資源暫時位於不同的 uri 底下，而且如果使用者代理程式執行自動重新導向至該 uri，則不能變更要求方法。 由於重新導向可能會隨著時間而改變，因此用戶端應該繼續使用原始的有效要求 URI 來處理未來的要求。
- **308 （永久重新導向）**：表示已將新的永久 URI 指派給目標資源，而且任何未來對此資源的參考，都應該使用其中一個包含的 uri。 具有連結編輯功能的用戶端，應該在可能的情況下，自動將有效要求 URI 的參考重新連結到伺服器所傳送的一或多個新參考。

## <a name="redirection-protocol"></a>重新導向通訊協定
您可以設定將用於重新導向的通訊協定。 這可允許重新導向功能的其中一個最常見使用案例，也就是將 HTTP 設定為 HTTPS 重新導向。

- **僅限 HTTPs**：如果您想要將流量從 HTTP 重新導向至 HTTPs，請將通訊協定設定為僅限 HTTPs。 Azure Front 門板建議您一律將重新導向設定為僅限 HTTPS。
- **僅限 HTTP**：這會將傳入要求重新導向至 HTTP。 只有當您想要保留流量 HTTP （非加密）時，才使用此值。
- **Match 要求**：此選項會保留連入要求所使用的通訊協定。 因此，HTTP 要求會保持 HTTP，而 HTTPS 要求會保留 HTTPS 後重新導向。

## <a name="destination-host"></a>目的地主機
在設定重新導向路由的過程中，您也可以變更重新導向要求的主機名稱或網域。 您可以設定此欄位來變更重新導向 URL 中的主機名稱，或保留傳入要求中的主機名稱。 因此，使用此欄位，您可以將傳送至的`https://www.contoso.com/*`所有`https://www.fabrikam.com/*`要求重新導向至。

## <a name="destination-path"></a>目的地路徑
如果您想要將 URL 的路徑區段取代為重新導向的一部分，您可以使用新的路徑值來設定此欄位。 否則，您可以選擇保留路徑值做為重新導向的一部分。 因此，使用此欄位，您可以將傳送至`https://www.contoso.com/\*`的所有要求`https://www.contoso.com/redirected-site`重新導向至。

## <a name="query-string-parameters"></a>查詢字串參數
您也可以取代重新導向 URL 中的查詢字串參數。 為了取代傳入要求 URL 中的任何常設查詢字串，請將此欄位設定為 [取代]，然後設定適當的值。 否則，您可以將欄位設定為 [保留]，以保留原始的查詢字串集。 例如，使用此欄位，您可以將傳送至`https://www.contoso.com/foo/bar`的所有流量重新導向`https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`至。 

## <a name="destination-fragment"></a>目的地片段
目的地片段是 URL 的部分，在 ' # ' 之後，瀏覽器通常會用來在頁面上的特定區段上進行放置。 您可以設定此欄位，以將片段新增至重新導向 URL。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png