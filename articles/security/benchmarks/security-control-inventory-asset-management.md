---
title: Azure 安全控制 - 庫存和資產管理
description: Azure 安全控制清單和資產管理
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: aece7278d134145e00b027c184f3817d5566e1e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408371"
---
# <a name="security-control-inventory-and-asset-management"></a>安全控制:庫存和資產管理

清單和資產管理建議側重於解決與主動管理(清單、跟蹤和更正)所有 Azure 資源相關的問題,以便僅授予授權資源訪問許可權,並識別和刪除未經授權的非託管資源。

## <a name="61-use-automated-asset-discovery-solution"></a>6.1: 使用自動資產發現解決方案

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | 客戶 |

使用 Azure 資源圖查詢/發現訂閱中的所有資源(如計算、存儲、網路、埠和協定等)。  請確保租戶中的適當(讀取)許可權,並枚舉訂閱中的所有 Azure 訂閱以及資源。

儘管可以通過資源圖發現經典 Azure 資源,但強烈建議今後創建和使用 Azure 資源管理器資源。

- [如何使用 Azure 資源圖建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [如何檢視 Azure 訂閱](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [瞭解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="62-maintain-asset-metadata"></a>6.2: 維護資產元資料

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 6.2 | 1.5 | 客戶 |

將標記應用於 Azure 資源,使元數據以邏輯方式將它們組織到分類中。

- [如何建立與使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="63-delete-unauthorized-azure-resources"></a>6.3: 刪除未經授權的 Azure 資源

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 6.3 | 1.6 | 客戶 |

在適當情況下,使用標記、管理組和單獨的訂閱來組織和跟蹤資產。 定期協調庫存,確保及時從訂閱中刪除未經授權的資源。

- [如何建立其他 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

- [如何建立與使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4:定義和維護已批准的 Azure 資源清單

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 6.4 | 2.1 | 客戶 |

根據我們的組織需求,創建已批准的 Azure 資源和已批准的軟體清單,以便計算資源。

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 監視未經批准的 Azure 資源

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 6.5 | 2.3、2.4 | 客戶 |

使用 Azure 策略對可在訂閱中創建的資源類型施加限制。

使用 Azure 資源圖查詢/發現其訂閱中的資源。  確保環境中的所有 Azure 資源都獲得批准。

- [如何設定與管理 Azure 政策](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何使用 Azure 圖形建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 監控計算資源中未經批准的軟體應用程式

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 6.6 | 2.3、2.4 | 客戶 |

使用 Azure 虛擬機器清單自動收集有關虛擬機器上所有軟體的資訊。 軟體名稱、版本、發行者和刷新時間可從 Azure 門戶獲得。 要造訪安裝日期和其他資訊,請啟用來賓級診斷並將 Windows 事件日誌引入日誌分析工作區。

- [如何開啟 Azure 虛擬機器清單](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 刪除未經批准的 Azure 資源和軟體應用程式

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 6.7 | 2.5 | 客戶 |

使用 Azure 安全中心的檔完整性監視(更改追蹤)和虛擬機清單標識安裝在虛擬機上的所有軟體。 您可以實現自己的刪除未經授權的軟體的過程。 您還可以使用第三方解決方案來識別未經批准的軟體。

- [如何使用檔案完整性監視](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring)

- [瞭解 Azure 變更追蹤](https://docs.microsoft.com/azure/automation/change-tracking)

- [如何開啟 Azure 虛擬機器清單](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="68-use-only-approved-applications"></a>6.8: 僅使用已批准的應用程式

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 6.8 | 2.6 | 客戶 |

使用 Azure 安全中心自適應應用程式控制項確保僅執行授權軟體,並且阻止在 Azure 虛擬機器上執行所有未經授權的軟體。

- [如何使用 Azure 安全中心自我調整應用程式控制項](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="69-use-only-approved-azure-services"></a>6.9: 僅使用已批准的 Azure 服務

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 6.9 | 2.6 | 客戶 |

使用 Azure 策略限制可以在環境中預配哪些服務。

- [如何設定與管理 Azure 政策](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何使用 Azure 政策拒絕特定資源類型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

## <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 維護已批准軟體標題的清單

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 6.10 | 2.7 | 客戶 |

使用 Azure 安全中心自適應應用程式控制項指定規則可能應用於哪些文件類型,也可能不適用於哪些檔案類型。

如果不符合要求,則實施第三方解決方案。

- [如何使用 Azure 安全中心自我調整應用程式控制項](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:限制使用者與 Azure 資源管理員互動的能力

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 6.11 | 2.9 | 客戶 |

使用 Azure 條件存取透過為「Microsoft Azure 管理」應用配置「阻止存取」來限制使用者與 Azure 資源管理器互動的能力。

- [如何設定條件存取以封鎖 Azure 資源管理員的存取](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 限制使用者在計算資源中執行文稿的能力

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 6.12 | 2.9 | 客戶 |

根據文本的類型,可以使用作業系統特定的配置或第三方資源來限制使用者在 Azure 計算資源中執行腳本的能力。  您還可以利用 Azure 安全中心自適應應用程式控制項來確保僅執行授權軟體,並且阻止在 Azure 虛擬機器上執行所有未經授權的軟體。

- [如何在 Windows 環境控制 PowerShell 文稿](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [如何使用 Azure 安全中心自我調整應用程式控制項](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 物理或邏輯上隔離高風險應用程式

| Azure 識別碼 | 獨聯體的代用品 | 責任 |
|--|--|--|
| 6.13 | 2.9 | 客戶 |

業務操作所需的軟體,但可能會給組織帶來更高的風險,應隔離在自己的虛擬機和/或虛擬網路中,並且使用 Azure 防火牆或網路安全組進行充分保護。

- [如何建立虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [如何建立具有安全設定的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)


## <a name="next-steps"></a>後續步驟

- 請參考下一個安全控制:[安全性設定](security-control-secure-configuration.md)