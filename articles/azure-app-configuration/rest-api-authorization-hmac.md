---
title: Azure 應用程式組態 REST API-HMAC 授權
description: 使用 HMAC 針對使用 REST API 的 Azure 應用程式組態進行授權
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 3746fcf06aea48c9c80696b634d6323b9cb21822
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932654"
---
# <a name="hmac-authorization---rest-api-reference"></a>HMAC 授權-REST API 參考

使用 HMAC 驗證時，作業會落在兩個類別中的其中一個： [讀取] 或 [寫入]。 讀寫存取金鑰會授與呼叫所有作業的許可權。 唯讀存取金鑰只會授與呼叫讀取作業的許可權。 存取金鑰是否為唯讀或讀寫取決於其 `readOnly` 屬性。 任何嘗試以唯讀存取金鑰提出寫入要求的嘗試，都會導致要求未經授權。

## <a name="obtaining-access-keys"></a>取得存取金鑰

[此處](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2019-10-01/appconfiguration.json)的 Azure 應用程式組態資源提供者規格會詳細說明用來取得存取金鑰的規格，以及用來取得這些金鑰的 API。 存取金鑰是透過「ConfigurationStores_ListKeys」操作取得。

## <a name="errors"></a>Errors

```http
HTTP/1.1 403 Forbidden
```

**原因：** 用來驗證要求的存取金鑰不提供執行所要求作業的必要許可權。
**解決方案：** 取得存取金鑰，以提供執行所要求作業的許可權，並使用它來驗證要求。
