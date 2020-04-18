---
title: Azure 自動化 設定非工作時間解決方案的啟動/停止 VM
description: 本文介紹如何在非工作時間解決方案期間配置開始/停止 VM 以支援不同的用例或方案。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9842a736cf922e0490f2b0c8acb1d2e5833f3d6c
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604766"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>如何在非工作時間設定啟動/停止 VM 解決方案

使用**非工作時間啟動/停止 VM**解決方案,您可以:

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

1. 配置`External_Stop_ResourceGroupNames`和`External_ExcludeVMNames`變數以指定目標 VM。

2. 啟用及更新 **Scheduled-StartVM** 和 **Scheduled-StopVM** 排程。

3. 執行**ScheduledStartStop_Parent**執行簿,**動作**會設定為**開始****,WHATIF**的數位設定為 True 以預覽變更。

### <a name="target-the-start-and-stop-action-by-vm-list"></a>透過 VM 清單設定啟動和停止動作目標

1. 執行ScheduledStartStop_Parent**執行簿****ACTION**,操作 設定為**啟動**,在**VMList**參數位段中添加逗號分隔的 VM 清單,然後將**WHATIF**參數位段設定為 True。 預覽變更。

2. 使用逗`External_ExcludeVMNames`號分隔的 VM 清單(VM1、VM2、VM3)配置變數。

3. 此方案不遵循和`External_Start_ResourceGroupNames``External_Stop_ResourceGroupnames`變數。 針對此案例，您需要建立自己的自動化排程。 如需詳細資訊，請參閱[在 Azure 自動化中排程 Runbook](../automation/automation-schedules.md)。

    > [!NOTE]
    > **目標資源組名稱**的值存儲為`External_Start_ResourceGroupNames`和`External_Stop_ResourceGroupNames`的值。 如需進一步的細微設定，您可以修改這些變數來以不同的資源群組為目標。 對於啟動操作,請使用`External_Start_ResourceGroupNames`和`External_Stop_ResourceGroupNames`用於停止操作。 虛擬機器會自動新增至啟動和停止排程。

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>案例 2：使用標記依順序啟動/停止 VM

在多部虛擬機器上包含兩個或多個元件並支援分散式工作負載的環境中，支援元件的啟動和停止順序是非常重要的。 您可以執行下列步驟來完成此案例：

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>針對訂用帳戶和資源群組設定啟動和停止動作目標

1. `sequencestart`將具有正整`sequencestop`數值的標記添加到`External_Start_ResourceGroupNames`針對的`External_Stop_ResourceGroupNames`VM 和變數。 啟動和停止動作會依遞增順序執行。 若要了解如何標記虛擬機器，請參閱[在 Azure 中標記 Windows 虛擬機器](../virtual-machines/windows/tag.md)和[在 Azure 中標記 Linux 虛擬機器](../virtual-machines/linux/tag.md)。

2. 修改排程 **Sequenced-StartVM** 和 **Sequenced-StopVM** 以符合您所需的日期和時間，然後啟用排程。

3. 執行**SequencedStartStop_Parent**執行簿,**操作**設定為**啟動****,WHATIF**設置為 True,以預覽更改。

4. 預覽動作，並在針對生產虛擬機器實作前進行所有必要的變更。 準備就緒后,手動執行運行簿,參數設置為**False**,或者讓`Sequenced-StartVM`自動化`Sequenced-StopVM`計畫 按照規定的計畫自動運行。

### <a name="target-the-start-and-stop-action-by-vm-list"></a>透過 VM 清單設定啟動和停止動作目標

1. `sequencestart`將具有正整`sequencestop`數值的標記加入到計畫加入到參數的 VM 中`VMList`。

2. 執行**SequencedStartStop_Parent**執行**簿,操作**設定為**啟動**,在**VMList**參數位段中添加逗號分隔的 VM 清單,然後將**WHATIF**設定為 True。 預覽變更。

3. 使用逗`External_ExcludeVMNames`號分隔的 VM 清單(VM1、VM2、VM3)配置變數。

4. 此方案不遵循和`External_Start_ResourceGroupNames``External_Stop_ResourceGroupnames`變數。 針對此案例，您需要建立自己的自動化排程。 如需詳細資訊，請參閱[在 Azure 自動化中排程 Runbook](../automation/automation-schedules.md)。

5. 預覽動作，並在針對生產虛擬機器實作前進行所有必要的變更。 準備就緒後,手動執行**監視和診斷/監視操作組 runbook,** 參數設定為**False**。 或者,讓自動化計劃`Sequenced-StartVM`並按`Sequenced-StopVM`您規定的計劃自動運行。

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>案例 3：根據 CPU 使用量自動啟動/停止

此解決方案通過評估非高峰時段(如下班後)未使用的 VM,説明管理訂閱中運行 Azure 資源管理器和經典虛擬機器的成本,並在處理器利用率低於指定百分比時自動關閉它們。

根據預設，解決方案已預先設定會對百分比 CPU 計量進行評估，以檢查平均使用率是否為 5% 或更低。 此方案由以下變數控制,如果預設值不符合您的要求,則可以對其進行修改:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

您可以針對訂閱和資源組啟用和定位操作,或針對特定 VM 清單。

運行**AutoStop_CreateAlert_Parent** runbook 時,它會驗證目標訂閱、資源組和 VM 是否存在。 如果存在 VM,則 Runbook 然後按父運行簿調用每個已驗證 VM**的AutoStop_CreateAlert_Child**執行簿。 此子執行簿執行以下操作:

* 為每個已驗證的 VM 創建指標警報規則。

* 如果 CPU 在指定的時間間隔內低於配置的閾值,則觸發特定 VM**的AutoStop_VM_Child**執行簿。 然後,此 Runbook 會嘗試停止 VM。

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>訂閱的 VM 執行自動停止操作

1. 確保`External_Stop_ResourceGroupNames`變數為空或設置為 * (通配符)。

2. [可選步驟]如果要從自動關閉中排除某些 VM,則可以`External_ExcludeVMNames`向 變數添加由逗號分隔的 VM 名稱清單。

3. 啟用`Schedule_AutoStop_CreateAlert_Parent`運行計劃以為訂閱中的所有 VM 創建所需的停止 VM 指標警報規則。 運行此類計畫,您可以在將新 VM 添加到訂閱時創建新的指標警報規則。

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>資源群組或多個資源群組中的所有 VM 執行自動停止操作

1. 新增`External_Stop_ResourceGroupNames`為逗號分隔的資源群組名稱清單。

2. 如果要從自動關機中排除某些 VM,則可以`External_ExcludeVMNames`向 變數添加與逗號分隔的 VM 名稱清單。

3. 啟用**執行Schedule_AutoStop_CreateAlert_Parent**計畫,以便為資源組中的所有 VM 創建所需的停止 VM 指標警報規則。 按計劃運行此操作,可以在將新 VM 添加到資源組時創建新的指標警報規則。

### <a name="to-target-the-autostop-action-to-a-list-of-vms"></a>將自動停止操作到 VM 清單中

1. 建立新[的計劃](shared-resources/schedules.md#creating-a-schedule)並將其連結到**AutoStop_CreateAlert_Parent** runbook,將 VM 名稱的`VMList`逗號分隔清單 添加到參數中。

2. 或者,如果要從自動關機中排除某些 VM,則可以`External_ExcludeVMNames`向 變數添加由逗號分隔的 VM 名稱清單。

## <a name="configure-email-notifications"></a>設定電子郵件通知

要在部署解決方案後更改電子郵件通知,請修改部署期間創建的操作組。  

> [!NOTE]
> Azure 政府雲中的訂閱不支援此解決方案的電子郵件功能。

1. 在 Azure 門戶中,導航到**監視器**,然後**導航操作群組**。 選擇名為**StartStop_VM_Notication**的操作組。

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

* 解決方案的每個父[運行簿](automation-solution-vm-management.md#runbooks)都有`VMList`一個 參數。 在為情況安排適當的父 Runbook 時,可以將 VM 名稱的逗號分隔清單傳遞給此參數,並且這些 VM 將在解決方案運行時包含。

* 要選擇多個 VM,`External_Start_ResourceGroupNames`請`External_Stop_ResourceGroupNames`設定 並且使用包含要啟動或停止的 VM 的資源組名稱。 還可以將變數設置為的值,`*`以便解決方案針對訂閱中的所有資源組運行。

### <a name="exclude-a-vm"></a>排除 VM

要從解決方案中排除 VM,可以將新增到變數中`External_ExcludeVMNames`。 此變數是要從「開始/停止」解決方案中排除的特定 VM 的逗號分隔清單。 此清單的上限為 140 個 VM。 如果將超過 140 個 VM 添加到此逗號分隔清單中,則設置為排除的 VM 可能會無意中啟動或停止。

## <a name="modify-the-startup-and-shutdown-schedules"></a>修改啟動和關機排程

在此解決方案中管理啟動和關機排程的步驟，與[在 Azure 自動化中排程 Runbook](automation-schedules.md) 中所概述的步驟相同。 需要個別的排程來啟動和停止 VM。

支援將解決方案設定為只在特定時間停止 VM。 在這種情況下,您只需創建一個**停止**計劃,並且沒有相應的**開始**計劃。 若要這樣做，您需要：

1. 確保已添加 VM`External_Stop_ResourceGroupNames`在變數中關閉的資源組。

2. 針對您需要關閉 VM 的時間建立您自己的排程。

3. 巡覽至 **ScheduledStartStop_Parent** Runbook，然後按一下 [排程]****。 這可讓您選取上一個步驟中建立的排程。

4. 選擇**參數並執行設定**,並將 **「操作」** 欄位設定為 **「停止**」 。

5. 選取 [ **確定** ] 以儲存變更。

## <a name="next-steps"></a>後續步驟

* 要瞭解如何在非工作時間對啟動/停止 VM 進行故障排除,請參閱 [故障排除啟動/停止 VM。](troubleshoot/start-stop-vm.md)

* [查看](automation-solution-vm-management-logs.md)寫入 Azure 監視器日誌的自動化記錄和範例日誌搜尋查詢,以分析從開始/停止 VM 分析自動化執行簿作業的狀態。
