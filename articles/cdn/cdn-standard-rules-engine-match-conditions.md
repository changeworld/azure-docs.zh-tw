---
title: 適用于 Azure CDN 之標準規則引擎中的比對條件 |Microsoft Docs
description: 適用于 Azure 內容傳遞網路（Azure CDN）之標準規則引擎中比對條件的參考檔。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b8050b973027ac91ede0ba98f4d1c76831da9828
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81259916"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>適用于 Azure CDN 之標準規則引擎中的比對條件

在適用于 Azure 內容傳遞網路（Azure CDN）的[標準規則引擎](cdn-standard-rules-engine.md)中，規則包含一或多個比對條件和動作。 本文提供您可以在適用于 Azure CDN 的標準規則引擎中使用之比對條件的詳細說明。

規則的第一個部分是比對條件或一組比對條件。 在適用于 Azure CDN 的標準規則引擎中，每個規則最多可以有四個符合條件。 比對條件會識別執行已定義動作的特定類型要求。 如果您使用多個比對條件，則比對條件會使用和邏輯群組在一起。

例如，您可以使用比對條件：

- 根據特定的 IP 位址、國家/地區或區域來篩選要求。
- 依標頭資訊篩選要求。
- 篩選來自行動裝置或桌面裝置的要求。

## <a name="match-conditions"></a>比對條件

下列比對條件可用於 Azure CDN 的標準規則引擎。 

### <a name="device-type"></a>裝置類型 

識別從行動裝置或桌面裝置提出的要求。  

#### <a name="required-fields"></a>必要的欄位

運算子 | 支援的值
---------|----------------
Equals，不等於 | 行動裝置、桌上型電腦

### <a name="http-version"></a>HTTP 版本

根據要求的 HTTP 版本來識別要求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 支援的值
---------|----------------
Equals，不等於 | 2.0、1.1、1.0、0.9、全部

### <a name="request-cookies"></a>要求 Cookie

根據傳入要求中的 cookie 資訊來識別要求。

#### <a name="required-fields"></a>必要的欄位

Cookie 名稱 | 運算子 | Cookie 值 | 大小寫轉換
------------|----------|--------------|---------------
String | [標準操作員清單](#standard-operator-list) | String、Int | 無任何轉換，大小為大寫，到小寫

#### <a name="key-information"></a>重要資訊

- 當您指定 cookie 名稱時，不能使用萬用字元值（包括星號（ \* ））; 您必須使用確切的 cookie 名稱。
- 針對此比對條件的每個實例，您只能指定一個 cookie 名稱。
- Cookie 名稱比較不區分大小寫。
- 若要指定多個 cookie 值，請在每個 cookie 值之間使用單一空格。 
- Cookie 值可以利用萬用字元值。
- 如果尚未指定萬用字元值，則只有完全符合符合此比對條件。 例如，"Value" 會比對 "Value"，但不符合 "Value1"。 

### <a name="post-argument"></a>Post 引數

根據針對要求中所使用的 POST 要求方法所定義的引數來識別要求。 

#### <a name="required-fields"></a>必要的欄位

引數名稱 | 運算子 | 引數值 | 大小寫轉換
--------------|----------|----------------|---------------
String | [標準操作員清單](#standard-operator-list) | String、Int | 無任何轉換，大小為大寫，到小寫

### <a name="query-string"></a>查詢字串

識別包含特定查詢字串參數的要求。 這個參數會設定為符合特定模式的值。 要求 URL 中的查詢字串參數（例如，**參數 = 值**）會判斷是否符合此條件。 此比對條件會依查詢字串參數的名稱來識別查詢字串參數，並接受一個或多個參數值的值。

#### <a name="required-fields"></a>必要的欄位

運算子 | 查詢字串 | 大小寫轉換
---------|--------------|---------------
[標準操作員清單](#standard-operator-list) | String、Int | 無任何轉換，大小為大寫，到小寫

### <a name="remote-address"></a>遠端位址

根據要求者的位置或 IP 位址來識別要求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 支援的值
---------|-----------------
任意 | N/A
地理位置比對 | 國碼 (地區碼)
IP 相符 | IP 位址（以空格分隔）
不是任何 | N/A
不符合地理位置 | 國碼 (地區碼)
不符合 IP | IP 位址（以空格分隔）

#### <a name="key-information"></a>重要資訊

- 使用 CIDR 標記法。
- 若要指定多個 IP 位址和 IP 位址區塊，請在這些值之間使用單一空格：
  - **IPv4 範例**： *1.2.3.4 10.20.30.40*符合從位址1.2.3.4 或10.20.30.40 抵達的任何要求。
  - **IPv6 範例**： *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80*符合從任何位址1:2:3:4:5:6:7:8 或10:20:30:40:50:60:70:80 抵達的任何要求。
- IP 位址區塊的語法是基底 IP 位址，後面接著正斜線和首碼大小。 例如：
  - **IPv4 範例**： *5.5.5.64/26*會比對透過5.5.5.127 從位址5.5.5.64 抵達的任何要求。
  - **IPv6 範例**： *1:2:3:/48*符合從位址1:2:3:0:0:0:0:0 抵達1：2：3： ffff： ffff： ffff： ffff： ffff 的任何要求。

### <a name="request-body"></a>Request body

根據要求主體中出現的特定文字來識別要求。

#### <a name="required-fields"></a>必要的欄位

運算子 | Request body | 大小寫轉換
---------|--------------|---------------
[標準操作員清單](#standard-operator-list) | String、Int | 無任何轉換，大小為大寫，到小寫

### <a name="request-header"></a>要求標頭

識別在要求中使用特定標頭的要求。

#### <a name="required-fields"></a>必要的欄位

標頭名稱 | 運算子 | 標頭值 | 大小寫轉換
------------|----------|--------------|---------------
String | [標準操作員清單](#standard-operator-list) | String、Int | 無任何轉換，大小為大寫，到小寫

### <a name="request-method"></a>要求方法

識別使用指定要求方法的要求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 支援的值
---------|----------------
Equals，不等於 | GET、POST、PUT、DELETE、HEAD、OPTIONS、TRACE

#### <a name="key-information"></a>重要資訊

- 只有 GET 要求方法可以在 Azure CDN 中產生快取的內容。 所有其他要求方法則是透過網路進行 Proxy 處理。 

### <a name="request-protocol"></a>要求通訊協定

識別使用所使用之指定通訊協定的要求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 支援的值
---------|----------------
Equals，不等於 | HTTP、HTTPS

### <a name="request-url"></a>要求 URL

識別符合指定之 URL 的要求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 要求 URL | 大小寫轉換
---------|-------------|---------------
[標準操作員清單](#standard-operator-list) | String、Int | 無任何轉換，大小為大寫，到小寫

#### <a name="key-information"></a>重要資訊

- 當您使用此規則條件時，請務必包含通訊協定資訊。 例如： * https://www 。 \<yourdomain\>com*。

### <a name="url-file-extension"></a>URL 副檔名

在要求 URL 的檔案名中，識別包含指定副檔名的要求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 分機 | 大小寫轉換
---------|-----------|---------------
[標準操作員清單](#standard-operator-list) | String、Int | 無任何轉換，大小為大寫，到小寫

#### <a name="key-information"></a>重要資訊

- 針對延伸模組，請勿包含前置句點;例如，使用*html* ，而不是 *.html*。

### <a name="url-file-name"></a>URL 檔案名

識別在要求的 URL 中包含指定檔案名的要求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 檔案名稱 | 大小寫轉換
---------|-----------|---------------
[標準操作員清單](#standard-operator-list) | String、Int | 無任何轉換，大小為大寫，到小寫

#### <a name="key-information"></a>重要資訊

- 若要指定多個檔案名，請以單一空格分隔每個檔案名。 

### <a name="url-path"></a>URL 路徑

識別在要求的 URL 中包含指定路徑的要求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 值 | 大小寫轉換
---------|-------|---------------
[標準操作員清單](#standard-operator-list) | String、Int | 無任何轉換，大小為大寫，到小寫

#### <a name="key-information"></a>重要資訊

- 檔案名稱值可以使用萬用字元值。 例如，每個檔案名稱模式可包含一個或多個星號 (*)，其中每個星號都符合一個或多個字元序列。

## <a name="reference-for-rules-engine-match-conditions"></a>規則引擎比對條件的參考

### <a name="standard-operator-list"></a>標準操作員清單

對於接受標準運算子清單值的規則，下列運算子是有效的：

- 任意
- 等於 
- 包含 
- 開頭是 
- 結束於 
- 小於
- 小於或等於
- 大於
- 大於或等於
- 不是任何
- 不包含
- 開頭不是 
- 結尾不是 
- 不小於
- 不小於或等於
- 不大於
- 不大於或等於

對於*小於*和*大於或等於*的數值運算子，所使用的比較會以長度為基礎。 在此情況下，比對條件中的值應為等於您要比較之長度的整數。 

## <a name="next-steps"></a>後續步驟

- [Azure CDN 概觀](cdn-overview.md)
- [標準規則引擎參考](cdn-standard-rules-engine-reference.md)
- [標準規則引擎中的動作](cdn-standard-rules-engine-actions.md)
- [使用標準規則引擎強制執行 HTTPS](cdn-standard-rules-engine.md)
