---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: e3732823be1c8391f2bec9018a094200c7c93a5e
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711189"
---
| 屬性 | 描述 |
|:--- |:---|
|**identity / type** | 用來提出要求的驗證類型。 例如：`OAuth`、`SAS Key`、`Account Key` 或 `Anonymous` |
|**identity / tokenHash**|這個欄位保留為僅供內部使用。 |
|**authorization / action** | 指派給要求的動作。 |
|**authorization / roleAssignmentId** | 角色指派識別碼。 例如： `4e2521b7-13be-4363-aeda-111111111111` 。|
|**authorization / roleDefinitionId** | 角色定義識別碼。 例如： `ba92f5b4-2d11-453d-a403-111111111111"` 。|
|**principals / id** | 安全性主體的識別碼。 例如： `a4711f3a-254f-4cfb-8a2d-111111111111` 。|
|**principals / type** | 安全性主體的類型。 例如： `ServicePrincipal` 。 |
|**requester / appID** | 用來作為要求者的 Open Authorization (OAuth) 應用程式識別碼。 <br> 例如： `d3f7d5fe-e64a-4e4e-871d-333333333333` 。|
|**requester / audience** | 要求的 OAuth 對象。 例如： `https://storage.azure.com` 。 |
|**requester / objectId** | 要求者的 OAuth 物件識別碼。 若為 Kerberos 驗證，則代表 Kerberos 已驗證使用者的物件識別碼。 例如： `0e0bf547-55e5-465c-91b7-2873712b249c` 。 |
|**requester / tenantId** | 身分識別的 OAuth 租用戶識別碼。 例如： `72f988bf-86f1-41af-91ab-222222222222` 。|
|**requester / tokenIssuer** | OAuth 權杖簽發者。 例如： `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/` 。|
|**requester / upn** | 要求者的使用者主體名稱 (UPN)。 例如： `someone@contoso.com` 。 |
|**requester / userName** | 這個欄位保留為僅供內部使用。|