---
title: 設定藍圖操作員的環境
description: 瞭解如何設定 Azure 環境以搭配藍圖操作員 Azure 內建角色使用。
ms.date: 11/24/2020
ms.topic: how-to
ms.openlocfilehash: db8450516feab2fae4136f549959a29b006059aa
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95804910"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>設定藍圖操作員環境

您可以將藍圖定義和藍圖指派的管理指派給不同的小組。 架構設計師或治理小組必須負責規劃藍圖定義的生命週期管理，而作業小組負責管理這些集中控制之藍圖定義的指派。

**藍圖操作員** 內建角色是專為在這類案例中使用所設計。 角色可讓作業類型小組管理組織藍圖定義的指派，但不能修改它們。 這麼做需要在您的 Azure 環境中進行某些設定，而本文將說明必要的步驟。

## <a name="grant-permission-to-the-blueprint-operator"></a>將許可權授與藍圖操作員

第一個步驟是將 **藍圖操作員** 角色授與帳戶或安全性群組， (建議) 將會指派藍圖。 此動作應在管理群組階層中的最高層級完成，其中包含作業小組應該具有藍圖指派存取權的所有管理群組和訂用帳戶。 建議您在授與這些許可權時，遵循最低許可權的原則。

1.  (建議) [建立安全性群組並新增成員](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. 將 **藍圖操作員** 的 [角色指派新增](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment)至帳戶或安全性群組

## <a name="user-assign-managed-identity"></a>使用者-指派受控識別

藍圖定義可以使用系統指派或使用者指派的受控識別。 不過，使用 **藍圖操作員** 角色時，藍圖定義必須設定為使用使用者指派的受控識別。 此外，授與 **藍圖操作員** 角色的帳戶或安全性群組，必須授與使用者指派的受控識別上的 **受控識別操作員** 角色。 若沒有此許可權，藍圖指派將會因為許可權不足而失敗。

1. [建立使用者指派的受控識別](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) ，以供指派的藍圖使用。

1. 針對預期的範圍，將藍圖定義所需的任何角色或許可權授與使用者指派的受控識別。

1. 將 **受控識別操作員** 的 [角色指派新增](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment)至帳戶或安全性群組。 將角色指派的範圍設為新使用者指派的受控識別。

1. 作為 **藍圖操作員**，指派使用新使用者指派受控識別的 [藍圖](../create-blueprint-portal.md#assign-a-blueprint) 。

## <a name="next-steps"></a>後續步驟

- 了解[藍圖生命週期](../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../concepts/resource-locking.md)。
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題。