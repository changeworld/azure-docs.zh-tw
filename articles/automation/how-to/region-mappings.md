---
title: Azure 自動化和紀錄分析工作區映射
description: 本文介紹了自動化帳戶和日誌分析工作區之間允許的映射,以支援解決方案
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7f6508648be1e857a29f46b57e8309a7ec797291
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681844"
---
# <a name="workspace-mappings"></a>工作區對應

啟用更新管理、更改追蹤和庫存等解決方案或非工作時間啟動/停止 VM 等解決方案時,僅支援某些區域連結日誌分析工作區和自動化帳戶。 此映射僅適用於自動化帳戶和日誌分析工作區。 報告到自動化帳戶或日誌分析工作區的資源可以駐留在其他區域。

## <a name="supported-mappings"></a>支援的對應

下表顯示支援的對應：

|**Log Analytics 工作區區域**|**Azure 自動化區域**|
|---|---|
|**我們**||
|東US<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|西中院<sup>2</sup>|西中院<sup>2</sup>|
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

<sup>1</sup>將日誌分析工作區映射到自動化帳戶的 EastUS 映射不是區域映射的確切區域,而是正確的映射。

<sup>2</sup>由於容量限制,在創建新資源時,該區域不可用。 這包括自動化帳戶和日誌分析工作區。 然而,該區域已有的相互聯繫資源應繼續發揮作用。

## <a name="unlink-workspace"></a>取消連結工作區

如果您決定不再希望將自動化帳戶與日誌分析工作區集成,則可以直接從 Azure 門戶取消連結帳戶。 在繼續操作之前,首先需要刪除更新管理、更改跟蹤和庫存,以及使用非工作時間解決方案時啟動/停止 VM。 如果不刪除它們,則無法完成取消連結操作。 檢閱已匯入特定解決方案的相關文章，以了解移除解決方案所需的步驟。

移除這些解決方案之後，您可以執行下列步驟以將您的自動化帳戶取消連結。

> [!NOTE]
> 某些解決方案(包括早期版本的 Azure SQL 監視解決方案)可能已創建了自動化資產,可能需要在取消連結工作區之前刪除。

1. 從 Azure 門戶打開自動化帳戶。 在"自動化帳戶"頁上,選擇 **"關聯資源**"下的 **"連結工作區**"。

2. 在 [取消連結工作區] 頁面上，按一下 [取消連結工作區]****。 您是否希望繼續,您將收到一個提示驗證。

3. 當 Azure 自動化嘗試將您的帳戶從 Log Analytics 工作區取消連結時，您可以從功能表在 [通知]**** 下追蹤進度。

4. 如果使用更新管理解決方案,則可選地可能需要刪除刪除解決方案後不再需要的以下專案。

    * 更新計劃 ─每個都有一個與您建立的更新部署匹配的名稱。
    * 為解決方案建立的混合工作群組 -每個群組的名稱都類似`machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`於 。

5. 如果在非工作時間使用"開始/停止 VM"解決方案,則可以選擇刪除刪除解決方案後不需要的以下專案。

    * 啟動及停止 VM Runbook 排程
    * 啟動及停止 VM Runbook
    * 變數

或者,您也可以從工作區中的自動化帳戶取消連結工作區。 

1. 在工作區中,在 **「相關資源**」下選擇 **「自動化帳戶**」。。 
2. 在"自動化帳戶"頁上,選擇 **"取消連結帳戶**"。

## <a name="next-steps"></a>後續步驟

* 瞭解如何在更新管理和更改追蹤和庫存解決方案中加入:

    * 從[虛擬機器](../automation-onboard-solutions-from-vm.md)
    * 從[您的自動化帳戶](../automation-onboard-solutions-from-automation-account.md)
    * [瀏覽多台電腦](../automation-onboard-solutions-from-browse.md)時
    * 從[執行簿](../automation-onboard-solutions.md)

* 瞭解如何在非工作時間使用啟動/停止 VM 解決方案:

    * [在非工作時間部署啟動/停止 VM](../automation-solution-vm-management.md)
