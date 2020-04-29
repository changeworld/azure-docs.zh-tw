---
title: 管理 Azure 自動化中的變更追蹤和清查
description: 本文說明如何使用變更追蹤和清查來追蹤軟體，以及在您的環境中發生的 Microsoft 服務變更。
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 7dc218416e3b91998a6f06a62670ff8d2158d533
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509124"
---
# <a name="manage-change-tracking-and-inventory"></a>管理變更追蹤和清查

當您新增要追蹤的新檔案或登錄機碼時，Azure 自動化將它啟用[變更追蹤和清查](change-tracking.md)功能。 本文包含使用這項功能的程式。

## <a name="enable-the-full-change-tracking-and-inventory-solution"></a>啟用完整的變更追蹤和清查解決方案

如果您已啟用[Azure 資訊安全中心檔案完整性監視（FIM）](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)，您可以使用完整的變更追蹤和清查解決方案，如下所述。 此處理程式不會移除您的設定。

> [!NOTE]
> 啟用完整的變更追蹤和清查解決方案可能會導致額外的費用。 請參閱[自動化定價](https://azure.microsoft.com/pricing/details/automation/)。

1. 流覽至工作區，然後在[已安裝的監視解決方案清單](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)中尋找，以移除監視解決方案。
2. 按一下解決方案的名稱以開啟其 [摘要] 頁面，然後按一下 [**刪除**]，如[移除監視解決方案](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution)中所述。
3. 若要重新啟用解決方案，請流覽至自動化帳戶，然後選取 [設定**管理**] 底下的 [**變更追蹤**]。
4. 選擇 Log Analytics 工作區和自動化帳戶，確認您的工作區設定，然後按一下 [**啟用**]。

## <a name="onboard-machines-to-change-tracking-and-inventory"></a><a name="onboard"></a>將機器上架到變更追蹤和清查

若要開始追蹤變更，您必須在 Azure 自動化中啟用變更追蹤和清查。 以下是將電腦上架到這項功能的建議和支援方式： 

* [從虛擬機器上架](automation-onboard-solutions-from-vm.md)
* [從流覽多部電腦上架](automation-onboard-solutions-from-browse.md)
* [從您的自動化帳戶上架](automation-onboard-solutions-from-automation-account.md)
* [在 Azure 自動化 runbook 中上架](automation-onboard-solutions.md)

## <a name="track-files"></a>追蹤檔案

### <a name="configure-file-tracking-on-windows"></a>在 Windows 上設定檔案追蹤

使用下列步驟，在 Windows 電腦上設定檔案追蹤：

1. 在您的自動化帳戶中，選取 [設定**管理**] 底下的 [**變更追蹤**]。 
2. 按一下 [編輯設定]**** \(齒輪符號)。
3. 在 [工作區設定] 頁面上，選取 [ **Windows**檔案]，然後按一下 [ **+ 新增**] 以新增要追蹤的新檔案。
4. 在 [加入變更追蹤的 Windows 檔案] 窗格中，輸入要追蹤之檔案的資訊，然後按一下 [**儲存**]。 下表定義您可以用於資訊的屬性。

    |屬性  |描述  |
    |---------|---------|
    |啟用     | 如果已套用設定，則為 True，否則為 False。        |
    |項目名稱     | 要追蹤之檔案的易記名稱。        |
    |群組     | 以邏輯方式分組檔案的群組名稱。        |
    |輸入路徑     | 要檢查檔案的路徑，例如**c：\temp\\\*。** 您也可以使用環境變數，例如`%winDir%\System32\\\*.*`。       |
    |路徑類型     | 路徑的類型。 可能的值為 File 和 Directory。        |    
    |遞迴     | 如果在尋找要追蹤的專案時使用遞迴，則為 True，否則為 False。        |    
    |上傳檔案內容 | True 表示在追蹤的變更上傳檔案內容，否則為 False。|

5. 請確定您為 **[上傳檔案內容**] 指定 [True]。 此設定會針對指定的檔案路徑啟用檔案內容追蹤。

### <a name="configure-file-tracking-on-linux"></a>在 Linux 上設定檔案追蹤

使用下列步驟，設定要在 Linux 電腦上追蹤的檔案：

1. 在您的自動化帳戶中，選取 [設定**管理**] 底下的 [**變更追蹤**]。 
2. 按一下 [編輯設定]**** \(齒輪符號)。
3. 在 [工作區設定] 頁面上，選取 [ **Linux**檔案]，然後按一下 [ **+ 新增**] 以新增要追蹤的新檔案。
4. 在 [為變更追蹤新增 Linux 檔案] 窗格中，輸入要追蹤的檔案或目錄資訊，然後按一下 [**儲存**]。 下表定義您可以用於資訊的屬性。

    |屬性  |描述  |
    |---------|---------|
    |啟用     | 如果已套用設定，則為 True，否則為 False。        |
    |項目名稱     | 要追蹤之檔案的易記名稱。        |
    |群組     | 以邏輯方式分組檔案的群組名稱。        |
    |輸入路徑     | 要檢查檔案的路徑，例如， **/etc/*. 會議**。       |
    |路徑類型     | 路徑的類型。 可能的值為 File 和 Directory。        |
    |遞迴     | 如果在尋找要追蹤的專案時使用遞迴，則為 True，否則為 False。        |
    |使用 Sudo     | 如果要在檢查項目時使用 sudo，則為 True，否則為 False。         |
    |連結     | 設定，決定在遍歷目錄時如何處理符號連結。 可能的值包括：<br> 忽略 - 忽略符號連結，而不包含參考的檔案/目錄。<br>跟隨-在遞迴期間遵循符號連結，並同時包含參考的檔案/目錄。<br>管理 - 遵循符號連結並允許變更傳回的內容。 **注意**-不建議使用此選項，因為它不支援檔案內容抓取。    |
    |上傳檔案內容 | True 表示在追蹤的變更上傳檔案內容，否則為 False。 |

5. 請確定您為 **[上傳檔案內容**] 指定 [True]。 此設定會針對指定的檔案路徑啟用檔案內容追蹤。

   ![新增 Linux 檔案](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>追蹤檔案內容

檔案內容追蹤可讓您在追蹤的變更前後，查看檔案的內容。 功能會在每次變更之後，將檔案內容儲存至儲存體帳戶。 以下是追蹤檔案內容時要遵循的一些規則：

* 儲存檔案內容需要有使用 Resource Manager 部署模型的標準儲存體帳戶。 

* 請勿使用 premium 和傳統部署模型儲存體帳戶。 請參閱[關於 Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md)。

* 您所使用的儲存體帳戶只能連接到一個自動化帳戶。

* 您的自動化帳戶中已啟用[變更追蹤和清查](change-tracking.md)。

### <a name="enable-tracking-for-file-content-changes"></a>啟用追蹤檔案內容變更

1. 在 [Azure 入口網站中，開啟您的自動化帳戶，然後選取 [設定**管理**] 底下的 [**變更追蹤**]。
2. 按一下 [編輯設定]**** \(齒輪符號)。
3. 選取 [檔案內容]****，然後按一下 [連結]****。 此選項會開啟 [加入內容位置以供變更追蹤] 窗格。

   ![啟用內容位置](./media/change-tracking-file-contents/enable.png)

4. 選取要用來儲存檔案內容的訂用帳戶和儲存體帳戶。 

5. 如果您想要啟用所有現有已追蹤檔案的檔案內容追蹤，請針對 [上傳所有設定的檔案內容]**** 選取 [開啓]****。 您稍後可以變更每個檔案路徑的這項設定。

   ![設定儲存體帳戶](./media/change-tracking-file-contents/storage-account.png)

6. 變更追蹤和清查在啟用檔案內容變更追蹤時，會顯示儲存體帳戶和共用存取簽章（SAS） Uri。 簽章會在365天后到期，您可以按一下 [**重新**產生] 來重新建立。

   ![列出帳戶金鑰](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>查看追蹤檔案的內容

變更追蹤和清查偵測到已追蹤檔案的變更後，您就可以在 [變更詳細資料] 窗格中查看檔案內容。  

![清單變更](./media/change-tracking-file-contents/change-list.png)

1. 在 [Azure 入口網站中，開啟您的自動化帳戶，然後選取 [設定**管理**] 底下的 [**變更追蹤**]。

2. 在變更清單中選擇檔案，然後選取 [**查看檔案內容變更**] 以查看檔案的內容。 [變更詳細資料] 窗格會顯示檔案資訊前後的標準。

   ![變更詳細資料](./media/change-tracking-file-contents/change-details.png)

3. 您正在並排查看檔案的內容。 您可以選取 [**內嵌**]，以查看變更的內嵌視圖。

## <a name="track-registry-keys"></a>追蹤登錄機碼

使用下列步驟，設定要在 Windows 電腦上追蹤的登錄機碼：

1. 在您的自動化帳戶中，選取 [設定**管理**] 底下的 [**變更追蹤**]。 
2. 按一下 [編輯設定]**** \(齒輪符號)。
3. 在 [工作區設定] 頁面上，選取 [ **Windows**登錄]。
4. 按一下 [ **+ 新增**] 以新增要追蹤的新登錄機碼。
5. 在 [新增變更追蹤的 Windows 登錄] 窗格上，輸入要追蹤之金鑰的資訊，然後按一下 [**儲存**]。 下表定義您可以用於資訊的屬性。

    |屬性  |描述  |
    |---------|---------|
    |啟用     | 如果套用了設定，則為 True，否則為 False。        |
    |項目名稱     | 要追蹤之登錄機碼的易記名稱。        |
    |群組     | 以邏輯方式分組登錄機碼的組名。        |
    |Windows 登錄機碼   | 具有路徑的索引鍵名稱，例如， **HKEY_LOCAL_MACHINE \Software\microsoft\windows\currentversion\explorer\user Shell Folders\Common 啟動**。      |

## <a name="search-logs-for-change-records"></a>搜尋記錄中的變更記錄

您可以針對變更記錄，針對 Azure 監視器記錄進行各種搜尋。 在 [變更追蹤] 頁面開啟的情況下，按一下 [ **Log Analytics** ] 以開啟 [記錄] 頁面。 下表提供變更記錄的記錄搜尋範例。

|查詢  |描述  |
|---------|---------|
|ConfigurationData<br>&#124;，其中 ConfigDataType = = "Microsoft services" 和 SvcStartupType = = "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | 顯示已設定為 [自動] 但回報為 [已停止] 之 Microsoft 服務最近的清查記錄。 結果會限制為指定軟體名稱和電腦的最新記錄。    |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|顯示已移除軟體的變更記錄。|

## <a name="create-alerts-on-changes"></a>建立變更的警示

下列範例顯示已在機器上修改檔案**C:\windows\system32\drivers\etc\hosts** 。 這個檔案很重要，因為 Windows 會使用它來將主機名稱解析為 IP 位址。 這項作業的優先順序高於 DNS，而且可能會導致連線問題。 它也可能會導致流量重新導向至惡意或危險的網站。

![顯示 hosts 檔案變更的圖表](./media/change-tracking-file-contents/changes.png)

讓我們使用此範例來討論針對變更建立警示的步驟。

1. 在您的自動化帳戶中，選取 [設定**管理**] 底下的 [**變更追蹤**]，然後選取 [ **Log Analytics**]。 
2. 在記錄搜尋中，使用查詢`ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`尋找**hosts**檔案的內容變更。 此查詢會尋找包含「主機」這個字之完整路徑的檔案內容變更。 您也可以要求特定檔案，方法是將路徑部分變更為其完整格式，例如使用`FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`。

3. 查詢傳回所需的結果之後，請按一下記錄搜尋中的 [**新增警示規則**]，以開啟 [警示建立] 頁面。 您也可以透過 Azure 入口網站中的**Azure 監視器**流覽至此頁面。 

4. 再次檢查您的查詢，並修改警示邏輯。 在此情況下，即使在環境中的所有電腦上偵測到一項變更，也要觸發警示。

    ![變更為查詢以追蹤主機檔案的變更](./media/change-tracking-file-contents/change-query.png)

5. 設定警示邏輯之後，請指派動作群組來執行動作，以回應所觸發的警示。 在此情況下，我們會設定要傳送的電子郵件，以及要建立的 IT 服務管理（ITSM）票證。 

    ![正在設定動作群組以在變更時發出警示](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>後續步驟

* 如需變更追蹤和清查的基本概念，請參閱[變更追蹤和清查的總覽](change-tracking.md)。
* 若要對 Azure VM 的變更進行疑難排解，請參閱針對[變更追蹤和清查問題進行疑難排解](troubleshoot/change-tracking.md)。
* 使用[Azure 監視器記錄檔中的記錄檔搜尋](../log-analytics/log-analytics-log-searches.md)，以查看詳細的變更追蹤資料。