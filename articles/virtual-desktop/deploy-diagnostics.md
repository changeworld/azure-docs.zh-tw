---
title: 部署 Windows 虛擬桌面的診斷工具 - Azure
description: 如何為 Windows 虛擬桌面部署診斷 UX 工具。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4eb63fe4bd8f8a8b0961aa6a7fccb8de9b7c2f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123413"
---
# <a name="deploy-the-diagnostics-tool"></a>部署診斷工具

>[!IMPORTANT]
>截至 2020 年 3 月 16 日，我們暫時禁用了由於服務需求增加而影響使用者體驗的診斷查詢。 這將導致該工具停止工作，因為它依賴于這些查詢來運行。 當診斷查詢再次可用時，我們將更新本文。
>
>在此之前，我們強烈建議您[使用日誌分析](diagnostics-log-analytics.md)進行持續監控。

以下是 Windows 虛擬桌面的診斷工具可以為您執行以下操作：

- 在一周內查找單個使用者的診斷活動（管理、連接或源）。
- 從日誌分析工作區收集連接活動的工作階段主機資訊。
- 查看特定主機的虛擬機器 （VM） 性能詳細資訊。
- 查看哪些使用者登錄到工作階段主機。
- 向特定工作階段主機上的活動使用者發送消息。
- 將使用者從工作階段主機登出。

## <a name="prerequisites"></a>Prerequisites

您需要創建 Azure 活動目錄應用註冊和日誌分析工作區，然後才能為該工具部署 Azure 資源管理器範本。 您或管理員需要這些許可權才能執行此操作：

- Azure 訂閱的擁有者
- 在 Azure 訂閱中創建資源的許可權
- 創建 Azure AD 應用的許可權
- RDS 擁有者或參與者許可權

在開始之前，您還需要安裝這兩個 PowerShell 模組：

- [Azure PowerShell 模組](/powershell/azure/install-az-ps?view=azps-2.4.0/)
- [Azure AD 模組](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0/)

請確保在登錄時已準備好訂閱 ID。

按順序排列所有內容後，可以創建 Azure AD 應用註冊。

## <a name="create-an-azure-active-directory-app-registration"></a>創建 Azure 活動目錄應用註冊

本節將介紹如何使用 PowerShell 使用服務主體創建 Azure 活動目錄應用並獲取其 API 許可權。

>[!NOTE]
>API 許可權是 Windows 虛擬桌面、日誌分析和 Microsoft 圖形 API 許可權添加到 Azure 活動目錄應用程式。

1. 以系統管理員身分開啟 PowerShell。
2. 使用具有要用於診斷工具的 Azure 訂閱的擁有者或參與者許可權的帳戶登錄到 Azure：
   ```powershell
   Login-AzAccount
   ```
3. 使用相同的帳戶登錄到 Azure AD：
   ```powershell
   Connect-AzureAD
   ```
4. 轉到[RDS 範本 GitHub 存儲庫](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts)，並在 PowerShell 中運行**CreateAdApp 註冊診斷.ps1**腳本。
5.  當腳本要求您命名應用時，請輸入唯一的應用名稱。


腳本成功運行後，應在其輸出中顯示以下內容：

-  確認應用的消息現在具有服務主體角色指派。
-  部署診斷工具時需要的用戶端 ID 和用戶端金鑰。

現在，您已經註冊了應用，是時候配置日誌分析工作區了。

## <a name="configure-your-log-analytics-workspace"></a>配置日誌分析工作區

為了獲得最佳體驗，我們建議您使用以下效能計數器配置日誌分析工作區，這些計數器允許您在遠端會話中派生使用者體驗的語句。 有關建議具有建議的閾值的計數器的清單，請參閱[Windows 效能計數器閾值](deploy-diagnostics.md#windows-performance-counter-thresholds)。

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>使用 PowerShell 創建 Azure 日誌分析工作區

您可以運行 PowerShell 腳本來創建日誌分析工作區，並配置建議的 Windows 效能計數器以監視使用者體驗和應用性能。

>[!NOTE]
>如果已有一個現有日誌分析工作區，您沒有要使用的 PowerShell 腳本，請先跳一跳以驗證[Azure 門戶中的結果腳本](#validate-the-script-results-in-the-azure-portal)。

要運行 PowerShell 腳本，

1.  以系統管理員身分開啟 PowerShell。
2.  轉到[RDS 範本 GitHub 存儲庫](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts)，並在 PowerShell 中運行**CreateLogAnalyticsFor診斷.ps1**腳本。
3. 為各個參數輸入下列值︰

    - 對於**資源組名稱**，請輸入資源組的名稱。
    - 對於**日誌分析工作區名稱**，請為日誌分析工作區輸入唯一名稱。
    - 對於**位置**，輸入正在使用的 Azure 區域。
    - 輸入**Azure 訂閱 ID**，您可以在 **"訂閱**"下的 Azure 門戶中找到該 ID。

4. 輸入具有委派管理員存取權限的使用者的憑據。
5. 使用同一使用者的憑據登錄到 Azure 門戶。
6. 記下或記住日誌分析工作區 ID，以便以後使用。
7. 如果使用 PowerShell 腳本設置日誌分析工作區，則應已配置效能計數器，並且可以跳過以[在 Azure 門戶中驗證腳本結果](#validate-the-script-results-in-the-azure-portal)。 否則，請繼續下一節。

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>在現有日誌分析工作區中配置 Windows 效能計數器

本節適用于希望使用上一節中沒有 PowerShell 腳本創建的現有 Azure 日誌分析工作區的使用者。 如果您尚未使用該腳本，則必須手動設定建議的 Windows 效能計數器。

以下是手動設定推薦的效能計數器：

1. 打開 Internet 瀏覽器並使用管理帳戶登錄到[Azure 門戶](https://portal.azure.com/)。
2. 接下來，轉到**日誌分析工作區**，查看配置的 Windows 效能計數器。
3. 在 **"設置"** 部分中，選擇 **"高級設置**"。
4. 之後，導航到**資料** > **Windows 效能計數器**並添加以下計數器：

    -   邏輯磁片（\*\\） %可用空間
    -   邏輯磁片（C：）\\平均磁片佇列長度
    -   記憶體（\*\\） 可用 MB
    -   處理器資訊（\*\\） 處理器時間
    -   每個會話的使用者輸入延遲（\*\\） 最大輸入延遲

詳細瞭解 Azure 監視器 中的[Windows 和 Linux 效能資料源](/azure/azure-monitor/platform/data-sources-performance-counters)的效能計數器。

>[!NOTE]
>您配置的任何其他計數器不會顯示在診斷工具本身中。 要使其顯示在診斷工具中，您需要組態工具的設定檔。 有關如何使用高級管理執行此操作的說明將在以後在 GitHub 中提供。

## <a name="validate-the-script-results-in-the-azure-portal"></a>在 Azure 門戶中驗證腳本結果

在繼續部署診斷工具之前，我們建議您驗證 Azure 活動目錄應用程式是否具有 API 許可權，並且日誌分析工作區具有預配置的 Windows 效能計數器。

### <a name="review-your-app-registration"></a>查看應用註冊

要確保應用註冊具有 API 許可權，請進行：

1. 打開瀏覽器並使用管理帳戶連接到[Azure 門戶](https://portal.azure.com/)。
2. 移至 **Azure Active Directory**。
3. 轉到**應用程式註冊**並選擇 **"所有應用程式**"。
4. 查找 Azure AD 應用註冊，其應用名稱與[創建 Azure 活動目錄應用註冊](deploy-diagnostics.md#create-an-azure-active-directory-app-registration)的步驟 5 中輸入的應用名稱相同。

### <a name="review-your-log-analytics-workspace"></a>查看日誌分析工作區

要確保日誌分析工作區具有預配置的 Windows 效能計數器，請執行以下操作：

1. 在[Azure 門戶](https://portal.azure.com/)中，轉到**日誌分析工作區**以查看配置的 Windows 效能計數器。
2. 在 **"設置"** 下，選擇 **"高級設置**"。
3. 之後，轉到**資料** > **Windows 效能計數器**。
4. 確保預先配置了以下計數器：

   - 邏輯磁片（\*\\） %可用空間：以百分比形式顯示磁片上總可用空間的可用空間量。
   - 邏輯磁片（C：）\\平均磁片佇列長度：C 磁碟機的磁片傳輸請求的長度。 該值不應超過 2，超過較短的時間。
   - 記憶體（\*\\） 可用 MB：系統的可用記憶體（以百萬位元組為單位）。
   - 處理器資訊（\*\\） 處理器時間：處理器執行非空閒執行緒所花費的經過時間的百分比。
   - 每個會話的使用者輸入延遲（\*\\） 最大輸入延遲

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>連接到日誌分析工作區中的 VM

為了能夠查看 VM 的運行狀況，您需要啟用日誌分析連接。 按照以下步驟連接 VM：

1. 打開瀏覽器並使用管理帳戶登錄到[Azure 門戶](https://portal.azure.com/)。
2. 轉到日誌分析工作區。
3. 在左側面板中，在工作區資料來源下，選擇**虛擬機器**。
4. 選擇要連接到的 VM 的名稱。
5. 選取 [連接]****。

## <a name="deploy-the-diagnostics-tool"></a>部署診斷工具

要為診斷工具部署 Azure 資源管理範本，請將範本部署：

1.  轉到 [GitHub Azure RDS 範本頁面](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy)。
2.  將範本部署到 Azure 並按照範本中的說明進行操作。 請確保您有以下資訊可用：

    -   用戶端 Id
    -   用戶端-機密
    -   Log Analytics 工作區識別碼

3.  提供輸入參數後，接受條款及條件，然後選擇 **"購買**"。

部署需要 2-3 分鐘。 成功部署後，轉到資源組並確保 Web 應用和應用服務方案資源在那裡。

之後，您需要設置重定向 URI。

### <a name="set-the-redirect-uri"></a>設置重定向 URI

要設置重定向 URI：

1.  在[Azure 門戶](https://portal.azure.com/)中，轉到**應用服務**並找到您創建的應用程式。
2.  轉到概述頁面並複製您在那裡找到的 URL。
3.  導航到**應用註冊**並選擇要部署的應用。
4.  在左側面板中，在"管理"部分下，選擇 **"身份驗證**"。
5.  將所需的重定向 URI 輸入重定向 URI 到 **"重定向 URI"** 文字方塊中，然後在功能表的左上角選擇 **"保存**"。
6. 在"類型"下的下拉式功能表中選擇**Web。**
7. 從應用概述頁輸入 URL，並在其末尾添加 **/安全/登錄回檔**。 例如：`https://<yourappname>.azurewebsites.net/security/signin-callback`。

   ![重定向 URI 頁面](media/redirect-uri-page.png)

8. 現在，轉到 Azure 資源，選擇具有範本中提供的名稱的 Azure 應用服務資源，然後導航到與其關聯的 URL。 （例如，如果您在範本中使用的應用名稱為`contosoapp45`，則關聯的 URL 為<https://contosoapp45.azurewebsites.net>。
9. 使用適當的 Azure Active Directory 使用者帳戶登入。
10.   選取 [接受]****。

## <a name="distribute-the-diagnostics-tool"></a>分發診斷工具

在使診斷工具可供使用者使用之前，請確保他們具有以下許可權：

- 使用者需要讀取存取許可權進行日誌分析。 有關詳細資訊，請參閱使用[Azure 監視器 開始使用角色、許可權和安全性](/azure/azure-monitor/platform/roles-permissions-security)。
-  使用者還需要 Windows 虛擬桌面租戶（RDS 讀取器角色）的讀取存取許可權。 有關詳細資訊，請參閱[Windows 虛擬桌面中的委派訪問](delegated-access-virtual-desktop.md)。

您還需要為使用者提供以下資訊：

- 應用的 URL
- 租戶組他們可以訪問的單個租戶的名稱。

## <a name="use-the-diagnostics-tool"></a>使用診斷工具

使用從組織收到的資訊登錄到您的帳戶後，準備好 UPN 以適合您的使用者查詢活動。 搜索將為您提供上周內發生的指定活動類型下的所有活動。

### <a name="how-to-read-activity-search-results"></a>如何閱讀活動搜尋結果

活動按時間戳記排序，首先使用最新活動。 如果結果返回錯誤，請先檢查是否為服務錯誤。 對於服務錯誤，創建包含活動資訊的支援票證，以説明我們調試問題。 所有其他錯誤類型通常可以通過使用者或管理員來解決。 有關最常見的錯誤方案的清單以及如何解決這些問題，請參閱[識別和診斷問題](diagnostics-role-service.md#common-error-scenarios)。

>[!NOTE]
>在連結的文檔中，服務錯誤稱為"外部錯誤"。 當我們更新 PowerShell 引用時，這將更改。

連接活動可能有多個錯誤。 您可以展開活動類型以查看使用者遇到的任何其他錯誤。 選擇錯誤代碼的名稱以打開對話方塊以查看有關它的詳細資訊。

### <a name="investigate-the-session-host"></a>調查工作階段主機 

在搜尋結果中，查找並選擇您想要的資訊的工作階段主機。

您可以分析工作階段主機運行狀況：

- 根據預定義的閾值，您可以檢索日誌分析查詢的工作階段主機運行狀況資訊。
- 如果沒有活動或工作階段主機未連接到日誌分析，則資訊將不可用。

您還可以與會話主機上的使用者進行交互：

- 您可以登出或向已簽名的使用者發送消息。
- 預設情況下，您最初搜索的使用者是被選中的，但您也可以選擇其他使用者來一次發送消息或登出多個使用者。

### <a name="windows-performance-counter-thresholds"></a>Windows 效能計數器閾值

- 邏輯磁片（\*\\） %可用空間：

    - 顯示可用邏輯磁片上總可用空間的百分比。
    - 閾值：小於 20% 被標記為不正常。

- 邏輯磁片（C：）\\平均磁片佇列長度：

    - 表示存儲系統條件。
    - 閾值：高於 5 標記為不正常。

- 記憶體（\*\\） 可用 MB：

    - 系統的可用記憶體。
    - 閾值：小於 500 MB 標記為不正常。

- 處理器資訊（\*\\） 處理器時間：

    - 閾值：高於 80% 被標記為不正常。

- [每個會話的使用者輸入延遲\*（\\） 最大輸入延遲](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)：

    - 閾值：高於 2000 毫秒被標記為不正常。

## <a name="next-steps"></a>後續步驟

- 瞭解如何[使用日誌分析在使用診斷](diagnostics-log-analytics.md)中監視活動日誌。
- 閱讀常見錯誤方案以及如何在[識別和診斷問題](diagnostics-role-service.md)時修復它們。
