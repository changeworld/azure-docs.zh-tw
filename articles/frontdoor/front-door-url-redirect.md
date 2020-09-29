---
title: Azure Front Door-URL 重新導向 |Microsoft Docs
description: 本文可協助您瞭解 Azure Front Door 如何針對其路由規則支援 URL 重新導向。
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 61077c7900530fd4c5be64054bedd9c5d087fe77
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91442054"
---
# <a name="url-redirect"></a>URL 重新導向
Azure Front Door 可以在下列每個層級重新導向流量：通訊協定、主機名稱、路徑、查詢字串。 這些功能可以針對個別的微服務進行設定，因為重新導向是以路徑為基礎。 這可以藉由將資源使用量優化，以簡化應用程式設定，並支援新的重新導向案例，包括全域和路徑型重新導向。
</br>

:::image type="content" source="./media/front-door-url-redirect/front-door-url-redirect.png" alt-text="Azure Front Door URL 重新導向":::

## <a name="redirection-types"></a>重新導向類型
重新導向類型會設定用戶端的回應狀態碼，以瞭解重新導向的目的。 支援下列類型的重新導向：

- **301 (永久移動) **：表示目標資源已獲指派新的永久 URI。 此資源的任何未來參考都會使用其中一個包含的 Uri。 使用301狀態碼進行 HTTP 至 HTTPS 重新導向。 
- **302 (找到) **：表示目標資源暫時在不同的 URI 下。 因為重新導向有時可能會變更，所以用戶端應該繼續使用有效的要求 URI 來取得未來的要求。
- **307 (暫時性重新導向) **：表示目標資源暫時在不同的 URI 下。 如果使用者代理程式會自動重新導向至該 URI，則不能變更此要求方法。 由於重新導向可能會隨著時間而改變，因此用戶端應該繼續使用原始的有效要求 URI 來取得未來的要求。
- **308 (永久重新導向) **：表示已為目標資源指派新的永久 URI。 此資源的未來任何參考都應該使用其中一個包含的 Uri。

## <a name="redirection-protocol"></a>重新導向通訊協定
您可以設定將用於重新導向的通訊協定。 重新導向功能最常見的使用案例是將 HTTP 設定為 HTTPS 重新導向。

- **僅限 HTTPs**：如果您想要將流量從 HTTP 重新導向至 HTTPs，請將通訊協定設定為 [僅 HTTPs]。 Azure Front Door 建議您一律將重新導向設定為僅限 HTTPS。
- **僅限 HTTP**：將連入要求重新導向至 HTTP。 只有當您想要保留流量 HTTP （非加密）時，才使用此值。
- **符合要求**：此選項會保留連入要求所使用的通訊協定。 因此，HTTP 要求會維持 HTTP，而 HTTPS 要求會維持 HTTPS 後續重新導向。

## <a name="destination-host"></a>目的地主機
在設定重新導向路由的過程中，您也可以變更重新導向要求的主機名稱或網域。 您可以設定此欄位來變更重新導向 URL 中的主機名稱，或從傳入要求中保留主機名稱。 因此，使用此欄位，您可以重新導向傳送至的所有要求 `https://www.contoso.com/*` `https://www.fabrikam.com/*` 。

## <a name="destination-path"></a>目的地路徑
如果您想要將 URL 的路徑區段取代為重新導向的一部分，您可以使用新的路徑值來設定此欄位。 否則，您可以選擇將路徑值保留為重新導向的一部分。 因此，使用此欄位，您可以將傳送至的所有要求重新導向至 `https://www.contoso.com/\*`  `https://www.contoso.com/redirected-site` 。

## <a name="query-string-parameters"></a>查詢字串參數
您也可以取代重新導向 URL 中的查詢字串參數。 若要從傳入的要求 URL 中取代任何現有的查詢字串，請將此欄位設定為 [取代]，然後設定適當的值。 否則，您可以將欄位設定為 [保留]，以保留原始的查詢字串集。 例如，使用此欄位，您可以將傳送至的所有流量重新導向 `https://www.contoso.com/foo/bar` 至 `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` 。 

## <a name="destination-fragment"></a>目的地片段
目的地片段是 URL 的部分，在 ' # ' 之後，瀏覽器會使用它來進入網頁的特定區段。 您可以設定此欄位將片段新增至重新導向 URL。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
