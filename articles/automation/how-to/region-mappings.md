---
title: Azure 自動化和 Log Analytics 工作區對應
description: 本文說明自動化帳戶與 Log Analytics 工作區之間允許的對應，以支援解決方案
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1bc47fe6e1f0730bcff49e138df92f85ba3ef1a8
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114474"
---
# <a name="workspace-mappings"></a>工作區對應

啟用更新管理、變更追蹤和清查，或在離峰時段啟動/停止 Vm 時，只有特定區域支援連結 Log Analytics 工作區和訂用帳戶中的自動化帳戶。 此對應僅適用于自動化帳戶和 Log Analytics 工作區。 Log Analytics 工作區和自動化帳戶必須位於相同的訂用帳戶中，但可位於部署至相同區域的不同資源群組中。

如需詳細資訊，請參閱[Log Analytics 工作區和自動化帳戶](../azure-monitor/insights/solutions.md#log-analytics- workspace-and-automation-account)。

## <a name="supported-mappings"></a>支援的對應

下表顯示支援的對應：

|**Log Analytics 工作區區域**|**Azure 自動化區域**|
|---|---|
|**回饋**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Canada**||
|CanadaCentral|CanadaCentral|
|**亞太地區**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**歐洲**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> Log Analytics 工作區與自動化帳戶的 EastUS 對應不是區域對應的確切區域，而是正確的對應。

<sup>2</sup>由於容量限制，建立新資源時無法使用區域。 這包括自動化帳戶和 Log Analytics 工作區。 不過，區域中預先存在的連結資源應該會繼續工作。

## <a name="unlink-workspace"></a>取消連結工作區

如果您決定不想再整合您的自動化帳戶與 Log Analytics 工作區，您可以直接從 Azure 入口網站取消連結您的帳戶。 在繼續之前，您必須先移除更新管理、變更追蹤和清查，並在離峰時段啟動/停止 Vm （如果您使用它們）。 如果您未移除它們，就無法完成取消連結操作。 請參閱您要啟用的每個文章，以瞭解移除它所需的步驟。

移除這些專案之後，您可以執行下列步驟，將您的自動化帳戶取消連結。

> [!NOTE]
> 某些解決方案（包括較早版本的 Azure SQL 監視解決方案）可能已建立自動化資產，而且可能需要在取消連結工作區之前移除。

1. 從 [Azure 入口網站] 中，開啟您的自動化帳戶。 在 [自動化帳戶] 頁面上，選取 [**相關資源**] 底下的 [**連結工作區**]。

2. 在 [取消連結工作區] 頁面上，按一下 [取消連結工作區]****。 您會收到提示，確認是否要繼續。

3. 當 Azure 自動化嘗試將您的帳戶從 Log Analytics 工作區取消連結時，您可以從功能表在 [通知]**** 下追蹤進度。

4. 如果您使用更新管理，您可以選擇性地移除移除之後不再需要的下列專案。

    * 更新排程-每個都有一個與您所建立之更新部署相符的名稱。
    * 為解決方案建立的混合式背景工作角色群組，每個都`machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`有類似的名稱。

5. 如果您在離峰時段使用 [啟動/停止 Vm]，您可以選擇性地移除移除之後不需要的下列專案。

    * 啟動及停止 VM Runbook 排程
    * 啟動及停止 VM Runbook
    * 變數

或者，您可以將工作區與工作區中的自動化帳戶取消連結。

1. 在工作區中，選取 [**相關資源**] 下的 [**自動化帳戶**]。 
2. 在 [自動化帳戶] 頁面上，選取 [**取消連結帳戶**]。

## <a name="next-steps"></a>後續步驟

* 瞭解如何將更新管理和變更追蹤和清查上架：

    * 從[虛擬機器](../automation-onboard-solutions-from-vm.md)
    * 從您的[自動化帳戶](../automation-onboard-solutions-from-automation-account.md)
    * [流覽多部電腦](../automation-onboard-solutions-from-browse.md)時
    * 從[runbook](../automation-onboard-solutions.md)

* 瞭解如何在離峰時段讓啟動/停止 Vm 上線：

    * [于下班時間啟動/停止 Vm 總覽](../automation-solution-vm-management.md)
