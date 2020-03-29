---
title: 為藍圖操作員設置環境
description: 瞭解如何配置 Azure 環境以與藍圖操作員內置基於角色的存取控制 （RBAC） 角色一起使用。
ms.date: 08/26/2019
ms.topic: how-to
ms.openlocfilehash: fba0dd3f2eeb69f768800d1d04640510462d3c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873211"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>設定藍圖操作員環境

可以將藍圖定義和藍圖分配的管理分配給不同的團隊。 架構師或治理團隊通常負責藍圖定義的生命週期管理，而運營團隊則負責管理這些集中控制的藍圖定義的分配。

**藍圖操作員**內置基於角色的存取控制 （RBAC） 專為此類方案而設計。 該角色允許操作類型團隊管理組織藍圖定義的分配，但不能修改它們。 這樣做需要在 Azure 環境中進行一些配置，本文將介紹必要的步驟。

## <a name="grant-permission-to-the-blueprint-operator"></a>授予藍圖操作員許可權

第一步是將**藍圖操作員**角色授予要分配藍圖的帳戶或安全性群組（建議）。 此操作應在管理組層次結構中的最高級別執行，該層次結構包含操作團隊應有權訪問的所有管理組和訂閱。 建議在授予這些許可權時遵循最低特權原則。

1. （推薦）[創建安全性群組並添加成員](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. 將**藍圖操作員**[的角色指派](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment)添加到帳戶或安全性群組

## <a name="user-assign-managed-identity"></a>使用者分配託管標識

藍圖定義可以使用系統分配或使用者分配的託管標識。 但是，使用**藍圖操作員**角色時，需要將藍圖定義配置為使用使用者分配的託管標識。 此外，需要授予**藍圖操作員**角色的帳戶或安全性群組在使用者分配的託管標識上被授予**託管標識操作員**角色。 如果沒有此許可權，藍圖分配將因缺少許可權而失敗。

1. [創建使用者分配的託管標識](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)，供分配的藍圖使用

1. 將**託管標識操作員**[的角色指派](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment)添加到帳戶或安全性群組。 將角色指派範圍限定為新使用者分配的託管標識。

1. 作為**藍圖運算子**，分配使用新使用者分配的託管標識的[藍圖](../create-blueprint-portal.md#assign-a-blueprint)。

## <a name="next-steps"></a>後續步驟

- 瞭解[藍圖生命週期](../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../concepts/resource-locking.md)。
- 在分配藍圖期間使用[常規故障排除時](../troubleshoot/general.md)解決問題。