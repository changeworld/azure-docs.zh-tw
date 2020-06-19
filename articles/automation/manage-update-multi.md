---
title: 在 Azure 自動化中管理多個 VM 的更新
description: 本文說明如何管理多個 VM 的更新。
services: automation
ms.subservice: update-management
ms.date: 03/26/2020
ms.topic: conceptual
ms.openlocfilehash: d08afc6e501fd76167e0939633442213958f0d49
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834623"
---
# <a name="manage-updates-for-multiple-vms"></a>管理多個虛擬機器的更新

您可以使用 Azure 自動化更新管理，來管理 Windows 和 Linux VM 的更新和修補程式。 您可以從您的 [Azure 自動化](automation-offering-get-started.md)帳戶：

- 啟用 VM 進行更新管理。
- 評估可用更新的狀態。
- 排程必要更新的安裝。
- 檢閱部署結果，以確認更新已成功套用至啟用更新管理的所有 VM。

若要了解更新管理的系統需求，請參閱[更新管理用戶端需求](automation-update-management.md#client-requirements)。

## <a name="prerequisites"></a>Prerequisites

* 已安裝搭配其中一個支援作業系統的 VM 或電腦。
* 可存取已啟用更新管理的 Linux VM 更新存放庫。

## <a name="enable-update-management-for-azure-vms"></a>為 Azure VM 啟用更新管理

1. 在 Azure 入口網站中，開啟您的自動化帳戶，然後選取 [更新管理]。

2. 選取 [加入 Azure VM]。

    ![[加入 Azure VM] 索引標籤](./media/manage-update-multi/update-onboard-vm.png)

3. 選取要啟用的 VM，然後選取 [啟用更新管理] 底下的 [啟用]。

    ![啟用 [更新管理] 對話方塊](./media/manage-update-multi/update-enable.png)

    當作業完成時，就會在您的 VM 上啟用更新管理。

## <a name="enable-update-management-for-non-azure-vms-and-computers"></a>為非 Azure VM 和電腦啟用更新管理

適用於 Windows 和 Linux 的 Log Analytics 代理程式必須安裝在公司網路或其他雲端環境上執行的 VM 上，才能使用更新管理來啟用這些 VM。 若要了解將代理程式部署至 Azure 外所裝載的機器時需符合哪些系統需求，以及有哪些支援的方法。請參閱 [Log Analytics 代理程式概觀](../azure-monitor/platform/log-analytics-agent.md)。

## <a name="view-computers-attached-to-your-automation-account"></a>檢視連結到自動化帳戶的電腦

為您的電腦啟用「更新管理」之後，就可以選取 [電腦] 以檢視機器資訊。 您可以看到電腦的機器名稱、合規性狀態、環境、OS 類型、已安裝的重大和安全性更新、其他已安裝的更新和更新代理程式整備程度等相關資訊。

  ![檢視電腦索引標籤](./media/manage-update-multi/update-computers-tab.png)

最近啟用「更新管理」的電腦可能尚未經過評估。 那些電腦的合規性狀態是 `Not assessed`。 以下是可能的合規性狀態值清單：

- `Compliant`:未錯過重大或安全性更新的電腦。
- `Non-compliant`:至少錯過一次重大或安全性更新的電腦。
- `Not assessed`:在預期的時間範圍內，未收到來自電腦的更新評估資料。 若為 Linux 電腦，預期時間範圍是過去 1 小時。 若為 Windows 電腦，預期時間範圍是過去 12 小時。

若要檢視代理程式的狀態，請選取 [更新代理程式整備程度] 欄中的連結。 選取這個選項會開啟 [混合式背景工作角色] 窗格，並顯示混合式背景工作角色的狀態。 下圖顯示長期未連線至「更新管理」的代理程式範例：

![檢視電腦索引標籤](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>檢視更新評估

啟用 [更新管理] 之後，[更新管理] 窗格隨即開啟。 您可以在 [遺失更新] 索引標籤上看到遺失的更新清單。

## <a name="collect-data"></a>收集資料

VM 和電腦上安裝的代理程式會收集關於更新的資料。 代理程式會將資料傳送至 Azure 更新管理。

### <a name="supported-agents"></a>支援的代理程式

下表描述更新管理所支援的連線來源：

| 連線的來源 | 支援 | 描述 |
| --- | --- | --- |
| Windows 代理程式 |是 |「更新管理」會從 Windows 代理程式收集系統更新的相關資訊，然後起始必要更新的安裝。 |
| Linux 代理程式 |是 |「更新管理」會從 Linux 代理程式收集系統更新的相關資訊，然後在支援的發行版本上起始必要更新的安裝。 |
| Operations Manager 管理群組 |是 |「更新管理」會從所連線之管理群組中的代理程式收集系統更新的相關資訊。 |
| Azure 儲存體帳戶 |否 |Azure 儲存體不包含系統更新的相關資訊。 |

### <a name="collection-frequency"></a>收集頻率

在電腦完成更新合規性掃描之後，代理程式會將資訊以大量方式轉送至 Azure 監視器記錄。 在 Windows 電腦上，合規性掃描預設會每 12 小時執行一次。

除了掃描排程，如果在更新安裝之前與更新安裝之後重新啟動 MMA，則會在 15 分鐘內起始更新合規性掃描。

若為 Linux 電腦，合規性掃描預設每一小時執行一次。 若 MMA 代理程式重新啟動，則會在 15 分鐘內起始合規性掃描。

儀表板可能需要 30 分鐘到 6 小時，才能顯示來自受控電腦的已更新資料。

## <a name="schedule-an-update-deployment"></a>排定更新部署

若要安裝更新，請將部署排定在發行排程和服務時段之後。 您可以選擇要在部署中包含的更新類型。 例如，您可以包含重大更新或安全性更新，並排除更新彙總套件。

>[!NOTE]
>當您排程更新部署時，將會建立一個連結至 **Patch-MicrosoftOMSComputers** Runbook 的[排程](shared-resources/schedules.md)資源，以處理目標電腦上的更新部署。 如果您在建立部署後從 Azure 入口網站或使用 PowerShell 刪除了排程資源，將會中斷已排程的更新部署，並在您嘗試從入口網站重新加以設定時顯示錯誤。 您只能藉由刪除對應的部署排程來刪除排程資源。
>

若要為一或多個 VM 排定新的更新部署，請在 [更新管理] 下，選取 [排程更新部署]。

在 [新增更新部署] 窗格中，指定下列資訊：

- **Name**：輸入唯一名稱來識別更新部署。
- **作業系統**：選取 [Windows] 或 [Linux]。
- **要更新的群組**：根據訂用帳戶、資源群組、位置及標記的組合來定義查詢，以建置要包含在您部署中的動態 Azure VM 群組。 若為非 Azure VM，已儲存的搜尋是用來建立要包含在您部署中的動態群組。 若要深入了解，請參閱[動態群組](automation-update-management-groups.md)。
- **要更新的機器**：選取已儲存的搜尋或已匯入的群組，或選取機器，以選擇您想要更新的機器。

   >[!NOTE]
   >選取 [已儲存的搜尋] 選項並不會傳回機器身分識別，只會傳回其名稱。 如果您在多個資源群組之間有多個相同名稱的 VM，其會在結果中傳回。 建議使用 [要更新的群組] 選項，以確保您包含符合您準則的唯一 VM。

   如果您選擇 [機器]，機器的整備程度會顯示於 [更新代理程式整備程度] 資料行中。 您可以在排程更新部署之前，先查看機器的健康情況。 若要深入了解在 Azure 監視器記錄中建立電腦群組的不同方法，請參閱 [Azure 監視器記錄中的電腦群組](../azure-monitor/platform/computer-groups.md)

  ![[新增更新部署] 窗格](./media/manage-update-multi/update-select-computers.png)

- **更新分類**：選取要包括在更新部署中的軟體類型。 如需分類類型的說明，請參閱[更新分類](automation-view-update-assessments.md#work-with-update-classifications)。 分類類型包括：
  - 重大更新
  - 安全性更新
  - 更新彙總套件
  - Feature Pack
  - Service Pack
  - 定義更新
  - 工具
  - 更新

- **要包含/排除的更新** - 這會開啟 [包含/排除] 頁面。 要包含或排除的更新會在個別的索引標籤上。 如需其他有關如何處理包含的資訊，請參閱[排定更新部署](automation-tutorial-update-management.md#schedule-an-update-deployment)。

> [!NOTE]
> 請務必了解，排除項目會覆寫納入的項目。 例如，如果您定義 `*` 排除規則，系統便不會安裝任何修補程式或套件，因為已將它們全部排除。 排除的修補程式仍然會顯示為從機器中遺漏。 就 Linux 機器而言，如果已包含某個套件，但排除了它的某個相依套件，就不會安裝該套件。

> [!NOTE]
> 您無法指定將已取代的更新納入更新部署中。

- **排程設定**：您可以接受預設的日期和時間 (目前時間之後的 30 分鐘)。 您也可以指定不同的時間。

   您也可以指定部署是否為發生一次，或為週期性排程。 若要設定週期性排程，請選取 [週期] 下的 [定期]。

   ![排程 [設定] 對話方塊](./media/manage-update-multi/update-set-schedule.png)

- **前置指令碼 + 後置指令碼**：選取要在部署前和部署後執行的指令碼。 若要深入了解，請參閱[管理前置和後置指令碼](pre-post-scripts.md)。
- **維護時間範圍 (分鐘)** ：指定需要執行更新部署的時段。 此設定有助於確保在您定義的服務時段內執行變更。

- **重新啟動控制** - 此設定可決定如何處理更新部署的重新開機。

   |選項|描述|
   |---|---|
   |在必要時重新開機| **(預設值)** 如有需要，會在維護時段允許的情況下啟動重新開機。|
   |一律重新開機|不論是否有必要，皆會啟動重新開機。 |
   |永不重新開機|不論是否需要重新開機，皆不啟動重新開機。|
   |僅重新開機 - 將不會安裝更新|此選項會略過安裝更新，而且只會啟動重新開機。|

完成排程設定之後，請選取 [建立] 按鈕以返回狀態儀表板。 [已排定] 表格會顯示您建立的部署排程。

> [!NOTE]
> 「更新管理」支援部署第一方更新及預先下載修補程式。 這需要對要修補的系統進行變更，請參閱[ 第一方和預先下載支援](automation-configure-windows-update.md#pre-download-updates)，以了解如何在系統上進行這些設定。

## <a name="view-results-of-an-update-deployment"></a>檢視更新部署的結果

已排程的部署開始之後，您就可以在 [更新管理] 之下的 [更新部署] 索引標籤上看到該部署的狀態。

如果目前正在執行部署，其狀態會是 [進行中]。 部署順利完成後，狀態會變更為 [已成功]。

如果部署中的一或多個更新失敗，則狀態會是 [部分失敗]。

![更新部署的狀態](./media/manage-update-multi/update-view-results.png)

若要查看更新部署的儀表板，請選取完成的部署。

[更新結果] 窗格會顯示 VM 的更新總數和部署結果。 右邊的表格會提供每個更新的明細與安裝結果。 安裝結果可為下列其中一個值：

- `Not attempted`:未安裝更新，因為以所定義的維護時間範圍為基礎，可用的時間不足。
- `Succeeded`:更新成功。
- `Failed`:更新失敗。

若要查看部署已建立的所有記錄項目，請選取 [所有記錄]。

若要查看在目標 VM 上管理更新部署的 Runbook 作業串流，請選取 [輸出] 圖格。

若要查看部署所傳回之任何錯誤的詳細資訊，請選取 [錯誤]。

## <a name="next-steps"></a>後續步驟

* 如果您需要搜尋更新記錄，請參閱[查詢更新管理記錄](automation-update-management-query-logs.md)。
