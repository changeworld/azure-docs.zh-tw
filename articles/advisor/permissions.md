---
title: Azure Advisor 中的許可權
description: Advisor 許可權，以及它們如何封鎖您設定訂閱或延期或解除建議的能力。
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: c850d757044066d5c4a793e076436906d715833c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75422325"
---
# <a name="permissions-in-azure-advisor"></a>Azure Advisor 中的許可權

Azure Advisor 會根據您的 Azure 資源和訂用帳戶的使用方式和設定提供建議。 Advisor 會使用[角色型存取控制](https://docs.microsoft.com/azure/role-based-access-control/overview)（RBAC）提供的[內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)來管理對建議和 Advisor 功能的存取。 

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

當您嘗試在 Advisor 中設定訂用帳戶或資源群組時，您可能會看到包含或排除的選項已停用。 此狀態表示您沒有足夠的許可權層級可供該資源群組或訂用帳戶使用。 若要解決此問題，請瞭解如何[授與使用者存取權](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)。

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>無法延遲或解除建議

如果您在嘗試延後或解除建議時收到錯誤，可能是您沒有足夠的許可權。 請確定您至少具有要延遲或關閉之建議的受影響資源的參與者存取權。 若要解決此問題，請瞭解如何[授與使用者存取權](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)。

## <a name="next-steps"></a>後續步驟

本文概述 Advisor 如何使用 RBAC 來控制使用者權限，以及如何解決常見的問題。 若要深入了解 Advisor，請參閱︰

- [何謂 Azure 建議程式？](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [開始使用 Advisor](https://docs.microsoft.com/azure/advisor/advisor-get-started)
