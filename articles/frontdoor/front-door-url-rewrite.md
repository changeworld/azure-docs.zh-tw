---
title: Azure Front Door-URL 重寫 |Microsoft Docs
description: 本文可協助您瞭解 Azure Front Door 如何針對您的路由進行 URL 重寫（如果已設定）。
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
ms.openlocfilehash: eb5b4ab8a23a374aec54d65dd5390ab3fec3e905
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91445492"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL 重寫 (自訂轉送路徑)
Azure Front Door 藉由設定選擇性的 **自訂轉送路徑** ，以在建立轉送至後端的要求時使用，藉以支援 URL 重寫。 根據預設，如果未提供自訂轉送路徑，Front Door 會將傳入的 URL 路徑複製到轉送要求中使用的 URL。 用於轉送要求中的主機標頭，與針對所選後端設定的標頭相同。 請參閱[後端主機標頭](front-door-backend-pool.md#hostheader)以了解其功用與設定方式。

URL 重寫的強大部分是，自訂轉送路徑會將符合萬用字元路徑的傳入路徑的任何部分複製到轉送路徑 (這些路徑區段是下列範例中的 **綠色** 區段) ：
</br>

:::image type="content" source="./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg" alt-text="Azure Front Door URL 重寫":::

## <a name="url-rewrite-example"></a>URL 重寫範例
請考慮使用下列前端主機和設定路徑組合的路由規則：

| 主機      | 路徑       |
|------------|-------------|
| www\.contoso.com | /\*   |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

下表的第一欄顯示傳入要求的範例，而第二欄則顯示「最明確」的相符路由「路徑」。  資料表的第三個和後續的資料行是設定的 **自訂轉送路徑**範例。

例如，如果我們閱讀第二列的內容，便能知道針對傳入要求 `www.contoso.com/sub`，如果自訂轉送路徑為 `/`，則轉送路徑將會是 `/sub`。 如果自訂轉送路徑為 `/fwd/`，則轉送路徑將會是 `/fwd/sub`。 針對其餘的欄也依此類推。 下列路徑中的**粗體**部分，則代表符合萬用字元的部分。

| 傳入要求       | 最明確的相符路徑 | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www \. contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www \. contoso.com/**子**     | /\*                      | /**子**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www \. contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www \. contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www \. contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www \. contoso.com/foo/**bar** | /foo/\*                  | /**酒吧**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |

## <a name="optional-settings"></a>選擇性設定
還有其他選擇性設定可供您針對任何指定的路由規則設定進行指定：

* 快取設定-如果已停用或未指定，與此路由規則**相符的要求**就不會嘗試使用快取的內容，而且一律會從後端提取。 深入閱讀[搭配 Front Door 進行快取](front-door-caching.md)。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。
