---
title: 適用于 Azure 安全性基準測試的 Azure Resource Graph 安全性基準
description: Azure Resource Graph 的安全性基準提供程式指引和資源，可用於執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4c4884b643c436eae377b294b2981aa61aa4689d
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86137581"
---
# <a name="azure-resource-graph-security-baseline-for-azure-security-benchmark"></a>適用于 Azure 安全性基準測試的 Azure Resource Graph 安全性基準

此安全性基準會將[Azure 安全性基準測試](../../../security/benchmarks/overview.md)的指引套用至 Azure Resource Graph。 Azure 安全性基準測試會提供您如何在 Azure 上保護雲端解決方案的建議。 內容是依 Azure 安全性基準測試所定義的**安全性控制**，以及適用于 Azure Resource Graph 的相關指引來分組。 不適用 Azure Resource Graph 的**控制項**已排除。 若要查看 Azure Resource Graph 如何完全對應到 Azure 安全性基準，請參閱[完整的 azure 虛擬網路安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。



## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指引**： Azure Resource Graph 根據角色型存取控制（RBAC）提供資源類型和屬性的存取權。 定期審查並審查授與安全性主體（使用者、群組和服務帳戶）的存取權，以確保查詢會傳回適當資源的結果。

* [Azure Resource Graph 中的權限](../overview.md#permissions-in-azure-resource-graph)

* [如何使用 Azure 身分識別存取權檢閱](../../../active-directory/governance/access-reviews-overview.md)


**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱[安全性控制：資料保護](/azure/security/benchmarks/security-control-data-protection)。*

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用以角色為基礎的存取控制來控制對資源的存取

**指導**方針：使用角色型存取控制（RBAC）來控制對資料和資源的存取。 若要使用 Azure Resource Graph，您也必須具有您想要查詢之資源的適當存取權。 此存取範圍應設定為 [唯讀]，而且只能授與必要的人員。

* [Azure Resource Graph 中的權限](../overview.md#permissions-in-azure-resource-graph)

* [如何在 Azure 中設定 RBAC](../../../role-based-access-control/role-assignments-rest.md)


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱[安全性控制：清查和資產管理](/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導**方針：使用 Azure Resource Graph 來查詢和探索訂用帳戶、管理群組和租使用者中所有支援的資源。 請確定您在您的租使用者中具有適當的許可權，而且能夠列舉訂用帳戶中的所有 Azure 訂用帳戶及資源。

* [如何使用 Azure Resource Graph 建立查詢](../first-query-portal.md)

* [了解 Azure RBAC](../../../role-based-access-control/overview.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義並維護已核准 Azure 資源的清查

**指導**方針：根據您的組織需求，建立已核准 Azure 資源的清查和適用于計算資源的已核准軟體。 使用 Azure Resource Graph 來查詢核准的 Azure 資源和變更歷程記錄（預覽），以查看快照集並查看變更的內容。

* [使用 Azure Resource Graph 查詢 Azure 資源](../first-query-portal.md)

* [取得資源變更](../how-to/get-resource-changes.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導**方針：使用 Azure Resource Graph 來查詢和探索訂用帳戶、管理群組和租使用者中的資源。 請確定環境中的所有 Azure 資源都已核准。

* [使用 Azure Resource Graph 查詢 Azure 資源](../first-query-portal.md)

* [範例： Azure Resource Graph 的入門查詢](../samples/starter.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)
