---
title: Azure Advisor 中的許可權
description: 建議程式許可權，以及它們可能會封鎖您設定訂閱或延遲或解除建議的能力。
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 402a21c47c4cba8f747d5d4601f9c95034c99262
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91712920"
---
# <a name="permissions-in-azure-advisor"></a>Azure Advisor 中的許可權

Azure Advisor 根據 Azure 資源和訂用帳戶的使用方式和設定來提供建議。 Advisor 會使用[azure 角色型存取控制](../role-based-access-control/overview.md)所提供的[內建角色](../role-based-access-control/built-in-roles.md) (azure RBAC) 來管理您對建議和 Advisor 功能的存取權。 

## <a name="roles-and-their-access"></a>角色及其存取權

下表定義角色以及它們在 Advisor 內的存取權：

| **角色** | **查看建議** | **編輯規則** | **編輯訂用帳戶設定** | **編輯資源群組設定**| **關閉並延遲建議**|
|---|:---:|:---:|:---:|:---:|:---:|
|訂用帳戶擁有者|**X**|**X**|**X**|**X**|**X**|
|訂用帳戶參與者|**X**|**X**|**X**|**X**|**X**|
|訂用帳戶讀取者|**X**|--|--|--|--|
|資源群組擁有者|**X**|--|--|**X**|**X**|
|資源群組參與者|**X**|--|--|**X**|**X**|
|資源群組讀取器|**X**|--|--|--|--|
|資源擁有者|**X**|--|--|--|**X**|
|資源參與者|**X**|--|--|--|**X**|
|資源讀取器|**X**|--|--|--|--|

> [!NOTE]
> 查看建議的存取權取決於您對建議受影響之資源的存取權。

## <a name="permissions-and-unavailable-actions"></a>許可權和無法使用的動作

缺乏適當的許可權可能會封鎖您在 Advisor 中執行動作的能力。 以下是一些常見的問題。

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>無法設定訂用帳戶或資源群組

當您嘗試在 Advisor 中設定訂用帳戶或資源群組時，您可能會看到要包含或排除的選項已停用。 此狀態表示您沒有該資源群組或訂用帳戶的足夠許可權層級。 若要解決此問題，請瞭解如何 [授與使用者存取權](../role-based-access-control/quickstart-assign-role-user-portal.md)。

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>無法延期或解除建議

如果您在嘗試延期或關閉建議時收到錯誤，您可能沒有足夠的許可權。 請確定您至少擁有所要延遲或關閉之建議受影響資源的參與者存取權。 若要解決此問題，請瞭解如何 [授與使用者存取權](../role-based-access-control/quickstart-assign-role-user-portal.md)。

## <a name="next-steps"></a>後續步驟

本文概述 Advisor 如何使用 Azure RBAC 來控制使用者權限，以及如何解決常見的問題。 若要深入了解 Advisor，請參閱︰

- [何謂 Azure 建議程式？](./advisor-overview.md)
- [開始使用 Advisor](./advisor-get-started.md)
