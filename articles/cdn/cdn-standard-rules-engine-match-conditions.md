---
title: Azure CDN 的標準規則引擎中的匹配條件 |微軟文檔
description: Azure 內容交付網路 （Azure CDN） 的標準規則引擎中匹配條件的參考文檔。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 425266e2a7ca42bb17ca598ddfc2f2b86591f32e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900182"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Azure CDN 的標準規則引擎中的匹配條件

在 Azure 內容交付網路 （Azure CDN）[的標準規則引擎](cdn-standard-rules-engine.md)中，規則由一個或多個匹配條件和操作組成。 本文詳細介紹了可在 Azure CDN 的標準規則引擎中使用匹配條件的詳細說明。

規則的第一部分是匹配條件或匹配條件集。 在 Azure CDN 的標準規則引擎中，每個規則最多可以有四個匹配條件。 匹配條件標識為其執行已定義操作的特定類型的請求。 如果使用多個匹配條件，則匹配條件將使用 AND 邏輯組合在一起。

例如，您可以使用比對條件：

- 基於特定 IP 位址、國家或地區篩選請求。
- 依標頭資訊篩選要求。
- 篩選來自行動裝置或桌面設備的請求。

## <a name="match-conditions"></a>比對條件

以下匹配條件可用於 Azure CDN 的標準規則引擎。 

### <a name="device-type"></a>裝置類型 

標識從行動裝置或桌面設備發出的請求。  

#### <a name="required-fields"></a>必要的欄位

運算子 | 支援的值
---------|----------------
等於，不等於 | 移動式、桌面式

### <a name="http-version"></a>HTTP 版本

根據請求的 HTTP 版本標識請求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 支援的值
---------|----------------
等於，不等於 | 2.0， 1.1， 1.0， 0.9， 全部

### <a name="request-cookies"></a>要求 Cookie

根據傳入請求中的 Cookie 資訊標識請求。

#### <a name="required-fields"></a>必要的欄位

Cookie 名稱 | 運算子 | 曲奇值 | 案例轉換
------------|----------|--------------|---------------
String | [標準操作員清單](#standard-operator-list) | 字串， int | 沒有轉換為大寫到小寫

#### <a name="key-information"></a>重要資訊

- 指定 Cookie 名稱時，不能使用萬用字元值（\*包括星號 （ ）;您必須使用確切的 Cookie 名稱。
- 每個匹配條件的實例只能指定一個 Cookie 名稱。
- Cookie 名稱比較不區分大小寫。
- 要指定多個 Cookie 值，請使用每個 Cookie 值之間的單個空格。 
- Cookie 值可以利用萬用字元值。
- 如果未指定萬用字元值，則只有完全符合滿足此匹配條件。 例如，"值"將匹配"價值"，而不是"值 1"。 

### <a name="post-argument"></a>後參數

根據為請求中使用的 POST 請求方法定義的參數標識請求。 

#### <a name="required-fields"></a>必要的欄位

引數名稱 | 運算子 | 引數值 | 案例轉換
--------------|----------|----------------|---------------
String | [標準操作員清單](#standard-operator-list) | 字串， int | 沒有轉換為大寫到小寫

### <a name="query-string"></a>查詢字串

標識包含特定查詢字串參數的請求。 此參數設置為與特定模式匹配的值。 請求 URL 中的查詢字串參數（例如參數 **=值**）確定是否滿足此條件。 此比對條件會依查詢字串參數的名稱來識別查詢字串參數，並接受一個或多個參數值的值。

#### <a name="required-fields"></a>必要的欄位

運算子 | 查詢字串 | 案例轉換
---------|--------------|---------------
[標準操作員清單](#standard-operator-list) | 字串， int | 沒有轉換為大寫到小寫

### <a name="remote-address"></a>遠端位址

根據要求者的位置或 IP 位址標識請求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 支援的值
---------|-----------------
任意 | N/A
地理匹配 | 國碼 (地區碼)
IP 匹配 | IP 位址（空間分隔）
沒有任何 | N/A
不是地理匹配 | 國碼 (地區碼)
不是 IP 匹配 | IP 位址（空間分隔）

#### <a name="key-information"></a>重要資訊

- 使用 CIDR 標記法。
- 要指定多個 IP 位址和 IP 位址塊，請在值之間使用單個空格：
  - **IPv4 示例**： *1.2.3.4 10.20.30.40*匹配從位址 1.2.3.4 或 10.20.30.40 到達的任何請求。
  - **IPv6**示例 *：1：2：3：4：4：5：6：8 10：20：30：40：60：70：80*匹配從任意一個位址 1：2：3：4：5：5：6：8 或 10：20：30：40：60：70：80 到達的任何請求。
- IP 位址區塊的語法是基底 IP 位址，後面接著正斜線和首碼大小。 例如：
  - **IPv4 示例**： *5.5.5.64/26*匹配從位址 5.5.5.64 到 5.5.5.127 到達的任何請求。
  - **IPv6 示例**： *1：2：3：/48*匹配從位址 1：2：3：0：0：0：0 到 1：2：3：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ffff：ff

### <a name="request-body"></a>Request body

根據請求正文中顯示的特定文本標識請求。

#### <a name="required-fields"></a>必要的欄位

運算子 | Request body | 案例轉換
---------|--------------|---------------
[標準操作員清單](#standard-operator-list) | 字串， int | 沒有轉換為大寫到小寫

### <a name="request-header"></a>要求標頭

標識在請求中使用特定標頭的請求。

#### <a name="required-fields"></a>必要的欄位

標頭名稱 | 運算子 | 標頭值 | 案例轉換
------------|----------|--------------|---------------
String | [標準操作員清單](#standard-operator-list) | 字串， int | 沒有轉換為大寫到小寫

### <a name="request-method"></a>要求方法

標識使用指定請求方法的請求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 支援的值
---------|----------------
等於，不等於 | 獲取， 發佈， 放， 刪除， 頭， 選項， 跟蹤

#### <a name="key-information"></a>重要資訊

- 只有 GET 請求方法才能在 Azure CDN 中生成緩存的內容。 所有其他要求方法則是透過網路進行 Proxy 處理。 

### <a name="request-protocol"></a>請求協定

標識使用所用指定協定的請求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 支援的值
---------|----------------
等於，不等於 | HTTP、HTTPS

### <a name="request-url"></a>要求 URL

標識與指定 URL 匹配的請求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 要求 URL | 案例轉換
---------|-------------|---------------
[標準操作員清單](#standard-operator-list) | 字串， int | 沒有轉換為大寫到小寫

#### <a name="key-information"></a>重要資訊

- 使用此規則條件時，請確保包含協定資訊。 例如： *https://www.\<yourdomain\>.com*.

### <a name="url-file-extension"></a>URL 檔副檔名

標識在請求 URL 中的檔案名中包含指定檔副檔名的請求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 分機 | 案例轉換
---------|-----------|---------------
[標準操作員清單](#standard-operator-list) | 字串， int | 沒有轉換為大寫到小寫

#### <a name="key-information"></a>重要資訊

- 對於擴展，不包括前導期;例如，使用*html*而不是 *.html*。

### <a name="url-file-name"></a>URL 檔案名

標識在請求 URL 中包含指定檔案名的請求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 檔案名稱 | 案例轉換
---------|-----------|---------------
[標準操作員清單](#standard-operator-list) | 字串， int | 沒有轉換為大寫到小寫

#### <a name="key-information"></a>重要資訊

- 要指定多個檔案名，請用單個空格分隔每個檔案名。 

### <a name="url-path"></a>URL 路徑

標識在請求 URL 中包含指定路徑的請求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 值 | 案例轉換
---------|-------|---------------
[標準操作員清單](#standard-operator-list) | 字串， int | 沒有轉換為大寫到小寫

#### <a name="key-information"></a>重要資訊

- 檔案名稱值可以使用萬用字元值。 例如，每個檔案名稱模式可包含一個或多個星號 (*)，其中每個星號都符合一個或多個字元序列。

## <a name="reference-for-rules-engine-match-conditions"></a>規則引擎比對條件的參考

### <a name="standard-operator-list"></a>標準操作員清單

對於接受標準運算子清單中的值的規則，以下運算子有效：

- 任意
- Equals 
- 包含 
- 開頭是 
- 結束於 
- 小於
- 小於或等於
- 大於
- 大於或等於
- 沒有任何
- 不包含
- 不以 
- 不以 
- 不小於
- 不小於或等於
- 不大於
- 不大於或等於

對於數位運算子（如*小於*和*大於或等於*），使用的比較基於長度。 在這種情況下，匹配條件中的值應為等於要比較的長度的整數。 

## <a name="next-steps"></a>後續步驟

- [Azure CDN 概觀](cdn-overview.md)
- [標準規則引擎參考](cdn-standard-rules-engine-reference.md)
- [標準規則引擎中的操作](cdn-standard-rules-engine-actions.md)
- [使用標準規則引擎強制執行 HTTPS](cdn-standard-rules-engine.md)
