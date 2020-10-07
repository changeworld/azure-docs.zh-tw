---
title: Azure Front Door 規則引擎比對條件
description: 本文提供您適用於 Azure Front Door 規則引擎的各種比對條件清單。
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 0e874ae3d29f4143a4f8a9275d5ffcde48d08e6d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91569761"
---
# <a name="azure-front-door-rules-engine-match-conditions"></a>Azure Front Door 規則引擎比對條件

在 [AFD 規則引擎](front-door-rules-engine.md)中，一個規則包含零個或更多比對條件和一個動作。 本文針對您可在 AFD 規則引擎中使用的比對條件，提供詳細的說明。

規則的第一個部分是一個比對條件或一組比對條件。 一個規則最多可以包含 10 個比對條件。 比對條件會識別特定類型的要求，以便針對這類要求執行已定義的動作。 如果您使用多個比對條件，則會使用 AND 邏輯將比對條件群組在一起。 對於支援多個值的所有比對條件 (標示為「以空格分隔」)，則會採用 "OR" 運算子。

例如，您可以使用比對條件：

- 根據特定 IP 位址、國家/地區或區域來篩選要求。
- 依標頭資訊篩選要求。
- 篩選來自行動裝置或傳統型裝置的要求。

下列比對條件可使用於 Azure Front Door 規則引擎。  

## <a name="device-type"></a>裝置類型 

識別從行動裝置或傳統型裝置提出的要求。  

#### <a name="required-fields"></a>必要的欄位

運算子 | 支援的值
---------|----------------
Equals、Not equals | 行動、傳統型

## <a name="post-argument"></a>Post 引數

根據針對要求中使用的 POST 要求方法所定義的引數來識別要求。 

#### <a name="required-fields"></a>必要的欄位

引數名稱 | 運算子 | 引數值 | 大小寫轉換
--------------|----------|----------------|---------------
String | [標準運算子清單](#standard-operator-list) | String、Int | 小寫、大寫、修剪、移除空白字元、URL 編碼、URL 解碼

## <a name="query-string"></a>查詢字串

識別包含特定查詢字串參數的要求。 這個參數會設定為符合特定模式的值。 要求 URL 中的查詢字串參數 (例如，**parameter=value**) 會決定是否符合此條件。 此比對條件會依查詢字串參數的名稱來識別查詢字串參數，並接受一個或多個參數值的值。

#### <a name="required-fields"></a>必要的欄位

運算子 | 查詢字串 | 大小寫轉換
---------|--------------|---------------
[標準運算子清單](#standard-operator-list) | String、Int | 小寫、大寫、修剪、移除空白字元、URL 編碼、URL 解碼

## <a name="remote-address"></a>遠端位址

根據要求者的位置或 IP 位址來識別要求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 支援的值
---------|-----------------
地理位置比對 | 國碼 (地區碼)
IP 比對 | IP 位址 (以空格分隔)
非地理位置比對 | 國碼 (地區碼)
非 IP 比對 | IP 位址 (以空格分隔)

#### <a name="key-information"></a>重要資訊

- 使用 CIDR 標記法。
- 若要指定多個 IP 位址和 IP 位址區塊，請在值之間使用一個空格：
  - **IPv4 範例**：*1.2.3.4 10.20.30.40* 會比對來自位址 1.2.3.4 或 10.20.30.40 的任何要求。
  - **IPv6 範例**：*1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* 會比對來自位址 1:2:3:4:5:6:7:8 或 10:20:30:40:50:60:70:80 的任何要求。
- IP 位址區塊的語法是基底 IP 位址，後面接著正斜線和首碼大小。 例如：
  - **IPv4 範例**：*5.5.5.64/26* 會比對來自 5.5.5.64 到 5.5.5.127 的任何要求。
  - **IPv6 範例**：*1:2:3:/48* 會比對來自位址 1:2:3:0:0:0:0:0 到 1:2:3: ffff:ffff:ffff:ffff:ffff 的任何要求。

## <a name="request-body"></a>Request body

根據要求主體中出現的特定文字來識別要求。

#### <a name="required-fields"></a>必要的欄位

運算子 | Request body | 大小寫轉換
---------|--------------|---------------
[標準運算子清單](#standard-operator-list) | String、Int | 小寫、大寫、修剪、移除空白字元、URL 編碼、URL 解碼

## <a name="request-header"></a>要求標頭

識別在要求中使用特定標頭的要求。

#### <a name="required-fields"></a>必要的欄位

標頭名稱 | 運算子 | 標頭值 | 大小寫轉換
------------|----------|--------------|---------------
String | [標準運算子清單](#standard-operator-list) | String、Int | 小寫、大寫、修剪、移除空白字元、URL 編碼、URL 解碼

## <a name="request-method"></a>要求方法

識別使用指定要求方法的要求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 支援的值
---------|----------------
Equals、Not equals | GET、POST、PUT、DELETE、HEAD、OPTIONS、TRACE

#### <a name="key-information"></a>重要資訊

- 只有 GET 要求方法可以在 Azure Front Door 中產生快取的內容。 所有其他要求方法則是透過網路進行 Proxy 處理。 

## <a name="request-protocol"></a>要求通訊協定

識別使用指定通訊協定的要求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 支援的值
---------|----------------
Equals、Not equals | HTTP、HTTPS

## <a name="request-url"></a>要求 URL

識別符合指定 URL 的要求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 要求 URL | 大小寫轉換
---------|-------------|---------------
[標準運算子清單](#standard-operator-list) | String、Int | 小寫、大寫、修剪、移除空白字元、URL 編碼、URL 解碼

#### <a name="key-information"></a>重要資訊

- 當您使用此規則條件時，請務必包含通訊協定資訊。 例如： *https://www.\<yourdomain\>.com*。

## <a name="request-file-extension"></a>要求副檔名

識別在要求的 URL 中的檔案名稱中包含指定副檔名的要求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 分機 | 大小寫轉換
---------|-----------|---------------
[標準運算子清單](#standard-operator-list) | String、Int | 小寫、大寫、修剪、移除空白字元、URL 編碼、URL 解碼

#### <a name="key-information"></a>重要資訊

- 例如，請勿包含前置句號；例如，使用 htm 而非 .htm。

## <a name="request-file-name"></a>要求檔案名稱

識別在要求的 URL 中包含指定檔案名稱的要求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 檔案名稱 | 大小寫轉換
---------|-----------|---------------
[標準運算子清單](#standard-operator-list) | String、Int | 小寫、大寫、修剪、移除空白字元、URL 編碼、URL 解碼

#### <a name="key-information"></a>重要資訊

- 若要指定多個檔案名稱，請按 ENTER 鍵來分隔每個檔案名稱。 

## <a name="request-path"></a>要求路徑

識別在要求的 URL 中包含指定路徑的要求。

#### <a name="required-fields"></a>必要的欄位

運算子 | 值 | 大小寫轉換
---------|-------|---------------
[標準運算子清單](#standard-operator-list) | String、Int | 小寫、大寫、修剪、移除空白字元、URL 編碼、URL 解碼

## <a name="standard-operator-list"></a>標準運算子清單

如果規則可接受標準運算子清單中的值，則有效的運算子如下：

- 任意
- Equals 
- 包含 
- Begins with 
- 結束於 
- 小於
- Less than or equals
- 大於
- Greater than or equals
- Not any
- Not contains
- Not begins with 
- Not ends with 
- Not less than
- Not less than or equals
- Not greater than
- Not greater than or equals

對於 *Less than* 和 *Greater than or equals* 之類的數值運算子，所使用的比較會以長度為基礎。 比對條件中的值應為與您要比較的長度相等的整數。 


## <a name="next-steps"></a>後續步驟

- 了解如何設定您的第一個[規則引擎](front-door-tutorial-rules-engine.md)。 
- 深入了解[規則引擎動作](front-door-rules-engine-actions.md)
- 深入了解 [Azure Front Door 規則引擎](front-door-rules-engine.md)
