---
title: 使用自訂標頭將資料新增至稽核記錄 - Azure API for FHIR
description: 本文說明如何在 Azure API for FHIR 中使用自訂 HTTP 標頭將資料新增至稽核記錄。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: 937be72bfec96119474e7effe9ba88a2cf253444
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86081838"
---
# <a name="add-data-to-audit-logs-by-using-custom-http-headers"></a>使用自訂 HTTP 標頭將資料新增至稽核記錄

在 Azure 快速健康照護互通資源 (FHIR) API 中，使用者可能會想要在記錄檔 (其來自呼叫端系統) 中包含其他資訊。

例如，當 API 的使用者由外部系統驗證時，該系統會將呼叫轉送至 FHIR API。 在 FHIR API 層，因為已轉送該呼叫，原始使用者的相關資訊已遺失。 可能需要記錄並保留此使用者資訊，以供進行稽核或管理之用。 呼叫端系統可以提供使用者身分識別、呼叫端位置，或 HTTP 標頭中的其他必要資訊，其會在轉送呼叫時一起傳送。

您可以在下圖中看到此資料流程：

:::image type="content" source="media/custom-headers/custom-headers-diagram.png" alt-text="自訂標頭的圖表&quot;:::

您可以使用自訂標頭來擷取數個類型的資訊。 例如：

* 識別或授權資訊
* 呼叫端的來源
* 原始組織
* 用戶端系統詳細資料 (電子健康記錄、病患入口網站)

> [!IMPORTANT]
> 請注意，在自訂標頭中傳送的資訊，於 Azure 記錄監視中提供後，會儲存在 Microsoft 內部記錄系統中 30 天。 我們建議您在將任何資訊新增至自訂標頭之前，先將其加密。 您不應該透過客戶標頭傳遞任何 PHI 資訊。

您必須針對 HTTP 標頭使用下列命名慣例：X-MS-AZUREFHIR-AUDIT-\<name>。

這些 HTTP 標頭會包含在新增至記錄檔的屬性包中。 例如：

* X-MS-AZUREFHIR-AUDIT-USERID:1234 
* X-MS-AZUREFHIR-AUDIT-USERLOCATION:XXXX
* X-MS-AZUREFHIR-AUDIT-XYZ:1234

然後此資訊會在新增至記錄檔中的屬性資料行時序列化至 JSON。 例如：

```json
{ &quot;X-MS-AZUREFHIR-AUDIT-USERID&quot; : &quot;1234&quot;,
&quot;X-MS-AZUREFHIR-AUDIT-USERLOCATION&quot; : &quot;XXXX&quot;,
&quot;X-MS-AZUREFHIR-AUDIT-XYZ&quot; : &quot;1234&quot; }
```
 
就像任何 HTTP 標頭一樣，可以使用不同的值來重複相同的標頭名稱。 例如：

* X-MS-AZUREFHIR-AUDIT-USERLOCATION:HospitalA
* X-MS-AZUREFHIR-AUDIT-USERLOCATION:緊急狀況

新增至記錄檔時，會使用逗號分隔的清單結合值。 例如：

{ &quot;X-MS-AZUREFHIR-AUDIT-USERLOCATION&quot; :&quot;HospitalA, Emergency" }
 
您最多可以新增 10 個唯一的標頭 (具有相同標頭但不同值的重複項目只會被計為一個)。 任一個標頭的值最大長度為 2048 個字元。

如果您使用的是 Firefly C# 用戶端 API 程式庫，則程式碼會看起來像這樣：

```C#
FhirClient client;
client = new FhirClient(serverUrl);
client.OnBeforeRequest += (object sender, BeforeRequestEventArgs e) =>
{
    // Add custom headers to be added to the logs
    e.RawRequest.Headers.Add("X-MS-AZUREFHIR-AUDIT-UserLocation", "HospitalA");
};
client.Get("Patient");
```
## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何在 Azure API for FHIR 中使用自訂標頭將資料新增至稽核記錄。 接下來，請了解您可以在 Azure API for FHIR 中設定的其他設定。
 
>[!div class="nextstepaction"]
>[其他設定](azure-api-for-fhir-additional-settings.md)
