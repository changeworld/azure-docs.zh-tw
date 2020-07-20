---
title: 藍圖部署的階段
description: 瞭解在建立藍圖指派時，Azure 藍圖服務所經歷的安全性和成品相關步驟。
ms.date: 05/06/2020
ms.topic: conceptual
ms.openlocfilehash: d3ccba6645e1b14fffc543af2a6ad40e3634e2ed
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970649"
---
# <a name="stages-of-a-blueprint-deployment"></a>藍圖部署的階段

部署藍圖時，Azure 藍圖服務會執行一系列的動作，以部署藍圖中定義的資源。 本文提供有關每個步驟所牽涉之內容的詳細資料。

藍圖部署是藉由將藍圖指派給訂用帳戶或[更新現有的指派](../how-to/update-existing-assignments.md)來觸發。 在部署期間，Azure 藍圖會採取下列高階步驟：

> [!div class="checklist"]
> - Azure 藍圖授與的擁有者許可權
> - 已建立藍圖指派物件
> - 選擇性-Azure 藍圖建立**系統指派的**受控識別
> - 受控識別會部署藍圖成品
> - 已撤銷 Azure 藍圖服務和**系統指派的**受控識別許可權

## <a name="azure-blueprints-granted-owner-rights"></a>Azure 藍圖授與的擁有者許可權

當使用[系統指派的受控識別](../../../active-directory/managed-identities-azure-resources/overview.md)受控識別時，Azure 藍圖服務主體會被授與指派的訂用帳戶或訂用帳戶的擁有者許可權。 授與的角色可讓 Azure 藍圖建立並于稍後撤銷**系統指派**的受控識別。 如果使用**使用者指派**的受控識別，則 Azure 藍圖服務主體不會取得訂用帳戶，也不需要訂閱的擁有者許可權。

如果指派是透過入口網站完成，系統就會自動授與許可權。 不過，如果指派是透過 REST API 完成，則授與許可權需要使用個別的 API 呼叫來完成。 Azure 藍圖 AppId 是 `f71766dc-90d9-4b7d-bd9d-4499c4331c3f` ，但服務主體會因租使用者而異。 使用[Azure Active Directory 圖形 API](../../../active-directory/develop/active-directory-graph-api.md)和 REST 端點[servicePrincipals](/graph/api/resources/serviceprincipal)來取得服務主體。 然後，透過[入口網站](../../../role-based-access-control/role-assignments-portal.md)、 [Azure CLI](../../../role-based-access-control/role-assignments-cli.md)、 [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md)、 [REST API](../../../role-based-access-control/role-assignments-rest.md)或[Azure Resource Manager 範本](../../../role-based-access-control/role-assignments-template.md)，將「_擁有_者」角色授與 Azure 藍圖。

Azure 藍圖服務不會直接部署資源。

## <a name="the-blueprint-assignment-object-is-created"></a>已建立藍圖指派物件

使用者、群組或服務主體會將藍圖指派給訂用帳戶。 指派物件存在於已指派藍圖的訂用帳戶層級。 部署所建立的資源不會在部署實體的內容中完成。

建立藍圖指派時，會選取[受控識別](../../../active-directory/managed-identities-azure-resources/overview.md)的類型。 預設值是**系統指派**的受控識別。 可以選擇**使用者指派**的受控識別。 使用**使用者指派**的受控識別時，必須先定義並授與許可權，才能建立藍圖指派。 「[擁有](../../../role-based-access-control/built-in-roles.md#owner)者」和「[藍圖」操作員](../../../role-based-access-control/built-in-roles.md#blueprint-operator)內建角色都具有 `blueprintAssignment/write` 建立使用**使用者指派**的受控識別之指派的必要許可權。

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>選擇性-Azure 藍圖建立系統指派的受控識別

在指派期間選取[系統指派的受控識別](../../../active-directory/managed-identities-azure-resources/overview.md)時，Azure 藍圖會建立身分識別，並將[擁有](../../../role-based-access-control/built-in-roles.md#owner)者角色授與受控識別。 如果[現有的指派已升級](../how-to/update-existing-assignments.md)，Azure 藍圖會使用先前建立的受控識別。

與藍圖指派相關的受控識別用來部署或重新部署藍圖中定義的資源。 這項設計可避免指派不小心干擾彼此。
這項設計也支援[資源鎖定](./resource-locking.md)功能，方法是從藍圖控制每個已部署資源的安全性。

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>受控識別會部署藍圖成品

受控識別接著會依照定義的[排序次序](./sequencing-order.md)，觸發藍圖內成品的 Resource Manager 部署。 您可以調整順序，以確保相依于其他成品的成品會以正確的順序部署。

部署的存取失敗通常是授與受控識別之存取層級的結果。 Azure 藍圖服務會管理**系統指派**之受控識別的安全性生命週期。 不過，使用者需負責管理**使用者指派**受控識別的許可權和生命週期。

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>已撤銷藍圖服務和系統指派的受控識別許可權

完成部署之後，Azure 藍圖撤銷訂用帳戶中**系統指派**之受控識別的許可權。 然後，Azure 藍圖服務會撤銷其對訂用帳戶的許可權。 許可權移除可防止 Azure 藍圖成為訂用帳戶的永久擁有者。

## <a name="next-steps"></a>後續步驟

- 了解如何使用[靜態與動態參數](parameters.md)。
- 了解如何自訂[藍圖排序順序](sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](resource-locking.md)。
- 了解如何[更新現有的指派](../how-to/update-existing-assignments.md)。
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題。
