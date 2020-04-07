---
title: 藍圖部署的階段
description: 瞭解 Azure 藍圖服務在創建藍圖分配時經歷的安全和項目相關步驟。
ms.date: 11/13/2019
ms.topic: conceptual
ms.openlocfilehash: 61d19c84cd659b9df3a272c5c2743944e51df06e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677320"
---
# <a name="stages-of-a-blueprint-deployment"></a>藍圖部署的階段

部署藍圖時,Azure 藍圖服務會執行一系列操作來部署藍圖中定義的資源。 本文詳細介紹了每個步驟涉及的內容。

藍圖部署是透過訂閱分配藍圖或[更新現有分配來觸發的](../how-to/update-existing-assignments.md)。 在部署過程中,Azure 藍圖採取以下高級步驟:

> [!div class="checklist"]
> - Azure 藍圖給擁有者權限
> - 建立藍圖分配物件
> - 選擇 - Azure 藍圖建立**系統分配的**託管識別
> - 託管識別部署藍圖工件
> - Azure 藍圖服務和**系統分配的**託管標識許可權被吊銷

## <a name="azure-blueprints-granted-owner-rights"></a>Azure 藍圖給擁有者權限

使用[系統分配的託管標識](../../../active-directory/managed-identities-azure-resources/overview.md)標識時,Azure 藍圖服務主體被授予已分配的訂閱或訂閱的所有者許可權。 授予的角色允許 Azure 藍圖創建並隨後撤銷**系統分配的**託管標識。 如果使用**使用者分配的**託管標識,Azure 藍圖服務主體將得不到並且不需要訂閱的擁有者許可權。

如果分配是通過門戶完成的,則會自動授予許可權。 但是,如果分配是通過 REST API 完成的,則授予許可權需要通過單獨的 API 調用來完成。 Azure 藍圖`f71766dc-90d9-4b7d-bd9d-4499c4331c3f`AppId 是 ,但服務主體因租戶而異。 使用[Azure 活動目錄圖形 API](../../../active-directory/develop/active-directory-graph-api.md)和 REST 終結點[服務主體](/graph/api/resources/serviceprincipal)獲取服務主體。 然後,通過[門戶](../../../role-based-access-control/role-assignments-portal.md)[、Azure CLI、Azure](../../../role-based-access-control/role-assignments-cli.md) [PowerShell、REST](../../../role-based-access-control/role-assignments-powershell.md) [API](../../../role-based-access-control/role-assignments-rest.md)或[資源管理器範本](../../../role-based-access-control/role-assignments-template.md)授予 Azure 藍圖_擁有者_角色。

Azure 藍圖服務不會直接部署資源。

## <a name="the-blueprint-assignment-object-is-created"></a>建立藍圖分配物件

使用者、組或服務主體為訂閱分配藍圖。 分配物件存在於分配藍圖的訂閱級別。 部署創建的資源不是在部署實體的上下文中完成的。

創建藍圖分配時,將選擇[託管標識](../../../active-directory/managed-identities-azure-resources/overview.md)的類型。 默認值為**系統分配的**託管標識。 可以選擇**使用者分配的**託管標識。 使用**使用者分配的**託管標識時,必須在創建藍圖分配之前定義它並授予它許可權。 [擁有者](../../../role-based-access-control/built-in-roles.md#owner)和[藍圖操作員](../../../role-based-access-control/built-in-roles.md#blueprint-operator)內置角色都具有創建使用**使用者分配的**託管`blueprintAssignment/write`標識的分配的必要許可權。

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>選擇 - Azure 藍圖建立系統分配的託管識別

在分配期間選擇[系統分配的託管標識](../../../active-directory/managed-identities-azure-resources/overview.md)時,Azure 藍圖將創建標識,並授予託管標識[擁有者](../../../role-based-access-control/built-in-roles.md#owner)角色。 如果[升級了現有分配](../how-to/update-existing-assignments.md),Azure 藍圖將使用以前創建的託管標識。

與藍圖分配相關的託管標識用於部署或重新部署藍圖中定義的資源。 此設計避免了互不經意間干擾的任務。
此設計還通過控制藍圖中每個已部署資源的安全性支援[資源鎖定](./resource-locking.md)功能。

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>託管識別部署藍圖工件

然後,託管標識按定義的[排序順序](./sequencing-order.md)觸發藍圖中工件的資源管理器部署。 可以調整順序,以確保以正確的順序部署依賴於其他工件的專案。

部署的訪問失敗通常是授予託管標識的訪問許可權級別的結果。 Azure 藍圖服務管理**系統分配的**託管標識的安全生命週期。 但是,使用者負責管理**使用者分配的**託管標識的許可權和生命週期。

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>藍圖服務和系統分配的託管標識許可權被吊銷

部署完成後,Azure 藍圖將從訂閱中撤銷**系統分配的**託管標識的許可權。 然後,Azure 藍圖服務從訂閱中撤銷其許可權。 許可權刪除可防止 Azure 藍圖成為訂閱的永久擁有者。

## <a name="next-steps"></a>後續步驟

- 了解如何使用[靜態與動態參數](parameters.md)。
- 了解如何自訂[藍圖排序順序](sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](resource-locking.md)。
- 瞭解如何[更新現有工作](../how-to/update-existing-assignments.md)。
- 在分配藍圖期間使用[常規故障排除時](../troubleshoot/general.md)解決問題。
