---
title: API 格式支援的限制和詳細資料
titleSuffix: Azure API Management
description: Azure API 管理中支援 Open API、WSDL 和 WADL 格式的已知問題和限制的詳細資料。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76513366"
---
# <a name="api-import-restrictions-and-known-issues"></a>API 匯入的限制和已知問題

## <a name="about-this-list"></a>關於本清單

匯入 API 時，您可能會遇到一些限制，或找出需要更正的問題，才能順利執行匯入。 本文記載這些限制，並依 API 的匯入格式加以組織。 它也會說明 OpenAPI 匯出的運作方式。

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>OpenAPI/Swagger 匯入限制

如果您在匯入 OpenAPI 文件時收到錯誤，請確定您已事先對其進行了驗證。 您可使用 Azure 入口網站中的設計工具 ([設計] - [前端] - [OpenAPI 規格編輯器])，或使用協力廠商工具 (例如 <a href="https://editor.swagger.io">Swagger 編輯器</a>) 來進行。

### <a name="general"></a><a name="open-api-general"> </a>一般

-   整個路徑和查詢所需的參數必須具有唯一的名稱。 (在 OpenAPI 中，參數名稱只需要在某個位置內是唯一的，例如路徑、查詢、標頭。 不過，在 API 管理中，可透過路徑和查詢參數來區分作業 (OpenAPI 並不支援此做法)。 這就是為什麼參數名稱必須在整個 URL 範本內是唯一的。)
-   `\$ref`指標無法參考外部檔案。
-   `x-ms-paths`和 `x-servers` 是唯一支援的延伸模組。
-   在匯入時系統會忽略自訂副檔名，不會儲存或保留用於匯出。
-   `Recursion`-API 管理不支援以遞迴方式定義的定義（例如，架構參考本身）。
-   來源檔案 URL (如果有的話) 會套用到相對的伺服器 URL。
-   系統會忽略安全性定義。
-   不支援 API 作業的內嵌架構定義。 架構定義會定義在 API 範圍中，並可在 API 作業要求或回應範圍中加以參考。
-   定義的 URL 參數必須是 URL 範本的一部分。
-   `Produces`關鍵字（描述 API 所傳回的 MIME 類型）不受支援。 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI 第 2 版

-   僅支援 JSON 格式。

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI 第 3 版

-   如果指定了多個 `servers` ，則 API 管理會嘗試選取第一個 HTTPS URL。 如果沒有任何 HTTPs URLs - 第一個 HTTP URL。 如果沒有任何 HTTP URLs，則伺服器 URL 將會是空的。
-   `Examples`不受支援，但 `example` 為。

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI 匯入、更新及匯出機制

### <a name="add-new-api-via-openapi-import"></a>透過 OpenAPI 匯入加入新的 API

針對在 OpenAPI 檔中找到的每個作業，將會建立新的作業，並將 Azure 資源名稱和顯示名稱分別設為 `operationId` 和 `summary` 。 `operationId`值會依照下面所述的規則正規化。 `summary`值是以-is 的形式匯入，其長度限制為300個字元。

如果 `operationId` 未指定（亦即不存在、 `null` 或空白），則會結合 HTTP 方法和路徑範本來產生 Azure 資源名稱值，例如 `get-foo` 。

如果 `summary` 未指定（亦即不存在、 `null` 或空白）， `display name` 值將會設定為 `operationId` 。 如果 `operationId` 未指定，則會藉由結合 HTTP 方法和路徑範本來產生顯示名稱值，例如 `Get - /foo` 。

### <a name="update-an-existing-api-via-openapi-import"></a>透過 OpenAPI 匯入來更新現有的 API

在匯入期間，現有的 API 會變更為符合 OpenAPI 檔中所述的 API。 OpenAPI 檔中的每個作業會藉由比較其 `operationId` 值與現有作業的 Azure 資源名稱，來比對現有的作業。

如果找到相符的內容，現有作業的屬性會「就地」更新。

如果找不到相符項，則會使用上一節所述的規則來建立新的作業。 針對每個新作業，匯入會嘗試使用相同的 HTTP 方法和路徑範本，從現有的作業複製原則。

將刪除所有現有的不相符作業。

若要讓匯入更容易預測，請遵循下列指導方針：

- 請務必指定 `operationId` 每個作業的屬性。
- 避免 `operationId` 在初始匯入後變更。
- 永遠不要 `operationId` 同時變更和 HTTP 方法或路徑範本。

### <a name="export-api-as-openapi"></a>將 API 匯出為 OpenAPI

針對每個作業，其 Azure 資源名稱會匯出為 `operationId` ，而顯示名稱會匯出為 `summary` 。
OperationId 的正規化規則

- 轉換為小寫。
- 以單一破折號取代每個非英數位元的序列，例如， `GET-/foo/{bar}?buzz={quix}` 將會轉換成 `get-foo-bar-buzz-quix-` 。
- 在兩邊修剪虛線，例如， `get-foo-bar-buzz-quix-` 將會變成`get-foo-bar-buzz-quix`
- 截斷以符合76個字元，小於資源名稱上限的四個字元。
- 如有需要，請為重複數據刪除尾碼使用剩餘的四個字元，格式為 `-1, -2, ..., -999` 。


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

WSDL 檔案是用來建立 SOAP 傳遞和 SOAP 對 REST Api。

-   **SOAP 繫結** - 僅支援「文件」和「常值」編碼的 SOAP 繫結樣式。 不支援「rpc」樣式或 SOAP 編碼。
-   **WSDL:Import** - 不支援此屬性。 客戶應該將匯入的項目合併成一份文件。
-   **具有多個部分的訊息** - 不支援這些類型的訊息。
-   **WCF wsHttpBinding** - 使用 Windows Communication Foundation 建立的 SOAP 服務應該使用 basicHttpBinding - 不支援 wsHttpBinding。
-   **MTOM** - 使用 MTOM 的服務「可能」<em></em>可以運作。 目前不提供官方支援。
-   **遞迴** - APIM 不支援遞迴定義的類型 (例如，參考自己的陣列)。
-   **多個命名空間** - 在結構描述中可使用多個命名空間，但只有目標命名空間可用來定義訊息部分。 目標以外的命名空間（用來定義其他輸入或輸出元素）不會保留。 雖然這類 WSDL 文件可以匯入，但在匯出慈時，所有訊息部分都會有 WSDL 的目標命名空間。
-   **陣列**-SOAP 對 REST 轉換僅支援下列範例中所示的包裝陣列：

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
