---
title: 適用于 Azure 安全性基準測試的 Azure Resource Graph 安全性基準
description: Azure Resource Graph 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a85fdf1e1e27b1ce5abb0c7890b717d48ba3bd3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89230444"
---
# <a name="azure-resource-graph-security-baseline-for-azure-security-benchmark"></a>適用于 Azure 安全性基準測試的 Azure Resource Graph 安全性基準

此安全性基準會將 [Azure 安全性基準測試](../../../security/benchmarks/overview.md) 的指引套用至 Azure Resource Graph。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 Azure Resource Graph 的相關指引來分組。 已排除不適用 Azure Resource Graph 的**控制項**。 若要查看 Azure Resource Graph 如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 Azure 虛擬網路安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。



## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](../../../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導**方針： Azure Resource Graph 可讓您根據 azure 角色型存取控制 (azure RBAC) 來存取資源類型和屬性。 審核並檢查授與安全性主體的存取權 (使用者、群組和服務帳戶) 定期執行，以確保查詢會傳回適當資源的結果。

* [Azure Resource Graph 中的權限](../overview.md#permissions-in-azure-resource-graph)

* [如何使用 Azure 身分識別存取權檢閱](../../../active-directory/governance/access-reviews-overview.md)


**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱[安全性控制：資料保護](../../../security/benchmarks/security-control-data-protection.md)。*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導**方針：使用 azure 角色型存取控制 (azure RBAC) 來控制資料和資源的存取權。 若要使用 Azure Resource Graph，您也必須具有您想要查詢之資源的適當存取權。 此存取範圍應設定為唯讀，並只授與必要的人員。

* [Azure Resource Graph 中的權限](../overview.md#permissions-in-azure-resource-graph)

* [如何設定 Azure RBAC](../../../role-based-access-control/role-assignments-rest.md)


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱[安全性控制：清查和資產管理](../../../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導**方針：使用 Azure Resource Graph 來查詢及探索訂用帳戶、管理群組和租使用者中所有支援的資源。 確定您的租使用者具有適當的許可權，而且能夠列舉訂用帳戶中的所有 Azure 訂用帳戶及資源。

* [如何使用 Azure Resource Graph 建立查詢](../first-query-portal.md)

* [了解 Azure RBAC](../../../role-based-access-control/overview.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導**方針：根據您的組織需求，建立已核准 Azure 資源的清查，以及適用于計算資源的已核准軟體。 使用 Azure Resource Graph 來查詢已核准的 Azure 資源，並變更歷程記錄 (預覽) 以檢查快照並查看變更的內容。

* [使用 Azure Resource Graph 來查詢 Azure 資源](../first-query-portal.md)

* [取得資源變更](../how-to/get-resource-changes.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導**方針：使用 Azure Resource Graph 來查詢及探索訂用帳戶、管理群組和租使用者中的資源。 請確定環境中的所有 Azure 資源都已核准。

* [使用 Azure Resource Graph 來查詢 Azure 資源](../first-query-portal.md)

* [範例： Azure Resource Graph 的入門查詢](../samples/starter.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../../../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../../../security/benchmarks/security-baselines-overview.md)
