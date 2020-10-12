---
title: 適用于 Azure 安全性基準測試的 Azure 原則安全性基準
description: Azure 原則安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8b18da5e41e235b560918ad26117a0162078862c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540977"
---
# <a name="azure-policy-security-baseline-for-azure-security-benchmark"></a>適用于 Azure 安全性基準測試的 Azure 原則安全性基準

此安全性基準會將 [Azure 安全性基準測試](../../../security/benchmarks/overview.md) 的指引套用至 Azure 原則。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **合規性網域** 和 **安全性控制項** ，以及適用于 Azure 原則的相關指引來分組。 已排除不適用 Azure 原則的**控制項**。 若要查看 Azure 原則如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 Azure 原則安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

如需透過內建方案將 Azure 安全性基準測試與內建原則定義的對應，請參閱 [法規合規性： Azure 安全性基準測試](../samples/azure-security-benchmark.md)。

Azure 原則使用詞彙的 _擁有權_ 來取代 _責任_。 如需 _擁有權_的詳細資訊，請參閱 [Azure 原則原則定義](./definition-structure.md#type) 和 [雲端中的共同責任](../../../security/fundamentals/shared-responsibility.md)。


## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱[安全性控制：記錄和監視](../../../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導**方針： Azure 原則使用自動啟用的活動記錄，以包含事件來源、日期、使用者、時間戳記、來源位址、目的地位址和其他實用元素。

* [如何使用 Azure 監視器收集平臺記錄和計量](../../../azure-monitor/platform/diagnostic-settings.md)

* [瞭解 Azure 中的記錄和不同的記錄類型](../../../azure-monitor/platform/platform-logs-overview.md)


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](../../../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。 

您也可以使用 [Azure AD Privileged Identity Management](../../../active-directory/privileged-identity-management/pim-configure.md) 特殊許可權角色或 [Azure Resource Manager](../../../azure-resource-manager/management/overview.md)，啟用即時/僅限存取的解決方案。


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指引**：使用已設定 MFA 的特殊權限存取 PAW (特殊權限存取工作站) 登入和設定 Azure 資源。

* [瞭解特殊權限存取工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中啟用 MFA](../../../active-directory/authentication/howto-mfa-getstarted.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱[安全性控制：資料保護](../../../security/benchmarks/security-control-data-protection.md)。*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導**方針：使用 azure 角色型存取控制 (azure RBAC) 來控制 Azure 原則的存取權。

* [Azure 原則中的 Azure RBAC 權限](../overview.md#azure-rbac-permissions-in-azure-policy)

* [如何設定 Azure RBAC](../../../role-based-access-control/role-assignments-portal.md)


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器搭配活動記錄來建立 Azure 原則中發生變更時的警示。

* [如何建立 Azure 活動記錄事件的警示](../../../azure-monitor/platform/alerts-activity-log.md)


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱[安全性控制：清查和資產管理](../../../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至提供中繼資料的 Azure 資源，以邏輯方式依分類組織這些資源。 使用 Azure 原則 _修改_ 效果來回報和強制執行合規性和一致的標記管理。

* [教學課程：建立和管理原則](../tutorials/create-and-manage.md)

* [教學課程：管理標籤治理](../tutorials/govern-tags.md)


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導**方針：依據您的組織需求建立已核准原則定義和原則指派的清查。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導**方針：使用 Azure 原則對可在訂用帳戶中建立的資源類型施加限制。

* [如何設定和管理 Azure 原則](../tutorials/create-and-manage.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../../../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../../../security/benchmarks/security-baselines-overview.md)
