---
title: 媒體服務帳戶的角色型存取控制-Azure |Microsoft Docs
description: 本文討論 Azure 媒體服務帳戶的角色型存取控制（RBAC）。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 93b2cd3a2565b14ea07d6db6b14dd146e4223528
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "66236908"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>媒體服務帳戶的角色型存取控制（RBAC）

目前，Azure 媒體服務不會定義服務特定的任何自訂角色。 若要取得媒體服務帳戶的完整存取權，客戶可以使用**擁有**者或**參與者**的內建角色。 這些角色的主要差異在於：**擁有**者可以控制誰有資源的存取權，而**參與者**則不能。 您也可以使用內建的**讀取**者角色，但使用者或應用程式只會擁有媒體服務 api 的讀取權限。 

## <a name="design-principles"></a>設計原則

v3 API 的金鑰設計原則之一，是讓 API 更為安全。 v3 Api 不會在**取得**或**列出**作業時傳回秘密或認證。 回應中的金鑰一律為 Null、空白或處理過的。 使用者必須呼叫個別的動作方法，才能取得秘密或認證。 「**讀取**者」角色無法呼叫 ListContainerSas、StreamingLocator. ListContentKeys、ContentKeyPolicies. GetPolicyPropertiesWithSecrets 之類的作業。 有個別的動作可讓您在自訂角色中設定更細微的 RBAC 安全性許可權（如有需要）。

若要列出媒體服務支援的作業，請執行：

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

[內建角色定義一](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)文會告訴您角色授與的確切內容。 

如需詳細資訊，請參閱下列文章：

- [傳統訂用帳戶管理員角色、Azure RBAC 角色和 Azure AD 管理員角色](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [什麼是適用于 Azure 資源的 RBAC？](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [使用 RBAC 來管理存取權](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [媒體服務資源提供者作業](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>後續步驟

- [使用媒體服務 v3 Api 進行開發](media-services-apis-overview.md)
- [使用媒體服務 .NET 取得內容金鑰原則](get-content-key-policy-dotnet-howto.md)
