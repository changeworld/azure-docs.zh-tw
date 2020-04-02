---
title: 設定啟動/停止 VM 解決方案
description: 本文介紹如何在非工作時間解決方案期間配置開始/停止 VM 以支援不同的用例或方案。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: d3ca8d17d6637f0ab2b5a5d3d7a99ac0beaafd2e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550379"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>如何在非工作時間設定啟動/停止 VM 解決方案

使用非工作時間啟動/停止 VM 解決方案,您可以:

- [計畫 VM 啟動與停止](#schedule)。
- 使用[Azure 標記](#tags)(經典 VM 不支援)計劃 VM 以升序啟動和停止。
- 基於[CPU 使用率低](#cpuutil)自動停止 VM。

本文介紹如何成功配置解決方案以支援這些方案。 您還可以瞭解如何為解決方案執行其他常見配置設定,例如:

* [設定電子郵件通知](#configure-email-notifications)

* [新增 VM](#add-a-vm)

* [排除 VM](#exclude-a-vm)

* [修改啟動和關機排程](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>案例 1：依排程啟動/停止 VM

此案例是您首次部署解決方案時的預設設定。 例如，您可以將它設定為在您晚上離開公司時停止訂用帳戶上的所有虛擬機器，並在您早上回到辦公室時啟動它們。 當您在部署期間設定排程 **Scheduled-StartVM** 和 **Scheduled-StopVM** 時，它們會啟動和停止目標虛擬機器。 支援將此解決方案設定為僅停止 VM，請參閱[修改啟動和關機排程](#modify-the-startup-and-shutdown-schedules)以了解如何設定自訂的排程。

> [!NOTE]
> 時區是您設定排程表時間參數時所在的目前時區。 但它會以 UTC 格式儲存在 Azure 自動化中。 您不需要執行任何時區轉換，因為它會在部署期間處理。

您可藉由設定下列變數：**External_Start_ResourceGroupNames**、**External_Stop_ResourceGroupNames** 和 **External_ExcludeVMNames** 來控制有哪些虛擬機器包含於範圍中。

您可以將動作的目標設為某個訂用帳戶和資源群組，或設為特定的虛擬機器清單，但不可同時設定為兩者。

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>針對訂用帳戶和資源群組設定啟動和停止動作目標

1. 設定 **External_Stop_ResourceGroupNames** 和 **External_ExcludeVMNames** 變數來指定目標 VM。

2. 啟用及更新 **Scheduled-StartVM** 和 **Scheduled-StopVM** 排程。

3. 執行 **ScheduledStartStop_Parent** Runbook，並將 ACTION 參數設為 **start**，然後將 WHATIF 參數設為 **True** 以預覽變更。

### <a name="target-the-start-and-stop-action-by-vm-list"></a>透過 VM 清單設定啟動和停止動作目標

1. 執行 **ScheduledStartStop_Parent** Runbook，並將 ACTION 參數設為 **start**，在 *VMList* 參數中新增以逗號分隔的 VM 清單，然後將 WHATIF 參數設為 **True**。 預覽變更。

2. 使用以逗號分隔的虛擬機器清單 (VM1、VM2、VM3) 設定 **External_ExcludeVMNames** 參數。

3. 此案例不會接受 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupnames** 變數。 針對此案例，您需要建立自己的自動化排程。 如需詳細資訊，請參閱[在 Azure 自動化中排程 Runbook](../automation/automation-schedules.md)。

    > [!NOTE]
    > **Target ResourceGroup Names** 的值會儲存為 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupNames** 的值。 如需進一步的細微設定，您可以修改這些變數來以不同的資源群組為目標。 針對啟動動作請使用 **External_Start_ResourceGroupNames**，而針對停止動作請使用 **External_Stop_ResourceGroupNames**。 虛擬機器會自動新增至啟動和停止排程。

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>案例 2：使用標記依順序啟動/停止 VM

在多部虛擬機器上包含兩個或多個元件並支援分散式工作負載的環境中，支援元件的啟動和停止順序是非常重要的。 您可以執行下列步驟來完成此案例：

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>針對訂用帳戶和資源群組設定啟動和停止動作目標

1. 向**External_Start_ResourceGroupNames**變數和**External_Stop_ResourceGroupNames**變數中為目標的 VM 添加序列**啟動**和序列**停止**標記,該標記具有正整數值。 啟動和停止動作會依遞增順序執行。 若要了解如何標記虛擬機器，請參閱[在 Azure 中標記 Windows 虛擬機器](../virtual-machines/windows/tag.md)和[在 Azure 中標記 Linux 虛擬機器](../virtual-machines/linux/tag.md)。

2. 修改排程 **Sequenced-StartVM** 和 **Sequenced-StopVM** 以符合您所需的日期和時間，然後啟用排程。

3. 執行 **SequencedStartStop_Parent** Runbook，並將 ACTION 參數設為 **start**，然後將 WHATIF 參數設為 **True** 以預覽變更。

4. 預覽動作，並在針對生產虛擬機器實作前進行所有必要的變更。 就緒後即可將參數設定為 **False** 並手動執行 Runbook，或是讓自動化排程 **Sequenced-StartVM** 和 **Sequenced-StopVM** 依照您指定的排程自動執行。

### <a name="target-the-start-and-stop-action-by-vm-list"></a>透過 VM 清單設定啟動和停止動作目標

1. 將使用正整數值的 **sequenceStart** 和 **sequenceStop** 標記，新增至您打算新增至 **VMList** 參數的 VM。

2. 執行 **SequencedStartStop_Parent** Runbook，並將 ACTION 參數設為 **start**，在 *VMList* 參數中新增以逗號分隔的虛擬機器清單，然後將 WHATIF 參數設為 **True**。 預覽變更。

3. 使用以逗號分隔的虛擬機器清單 (VM1、VM2、VM3) 設定 **External_ExcludeVMNames** 參數。

4. 此案例不會接受 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupnames** 變數。 針對此案例，您需要建立自己的自動化排程。 如需詳細資訊，請參閱[在 Azure 自動化中排程 Runbook](../automation/automation-schedules.md)。

5. 預覽動作，並在針對生產虛擬機器實作前進行所有必要的變更。 就緒後即可將參數設定為 **False** 並手動執行 monitoring-and-diagnostics/monitoring-action-groupsrunbook，或是讓自動化排程 **Sequenced-StartVM** 和 **Sequenced-StopVM** 依照您指定的排程自動執行。

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>案例 3：根據 CPU 使用量自動啟動/停止

此解決方案通過評估非高峰時段(如下班後)未使用的 VM,説明管理訂閱中運行 Azure 資源管理器和經典虛擬機器的成本,並在處理器利用率低於指定百分比時自動關閉它們。

根據預設，解決方案已預先設定會對百分比 CPU 計量進行評估，以檢查平均使用率是否為 5% 或更低。 此案例是由下列變數進行控制，並可在預設值不符合需求時加以修改：

* **External_AutoStop_MetricName**

* **External_AutoStop_Threshold**

* **External_AutoStop_TimeAggregationOperator**

* **External_AutoStop_TimeWindow**

* **External_AutoStop_Frequency**

* **External_AutoStop_Severity**

您可以針對訂閱和資源組啟用和定位操作,或針對特定 VM 清單。

運行**AutoStop_CreateAlert_Parent** runbook 時,它會驗證目標訂閱、資源組和 VM 是否存在。 如果存在 VM,則父 Runbook 會為每個已驗證 VM 調用**AutoStop_CreateAlert_Child**執行簿。 此子執行簿執行以下操作:

* 為每個已驗證的 VM 創建指標警報規則。

* 如果 CPU 在指定的時間間隔內低於配置的閾值,則觸發特定 VM**的AutoStop_VM_Child**執行簿。 然後,此 Runbook 會嘗試停止 VM。

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>訂閱的 VM 執行自動停止操作

1. 確保**External_Stop_ResourceGroupNames**變數為空或設置為 * (通配符)。

2. [可選步驟]如果要從自動關閉中排除某些 VM,則可以向**External_ExcludeVMNames**變數添加逗號分隔的 VM 名稱清單。

3. 啟用**執行Schedule_AutoStop_CreateAlert_Parent**計劃,為訂閱中的所有 VM 創建所需的停止 VM 指標警報規則。 按計劃運行此規則將允許您創建新的指標警報規則,因為新 VM 會添加到訂閱中。

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-groupmultiple-resource-groups"></a>資源群組/多個資源群組中的所有 VM 執行自動停止操作

1. 將資源群組名稱的通用分隔清單添加到**External_Stop_ResourceGroupNames**變數。

2. 或者,如果要從自動關閉中排除某些 VM,則可以向**External_ExcludeVMNames**變數添加逗號分隔的 VM 名稱清單。

3. 啟用**Schedule_AutoStop_CreateAlert_Parent**計劃以為資源群組中的所有 VM 創建所需的**停止 VM 指標**警報規則。 按計劃運行此規則,可以在將新 VM 添加到資源組時創建新的指標警報規則。

### <a name="to-target-the-auto-stop-action-to-a-list-of-vms"></a>將自動停止操作到 VM 清單中

1. 創建新[計劃](shared-resources/schedules.md#creating-a-schedule)並將其連結到**AutoStop_CreateAlert_Parent** runbook,將 VM 名稱的逗號分隔清單添加到**VMList**參數。

2. 或者,如果要從自動關閉中排除某些 VM,則可以向**External_ExcludeVMNames**變數添加 vm 名稱的逗號分隔清單。

## <a name="configure-email-notifications"></a>設定電子郵件通知

若要在部署解決方案之後變更電子郵件通知，請修改在部署期間建立的動作群組。  

> [!NOTE]
> Azure Government 雲端中的訂用帳戶不支援此解決方案的電子郵件功能。

1. 在 Azure 入口網站中，巡覽至 [監視] -> [動作群組]。 選取標題為 **StartStop_VM_Notication** 的動作群組。

    ![自動化更新管理解決方案頁面](media/automation-solution-vm-management/azure-monitor.png)

2. 在 [StartStop_VM_Notification]**** 頁面上，按一下 [詳細資料]**** 底下的 [編輯詳細資料]****。 這樣會開啟 [電子郵件/SMS/推播/語音]**** 頁面。 請更新電子郵件地址，然後按一下 [確定]**** 以儲存變更。

    ![自動化更新管理解決方案頁面](media/automation-solution-vm-management/change-email.png)

    或者，您也可以將其他動作新增至動作群組中；若要深入了解動作群組，請參閱[動作群組](../azure-monitor/platform/action-groups.md)

以下是當解決方案關閉虛擬機器時，所傳送的範例電子郵件。

![自動化更新管理解決方案頁面](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>新增/排除 VM

此解決方案能夠新增 VM 成為解決方案的目標，或特別排除解決方案中的機器。

### <a name="add-a-vm"></a>新增 VM

有兩個選項可用於確保 VM 在執行時包含在「開始/停止」解決方案中。

* 解決方案的每個父[運行簿](automation-solution-vm-management.md#runbooks)都有一個**VMList**參數。 在為情況安排適當的父 Runbook 時,可以將 VM 名稱的逗號分隔清單傳遞給此參數,並且這些 VM 將在解決方案運行時包含。

* 若要選取多個 VM，以包含您要啟動或停止之 VM 的資源群組名稱設定 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupNames**。 您也可以將此值設定為 `*`，讓解決方案針對訂用帳戶中的所有資源群組執行。

### <a name="exclude-a-vm"></a>排除 VM

若要從解決方案中排除 VM，您也可以將它新增至 **External_ExcludeVMNames** 變數。 此變數是要從「開始/停止」解決方案中排除的特定 VM 的逗號分隔清單。 此清單的上限為 140 個 VM。 如果將超過 140 個 VM 添加到此逗號分隔清單中,則設置為排除的 VM 可能會無意中啟動或停止。

## <a name="modify-the-startup-and-shutdown-schedules"></a>修改啟動和關機排程

在此解決方案中管理啟動和關機排程的步驟，與[在 Azure 自動化中排程 Runbook](automation-schedules.md) 中所概述的步驟相同。 需要個別的排程來啟動和停止 VM。

支援將解決方案設定為只在特定時間停止 VM。 在此案例中，您剛建立了**停止**排程，但未安排任何對應的**啟動**排程。 若要這樣做，您需要：

1. 請確定您已新增要在 **External_Stop_ResourceGroupNames** 變數中關閉之 VM 的資源群組。

2. 針對您需要關閉 VM 的時間建立您自己的排程。

3. 巡覽至 **ScheduledStartStop_Parent** Runbook，然後按一下 [排程]****。 這可讓您選取上一個步驟中建立的排程。

4. 選擇**參數並執行設定**,並將 **「操作」** 參數設定為 **「停止**」 。

5. 選取 [ **確定** ] 以儲存變更。

## <a name="next-steps"></a>後續步驟

* 要瞭解如何在非工作時間對啟動/停止 VM 進行故障排除,請參閱 [故障排除啟動/停止 VM。](troubleshoot/start-stop-vm.md)

* [查看](automation-solution-vm-management-logs.md)寫入 Azure 監視器日誌的自動化記錄和範例日誌搜尋查詢,以分析從開始/停止 VM 分析自動化執行簿作業的狀態。
