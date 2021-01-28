---
title: 媒體服務帳戶的角色型存取控制
description: 本文討論 Azure 角色型存取控制 (適用于 Azure 媒體服務帳戶的 Azure RBAC) 。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 7c9edce71e0d0565de80153bfd5b3d5f1edb2e8f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955151"
---
# <a name="azure-role-based-access-control-azure-rbac-for-media-services-accounts"></a>Azure 角色型存取控制 (適用于媒體服務帳戶的 Azure RBAC) 

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

目前，Azure 媒體服務不會定義服務特定的任何自訂角色。 若要取得媒體服務帳戶的完整存取權，客戶可以使用 **擁有** 者或 **參與者** 的內建角色。 這些角色之間的主要差異在於： **擁有** 者可以控制誰可以存取資源，而 **參與者** 無法存取。 您也可以使用內建的「 **讀取** 者」角色，但使用者或應用程式只會有媒體服務 api 的讀取權限。 

## <a name="design-principles"></a>設計原則

v3 API 的金鑰設計原則之一，是讓 API 更為安全。 v3 Api 不會傳回 **取得** 或 **列出** 作業的秘密或認證。 回應中的金鑰一律為 Null、空白或處理過的。 使用者必須呼叫個別的動作方法，才能取得秘密或認證。 **讀者** 角色無法呼叫 ListContainerSas、StreamingLocator、ListContentKeys、ContentKeyPolicies 等作業。 擁有不同的動作可讓您視需要在自訂角色中設定更細微的 Azure RBAC 安全性許可權。

若要列出媒體服務支援的作業，請執行：

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

[內建的角色定義](../../role-based-access-control/built-in-roles.md)文章會告訴您該角色所授與的確切內容。 

如需詳細資訊，請參閱下列文章：

- [傳統訂用帳戶管理員角色、Azure 角色和 Azure AD 角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [什麼是 Azure 角色型存取控制 (Azure RBAC)？](../../role-based-access-control/overview.md)
- [使用 REST API 新增或移除 Azure 角色指派](../../role-based-access-control/role-assignments-rest.md)
- [媒體服務資源提供者作業](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>下一步

- [使用媒體服務 v3 Api 進行開發](media-services-apis-overview.md)
- [使用媒體服務 .NET 取得內容金鑰原則](get-content-key-policy-dotnet-howto.md)
