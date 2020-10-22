---
title: 在 Azure 自動化中針對 Azure VM 上的變更進行疑難排解 | Microsoft Docs
description: 本文說明如何針對 Azure VM 上的變更進行疑難排解。
services: automation
ms.subservice: change-inventory-management
keywords: 變更, 追蹤, 變更追蹤, 清查, 自動化
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 28c440f27dcbd4ac509adea83d5c3085488cb488
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92204243"
---
# <a name="troubleshoot-changes-on-an-azure-vm"></a>針對 Azure VM 上的變更進行疑難排解

在本教學課程中，您會了解如何針對 Azure 虛擬機器上的變更進行疑難排解。 您可以啟用變更追蹤和清查，以追蹤軟體、檔案、Linux 精靈、Windows 服務和 Windows 登錄機碼的變更。
識別這些組態變更可協助您找出環境中的操作問題。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 為 VM 啟用變更追蹤和清查
> * 搜尋已停止服務的變更記錄
> * 設定變更追蹤
> * 啟用活動記錄連線
> * 觸發事件
> * 檢視變更
> * 設定警示

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](./index.yml)可保存監看員和動作 Runbook，以及監看員工作。
* 要啟用此功能的[虛擬機器](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="enable-change-tracking-and-inventory"></a>啟用變更追蹤和清查

您必須先在本教學課程中啟用變更追蹤和清查。 如果您先前已啟用此功能，就不需要執行此步驟。

>[!NOTE]
>如果欄位呈現灰色，就表示 VM 已啟用另一個自動化功能，而您必須使用相同的工作區和自動化帳戶。

1. 選取 [虛擬機器]，然後從清單中選取 VM。
2. 在左側功能表上，選取 [作業] 底下的 [清查]。 [清查] 頁面隨即開啟。

    ![啟用變更](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

3. 選擇 [Log Analytics](../azure-monitor/log-query/log-query-overview.md) 工作區。 此工作區會收集變更追蹤和清查等功能所產生的資料。 工作區提供單一位置來檢閱和分析來自多個來源的資料。

    [!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

4. 選取要使用的自動化帳戶。

5. 設定部署的位置。

5. 按一下 [啟用] 為您的 VM 部署此功能。 

在設定期間，VM 的佈建會透過適用於 Windows 的 Log Analytics 代理程式和[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)來進行。 啟用變更追蹤和清查可能需要多達 15 分鐘的時間。 在此期間，請勿關閉瀏覽器視窗。

啟用此功能之後，VM 上安裝的軟體和變更相關資訊會流向 Azure 監視器記錄。
可能需要 30 分鐘到 6 小時，資料才可供分析。

## <a name="use-change-tracking-and-inventory-in-azure-monitor-logs"></a>在 Azure 監視器記錄中使用變更追蹤和清查

變更追蹤和清查所產生的記錄資料會傳送到 Azure 監視器記錄。 若要透過執行查詢來搜尋記錄，請選取 [變更追蹤] 頁面頂端的 [Log Analytics]。 變更追蹤資料會儲存在 `ConfigurationChange` 類型之下。

下列範例 Log Analytics 查詢會傳回所有已停止的 Windows 服務。

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

若要深入了解如何在 Azure 監視器記錄中執行和搜尋記錄，請參閱 [Azure 監視器記錄](../azure-monitor/log-query/log-query-overview.md)。

## <a name="configure-change-tracking"></a>設定變更追蹤

透過變更追蹤，您可使用 VM 上 [變更追蹤] 頁面頂端的 [編輯設定]，選擇要收集及追蹤的檔案和登錄機碼。 在 [工作區設定] 頁面上，您可新增要追蹤的 Windows 登錄機碼、Windows 檔案或 Linux 檔案。

> [!NOTE]
> 變更追蹤和清查都會使用相同的集合設定，且設定會在工作區層級進行設定。

### <a name="add-a-windows-registry-key"></a>新增 Windows 登錄機碼

1. 在 [Windows 登錄] 索引標籤上，選取 [新增]。 

1. 在 [為變更追蹤新增 Windows 登錄] 上，輸入要追蹤的機碼資訊，然後按一下 [儲存]

    |屬性  |描述  |
    |---------|---------|
    |啟用     | 判斷是否已套用設定        |
    |項目名稱     | 所要追蹤檔案的易記名稱        |
    |群組     | 用於將檔案以邏輯方式分組的群組名稱        |
    |Windows 登錄機碼   | 要檢查檔案的路徑。例如："HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="add-a-windows-file"></a>新增 Windows 檔案

1. 在 [Windows 檔案] 索引標籤上，選取 [新增]。 

1. 在 [為變更追蹤新增 Windows 檔案] 頁面上，輸入要追蹤的檔案或目錄資訊，然後按一下 [儲存]

    |屬性  |描述  |
    |---------|---------|
    |啟用     | 判斷是否已套用設定        |
    |項目名稱     | 所要追蹤檔案的易記名稱        |
    |群組     | 用於將檔案以邏輯方式分組的群組名稱        |
    |輸入路徑     | 要檢查檔案的路徑，例如："c:\temp\\\*.txt"<br>您也可以使用環境變數，例如 "%winDir%\System32\\\*.*"         |
    |遞迴     | 決定在尋找所要追蹤的項目時是否使用遞迴。        |
    |上傳所有的檔案內容設定| 開啟或關閉追蹤變更上的檔案內容上傳。 可用選項：**True** 或 **False**。|

### <a name="add-a-linux-file"></a>新增 Linux 檔案

1. 在 [Linux 檔案] 索引標籤上，選取 [新增]。 

1. 在 [為變更追蹤新增 Linux 檔案] 頁面上，輸入要追蹤的檔案或目錄資訊，然後按一下 [儲存]。

    |屬性  |描述  |
    |---------|---------|
    |啟用     | 判斷是否已套用設定        |
    |項目名稱     | 所要追蹤檔案的易記名稱        |
    |群組     | 用於將檔案以邏輯方式分組的群組名稱        |
    |輸入路徑     | 要檢查檔案的路徑。例如："/etc/*.conf"       |
    |路徑類型     | 要追蹤的項目類型，可能值為 [檔案] 和 [目錄]        |
    |遞迴     | 決定在尋找所要追蹤的項目時是否使用遞迴。        |
    |使用 Sudo     | 此設定會決定在檢查項目時是否使用 sudo。         |
    |連結     | 此設定會決定在周遊目錄時處理符號連結的方式。<br> **忽略** - 忽略符號連結，而不包含參考的檔案/目錄<br>**遵循** - 遵循遞迴期間的符號連結，而且包含參考的檔案/目錄<br>**管理** - 遵循符號連結並允許變更所傳回內容的處理方式      |
    |上傳所有的檔案內容設定| 開啟或關閉追蹤變更上的檔案內容上傳。 可用選項：True 或 False。|

   > [!NOTE]
   > 不建議使用 [連結] 屬性的 [管理] 值。 不支援檔案內容擷取。

## <a name="enable-activity-log-connection"></a>啟用活動記錄連線

1. 從您 VM 上的 [變更追蹤] 頁面，選取 [管理活動記錄連線]。 

2. 在 Azure 活動記錄頁面上，按一下 [連線] 將變更追蹤和清查連線至 VM 的 Azure 活動記錄。

3. 瀏覽至您 VM 的 [概觀] 頁面，並選取 [停止] 來將您的 VM 停止。 

4. 出現提示時，選取 [是] 可停止 VM。 

5. 在 VM 解除配置後，選取 [啟動] 加以啟動。 將 VM 記錄停止和啟動，可在其活動記錄中記錄事件。 

## <a name="view-changes"></a>檢視變更

1. 瀏覽回到 [變更追蹤] 頁面，然後選取頁面底部的 [事件] 索引標籤。 

2. 一段時間之後，變更追蹤事件就會在顯示在圖表和表格中。 圖表會顯示一段時間內已發生的變更。 頂端的折線圖會顯示 Azure 活動記錄事件。 長條圖中的每個資料列都代表不同的可追蹤變更類型。 這些類型為 Linux 精靈、檔案、Windows 登錄機碼、軟體和 Windows 服務。 變更索引標籤會顯示所顯示變更的詳細資料，其中先顯示最新的變更。

    ![在入口網站中檢視事件](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

3. 請注意，系統已有多次變更，包括服務和軟體的變更。 您可以使用頁面頂端的篩選條件，依 [變更類型] 或依時間範圍來篩選結果。

    ![VM 的變更清單](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

4. 選取 **WindowsServices** 的變更。 此選取動作會開啟 [變更詳細資料] 頁面，其中會顯示有關變更的詳細資料，以及變更前後的值。 在本範例中，軟體保護服務已停止。

    ![在入口網站中檢視變更詳細資料](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>設定警示

在 Azure 入口網站中檢視變更會有幫助，但能夠在發生變更 (例如服務已停止) 時收到警示會更有用。 讓我們為已停止的服務新增警示。 

1. 在 Azure 入口網站中，移至 [監視]。 

2. 在 [共用服務] 之下選取 [警示]，然後按一下 [+ 新增警示規則]。

3. 按一下 [選取] 來選擇資源。 

4. 在 [選取資源] 頁面上，從 [依資源類型篩選] 下拉式功能表中選擇 [Log Analytics]。 

5. 選取您的 Log Analytics 工作區，然後按一下 [完成]。

    ![選取資源](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

6. 按一下 [新增條件]。

7. 在 [設定訊號邏輯] 頁面的表格中，選取 [自訂記錄搜尋]。 

8. 在 [搜尋查詢] 文字方塊中，輸入下列查詢：

    ```loganalytics
    ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
    ```

    此查詢會傳回在指定時間範圍內有 W3SVC 服務停止的電腦。

9. 針對 [警示邏輯] 之下的 [閾值]，輸入 **0**。 完成時，請按一下 [完成]。

    ![設定訊號邏輯](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

10. 在 [動作群組] 底下選取 [新建]。 動作群組是一組可讓您跨多個警示使用的動作。 這些動作可包括 (但不限於) 電子郵件通知、Runbook、Webhook 和等多種項目。 若要深入了解動作群組，請參閱[建立及管理動作群組](../azure-monitor/platform/action-groups.md)。

11. 在 [警示詳細資料] 之下，輸入警示的名稱和描述。 

12. 將 [嚴重性] 設定為 [資訊 (Sev 2)]、[警告 (Sev 1)] 或 [重大 (Sev 0)]。

13. 在 [動作群組名稱] 方塊中，輸入警示名稱和簡短名稱。 使用這個群組傳送通知時，會使用簡短名稱來取代完整的動作群組名稱。

14. 針對 [動作]，輸入動作的名稱，例如 [傳送電子郵件給系統管理員]。 

15. 針對 [動作類型]，選取 [電子郵件/簡訊/推播/語音]。 

16. 針對 [詳細資料]，選取 [編輯詳細資料]。

    ![新增動作群組](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

17. 在 [電子郵件/SMS/推播/語音] 窗格中，輸入名稱，選取 [電子郵件] 核取方塊，然後輸入有效的電子郵件地址。 完成後，按一下窗格上的 [確定]，然後按一下 [新增動作群組] 頁面上的 [確定]。

18. 若要自訂警示電子郵件的主旨，請選取 [自訂動作]。 

19. 針對 [建立規則]，選取 [電子郵件主旨]，然後選擇 [建立警示規則]。 警示會在更新部署成功時，告知您有哪些機器包含在該更新部署執行中。 下圖是 W3SVC 服務停止時所收到的電子郵件範例。

    ![顯示當 W3SVC 服務停止時所收到電子郵件通知的螢幕擷取畫面。](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 為 VM 啟用變更追蹤和清查
> * 搜尋已停止服務的變更記錄
> * 設定變更追蹤
> * 啟用活動記錄連線
> * 觸發事件
> * 檢視變更
> * 設定警示

如需深入了解，請繼續閱讀變更追蹤和清查功能的概觀。

> [!div class="nextstepaction"]
> [變更追蹤和清查概觀](change-tracking/overview.md)
