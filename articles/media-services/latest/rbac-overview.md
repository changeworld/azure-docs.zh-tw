---
title: 媒體服務帳戶基於角色的存取控制 - Azure |微軟文檔
description: 本文討論 Azure 媒體服務帳戶基於角色的存取控制 （RBAC）。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "66236908"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>媒體服務帳戶基於角色的存取控制 （RBAC）

目前，Azure 媒體服務不定義特定于該服務的任何自訂角色。 要獲得對媒體服務帳戶的完全存取權限，客戶可以使用**擁有者**或**參與者**的內置角色。 這些角色之間的主要區別是：**擁有者**可以控制誰有權訪問資源，**而參與者**不能。 也可以使用內置**讀取器**角色，但使用者或應用程式將只能對媒體服務 API 具有讀取存取許可權。 

## <a name="design-principles"></a>設計原則

v3 API 的金鑰設計原則之一，是讓 API 更為安全。 v3 API 不會在**獲取**或**清單**操作上返回機密或憑據。 回應中的金鑰一律為 Null、空白或處理過的。 使用者需要調用單獨的操作方法來獲取機密或憑據。 **讀取器**角色不能調用資產、清單容器、流式處理器、清單內容金鑰、內容關鍵策略等操作。 如果需要，通過單獨操作，您可以在自訂角色中設置更精細的 RBAC 安全許可權。

要列出媒體服務支援的操作，執行以下操作：

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

[內置角色定義](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)一文準確告訴您角色授予的內容。 

如需詳細資訊，請參閱下列文章：

- [傳統訂用帳戶管理員角色、Azure RBAC 角色和 Azure AD 管理員角色](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [什麼是 Azure 資源的 RBAC？](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [使用 RBAC 管理訪問](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [媒體服務資來源提供者操作](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>後續步驟

- [使用媒體服務 v3 API 進行開發](media-services-apis-overview.md)
- [使用媒體服務 .NET 獲取內容金鑰策略](get-content-key-policy-dotnet-howto.md)
