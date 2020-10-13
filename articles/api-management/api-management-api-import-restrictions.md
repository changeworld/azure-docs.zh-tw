---
title: API 格式支援的限制和詳細資料
titleSuffix: Azure API Management
description: Azure API 管理中的 Open API、WSDL 和 WADL 格式支援的已知問題和限制的詳細資料。
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
ms.openlocfilehash: 86ed7f3941965bcac525a2ba71786d20a4753489
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335495"
---
# <a name="api-import-restrictions-and-known-issues"></a>API 匯入的限制和已知問題

## <a name="about-this-list"></a>關於本清單

匯入 API 時，您可能會遇到一些限制或找出需要修正的問題，才能成功執行匯入。 本文記錄這些限制，並依 API 的匯入格式來組織。 它也會說明 OpenAPI 匯出的運作方式。

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>OpenAPI/Swagger 匯入限制

如果您在匯入 OpenAPI 文件時收到錯誤，請確定您已事先對其進行了驗證。 您可使用 Azure 入口網站中的設計工具 ([設計] - [前端] - [OpenAPI 規格編輯器])，或使用協力廠商工具 (例如 <a href="https://editor.swagger.io">Swagger 編輯器</a>) 來進行。

### <a name="general"></a><a name="open-api-general"> </a>一般

-   整個路徑和查詢所需的參數必須具有唯一的名稱。 (在 OpenAPI 中，參數名稱只需要在某個位置內是唯一的，例如路徑、查詢、標頭。 不過，在 API 管理中，可透過路徑和查詢參數來區分作業 (OpenAPI 並不支援此做法)。 這就是為什麼參數名稱必須在整個 URL 範本內是唯一的。)
-   `\$ref` 指標無法參考外部檔案。
-   `x-ms-paths` 和 `x-servers` 是唯一支援的延伸模組。
-   在匯入時系統會忽略自訂副檔名，不會儲存或保留用於匯出。
-   `Recursion` -API 管理不支援以遞迴方式定義的定義 (例如，架構本身) 。
-   來源檔案 URL (如果有的話) 會套用到相對的伺服器 URL。
-   系統會忽略安全性定義。
-   不支援 API 作業的內嵌架構定義。 架構定義是在 API 範圍中定義，而且可以在 API 作業要求或回應範圍中參考。
-   定義的 URL 參數必須是 URL 範本的一部分。
-   `Produces` 不支援描述 API 所傳回之 MIME 類型的關鍵字。 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI 第 2 版

-   僅支援 JSON 格式。

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI 第 3 版

-   如果 `servers` 指定了多個，API 管理會嘗試選取第一個 HTTPS URL。 如果沒有任何 HTTPs URLs - 第一個 HTTP URL。 如果沒有任何 HTTP URLs，則伺服器 URL 將會是空的。
-   `Examples` 不受支援，但 `example` 為。

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI 匯入、更新和匯出機制

### <a name="general"></a><a name="open-import-export-general"> </a>一般

-   從 API 管理服務匯出的 API 定義主要適用于需要呼叫 api 管理服務中所裝載 API 之 API 管理服務外部的應用程式。 匯出的 API 定義不打算再次匯入至相同或不同的 API 管理服務。 如需跨不同服務/envionments 進行 API defiitions 的設定管理，請參閱搭配使用 API 管理服務與 Git 的相關檔。 

### <a name="add-new-api-via-openapi-import"></a>透過 OpenAPI 匯入來新增 API

針對 OpenAPI 檔中找到的每個作業，將會建立新的作業，並將 Azure 資源名稱和顯示名稱分別設定為 `operationId` 和 `summary` 。 `operationId` 值會依照下面所述的規則正規化。 `summary` 值會依原樣匯入，且長度限制為300個字元。

如果 `operationId` 未指定 (也就是不存在、 `null` 或空白) ，則會結合 HTTP 方法和路徑範本來產生 Azure 資源名稱值，例如 `get-foo` 。

如果 `summary` 未指定 (也就是、不存在、 `null` 或空白) ， `display name` 值會設為 `operationId` 。 如果 `operationId` 未指定，則會藉由結合 HTTP 方法和路徑範本（例如）來產生顯示名稱值 `Get - /foo` 。

### <a name="update-an-existing-api-via-openapi-import"></a>透過 OpenAPI 匯入來更新現有的 API

匯入期間，現有的 API 會變更為符合 OpenAPI 檔中所述的 API。 OpenAPI 檔中的每個作業都會藉由比較其 `operationId` 值與現有作業的 Azure 資源名稱，來與現有的作業進行比對。

如果找到相符的內容，現有作業的屬性將會「就地」更新。

如果找不到相符項，則會使用上一節所述的規則來建立新的作業。 針對每個新的作業，匯入將會嘗試從具有相同 HTTP 方法和路徑範本的現有作業複製原則。

將會刪除所有現有的不相符作業。

若要讓匯入更容易預測，請遵循下列指導方針：

- 請務必為 `operationId` 每個作業指定屬性。
- 避免在 `operationId` 初始匯入後變更。
- 永遠不會 `operationId` 同時變更 HTTP 方法或路徑範本。

### <a name="export-api-as-openapi"></a>將 API 匯出為 OpenAPI

針對每個作業，其 Azure 資源名稱將會匯出為 `operationId` ，而顯示名稱會匯出為 `summary` 。
OperationId 的正規化規則

- 轉換為小寫。
- 以單一破折號取代非英數位元的每個序列，例如， `GET-/foo/{bar}?buzz={quix}` 將會轉換成 `get-foo-bar-buzz-quix-` 。
- 例如，將兩個邊的虛線修剪 `get-foo-bar-buzz-quix-` 將會變成 `get-foo-bar-buzz-quix`
- 截斷以容納76個字元，不超過資源名稱上限的四個字元。
- 如有必要，請使用剩餘的四個字元做為重復資料刪除尾碼（如有必要），格式為 `-1, -2, ..., -999` 。


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

WSDL 檔案是用來建立 SOAP 傳遞和 SOAP 對 REST Api。

-   **SOAP 繫結** - 僅支援「文件」和「常值」編碼的 SOAP 繫結樣式。 不支援「rpc」樣式或 SOAP 編碼。
-   **WSDL:Import** - 不支援此屬性。 客戶應該將匯入的項目合併成一份文件。
-   **具有多個部分的訊息** - 不支援這些類型的訊息。
-   **WCF wsHttpBinding** - 使用 Windows Communication Foundation 建立的 SOAP 服務應該使用 basicHttpBinding - 不支援 wsHttpBinding。
-   **MTOM** - 使用 MTOM 的服務「可能」<em></em>可以運作。 目前不提供官方支援。
-   **遞迴** - APIM 不支援遞迴定義的類型 (例如，參考自己的陣列)。
-   **多個命名空間** - 在結構描述中可使用多個命名空間，但只有目標命名空間可用來定義訊息部分。 不會保留目標以外的命名空間（用來定義其他輸入或輸出元素）。 雖然這類 WSDL 文件可以匯入，但在匯出慈時，所有訊息部分都會有 WSDL 的目標命名空間。
-   **陣列** -SOAP 對 REST 轉換僅支援下列範例中所示的包裝陣列：

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
