---
title: Azure Front Door 規則引擎動作
description: 本文提供您可以使用 Azure Front Door 規則引擎執行的各種動作清單。
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: 74c0a2617a01e8c24cd93a015b667081250657ad
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86521491"
---
# <a name="azure-front-door-rules-engine-actions"></a>Azure Front Door 規則引擎動作

在 [AFD 規則引擎](front-door-rules-engine.md)中，一個規則包含零個或更多比對條件和動作。 本文針對您可在 AFD 規則引擎中使用的動作，提供詳細的說明。

動作可定義一個比對條件或一組比對條件識別的要求類型所要套用的行為。 在 AFD 規則引擎中，一個規則最多可包含五個動作，其中只有一個動作可以是路由組態覆寫動作 (轉送或重新導向)。

下列動作可使用於 Azure Front Door 規則引擎。  

## <a name="modify-request-header"></a>修改要求標頭

使用此動作來修改在傳送至原始來源的要求中存在的標頭。

### <a name="required-fields"></a>必要的欄位

動作 | HTTP 標頭名稱 | 值
-------|------------------|------
附加 | 若已選取此選項且符合規則，在 [標頭名稱] 中指定的標頭會新增至具有指定值的要求。 如果標頭已存在，此值就會附加至現有的值。 | String
Overwrite | 若已選取此選項且符合規則，在 [標頭名稱] 中指定的標頭會新增至具有指定值的要求。 如果標頭已存在，指定的值就會覆寫現有的值。 | String
刪除 | 若已選取此選項、符合規則，且規則中指定的標頭存在，則會從要求中刪除此標頭。 | String

## <a name="modify-response-header"></a>修改回應標頭

使用此動作來修改在傳回用戶端的回應中存在的標頭。

### <a name="required-fields"></a>必要的欄位

動作 | HTTP 標頭名稱 | 值
-------|------------------|------
附加 | 若已選取此選項且符合規則，則會使用指定的 [值]，將 [標頭名稱] 中指定的標頭新增至回應。 如果標頭已存在，[值] 就會附加至現有的值。 | String
Overwrite | 若已選取此選項且符合規則，則會使用指定的 [值]，將 [標頭名稱] 中指定的標頭新增至回應。 如果標頭已存在，[值] 就會覆寫現有的值。 | String
刪除 | 若已選取此選項、符合規則，且規則中指定的標頭存在，則會從回應中刪除此標頭。 | String

## <a name="route-configuration-overrides"></a>路由組態覆寫 

### <a name="route-type-redirect"></a>路由類型：重新導向

使用此動作將用戶端重新導向至新的 URL。 

#### <a name="required-fields"></a>必要的欄位

欄位 | 描述 
------|------------
重新導向類型 | 選取要傳回給要求者的回應類型：找到 (302)、已移動 (301)、暫時重新導向 (307)，以及永久重新導向 (308)。
重新導向通訊協定 | 符合要求、HTTP、HTTPS。
目的地主機 | 選取您要將要求重新導向至的主機名稱。 保持空白以保留傳入主機。
目的地路徑 | 定義要在重新導向中使用的路徑。 保持空白以保留傳入路徑。  
查詢字串 | 定義重新導向中使用的查詢字串。 保持空白以保留傳入查詢字串。 
目的地片段 | 定義要在重新導向中使用的片段。 保持空白以保留傳入片段。 


### <a name="route-type-forward"></a>路由類型：下一頁

使用此動作將用戶端轉送至新的 URL。 此動作也包含 URL 重寫和快取的子動作。 

欄位 | 描述 
------|------------
後端集區 | 選取要覆寫並從中提供要求的後端集區。 這會顯示目前在 Front Door 設定檔中所有預先設定的後端集區。 
轉送通訊協定 | 符合要求、HTTP、HTTPS。
URL 重寫 | 使用此動作，針對正要路由傳送至原始來源的要求重寫其路徑。 若已啟用，請參閱下方以取得其他必要的欄位
Caching | Enabled、Disabled。 若已啟用，請參閱下方以取得其他必要的欄位。 

#### <a name="url-rewrite"></a>URL 重寫

使用此設定來設定選擇性 [自訂轉送路徑]，以在建構要轉送至後端的要求時使用。

欄位 | 描述 
------|------------
自訂轉送路徑 | 定義要求要轉送至的路徑。 

#### <a name="caching"></a>Caching

使用這些設定來控制針對含有查詢字串的要求快取檔案的方式，以及要根據所有參數或選取的參數來快取內容。 您可使用其他設定來覆寫存留時間 (TTL) 值，以針對規則比對條件所指定的要求，控制內容保留在快取中的時間長度。 若要強制以快取作為動作，請將快取欄位設定為 [已啟用]。 當您這麼做時，會出現下列選項： 

快取行為 |  描述              
---------------|----------------
忽略查詢字串 | 快取資產後，所有後續要求都會忽略查詢字串，直到所快取的資產到期為止。
快取每個唯一的 URL | 每個具有唯一 URL (包含查詢字串) 的要求都會被視為具有專屬快取的唯一資產。
忽略指定的查詢字串 | 快取會忽略 [查詢參數] 設定中所列的要求 URL 查詢字串。
包含指定的查詢字串 | [查詢參數] 設定中所列的要求 URL 查詢字串會用於快取。

其他欄位 |  描述 
------------------|---------------
動態壓縮 | Front Door 可在邊緣動態壓縮內容，因而產生較小且更快速的回應。
查詢參數 | 已允許 (或不允許) 的參數清單 (以逗號分隔)，用來作為快取的基礎。
快取持續時間 | 快取到期持續時間 (天、小時、分鐘、秒)。 所有值都必須為 Int。 

## <a name="next-steps"></a>後續步驟

- 了解如何設定您的第一個[規則引擎組態](front-door-tutorial-rules-engine.md)。 
- 深入了解[規則引擎比對條件](front-door-rules-engine-match-conditions.md)
- 深入了解 [Azure Front Door 規則引擎](front-door-rules-engine.md)
