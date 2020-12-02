---
title: Azure 安全性控制-清查和資產管理
description: Azure 安全性控制清查和資產管理
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 464439516ee86d2fb6f900c3fcaf179095192acf
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498875"
---
# <a name="security-control-inventory-and-asset-management"></a>安全性控制：清查和資產管理

清查和資產管理建議著重于解決有關主動管理 (清查、追蹤和正確) 所有 Azure 資源的問題，因此只會授與授權的資源存取權，並識別並移除未授權和未受管理的資源。

## <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.1 | 1.1、1.2、1.3、1.4、9.1、12。1 | 客戶 |

使用 Azure Resource Graph 來查詢/探索所有資源 (例如計算、儲存體、網路、埠和通訊協定等，) 您的 (訂用帳戶內 ) 。  確保您的租用戶中有適當的 (讀取) 權限，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。

雖然可透過 Resource Graph 探索傳統 Azure 資源，但強烈建議您從現在開始建立並使用 Azure Resource Manager 資源。

- [如何使用 Azure Resource Graph 建立查詢](../../governance/resource-graph/first-query-portal.md)

- [如何檢視您的 Azure 訂用帳戶](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](../../role-based-access-control/overview.md)

## <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.2 | 1.5 | 客戶 |

將標籤套用至 Azure 資源，提供中繼資料，以邏輯方式將它們組織成分類法。

- [如何建立和使用標籤](../../azure-resource-manager/management/tag-resources.md)

## <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.3 | 1.6 | 客戶 |

適當地使用標記、管理群組和個別訂用帳戶來組織和追蹤資產。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

- [如何建立額外的 Azure 訂閱](../../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../../governance/management-groups/create-management-group-portal.md)

- [如何建立和使用標籤](../../azure-resource-manager/management/tag-resources.md)

## <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.4 | 2.1 | 客戶 |

依據我們的組織需求，建立已核准的 Azure 資源和已核准的計算資源軟體的清查。

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.5 | 2.3、2.4 | 客戶 |

使用 Azure 原則來限制可在訂用帳戶中建立的資源類型 (s) 。

使用 Azure Resource Graph 來查詢/探索其訂閱內的資源。  確保已核准環境中的所有 Azure 資源。

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 建立查詢](../../governance/resource-graph/first-query-portal.md)

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.6 | 2.3、2.4 | 客戶 |

使用 Azure 虛擬機器清查，將虛擬機器上所有軟體的資訊收集自動化。 您可以從 Azure 入口網站取得軟體名稱、版本、發行者和重新整理時間。 若要取得安裝日期和其他資訊的存取權，請啟用來賓層級的診斷，並將 Windows 事件記錄檔帶入 Log Analytics 工作區。

- [如何啟用 Azure 虛擬機器清查](../../automation/automation-tutorial-installed-software.md)

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.7 | 2.5 | 客戶 |

使用 Azure 資訊安全中心的檔案完整性監視 (變更追蹤) 和虛擬機器清查，來識別安裝在虛擬機器上的所有軟體。 您可以執行自己的流程，以移除未經授權的軟體。 您也可以使用協力廠商解決方案來識別未核准的軟體。

- [如何使用檔案完整性監視](../../security-center/security-center-file-integrity-monitoring.md)

- [瞭解 Azure 變更追蹤](../../automation/change-tracking/overview.md)

- [如何啟用 Azure 虛擬機器清查](../../automation/automation-tutorial-installed-software.md)

## <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.8 | 2.6 | 客戶 |

使用 Azure 資訊安全中心適應性應用程式控制，以確保只有經過授權的軟體會執行，而且所有未經授權的軟體都會在 Azure 虛擬機器上遭到封鎖而無法執行。

- [如何使用 Azure 資訊安全中心適應性應用程式控制](../../security-center/security-center-adaptive-application.md)

## <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.9 | 2.6 | 客戶 |

使用 Azure 原則來限制您可以在環境中布建的服務。

- [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](../../governance/policy/samples/index.md)

## <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.10 | 2.7 | 客戶 |

使用 Azure 資訊安全中心適應性應用程式控制，以指定規則可能或不適用的檔案類型。

如果這不符合需求，請執行協力廠商解決方案。

- [如何使用 Azure 資訊安全中心適應性應用程式控制](../../security-center/security-center-adaptive-application.md)

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.11 | 2.9 | 客戶 |

使用 Azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 Azure 資源管理員互動的能力。

- [如何設定條件式存取以封鎖對 Azure 資源管理員的存取](../../role-based-access-control/conditional-access-azure-management.md)

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.12 | 2.9 | 客戶 |

根據腳本的類型而定，您可以使用作業系統專屬設定或協力廠商資源，以限制使用者在 Azure 計算資源內執行腳本的能力。  您也可以運用 Azure 資訊安全中心的自我調整應用程式控制，以確保只有授權的軟體會執行，而且所有未經授權的軟體都會在 Azure 虛擬機器上遭到封鎖而無法執行。

- [如何在 Windows 環境中控制 PowerShell 腳本執行](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [如何使用 Azure 資訊安全中心適應性應用程式控制](../../security-center/security-center-adaptive-application.md)

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.13 | 2.9 | 客戶 |

商務營運所需的軟體，但可能會對組織產生更高的風險，應該隔離在自己的虛擬機器和（或）虛擬網路內，並使用 Azure 防火牆或網路安全性群組來充分保護。

- [如何建立虛擬網路](../../virtual-network/quick-create-portal.md)

- [如何使用安全性設定建立 NSG](../../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-steps"></a>後續步驟

- 請參閱下一個安全性控制：[安全](security-control-secure-configuration.md)設定