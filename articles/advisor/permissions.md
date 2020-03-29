---
title: Azure 顧問中的許可權
description: 顧問許可權及其如何阻止您配置訂閱或推遲或拒絕建議的能力。
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: c850d757044066d5c4a793e076436906d715833c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422325"
---
# <a name="permissions-in-azure-advisor"></a>Azure 顧問中的許可權

Azure 顧問根據 Azure 資源和訂閱的使用和配置提供建議。 顧問使用[基於角色的存取控制](https://docs.microsoft.com/azure/role-based-access-control/overview)（RBAC） 提供的[內置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)來管理您對建議和顧問功能的訪問。 

## <a name="roles-and-their-access"></a>角色及其存取權限

下表定義了他們在 Advisor 中的角色和存取權限：

| **角色** | **查看建議** | **編輯規則** | **編輯訂閱配置** | **編輯資源組配置**| **取消和推遲建議**|
|---|:---:|:---:|:---:|:---:|:---:|
|訂用帳戶擁有者|**Ⅹ**|**Ⅹ**|**Ⅹ**|**Ⅹ**|**Ⅹ**|
|訂用帳戶參與者|**Ⅹ**|**Ⅹ**|**Ⅹ**|**Ⅹ**|**Ⅹ**|
|訂閱讀取器|**Ⅹ**|--|--|--|--|
|資源組擁有者|**Ⅹ**|--|--|**Ⅹ**|**Ⅹ**|
|資源組參與者|**Ⅹ**|--|--|**Ⅹ**|**Ⅹ**|
|資源組讀取器|**Ⅹ**|--|--|--|--|
|資源擁有者|**Ⅹ**|--|--|--|**Ⅹ**|
|資源貢獻者|**Ⅹ**|--|--|--|**Ⅹ**|
|資源讀取器|**Ⅹ**|--|--|--|--|

> [!NOTE]
> 查看建議的訪問取決於您對建議受影響資源的訪問。

## <a name="permissions-and-unavailable-actions"></a>許可權和不可用操作

缺少適當的許可權可能會阻止您在 Advisor 中執行操作的能力。 以下是一些常見問題。

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>無法配置訂閱或資源組

當您嘗試在 Advisor 中配置訂閱或資源組時，您可能會看到已禁用包含或排除的選項。 此狀態表示您沒有足夠的權限等級用於該資源組或訂閱。 要解決此問題，瞭解如何[授予使用者存取權限](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)。

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>無法推遲或拒絕建議

如果在嘗試推遲或關閉建議時收到錯誤，則可能沒有足夠的許可權。 請確保您至少具有對要推遲或關閉的建議的影響資源的貢獻者存取權限。 要解決此問題，瞭解如何[授予使用者存取權限](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)。

## <a name="next-steps"></a>後續步驟

本文概述了 Advisor 如何使用 RBAC 來控制使用者許可權以及如何解決常見問題。 若要深入了解 Advisor，請參閱︰

- [何謂 Azure 建議程式？](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [開始使用 Advisor](https://docs.microsoft.com/azure/advisor/advisor-get-started)
