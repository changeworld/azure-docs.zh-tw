---
title: Azure 前門 - URL 重定向 |微軟文檔
description: 本文可説明您瞭解 Azure 前門如何支援其路由的 URL 重定向（如果配置）。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295466"
---
# <a name="url-redirect"></a>URL 重新導向
您可以使用 Azure 前門重定向流量。 您可以在多個級別（協定、主機名稱、路徑、查詢字串）重定向流量，並且所有功能都可以配置為單個微服務，因為重定向是基於路徑的。 這可簡化應用程式組態、將資源使用量最佳化，並支援新的重新導向案例，包括全域和路徑式重新導向。
</br>

![Azure 前門 URL 重定向][1]

## <a name="redirection-types"></a>重定向類型
重定向類型設置回應狀態碼，以便用戶端瞭解重定向的用途。 支援以下類型的重定向：

- **301（永久移動）：** 表示目標資源已分配新的永久 URI，將來對此資源的任何引用應使用隨附的 URI 之一。 使用 301 狀態碼進行 HTTP 到 HTTPS 重定向。 
- **302 （已找到）：** 指示目標資源暫時駐留在不同的 URI 下。 由於重定向有時可能會更改，因此用戶端應繼續使用有效的請求 URI 來執行將來的請求。
- **307（臨時重定向）：** 指示目標資源暫時駐留在不同的 URI 下，並且使用者代理如果自動重定向到該 URI，則使用者代理不得變更要求方法。 由於重定向可能會隨時間而變化，因此用戶端應繼續使用原始有效請求 URI 來執行將來的請求。
- **308（永久重定向）：** 指示目標資源已分配新的永久 URI，將來對此資源的任何引用應使用隨附的 URI 之一。 具有連結編輯功能的用戶端應盡可能自動將指向有效請求 URI 的引用重新連結到伺服器發送的一個或多個新引用。

## <a name="redirection-protocol"></a>重定向協定
您可以設置將用於重定向的協定。 這允許重定向功能的最常見用例之一，即將 HTTP 設置為 HTTPS 重定向。

- **僅 HTTPS**：如果希望將流量從 HTTP 重定向到 HTTPS，則僅將協定設置為 HTTPS。 Azure 前門建議應始終僅將重定向設置為 HTTPS。
- **僅 HTTP**：這將傳入請求重定向到 HTTP。 僅當要保留流量 HTTP（即非加密）時，才使用此值。
- **匹配請求**：此選項保留傳入請求使用的協定。 因此，HTTP 要求將保持 HTTP，HTTPS 請求將保持 HTTPS 後重定向。

## <a name="destination-host"></a>目標主機
作為配置重定向路由的一部分，您還可以更改重定向請求的主機名稱或域。 您可以將此欄位設置為更改重定向的 URL 中的主機名稱，或者以其他方式保留來自傳入請求的主機名稱。 因此，使用此欄位，您可以將發送到 的所有請求重定向`https://www.contoso.com/*`到`https://www.fabrikam.com/*`。

## <a name="destination-path"></a>目的地路徑
對於要將 URL 的路徑段作為重定向的一部分替換的情況，可以使用新的路徑值設置此欄位。 否則，您可以選擇保留路徑值作為重定向的一部分。 因此，使用此欄位，您可以將發送到 的所有請求重定向到`https://www.contoso.com/\*``https://www.contoso.com/redirected-site`。

## <a name="query-string-parameters"></a>查詢字串參數
您還可以替換重定向 URL 中的查詢字串參數。 為了替換來自傳入請求 URL 的任何常設查詢字串，請將此欄位設置為"替換"，然後設置相應的值。 否則，您可以通過將欄位設置為"保留"來保留原始查詢字串集。 例如，使用此欄位，可以將發送到 的所有流量重定向到`https://www.contoso.com/foo/bar``https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`。 

## <a name="destination-fragment"></a>目標片段
目標片段是"+"之後 URL 的部分，瀏覽器通常用於在頁面上的特定部分著陸。 您可以將此欄位設置為向重定向 URL 添加片段。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png