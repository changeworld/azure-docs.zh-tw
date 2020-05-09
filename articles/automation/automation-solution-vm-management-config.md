---
title: Azure 自動化在離峰期間設定啟動/停止 Vm 解決方案
description: 本文說明如何在離峰時間解決方案中設定啟動/停止 Vm，以支援不同的使用案例或案例。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 4cceb0d5ada82de73bc74c0ed408f8eb988ea8ec
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864261"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>如何在離峰時間解決方案中設定啟動/停止 Vm

使用在**離峰時間啟動/停止 vm 解決方案時**，您可以：

- [排程要啟動和停止的 vm](#schedule)。
- [使用 Azure 標記](#tags)（不支援傳統 vm），以遞增順序將 vm 排程為啟動和停止。
- 根據[低 CPU 使用量](#cpuutil)自動停止 vm。

本文說明如何成功設定解決方案以支援這些案例。 您也可以瞭解如何執行解決方案的其他一般設定，例如：

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

1. 設定`External_Stop_ResourceGroupNames`和`External_ExcludeVMNames`變數以指定目標 vm。

2. 啟用及更新 **Scheduled-StartVM** 和 **Scheduled-StopVM** 排程。

3. 執行**ScheduledStartStop_Parent** runbook，並將**ACTION**參數欄位設定為**start** ，然後將**WHATIF**參數欄位設定為 True 以預覽您的變更。

### <a name="target-the-start-and-stop-action-by-vm-list"></a>透過 VM 清單設定啟動和停止動作目標

1. 執行**ScheduledStartStop_Parent** runbook，並將**ACTION**設定為**start**，在**VMList**參數欄位中新增以逗號分隔的 vm 清單，然後將**WHATIF**參數欄位設定為 True。 預覽變更。

2. 以逗號`External_ExcludeVMNames`分隔的 vm 清單來設定變數（VM1、VM2、VM3）。

3. 此案例不接受`External_Start_ResourceGroupNames`和`External_Stop_ResourceGroupnames`變數。 針對此案例，您需要建立自己的自動化排程。 如需詳細資訊，請參閱[在 Azure 自動化中排程 Runbook](../automation/automation-schedules.md)。

    > [!NOTE]
    > **目標 ResourceGroup 名稱**的值會儲存為`External_Start_ResourceGroupNames`和`External_Stop_ResourceGroupNames`的值。 如需進一步的細微設定，您可以修改這些變數來以不同的資源群組為目標。 針對 [啟動動作] `External_Start_ResourceGroupNames`，請使用`External_Stop_ResourceGroupNames` ，並使用進行停止動作。 虛擬機器會自動新增至啟動和停止排程。

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>案例 2：使用標記依順序啟動/停止 VM

在多部虛擬機器上包含兩個或多個元件並支援分散式工作負載的環境中，支援元件的啟動和停止順序是非常重要的。 您可以執行下列步驟來完成此案例：

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>針對訂用帳戶和資源群組設定啟動和停止動作目標

1. 將具有`sequencestart`正整數值`sequencestop`的和標記新增至目標為`External_Start_ResourceGroupNames`和`External_Stop_ResourceGroupNames`變數的 vm。 啟動和停止動作會依遞增順序執行。 若要了解如何標記虛擬機器，請參閱[在 Azure 中標記 Windows 虛擬機器](../virtual-machines/windows/tag.md)和[在 Azure 中標記 Linux 虛擬機器](../virtual-machines/linux/tag.md)。

2. 修改排程 **Sequenced-StartVM** 和 **Sequenced-StopVM** 以符合您所需的日期和時間，然後啟用排程。

3. 執行**SequencedStartStop_Parent** runbook，並將 [**動作**] 設定為 [**啟動**]，並將 [ **WHATIF** ] 設定為 True 以預覽變更。

4. 預覽動作，並在針對生產虛擬機器實作前進行所有必要的變更。 準備好時，請以手動方式執行 runbook，並將參數設為**False**，或`Sequenced-StartVM`讓`Sequenced-StopVM`自動化排程和按照您指定的排程自動執行。

### <a name="target-the-start-and-stop-action-by-vm-list"></a>透過 VM 清單設定啟動和停止動作目標

1. 將具有`sequencestart`正整數值`sequencestop`的和標記新增至您打算新增至參數的`VMList` vm。

2. 執行**SequencedStartStop_Parent** runbook，並將 [**動作**] 設定為 [**開始**]，在 [ **VMList** ] 參數欄位中新增以逗號分隔的 vm 清單，然後將 [ **WHATIF** ] 設定為 [True]。 預覽變更。

3. 以逗號`External_ExcludeVMNames`分隔的 vm 清單來設定變數（VM1、VM2、VM3）。

4. 此案例不接受`External_Start_ResourceGroupNames`和`External_Stop_ResourceGroupnames`變數。 針對此案例，您需要建立自己的自動化排程。 如需詳細資訊，請參閱[在 Azure 自動化中排程 Runbook](../automation/automation-schedules.md)。

5. 預覽動作，並在針對生產虛擬機器實作前進行所有必要的變更。 準備好時，請手動執行**監視-和診斷/監視-動作-groupsrunbook** ，並將參數設定為**False**。 或者，讓自動化排程`Sequenced-StartVM`和`Sequenced-StopVM`按照您指定的排程自動執行。

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>案例 3：根據 CPU 使用量自動啟動/停止

此解決方案可評估非尖峰期間（例如下班時間）未使用的 Vm，並在處理器使用率低於指定的百分比時，自動將其關閉，以協助管理您的訂用帳戶中執行 Azure Resource Manager 和傳統虛擬機器的成本。

根據預設，解決方案已預先設定會對百分比 CPU 計量進行評估，以檢查平均使用率是否為 5% 或更低。 此案例是由下列變數所控制，而且如果預設值不符合您的需求，即可加以修改：

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

您可以針對訂用帳戶和資源群組啟用和鎖定動作，或以特定的 Vm 清單為目標。

當您執行**AutoStop_CreateAlert_Parent** runbook 時，它會驗證目標訂用帳戶、資源群組和 vm 是否存在。 如果 Vm 存在，則 runbook 接著會針對父 runbook，為每個已驗證的 VM 呼叫**AutoStop_CreateAlert_Child** runbook。 此子 runbook 會執行下列動作：

* 為每個已驗證的 VM 建立度量警示規則。

* 如果 CPU 低於指定時間間隔的設定臨界值，則觸發特定 VM 的**AutoStop_VM_Child** runbook。 此 runbook 接著會嘗試停止 VM。

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>針對訂用帳戶中的所有 Vm 以「自動停止」動作為目標

1. 請確定變數`External_Stop_ResourceGroupNames`是空的或設為 * （萬用字元）。

2. [選用步驟]如果您想要從自動關機中排除部分 Vm，您可以將以逗號分隔的 VM 名稱清單新增至`External_ExcludeVMNames`變數。

3. 啟用`Schedule_AutoStop_CreateAlert_Parent`排程來執行，以針對您訂用帳戶中的所有 vm 建立必要的停止 VM 計量警示規則。 執行此類型的排程可讓您在將新的 Vm 新增至訂用帳戶時，建立新的計量警示規則。

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>針對資源群組或多個資源群組中的所有 Vm 以自動停止動作為目標

1. 將以逗號分隔的資源組名清單新增至`External_Stop_ResourceGroupNames`變數。

2. 如果您想要從自動關機中排除部分 Vm，您可以將以逗號分隔的 VM 名稱清單新增至`External_ExcludeVMNames`變數。

3. 啟用**Schedule_AutoStop_CreateAlert_Parent**排程來執行，以針對您資源群組中的所有 vm 建立必要的停止 VM 計量警示規則。 依排程執行此作業可讓您在將新的 Vm 新增至資源群組時，建立新的計量警示規則。

### <a name="to-target-the-autostop-action-to-a-list-of-vms"></a>將 autostop 動作的目標設為 Vm 清單

1. 建立新的[排程](shared-resources/schedules.md#create-a-schedule)並將它連結到**AutoStop_CreateAlert_Parent** runbook，並將以逗號分隔的 VM 名稱清單新增至`VMList`參數。

2. 或者，如果您想要從自動關機中排除部分 Vm，您可以將以逗號分隔的 VM 名稱清單新增至`External_ExcludeVMNames`變數。

## <a name="configure-email-notifications"></a>設定電子郵件通知

若要在部署解決方案後變更電子郵件通知，請修改在部署期間建立的動作群組。  

> [!NOTE]
> Azure Government 雲端中的訂用帳戶不支援此解決方案的電子郵件功能。

1. 在 [Azure 入口網站中，流覽至 [**監視**]，然後依 [**動作群組**]。 選取名為 [ **StartStop_VM_Notication**] 的 [動作群組]。

    ![自動化更新管理解決方案頁面](media/automation-solution-vm-management/azure-monitor.png)

2. 在 [StartStop_VM_Notification]**** 頁面上，按一下 [詳細資料]**** 底下的 [編輯詳細資料]****。 這樣會開啟 [電子郵件/SMS/推播/語音]**** 頁面。 請更新電子郵件地址，然後按一下 [確定]**** 以儲存變更。

    ![自動化更新管理解決方案頁面](media/automation-solution-vm-management/change-email.png)

    或者，您也可以將其他動作新增至動作群組中；若要深入了解動作群組，請參閱[動作群組](../azure-monitor/platform/action-groups.md)

以下是當解決方案關閉虛擬機器時，所傳送的範例電子郵件。

![自動化更新管理解決方案頁面](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>新增/排除 VM

此解決方案能夠新增 VM 成為解決方案的目標，或特別排除解決方案中的機器。

### <a name="add-a-vm"></a>新增 VM

有兩個選項可讓您在啟動/停止解決方案執行時，用來確定 VM 已包含在其中。

* 解決方案的每個父[runbook](automation-solution-vm-management.md#runbooks)都有`VMList`參數。 針對您的情況排程適當的父系 runbook 時，您可以將以逗號分隔的 VM 名稱清單傳遞給此參數，並在解決方案執行時包含這些 Vm。

* 若要選取多個 Vm `External_Start_ResourceGroupNames` ， `External_Stop_ResourceGroupNames`請使用包含您想要啟動或停止之 vm 的資源組名來設定和。 您也可以將變數設定為的`*`值，讓解決方案針對訂用帳戶中的所有資源群組執行。

### <a name="exclude-a-vm"></a>排除 VM

若要從解決方案中排除 VM，您可以將它新增至`External_ExcludeVMNames`變數。 此變數是要從啟動/停止解決方案排除的特定 Vm 清單（以逗號分隔）。 此清單的上限為 140 個 VM。 如果您將超過140個 Vm 新增至此逗號分隔清單，則設定為 [已排除] 的 Vm 可能會不慎啟動或停止。

## <a name="modify-the-startup-and-shutdown-schedules"></a>修改啟動和關機排程

在此解決方案中管理啟動和關機排程的步驟，與[在 Azure 自動化中排程 Runbook](automation-schedules.md) 中所概述的步驟相同。 需要個別的排程來啟動和停止 VM。

支援將解決方案設定為只在特定時間停止 VM。 在此案例中，您只需要建立**停止**排程，而不是相對應的**啟動**排程。 若要這樣做，您需要：

1. 請確定您已新增 Vm 的資源群組，以在`External_Stop_ResourceGroupNames`變數中關閉。

2. 針對您需要關閉 VM 的時間建立您自己的排程。

3. 巡覽至 **ScheduledStartStop_Parent** Runbook，然後按一下 [排程]****。 這可讓您選取上一個步驟中建立的排程。

4. 選取 [**參數] 和**[回合設定]，並將**動作**欄位設定為 [**停止**]。

5. 按一下 [確定]  以儲存您的變更。

## <a name="next-steps"></a>後續步驟

* 若要瞭解如何針對在離峰期間啟動/停止 Vm 進行疑難排解，請參閱 [啟動/停止 Vm 疑難排解](troubleshoot/start-stop-vm.md)。

* [檢查](automation-solution-vm-management-logs.md)寫入 Azure 監視器記錄檔的自動化記錄，以及範例記錄檔搜尋查詢，以分析啟動/停止 Vm 的自動化 runbook 作業狀態。
