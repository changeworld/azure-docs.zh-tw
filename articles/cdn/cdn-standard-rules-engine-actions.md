---
title: Azure CDN 的標準規則引擎中的操作 |微軟文檔
description: Azure 內容交付網路 （Azure CDN） 的標準規則引擎中操作的參考文檔。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 53280bc90f629d93ff8a045c80f34a73970b43f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74171641"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Azure CDN 的標準規則引擎中的操作

在 Azure 內容交付網路 （Azure CDN）[的標準規則引擎](cdn-standard-rules-engine.md)中，規則由一個或多個匹配條件和操作組成。 本文詳細介紹了在 Azure CDN 的標準規則引擎中可以使用的操作。

規則的第二部分是操作。 操作定義應用於匹配條件或匹配條件集標識的請求類型的行為。

## <a name="actions"></a>動作

以下操作可用於 Azure CDN 的標準規則引擎。 

### <a name="cache-expiration"></a>快取到期

使用此操作可以覆蓋規則與條件匹配的請求的終結點的活存時間 （TTL） 值。

#### <a name="required-fields"></a>必要的欄位

緩存行為 |  描述              
---------------|----------------
旁路緩存 | 選擇此選項且規則匹配時，不會緩存內容。
覆寫 | 選擇此選項且規則匹配時，從原點返回的 TTL 值將被用操作中指定的值覆蓋。
如果丟失，則設置 | 選擇此選項且規則匹配時，如果未從源返回 TTL 值，則規則將 TTL 設置到操作中指定的值。

#### <a name="additional-fields"></a>其他欄位

Days | 小時 | 分鐘 | 秒
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>緩存金鑰查詢字串

使用此操作可基於查詢字串修改緩存金鑰。

#### <a name="required-fields"></a>必要的欄位

行為 | 描述
---------|------------
包含 | 選擇此選項且規則匹配時，在生成緩存鍵時包括參數中指定的查詢字串。 
快取每個唯一的 URL | 選擇此選項且規則匹配時，每個唯一 URL 都有自己的緩存金鑰。 
排除 | 選擇此選項且規則匹配時，生成緩存鍵時將排除參數中指定的查詢字串。
忽略查詢字串 | 選擇此選項且規則匹配時，生成緩存鍵時不考慮查詢字串。 

### <a name="modify-request-header"></a>修改請求標頭

使用此操作可以修改發送到您的源的請求中存在的標頭。

#### <a name="required-fields"></a>必要的欄位

動作 | HTTP 標頭名稱 | 值
-------|------------------|------
附加 | 選擇此選項且規則匹配時，**標題名稱**中指定的標頭將添加到具有指定值的請求中。 如果標頭已存在，則該值將追加到現有值。 | String
Overwrite | 選擇此選項且規則匹配時，**標題名稱**中指定的標頭將添加到具有指定值的請求中。 如果標頭已存在，則指定的值將覆蓋現有值。 | String
刪除 | 選擇此選項後，規則匹配，並且規則中指定的標頭存在，標頭將從請求中刪除。 | String

### <a name="modify-response-header"></a>修改回應標頭

使用此操作可以修改返回給用戶端的回應中存在的標頭。

#### <a name="required-fields"></a>必要的欄位

動作 | HTTP 標頭名稱 | 值
-------|------------------|------
附加 | 選擇此選項且規則匹配時，使用指定的**值**將標題**名稱**中指定的標頭添加到回應中。 如果標頭已存在，則**將值**追加到現有值。 | String
Overwrite | 選擇此選項且規則匹配時，使用指定的**值**將標題**名稱**中指定的標頭添加到回應中。 如果標頭已存在，**則"值**"將覆蓋現有值。 | String
刪除 | 選擇此選項後，規則匹配，並且規則中指定的標頭存在，標頭將從回應中刪除。 | String

### <a name="url-redirect"></a>URL 重新導向

使用此操作將用戶端重定向到新 URL。 

#### <a name="required-fields"></a>必要的欄位

欄位 | 描述 
------|------------
類型 | 選擇要返回到請求器的回應類型：找到 （302）、移動 （301）、臨時重定向 （307） 和永久重定向 （308）。
通訊協定 | 匹配請求，HTTP，HTTPS。
主機名稱 | 選擇要將請求重定向到的主機名稱。 留空以保留傳入主機。
Path | 定義要在重定向中使用的路徑。 留空以保留傳入路徑。  
查詢字串 | 定義重定向中使用的查詢字串。 留空以保留傳入查詢字串。 
片段 | 定義要在重定向中使用的片段。 留空以保留傳入的片段。 

我們強烈建議您使用絕對 URL。 使用相對 URL 可能會將 Azure CDN URL 重定向到無效路徑。 

### <a name="url-rewrite"></a>URL 重寫

使用此操作重寫正在前往您的原點的請求的路徑。

#### <a name="required-fields"></a>必要的欄位

欄位 | 描述 
------|------------
源模式 | 定義要替換的 URL 路徑中的源模式。 目前，源模式使用基於首碼的匹配。 要匹配所有 URL 路徑，請使用正向斜**/** 杠 （ ） 作為源模式值。
Destination | 定義要在重寫中使用的目標路徑。 目標路徑覆蓋源模式。
保留無與倫比的路徑 | 如果設置為 **"是**"，則源模式後的剩餘路徑將追加到新目標路徑。 

## <a name="next-steps"></a>後續步驟

- [Azure CDN 概觀](cdn-overview.md)
- [標準規則引擎參考](cdn-standard-rules-engine-reference.md)
- [標準規則引擎中的匹配條件](cdn-standard-rules-engine-match-conditions.md)
- [使用標準規則引擎強制執行 HTTPS](cdn-standard-rules-engine.md)
