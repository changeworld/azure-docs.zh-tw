---
title: 在 Azure 自動化中管理變更追蹤和清查
description: 本文說明如何使用變更追蹤和清查，來追蹤軟體和 Microsoft 服務在您環境中的變更。
services: automation
ms.subservice: change-inventory-management
ms.date: 06/15/2020
ms.topic: conceptual
ms.openlocfilehash: 0eebd626013614bb6240fc0e6530a358a2b86d1c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84781186"
---
# <a name="manage-change-tracking-and-inventory"></a>管理變更追蹤和清查

當您新增要追蹤的新檔案或登錄機碼時，Azure 自動化可讓它進行[變更追蹤和清查](change-tracking.md)。 本文說明如何設定追蹤、審核追蹤結果，以及在偵測到變更時處理警示。

使用本文中的程式之前，請確定您已使用下列其中一種技術，在您的 Vm 上啟用變更追蹤和清查：

* [自動化帳戶的 [啟用變更追蹤和清查]](automation-enable-changes-from-auto-acct.md)
* [藉由流覽 Azure 入口網站來啟用變更追蹤和清查](automation-enable-changes-from-browse.md)
* [Runbook 的 [啟用變更追蹤和清查]](automation-enable-changes-from-runbook.md)
* [Azure VM 的 [啟用變更追蹤和清查]](automation-enable-changes-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>限制部署的範圍

變更追蹤和清查會使用工作區中的範圍設定，來設定要接收變更的目標電腦。 如需詳細資訊，請參閱[限制變更追蹤和清查部署範圍](automation-scope-configurations-change-tracking.md)。

## <a name="track-files"></a>追蹤檔案

您可以使用變更追蹤和清查來追蹤檔案和資料夾/目錄的變更。 本節說明如何在 Windows 和 Linux 上設定檔案追蹤。

### <a name="configure-file-tracking-on-windows"></a>在 Windows 上設定檔案追蹤

使用下列步驟，設定要在 Windows 電腦上追蹤的檔案：

1. 從您的自動化帳戶中，選取 [組態管理] 下的 [變更追蹤]。 
2. 按一下 [編輯設定] \(齒輪符號)。
3. 在 [工作區設定] 頁面上選取 [Windows 檔案]，然後按一下 [+ 新增]，以新增要追蹤的新檔案。
4. 在 [新增變更追蹤的 Windows 檔案] 窗格中，輸入要追蹤之檔案或資料夾的資訊，然後按一下 [**儲存**]。 下表定義可用於資訊的屬性。

    |屬性  |說明  |
    |---------|---------|
    |啟用     | 如果已套用設定，則為 True，否則為 False。        |
    |項目名稱     | 要追蹤之檔案的易記名稱。        |
    |群組     | 以邏輯方式分組檔案的群組名稱。        |
    |輸入路徑     | 要檢查檔案的路徑，例如 **c:\temp\\\*.txt**。 您也可以使用環境變數，例如 `%winDir%\System32\\\*.*`。       |
    |路徑類型     | 路徑的類型。 可能的值為 File 和 Folder。        |    
    |遞迴     | 如果在尋找要追蹤的項目時使用遞迴，則為 True，否則為 False。        |    
    |上傳檔案內容 | True 會針對追蹤的變更上傳檔案內容，False 則不會。|

5. 請確實將 [上傳檔案內容] 指定為 True。 此設定會針對指定的檔案路徑啟用檔案內容追蹤。

### <a name="configure-file-tracking-on-linux"></a>在 Linux 上設定檔案追蹤

使用下列步驟，設定要在 Linux 電腦上追蹤的檔案：

1. 從您的自動化帳戶中，選取 [組態管理] 下的 [變更追蹤]。 
2. 按一下 [編輯設定] \(齒輪符號)。
3. 在 [工作區設定] 頁面上選取 [Linux 檔案]，然後按一下 [+ 新增]，以新增要追蹤的新檔案。
4. 在 [為變更追蹤新增 Linux 檔案] 窗格上，輸入要追蹤的檔案或目錄資訊，然後按一下 [儲存]。 下表定義可用於資訊的屬性。

    |屬性  |說明  |
    |---------|---------|
    |啟用     | 如果已套用設定，則為 True，否則為 False。        |
    |項目名稱     | 要追蹤之檔案的易記名稱。        |
    |群組     | 以邏輯方式分組檔案的群組名稱。        |
    |輸入路徑     | 要檢查檔案的路徑，例如， **/etc/*.conf**。       |
    |路徑類型     | 路徑的類型。 可能值為「檔案」和「目錄」。        |
    |遞迴     | 如果在尋找要追蹤的項目時使用遞迴，則為 True，否則為 False。        |
    |使用 Sudo     | True 會在檢查項目時使用 sudo，False 否不會。         |
    |連結     | 此設定會決定在周遊目錄時處理符號連結的方式。 可能的值包括：<br> 忽略 - 忽略符號連結，而不包含參考的檔案/目錄。<br>遵循 - 在遞迴期間遵循符號連結，並且包含參考的檔案/目錄。<br>管理-遵循符號連結並允許變更傳回的內容。<br>**注意：** 不建議使用 [管理] 選項，因為它不支援檔案內容抓取。    |
    |上傳檔案內容 | True 會針對追蹤的變更上傳檔案內容，False 則不會。 |

5. 請確實將 [上傳檔案內容] 指定為 True。 此設定會針對指定的檔案路徑啟用檔案內容追蹤。

   ![新增 Linux 檔案](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>追蹤檔案內容

檔案內容追蹤可讓您在追蹤變更前後，檢視檔案的內容。 功能會在每次變更之後，將檔案內容[儲存至儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。 以下是追蹤檔案內容時需遵循的一些規則：

* 儲存檔案內容需要有使用 Resource Manager 部署模型的標準儲存體帳戶。 
* 請勿使用進階和傳統的部署模型儲存體帳戶。 請參閱[關於 Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md)。
* 您只能將儲存體帳戶連線到一個自動化帳戶。
* 必須在您的自動化帳戶中啟用[變更追蹤和清查](change-tracking.md)。

### <a name="enable-tracking-for-file-content-changes"></a>啟用檔案內容變更的追蹤

使用下列步驟啟用追蹤檔案內容的變更：

1. 在 Azure 入口網站中開啟您的自動化帳戶，然後選取 [組態管理] 底下的 [變更追蹤]。
2. 按一下 [編輯設定] \(齒輪符號)。
3. 選取 [檔案內容]，然後按一下 [連結]。 進行此選取後，會開啟 [新增變更追蹤的內容位置] 窗格。

   ![新增內容位置](./media/change-tracking-file-contents/enable.png)

4. 選取要用來儲存檔案內容的訂用帳戶和儲存體帳戶。 

5. 如果您想要啟用所有現有已追蹤檔案的檔案內容追蹤，請針對 [上傳所有設定的檔案內容] 選取 [開啓]。 您後續可為各個檔案路徑變更此設定。

   ![設定儲存體帳戶](./media/change-tracking-file-contents/storage-account.png)

6. 變更追蹤和清查在啟用檔案內容變更追蹤後，會顯示儲存體帳戶和共用存取簽章 (SAS) URI。 簽章會在 365 天後到期，您可以按一下 [重新產生] 重新加以建立。

   ![列出帳戶金鑰](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>檢視追蹤的檔案內容

變更追蹤和清查驗偵測到追蹤的檔案有所變更時，您可以在 [變更詳細資料] 窗格中檢視檔案內容。  

![列出變更](./media/change-tracking-file-contents/change-list.png)

1. 在 Azure 入口網站中開啟您的自動化帳戶，然後選取 [組態管理] 底下的 [變更追蹤]。

2. 在變更清單中選擇檔案，然後選取 [檢視檔案內容變更]，以查看檔案的內容。 [變更詳細資料] 窗格會顯示每個屬性的檔案資訊前後的標準。

   ![變更詳細資料](./media/change-tracking-file-contents/change-details.png)

3. 您在並排檢視中檢視檔案內容。 您可以選取 [內嵌] 查看變更的內嵌檢視。

## <a name="track-registry-keys"></a>追蹤登錄機碼

使用下列步驟，設定要在 Windows 電腦上追蹤的登錄機碼：

1. 在 Azure 入口網站中開啟您的自動化帳戶，然後選取 [組態管理] 底下的 [變更追蹤]。 
2. 按一下 [編輯設定] \(齒輪符號)。
3. 在 [工作區設定] 頁面上，選取 [Windows 登錄]。
4. 按一下 [+ 新增]，以新增要追蹤的新登錄機碼。
5. 在 [為變更追蹤新增 Windows 登錄] 窗格上，輸入要追蹤的機碼資訊，然後按一下 [儲存]。 下表定義可用於資訊的屬性。

    |屬性  |說明  |
    |---------|---------|
    |啟用     | 如果已套用設定，則為 True，否則為 False。        |
    |項目名稱     | 要追蹤之登錄機碼的易記名稱。        |
    |群組     | 以邏輯方式分組登錄機碼的群組名稱。        |
    |Windows 登錄機碼   | 具有路徑的索引鍵名稱，例如 `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup` 。      |

## <a name="search-logs-for-change-records"></a>搜尋記錄中的變更記錄

您可以對 Azure 監視器記錄進行各種搜尋，以找出變更記錄。 在 [變更追蹤] 頁面開啟後，按一下 [Log Analytics] 以開啟 [記錄] 頁面。 下表提供變更記錄的記錄搜尋範例。

|查詢  |描述  |
|---------|---------|
|`ConfigurationData`<br>&#124;`where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124;`where SvcState == "Stopped"`<br>&#124;`summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | 針對已設為 [自動]、但回報為 [正在停止] 的 Microsoft 服務顯示最新的清查記錄。 結果會限定為指定的軟體名稱和電腦的最新記錄。    |
|`ConfigurationChange`<br>&#124;`where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124;`order by TimeGenerated desc`|顯示已移除之軟體的變更記錄。|

## <a name="create-alerts-on-changes"></a>建立變更的警示

下列範例顯示已在機器上修改檔案**c:\windows\system32\drivers\etc\hosts** 。 這個檔案很重要，因為 Windows 會使用此檔案將主機名稱解析為 IP 位址。 這項作業的優先順序高於 DNS，且可能會導致連線問題。 此外也可能會導致流量重新導向至惡意或危險的網站。

![顯示主機檔案變更的圖表](./media/change-tracking-file-contents/changes.png)

我們將使用此範例來討論建立變更警示的步驟。

1. 在您的自動化帳戶中，選取 [組態管理]下的 [變更追蹤]，然後選取 [Log Analytics]。 
2. 在 [記錄] 搜尋中，使用查詢 `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` 尋找 **hosts** 檔案的內容變更。 此查詢會尋找完整路徑名稱包含單字之檔案的內容變更 `hosts` 。 您也可以將路徑部分變更為其完整形式以要求特定檔案，例如使用 `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`。

3. 查詢傳回其結果之後，請按一下記錄搜尋中的 [**新增警示規則**]，以開啟 [警示建立] 頁面。 您也可以在 Azure 入口網站中透過 [Azure 監視器] 瀏覽至此頁面。 

4. 再次檢查我們的查詢並修改警示邏輯。 在此情況下，即使在環境中的所有電腦上偵測到一項變更，也要觸發警示。

    ![變更查詢以追蹤 hosts 檔案的變更](./media/change-tracking-file-contents/change-query.png)

5. 設定警示邏輯之後，請指派動作群組來執行動作，以回應觸發警示。 在此案例中，我們設定要傳送的電子郵件，以及要建立的 IT 服務管理 (ITSM) 票證。 

    ![設定在變更時發出警示的動作群組](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>後續步驟

* 如需範圍設定的詳細資訊，請參閱[限制變更追蹤和清查部署範圍](automation-scope-configurations-change-tracking.md)。
* 如果您需要搜尋儲存在 Log Analytics 工作區中的記錄，請參閱 [Azure 監視器記錄中的記錄搜尋](../log-analytics/log-analytics-log-searches.md)。
* 如果已完成部署，請參閱[從自動化帳戶取消連結工作區以進行變更追蹤和清查](automation-unlink-workspace-change-tracking.md)。
* 若要從變更追蹤和清查中刪除您的 Vm，請參閱[從變更追蹤和清查移除 vm](automation-remove-vms-from-change-tracking.md)。
* 若要針對功能錯誤進行疑難排解，請參閱[針對變更追蹤和清查問題進行疑難排解](troubleshoot/change-tracking.md)。
