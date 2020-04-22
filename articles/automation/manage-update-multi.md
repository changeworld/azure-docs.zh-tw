---
title: 管理多部 Azure 虛擬機器的更新
description: 本文介紹如何管理 Azure 和非 Azure 虛擬機器的更新。
services: automation
ms.subservice: update-management
ms.date: 03/26/2020
ms.topic: conceptual
ms.openlocfilehash: 5376562d9df35539a33f6746b387a1ff7083b8f1
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676436"
---
# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>管理多部 Azure 虛擬機器的更新

可以使用 Azure 自動化更新管理來管理 Windows 和 Linux 虛擬機器更新和修補程式。 您可以從您的 [Azure 自動化](automation-offering-get-started.md)帳戶：

- 上架虛擬機器。
- 評估可用更新的狀態。
- 排程必要更新的安裝。
- 查看部署結果,以驗證更新是否已成功應用於為其啟用更新管理的所有虛擬機器。

要瞭解更新管理的系統要求,請參閱[更新管理用戶端要求](automation-update-management.md#clients)。

## <a name="prerequisites"></a>Prerequisites

* 已安裝其中一個支援作業系統的虛擬機器或電腦。
* 訪問已連接到更新管理的 Linux VM 的更新儲存庫。

## <a name="enable-update-management-for-azure-virtual-machines"></a>為 Azure 虛擬機器啟用「更新管理」

在 Azure 入口網站中，開啟您的自動化帳戶，然後選取 [更新管理]****。

選取 [加入 Azure VM]****。

![[加入 Azure VM] 索引標籤](./media/manage-update-multi/update-onboard-vm.png)

選取要上線的虛擬機器。

在 [啟用更新管理]**** 下方，選取 [啟用]**** 以讓虛擬機器上線。

![啟用 [更新管理] 對話方塊](./media/manage-update-multi/update-enable.png)

完成上線之後，隨即會為您的虛擬機器啟用「更新管理」。

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>為非 Azure 虛擬機器和電腦啟用「更新管理」

Windows 和 Linux 的日誌分析代理需要在公司網路或其他雲端環境中運行的 VM 上安裝,以便透過更新管理啟用它們。 要瞭解將代理部署到 Azure 外部託管的電腦的系統要求和支援的方法,請參閱[紀錄分析代理概述](../azure-monitor/platform/log-analytics-agent.md)。

## <a name="view-computers-attached-to-your-automation-account"></a>檢視連結到自動化帳戶的電腦

為您的電腦啟用「更新管理」之後，就可以選取 [電腦]**** 以檢視機器資訊。 您可以看到電腦的機器名稱、合規性狀態、環境、OS 類型、已安裝的重大和安全性更新、其他已安裝的更新和更新代理程式整備程度等相關資訊。

  ![檢視電腦索引標籤](./media/manage-update-multi/update-computers-tab.png)

最近啟用「更新管理」的電腦可能尚未經過評估。 這些電腦的符合性狀態為`Not assessed`。 以下是可能的合規性狀態值清單：

- `Compliant`:未缺少關鍵或安全更新的計算機。
- `Non-compliant`:至少缺少一個關鍵或安全更新的計算機。
- `Not assessed`:更新評估數據未在預期的時間內從計算機接收。 對於 Linux 計算機,預期的時間範圍是最後一小時。 對於 Windows 計算機,預期時間範圍是最後 12 小時。

要查看代理的狀態,請在 **「更新代理就緒」** 列中選擇連結。 選取這個選項會開啟 [混合式背景工作角色] 窗格，並顯示混合式背景工作角色的狀態。 下圖顯示長期未連線至「更新管理」的代理程式範例：

![檢視電腦索引標籤](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>檢視更新評估

啟用更新管理後,將打開"更新管理"窗格。 您可以在 [遺失更新]**** 索引標籤上看到遺失的更新清單。

## <a name="collect-data"></a>收集資料

虛擬機器和電腦上安裝的代理程式會收集關於更新的資料。 代理程式會將資料傳送至 Azure 更新管理。

### <a name="supported-agents"></a>支援的代理程式

下表描述本解決方案支援的連線來源：

| 連線的來源 | 支援 | 描述 |
| --- | --- | --- |
| Windows 代理程式 |是 |「更新管理」會從 Windows 代理程式收集系統更新的相關資訊，然後起始必要更新的安裝。 |
| Linux 代理程式 |是 |「更新管理」會從 Linux 代理程式收集系統更新的相關資訊，然後在支援的發行版本上起始必要更新的安裝。 |
| Operations Manager 管理群組 |是 |「更新管理」會從所連線之管理群組中的代理程式收集系統更新的相關資訊。 |
| Azure 儲存體帳戶 |否 |Azure 儲存體不包含系統更新的相關資訊。 |

### <a name="collection-frequency"></a>收集頻率

計算機完成更新合規性掃描後,代理將資訊批量轉發到 Azure 監視器日誌。 在 Windows 電腦上，合規性掃描預設會每 12 小時執行一次。

除了掃描排程，如果在更新安裝之前與更新安裝之後重新啟動 MMA，則會在 15 分鐘內起始更新合規性掃描。

默認情況下,對於 Linux 計算機,每小時執行一次合規性掃描。 若 MMA 代理程式重新啟動，則會在 15 分鐘內起始合規性掃描。

儀表板可能需要 30 分鐘到 6 小時，才能顯示來自受控電腦的已更新資料。

## <a name="schedule-an-update-deployment"></a>排定更新部署

若要安裝更新，請將部署排定在發行排程和服務時段之後。 您可以選擇要在部署中包含的更新類型。 例如，您可以包含重大更新或安全性更新，並排除更新彙總套件。

>[!NOTE]
>當您排程更新部署時，將會建立一個連結至 **Patch-MicrosoftOMSComputers** Runbook 的[排程](shared-resources/schedules.md)資源，以處理目標電腦上的更新部署。 如果您在建立部署後從 Azure 入口網站或使用 PowerShell 刪除了排程資源，將會中斷已排程的更新部署，並在您嘗試從入口網站重新加以設定時顯示錯誤。 您只能藉由刪除對應的部署排程來刪除排程資源。
>

若要為一或多部虛擬機器排定新的更新部署，請在 [更新管理]**** 下，選取 [排程更新部署]****。

在 [新增更新部署]**** 窗格中，指定下列資訊：

- **名稱**：輸入唯一名稱來識別更新部署。
- **作業系統**：選取 [Windows]**** 或 [Linux]****。
- **要更新的組**:基於訂閱、資源組、位置和標記的組合定義查詢,以生成要包含在部署中的 Azure VM 的動態群組。 對於非 Azure VM,保存的搜索用於創建要包含在部署中的動態組。 要瞭解更多資訊,[請參考動態群組](automation-update-management-groups.md)。
- **要更新的機器**：選取已儲存的搜尋或已匯入的群組，或選取機器，以選擇您想要更新的機器。

   >[!NOTE]
   >選擇"保存的搜索"選項不會返回計算機標識,而只返回其名稱。 如果多個資源組中有多個具有相同名稱的 VM,則結果將返回它們。 建議使用 **「組」更新**選項,以確保包含符合條件的唯一 VM。

   如果選擇 **「電腦」,** 則電腦的就緒狀態將顯示在 **「更新代理就緒」** 列中。 您可以在排程更新部署之前，先查看機器的健康情況。 要瞭解在 Azure 監視器紀錄中建立電腦群組的不同方法,請參閱[Azure 監視器紀錄中的電腦群組](../azure-monitor/platform/computer-groups.md)

  ![[新增更新部署] 窗格](./media/manage-update-multi/update-select-computers.png)

- **更新分類**：選取要包括在更新部署中的軟體類型。 有關分類類型的說明,請參閱[更新分類](automation-view-update-assessments.md#update-classifications)。 分類類型包括：
  - 重大更新
  - 安全性更新
  - 更新彙總套件
  - Feature Pack
  - Service Pack
  - 定義更新
  - 工具
  - 更新

- **要包含/排除的更新** - 這會開啟 [包含]/[排除] 頁面。 要包含或排除的更新會在個別的索引標籤上。 有關如何處理包含的其他資訊,請參閱[計畫更新部署](automation-tutorial-update-management.md#schedule-an-update-deployment)。

> [!NOTE]
> 請務必了解，排除會覆寫包含。 例如，如果您定義 `*` 排除規則，系統便不會安裝任何修補程式或套件，因為已將它們全部排除。 排除的修補程式仍然會顯示為從機器中遺漏。 就 Linux 機器而言，如果已包含某個套件，但排除了它的某個相依套件，就不會安裝該套件。

> [!NOTE]
> 您無法指定將已被取代的更新納入更新部署中。
>

- **排程設定**：您可以接受預設的日期和時間 (目前時間之後的 30 分鐘)。 您也可以指定不同的時間。

   您也可以指定部署是否為發生一次，或為週期性排程。 若要設定週期性排程，請選取 [週期]**** 下的 [定期]****。

   ![排程 [設定] 對話方塊](./media/manage-update-multi/update-set-schedule.png)

- **前置指令碼 + 後置指令碼**：選取要在部署前和部署後執行的指令碼。 若要深入了解，請參閱[管理前置和後置指令碼](pre-post-scripts.md)。
- **維護時間範圍 (分鐘)**：指定您要執行更新部署的時段。 此設定有助於確保在您定義的服務時段內執行變更。

- **重新啟動控制** - 此設定可決定如何處理更新部署的重新開機。

   |選項|描述|
   |---|---|
   |在必要時重新開機| **(預設值)** 如有需要，會在維護時段允許的情況下啟動重新開機。|
   |一律重新開機|不論是否有必要，皆會啟動重新開機。 |
   |永不重新開機|不論是否需要重新開機，皆不啟動重新開機。|
   |僅重新開機 - 將不會安裝更新|此選項會略過安裝更新，而且只會啟動重新開機。|

完成排程設定之後，請選取 [建立]**** 按鈕以返回狀態儀表板。 [已排定]**** 表格會顯示您建立的部署排程。

> [!NOTE]
> 「更新管理」支援部署第一方更新及預先下載修補程式。 這需要對要修補的系統進行變更，請參閱[ 第一方和預先下載支援](automation-configure-windows-update.md#pre-download-updates)，以了解如何在系統上進行這些設定。

## <a name="view-results-of-an-update-deployment"></a>檢視更新部署的結果

已排程的部署開始之後，您就可以在 [更新管理]  之下的 [更新部署]  索引標籤上看到該部署的狀態。

如果目前正在執行部署，其狀態會是 [進行中]****。 部署順利完成後，狀態會變更為 [已成功]****。

如果部署中的一或多個更新失敗，則狀態會是 [部分失敗]****。

![更新部署的狀態](./media/manage-update-multi/update-view-results.png)

若要查看更新部署的儀表板，請選取完成的部署。

[更新結果] 窗格會顯示虛擬機器的更新總數和部署結果。 右邊的表格會提供每個更新的明細與安裝結果。 安裝結果可為下列其中一個值：

- `Not attempted`: 未安裝更新,因為根據定義的維護視窗可用時間不足。
- `Succeeded`:更新成功。
- `Failed`:更新失敗。

若要查看部署已建立的所有記錄項目，請選取 [所有記錄]****。

若要查看管理目標虛擬機器上更新部署之 Runbook 的作業串流，請選取 [輸出] 圖格。

若要查看部署所傳回之任何錯誤的詳細資訊，請選取 [錯誤]****。

## <a name="next-steps"></a>後續步驟

要瞭解有關更新管理紀錄、輸出與錯誤的詳細資訊,請參閱[更新管理的查詢更新紀錄](automation-update-management-query-logs.md)。
