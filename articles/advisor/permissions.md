---
title: Azure Advisor 中的許可權
description: Advisor 許可權，以及它們如何封鎖您設定訂閱或延期或解除建議的能力。
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 3c7b42372ae6de5c2f519b69aec8a154a2f35a82
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927380"
---
# <a name="permissions-in-azure-advisor"></a>Azure Advisor 中的許可權

Azure Advisor 會根據您的 Azure 資源和訂用帳戶的使用方式和設定提供建議。 Advisor 會使用[azure 角色型存取控制](../role-based-access-control/overview.md)所提供的[內建角色](../role-based-access-control/built-in-roles.md) (azure RBAC) 來管理對建議和 Advisor 功能的存取。 

## <a name="roles-and-their-access"></a>角色及其存取權

下表定義角色以及它們在 Advisor 中的存取權：

| **角色** | **查看建議** | **編輯規則** | **編輯訂用帳戶設定** | **編輯資源群組設定**| **關閉並延遲建議**|
|---|:---:|:---:|:---:|:---:|:---:|
|訂用帳戶擁有者|**X**|**X**|**X**|**X**|**X**|
|訂用帳戶參與者|**X**|**X**|**X**|**X**|**X**|
|訂用帳戶讀取器|**X**|--|--|--|--|
|資源群組擁有者|**X**|--|--|**X**|**X**|
|資源群組參與者|**X**|--|--|**X**|**X**|
|資源群組讀者|**X**|--|--|--|--|
|資源擁有者|**X**|--|--|--|**X**|
|資源參與者|**X**|--|--|--|**X**|
|資源讀取器|**X**|--|--|--|--|

> [!NOTE]
> View 建議的存取權取決於您對建議的受影響資源的存取權。

## <a name="permissions-and-unavailable-actions"></a>許可權和無法使用的動作

缺少適當許可權可能會封鎖您在 Advisor 中執行動作的能力。 以下是一些常見的問題。

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>無法設定訂用帳戶或資源群組

當您嘗試在 Advisor 中設定訂用帳戶或資源群組時，您可能會看到包含或排除的選項已停用。 此狀態表示您沒有足夠的許可權層級可供該資源群組或訂用帳戶使用。 若要解決此問題，請瞭解如何[授與使用者存取權](../role-based-access-control/quickstart-assign-role-user-portal.md)。

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>無法延遲或解除建議

如果您在嘗試延後或解除建議時收到錯誤，可能是您沒有足夠的許可權。 請確定您至少具有要延遲或關閉之建議的受影響資源的參與者存取權。 若要解決此問題，請瞭解如何[授與使用者存取權](../role-based-access-control/quickstart-assign-role-user-portal.md)。

## <a name="next-steps"></a>後續步驟

本文概述 Advisor 如何使用 RBAC 來控制使用者權限，以及如何解決常見的問題。 若要深入了解 Advisor，請參閱︰

- [什麼是 Azure Advisor？](./advisor-overview.md)
- [開始使用 Advisor](./advisor-get-started.md)
