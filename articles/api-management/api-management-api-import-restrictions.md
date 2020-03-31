---
title: API 格式支援的限制和詳細資訊
titleSuffix: Azure API Management
description: Azure API 管理中支援有關打開 API、WSDL 和 WADL 格式的已知問題和限制的詳細資訊。
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: 61d43addfdf9008cb7aa8a073dcf3bb702cb55f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513366"
---
# <a name="api-import-restrictions-and-known-issues"></a>API 匯入的限制和已知問題

## <a name="about-this-list"></a>關於本清單

導入 API 時，可能會遇到一些限制或識別在成功執行導入之前需要糾正的問題。 本文記錄這些限制，按 API 的導入格式組織。 它還描述了 OpenAPI 匯出的工作原理。

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>OpenAPI/搖擺器導入限制

如果您在匯入 OpenAPI 文件時收到錯誤，請確定您已事先對其進行了驗證。 您可使用 Azure 入口網站中的設計工具 ([設計] - [前端] - [OpenAPI 規格編輯器])，或使用協力廠商工具 (例如 <a href="https://editor.swagger.io">Swagger 編輯器</a>) 來進行。

### <a name="general"></a><a name="open-api-general"> </a>常規

-   整個路徑和查詢所需的參數必須具有唯一的名稱。 (在 OpenAPI 中，參數名稱只需要在某個位置內是唯一的，例如路徑、查詢、標頭。 不過，在 API 管理中，可透過路徑和查詢參數來區分作業 (OpenAPI 並不支援此做法)。 這就是為什麼參數名稱必須在整個 URL 範本內是唯一的。)
-   `\$ref`指標無法引用外部檔。
-   `x-ms-paths`是唯`x-servers`一受支援的擴展。
-   在匯入時系統會忽略自訂副檔名，不會儲存或保留用於匯出。
-   `Recursion`- API 管理不支援遞迴定義的定義（例如，引用自己的架構）。
-   來源檔案 URL (如果有的話) 會套用到相對的伺服器 URL。
-   安全定義將被忽略。
-   不支援 API 操作的內聯架構定義。 架構定義在 API 作用域中定義，可以在 API 操作請求或回應作用域中引用。
-   定義的 URL 參數需要是 URL 範本的一部分。
-   `Produces`關鍵字，描述 API 返回的 MIME 類型，不受支援。 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI 第 2 版

-   僅支援 JSON 格式。

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI 第 3 版

-   如果指定`servers`了多個，API 管理將嘗試選擇第一個 HTTP URL。 如果沒有任何 HTTPs URLs - 第一個 HTTP URL。 如果沒有任何 HTTP URLs，則伺服器 URL 將會是空的。
-   `Examples`不受支援，但`example`為。

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI 導入、更新和匯出機制

### <a name="add-new-api-via-openapi-import"></a>通過 OpenAPI 導入添加新 API

對於 OpenAPI 文檔中找到的每個操作，將創建一個新操作，將 Azure 資源名稱和顯示名稱`operationId`分別`summary`設置為 和。 `operationId`值按照下面描述的規則正常化。 `summary`值作為原樣導入，其長度限制為 300 個字元。

如果未`operationId`指定（即不存在、`null`不存在或為空），則 Azure 資源名稱值將通過組合 HTTP 方法和路徑範本（例如）。 `get-foo`

如果未`summary`指定（即不存在、`null`或為空），`display name`則值將設置為`operationId`。 如果未`operationId`指定，則顯示名稱值將通過組合 HTTP 方法和路徑範本（例如 ）。 `Get - /foo`

### <a name="update-an-existing-api-via-openapi-import"></a>通過 OpenAPI 導入更新現有 API

在導入期間，將更改現有 API 以匹配 OpenAPI 文檔中描述的 API。 OpenAPI 文檔中的每個操作通過將其`operationId`值與現有操作的 Azure 資源名稱進行比較，與現有操作的值相匹配。

如果找到匹配項，則現有操作的屬性將"就地"更新。

如果未找到匹配項，將使用上述部分中描述的規則創建新操作。 對於每個新操作，導入將嘗試使用相同的 HTTP 方法和路徑範本從現有操作複寫原則。

將刪除所有現有的不匹配的操作。

要使導入更加可預測，請遵循以下準則：

- 請確保為每個操作指定`operationId`屬性。
- 在初始導入`operationId`後避免更改。
- 切勿同時`operationId`更改 HTTP 方法或路徑範本。

### <a name="export-api-as-openapi"></a>將 API 匯出為 OpenAPI

對於每個操作，其 Azure 資源名稱將匯出為`operationId`，並且顯示名稱將匯出為`summary`。
操作 Id 的正常化規則

- 轉換為小寫。
- 將每個非字母數位字元序列替換為單個破折號，例如，`GET-/foo/{bar}?buzz={quix}`將轉換為`get-foo-bar-buzz-quix-`。
- 例如，兩側的修剪破折號`get-foo-bar-buzz-quix-`將成為`get-foo-bar-buzz-quix`
- 截取以適合 76 個字元，資源名稱的 4 個字元小於最大限制。
- 如有必要，使用其餘四個字元進行重復資料消除尾碼，其形式為`-1, -2, ..., -999`。


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

WSDL 檔案用於創建 SOAP 直通和 SOAP 到 REST API。

-   **SOAP 繫結** - 僅支援「文件」和「常值」編碼的 SOAP 繫結樣式。 不支援「rpc」樣式或 SOAP 編碼。
-   **WSDL:Import** - 不支援此屬性。 客戶應該將匯入的項目合併成一份文件。
-   **具有多個部分的訊息** - 不支援這些類型的訊息。
-   **WCF wsHttpBinding** - 使用 Windows Communication Foundation 建立的 SOAP 服務應該使用 basicHttpBinding - 不支援 wsHttpBinding。
-   **MTOM** - 使用 MTOM 的服務「可能」<em></em>可以運作。 目前不提供官方支援。
-   **遞迴** - APIM 不支援遞迴定義的類型 (例如，參考自己的陣列)。
-   **多個命名空間** - 在結構描述中可使用多個命名空間，但只有目標命名空間可用來定義訊息部分。 目標以外的命名空間（用於定義其他輸入或輸出元素）不保留。 雖然這類 WSDL 文件可以匯入，但在匯出慈時，所有訊息部分都會有 WSDL 的目標命名空間。
-   **陣列**- SOAP 到 REST 轉換僅支援下面示例中顯示的包裝陣列：

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>WADL

目前沒有任何已知的 WADL 匯入問題。
