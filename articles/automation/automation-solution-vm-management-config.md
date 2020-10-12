---
title: 設定在 Azure 自動化的停機期間啟動/停止 VM
description: 本文說明如何設定在停機期間設定啟動/停止 VM 功能，以支援不同的使用案例或情況。
services: automation
ms.subservice: process-automation
ms.date: 06/01/2020
ms.topic: conceptual
ms.openlocfilehash: b0bc23d515bebdd0d943bbad33c5ebba35a35605
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987200"
---
# <a name="configure-startstop-vms-during-off-hours"></a>設定在停機期間啟動/停止 VM

本文描述如何設定[在停機期間啟動/停止 VM](automation-solution-vm-management.md) 功能，以支援所述的案例。 您也可以了解如何：

* [設定電子郵件通知](#configure-email-notifications)
* [新增 VM](#add-a-vm)
* [排除 VM](#exclude-a-vm)
* [修改啟動和關閉排程](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>案例 1：依排程啟動/停止 VM

本案例是首次部署停機期間啟動/停止 VM 時的預設設定。 例如，您可將此功能設定為在晚上離開公司時，停止訂用帳戶上的所有虛擬機器，並在早上回到辦公室時啟動。 當您在部署期間設定排程 **Scheduled-StartVM** 和 **Scheduled-StopVM** 時，它們會啟動和停止目標虛擬機器。 

支援將功能設定為僅停止 VM。 請參閱[修改啟動和關閉排程](#modify-the-startup-and-shutdown-schedules)以了解如何設定自訂的排程。

> [!NOTE]
> 此功能使用的時區是您設定排程表時間參數時所在目前時區。 不過，Azure 自動化會將時區以 UTC 格式儲存在 Azure 自動化中。 您不需要執行任何時區轉換，因為這項工作會在機器部署期間得到處理。

若要控制範圍內的 VM，請設定變數：`External_Start_ResourceGroupNames`、`External_Stop_ResourceGroupNames` 和 `External_ExcludeVMNames`。

您可以將動作的目標設為某個訂用帳戶和資源群組，或設為特定的虛擬機器清單，但不可同時設定為兩者。

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>針對訂用帳戶和資源群組設定啟動和停止動作目標

1. 設定 `External_Stop_ResourceGroupNames` 和 `External_ExcludeVMNames` 變數以指定目標 VM。

2. 啟用及更新 **Scheduled-StartVM** 和 **Scheduled-StopVM** 排程。

3. 執行 **ScheduledStartStop_Parent** Runbook，並將 **ACTION** 參數欄位設為 **start**，然後將 **WHATIF** 參數欄位設為 True 以預覽變更。

### <a name="target-the-start-and-stop-action-by-vm-list"></a>透過 VM 清單設定啟動和停止動作目標

1. 執行 **ScheduledStartStop_Parent** 的 runbook，並將 **動作** 設定為 [ **啟動**]。

2. 在 [ **VMList** 參數] 欄位中，新增以逗號分隔的 vm 清單， (沒有空格) 。 範例清單為 `vm1,vm2,vm3` 。

3. 將 [ **WHATIF** 參數] 欄位設定為 [True]。

4. `External_ExcludeVMNames`使用以逗號分隔的 vm 清單來設定變數 (VM1、VM2、VM3) ，逗號分隔值之間沒有空格。

5. 本案例不接受 `External_Start_ResourceGroupNames` 和 `External_Stop_ResourceGroupnames` 變數。 針對此案例，您需要建立自己的自動化排程。 如需詳細資料，請參閱[在 Azure 自動化中為 Runbook 進行排程](shared-resources/schedules.md)。

    > [!NOTE]
    > **目標資源群組名稱**其值會同時儲存為 `External_Start_ResourceGroupNames` 和 `External_Stop_ResourceGroupNames` 的值。 如需進一步的細微設定，您可以修改這些變數來以不同的資源群組為目標。 使用 `External_Start_ResourceGroupNames` 作為啟動動作，並使用 `External_Stop_ResourceGroupNames` 作為停止動作。 虛擬機器會自動新增至啟動和停止排程。

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>案例 2：使用標記依順序啟動/停止 VM

在多部虛擬機器上包含兩個或多個元件並支援分散式工作負載的環境中，支援元件的啟動和停止順序是非常重要的。 

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>針對訂用帳戶和資源群組設定啟動和停止動作目標

1. 將具有正整數值的 `sequencestart` 和 `sequencestop` 標籤新增至 VM (`External_Start_ResourceGroupNames` 和 `External_Stop_ResourceGroupNames` 變數中已將該 VM 設定為目標)。 啟動和停止動作會依遞增順序執行。 若要了解如何標籤虛擬機器，請參閱[在 Azure 中為 Windows 虛擬機器加上標籤](../virtual-machines/windows/tag.md)和[在 Azure 中為 Linux 虛擬機器加上標籤](../virtual-machines/linux/tag.md)。

2. 修改排程 **Sequenced-StartVM** 和 **Sequenced-StopVM** 以符合您所需的日期和時間，然後啟用排程。

3. 執行 **SequencedStartStop_Parent** Runbook，並將 **ACTION** 設為 **start**，然後將 **WHATIF** 設為 True 以預覽變更。

4. 預覽動作，並在針對生產虛擬機器實作前進行所有必要的變更。 就緒後即可將參數設定為 **False** 並手動執行 Runbook，或讓自動化排程 **Sequenced-StartVM** 和 **Sequenced-StopVM** 遵循指定的排程自動執行。

### <a name="target-the-start-and-stop-actions-by-vm-list"></a>透過 VM 清單設定啟動和停止動作目標

1. 將具有正整數值的 `sequencestart` 和 `sequencestop` 標籤，新增至想要新增到 `VMList` 參數的 VM。

2. 執行 **SequencedStartStop_Parent** 的 runbook，並將 **動作** 設定為 [ **啟動**]。

3. 在 [ **VMList** 參數] 欄位中，新增以逗號分隔的 vm 清單， (沒有空格) 。 範例清單為 `vm1,vm2,vm3` 。

4. 將 **WHATIF** 設定為 True。 

5. `External_ExcludeVMNames`使用以逗號分隔的 vm 清單來設定變數，以逗點分隔值之間沒有空格。

6. 本案例不接受 `External_Start_ResourceGroupNames` 和 `External_Stop_ResourceGroupnames` 變數。 針對此案例，您需要建立自己的自動化排程。 如需詳細資料，請參閱[在 Azure 自動化中為 Runbook 進行排程](shared-resources/schedules.md)。

7. 預覽動作，並在針對生產虛擬機器實作前進行所有必要的變更。 準備好時，請手動執行 **monitoring-and-diagnostics/monitoring-action-groupsrunbook**，並將參數設定為 **False**。 或者，讓自動化為 **Sequenced-StartVM** 與 **Sequenced-StopVM** 排程，以遵循指定的排程自動執行。

## <a name="scenario-3-start-or-stop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>案例 3：根據 CPU 使用率自動啟動或停止

停機期間啟動/停止 VM 可評估非尖峰期間 (例如下班時間) 未使用的機器，並在處理器使用率低於指定百分比時自動關閉，藉以協助管理訂用帳戶中 Azure Resource Manager 與傳統 VM 執行的成本。

根據預設，此功能已預先設定會對百分比 CPU 計量進行評估，以檢查平均使用率是否為 5% 或更低。 本案例是由下列變數進行控制，並可在預設值不符合需求時加以修改：

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

您可啟用並將動作的目標設為某個訂用帳戶和資源群組，或將目標設為特定的 VM 清單。

當執行 **AutoStop_CreateAlert_Parent** Runbook 時，Runbook 即會驗證目標訂用帳戶、資源群組和 VM 是否存在。 如果 VM 存在，則 Runbook 會針對父 Runbook 所驗證的每個 VM 呼叫 **AutoStop_CreateAlert_Child** Runbook。 這個子 Runbook 會：

* 為每個已驗證的 VM 建立度量警示規則。
* 如果 CPU 在指定時間間隔低於設定的閾值，則會為特定 VM 觸發 **AutoStop_VM_Child** Runbook。 
* 嘗試停止該 VM。

### <a name="target-the-autostop-action-against-all-vms-in-a-subscription"></a>將自動停止動作目標設為訂用帳戶中的所有 VM

1. 請確定 `External_Stop_ResourceGroupNames` 變數是空白或設為 * (萬用字元)。

2. [選擇性] 如果想要排除部分 VM 的自動停止動作，則可將以逗號分隔的 VM 名稱清單新增至 `External_ExcludeVMNames` 變數。

3. 啟用 **Schedule_AutoStop_CreateAlert_Parent** 排程來執行，為訂用帳戶中所有 VM 建立所需的停止 VM 度量警示規則。 執行此類型的排程可供在將新 VM 新增至訂用帳戶時，建立新的度量警示規則。

### <a name="target-the-autostop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>將自動停止動作目標設為一或多個資源群組中的所有 VM

1. 將以逗號分隔的資源群組名稱清單新增至 `External_Stop_ResourceGroupNames` 變數。

2. 如果想要在其中排除部分 VM 的自動停止，則可將以逗號分隔的 VM 名稱清單新增至 `External_ExcludeVMNames` 變數。

3. 啟用 **Schedule_AutoStop_CreateAlert_Parent** 排程來執行，以針對資源群組中所有 VM 建立所需的停止 VM 度量警示規則。 依排程執行此作業可供在將新 VM 新增至資源群組時，建立新的度量警示規則。

### <a name="target-the-autostop-action-to-a-list-of-vms"></a>將自動停止動作目標設為 VM 清單

1. 建立新的[排程](shared-resources/schedules.md#create-a-schedule)並將其連結至 **AutoStop_CreateAlert_Parent** Runbook，然後將以逗號分隔的 VM 名稱清單新增至 `VMList` 參數。

2. （選擇性）如果您想要從 autostop 動作中排除某些 Vm，您可以新增以逗號分隔的 VM 名稱清單， (沒有空間) 的 `External_ExcludeVMNames` 變數。

## <a name="configure-email-notifications"></a>設定電子郵件通知

若要在部署停機期間啟動/停止 VM 之後變更電子郵件通知，則可修改在部署期間建立的動作群組。  

> [!NOTE]
> Azure Government 雲端中訂用帳戶不支援此功能的電子郵件功能。

1. 在 Azure 入口網站中，依序巡覽至 [監視] 和 [動作群組]。 選取稱為 **StartStop_VM_Notication** 的動作群組。

    :::image type="content" source="media/automation-solution-vm-management/azure-monitor.png" alt-text="[監視-動作群組] 頁面的螢幕擷取畫面。":::

2. 在 [StartStop_VM_Notification] 頁面上，按一下 [詳細資料] 底下的 [編輯詳細資料]。 隨即會開啟 [電子郵件/SMS/推播/語音] 頁面。 請更新電子郵件地址，然後按一下 [確定] 以儲存變更。

    :::image type="content" source="media/automation-solution-vm-management/change-email.png" alt-text="[監視-動作群組] 頁面的螢幕擷取畫面。":::

    或者，您也可以將其他動作新增至動作群組中；若要深入了解動作群組，請參閱[動作群組](../azure-monitor/platform/action-groups.md)

下列是當功能關閉虛擬機器時，所傳送的範例電子郵件。

:::image type="content" source="media/automation-solution-vm-management/email.png" alt-text="[監視-動作群組] 頁面的螢幕擷取畫面。":::

## <a name="add-or-exclude-vms"></a><a name="add-exclude-vms"></a>新增或排除 VM

此功能可供新增要設為目標的 VM，或將其排除。 

### <a name="add-a-vm"></a>新增 VM

有兩種方式可確保在功能執行時包含 VM：

* 功能的每個父 [Runbook](automation-solution-vm-management.md#runbooks) 都具有 `VMList` 參數。 針對您的情況，您可以在排程適當的父 runbook 時，將以逗號分隔的 VM 名稱清單 (沒有空格) 至此參數，而且這些 Vm 將會在功能執行時包含。

* 若要選取多部 VM，請使用包含想要啟動或停止 VM 的資源群組名稱來設定 `External_Start_ResourceGroupNames` 和 `External_Stop_ResourceGroupNames`。 您也可將此變數設定為 `*` 值，讓功能針對訂用帳戶中的所有資源群組執行。

### <a name="exclude-a-vm"></a>排除 VM

若要將 VM 排除在停機期間啟動/停止 VM 清單外，您可將其名稱新增至 `External_ExcludeVMNames` 變數。 此變數是特定 Vm 的逗號分隔清單， (沒有空間) 要從功能中排除。 此清單的上限為 140 個 VM。 如果新增至此清單的 VM 超過 140 個，則可能會不小心啟動或停止已設定排除的 VM。

## <a name="modify-the-startup-and-shutdown-schedules"></a>修改啟動和關機排程

在此功能中管理啟動和關閉排程的步驟，與[在 Azure 自動化中排程 Runbook](shared-resources/schedules.md) 中所概述的步驟相同。 您需要進行個別排程才能啟動和停止 VM。

此功能支援將其設定為只在特定時間停止 VM。 在本案例中，您已建立了停止排程，但未安排任何對應的啟動排程。 

1. 請確定已在 `External_Stop_ResourceGroupNames` 變數中新增要關閉 VM 的資源群組。

2. 針對需要關閉 VM 的時間建立自己的排程。

3. 巡覽至 **ScheduledStartStop_Parent** Runbook，然後按一下 [排程]。 這可讓您選取上一個步驟中建立的排程。

4. 選取 [參數與回合設定] 並將 **ACTION** 欄位設定為 **Stop**。

5. 選取 [ **確定** ] 以儲存變更。

## <a name="next-steps"></a>後續步驟

* 若要在作業期間監視功能，請參閱[在停機期間啟動/停止 VM 的查詢記錄](automation-solution-vm-management-logs.md)。
* 若要解決在管理 VM 期間的問題，請參閱[針對在停機期間啟動/停止 VM 的問題進行疑難排解](troubleshoot/start-stop-vm.md)。
