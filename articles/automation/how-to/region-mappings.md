---
title: 已連結 Log Analytics 工作區的支援區域
description: 本文說明自動化帳戶與 Log Analytics 工作區之間支援的區域對應。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 06/12/2020
ms.topic: conceptual
manager: carmonm
ms.custom: references_regions
ms.openlocfilehash: a8b5f14147ee748f5d32d794f51d5437eeddfb51
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261183"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>已連結 Log Analytics 工作區的支援區域

在 Azure 自動化中，您可以針對 VM 啟用更新管理、變更追蹤和清查，以及停機期間啟動/停止 VM 功能。 不過，只有特定區域支援連結 Log Analytics 工作區以及訂閱中的自動化帳戶。 區域對應僅適用於自動化帳戶和 Log Analytics 工作區。 Log Analytics 工作區和自動化帳戶必須位於相同的訂閱中，但可位於部署至相同區域的不同資源群組中。 如需進一步資訊，請檢閱 [Log Analytics 工作區與自動化帳戶](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)。

## <a name="supported-mappings"></a>支援的對應

下表顯示支援的對應：

|**Log Analytics 工作區區域**|**Azure 自動化區域**|
|---|---|
|**美國**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS|WestCentralUS|
|**加拿大**||
|CanadaCentral|CanadaCentral|
|**亞太地區**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|ChinaEast2<sup>2</sup>|ChinaEast2|
|JapanEast|JapanEast|
|**歐洲**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**US Gov**||
|USGovVirginia|USGovVirginia|
|USGovArizona<sup>2</sup>|USGovArizona|

<sup>1</sup> Log Analytics 工作區與自動化帳戶的 EastUS 對應不是確切的區域對區域對應，但卻是正確的對應。

<sup>2</sup>在此區域中，僅支援更新管理，而且目前無法使用變更追蹤和清查之類的其他功能。

## <a name="unlink-a-workspace"></a>取消連結工作區

若決定不想再讓自動化帳戶與 Log Analytics 工作區整合，您可以直接從 Azure 入口網站將您的帳戶取消連結。 在繼續之前，首先您必須[移除](move-account.md#remove-features)更新管理、變更追蹤和清查，以及停機期間啟動/停止 VM (如果您正在使用它們)。 如果您未移除它們，則無法完成取消連結作業。 

移除功能後，您可以遵循下列步驟，取消連結您的自動化帳戶。

> [!NOTE]
> 某些包含舊版 Azure SQL 監視解決方案的功能可能已建立自動化資產，因此在取消連結工作區之前，可能需要將其移除。

1. 從 Azure 入口網站中，開啟您的自動化帳戶。 在 [自動化帳戶] 頁面上，選取 [相關資源] 底下的 [連結的工作區]。

2. 在 [取消連結工作區] 頁面上，選取 [取消連結工作區]。 您會收到提示，驗證您是否想要繼續。

3. 當 Azure 自動化將帳戶從您的 Log Analytics 工作區取消連結時，您可以從功能表在 [通知] 下追蹤進度。

4. 如果已使用更新管理，則您可能想要選擇移除不再需要的下列項目：

    * 更新排程：每個排程都有一個名稱符合您建立的更新部署。
    * 針對解決方案建立的混合式背景工作角色群組：每個都有類似於 `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8` 的名稱。

5. 如果您已使用停機期間啟動/停止 VM，則可以選擇移除不再需要的下列項目：

    * 啟動及停止 VM Runbook 排程
    * 啟動及停止 VM Runbook
    * 變數

或者，您可以將工作區與工作區內的自動化帳戶取消連結。

1. 在工作區中，選取 [相關資源] 底下的 [自動化帳戶]。 
2. 在 [自動化帳戶] 頁面上，選取 [取消連結帳戶]。

## <a name="next-steps"></a>後續步驟

* 了解[更新管理概觀](../automation-update-management.md)中的更新管理。
* 了解[變更追蹤和清查概觀](../change-tracking.md)中的變更追蹤和清查。
* 了解[停機期間啟動/停止 VM 概觀](../automation-solution-vm-management.md)中的停機期間啟動/停止 VM。
