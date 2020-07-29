---
title: 設定藍圖操作員的環境
description: 瞭解如何設定您的 Azure 環境，以用於藍圖操作員內建的角色型存取控制（RBAC）角色。
ms.date: 05/06/2020
ms.topic: how-to
ms.openlocfilehash: a61997f5e3247e5b42d5e2f07c0fc38cc1b5114d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82864006"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>設定藍圖操作員環境

您的藍圖定義和藍圖指派的管理可以指派給不同的小組。 架構設計師或治理小組通常會負責藍圖定義的生命週期管理，而作業小組則負責管理這些集中控制之藍圖定義的指派。

**藍圖操作員**內建的角色型存取控制（RBAC）是專為在這類案例中使用而設計的。 此角色可讓作業類型小組管理組織藍圖定義的指派，但無法修改其功能。 若要這麼做，您必須在 Azure 環境中進行一些設定，這篇文章會說明必要的步驟。

## <a name="grant-permission-to-the-blueprint-operator"></a>授與藍圖運算子的許可權

第一個步驟是將要指派藍圖的帳戶或安全性群組（建議）授與**藍圖操作員**角色。 此動作應在管理群組階層中的最高層級完成，其中包含作業小組應該具有藍圖指派存取權的所有管理群組和訂用帳戶。 建議您在授與這些許可權時，遵循最低許可權的原則。

1. 使用[建立安全性群組並新增成員](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. 將**藍圖操作員**的[角色指派新增](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment)至帳戶或安全性群組

## <a name="user-assign-managed-identity"></a>使用者-指派受控識別

藍圖定義可以使用系統指派或使用者指派的受控識別。 不過，使用**藍圖操作員**角色時，藍圖定義必須設定為使用使用者指派的受控識別。 此外，授與**藍圖操作員**角色的帳戶或安全性群組，必須在使用者指派的受控識別上授與**受控識別操作員**角色。 若沒有此許可權，藍圖指派會因為許可權不足而失敗。

1. [建立使用者指派的受控識別](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)，供指派的藍圖使用

1. 將**受控識別操作員**的[角色指派新增](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment)至帳戶或安全性群組。 將角色指派的範圍限定為新使用者指派的受控識別。

1. 作為**藍圖運算子**，指派使用新使用者指派受控識別的[藍圖](../create-blueprint-portal.md#assign-a-blueprint)。

## <a name="next-steps"></a>後續步驟

- 了解[藍圖生命週期](../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../concepts/resource-locking.md)。
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題。