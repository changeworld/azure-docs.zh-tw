---
title: 部署適用于 Windows 虛擬桌面 (傳統) 的診斷工具-Azure
description: 如何部署適用于 Windows 虛擬桌面的診斷 UX 工具 (傳統) 。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 61c31b24b01b40da4d73a308a4f304f6ff242e41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88691406"
---
# <a name="deploy-the-windows-virtual-desktop-classic-diagnostics-tool"></a>部署 Windows 虛擬桌面 (傳統) 診斷工具

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。

>[!IMPORTANT]
>從2020年3月16日起，我們已暫時停用會影響使用者體驗的診斷查詢（因為服務的需求增加）。 這會導致工具停止運作，因為它依賴這些查詢才能運作。 當診斷查詢再次可供使用時，我們將會更新這篇文章。
>
>在那之前，我們強烈建議您 [使用 Log Analytics](diagnostics-log-analytics-2019.md) 來繼續監視。

以下是適用于 Windows 虛擬桌面的診斷工具可為您執行的動作：

- 針對單一使用者在一周內的 (管理、連線或摘要) 查閱診斷活動。
- 從 Log Analytics 工作區收集連接活動的工作階段主機資訊。
- 請參閱虛擬機器 (VM) 特定主機的效能詳細資料。
- 查看哪些使用者已登入工作階段主機。
- 將訊息傳送給特定工作階段主機上的作用中使用者。
- 將使用者登出工作階段主機。

## <a name="prerequisites"></a>Prerequisites

您必須先建立 Azure Active Directory 應用程式註冊和 Log Analytics 工作區，才能部署此工具的 Azure Resource Manager 範本。 您或系統管理員需要這些許可權才能執行此作業：

- Azure 訂用帳戶的擁有者
- 在您的 Azure 訂用帳戶中建立資源的許可權
- 建立 Azure AD 應用程式的許可權
- RDS 擁有者或參與者許可權

在開始之前，您也必須安裝這兩個 PowerShell 模組：

- [Azure PowerShell 模組](/powershell/azure/install-az-ps?view=azps-2.4.0/)
- [Azure AD 模組](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0/)

當您登入時，請確定您已備妥訂用帳戶識別碼。

依序取得所有專案之後，您就可以建立 Azure AD 應用程式註冊。

## <a name="create-an-azure-active-directory-app-registration"></a>建立 Azure Active Directory 應用程式註冊

本節將說明如何使用 PowerShell 來建立具有服務主體的 Azure Active Directory 應用程式，並取得其 API 許可權。

>[!NOTE]
>API 許可權為「Windows 虛擬桌面」、Log Analytics 和 Microsoft Graph API 許可權會新增至 Azure Active Directory 應用程式。

1. 以系統管理員身分開啟 PowerShell。
2. 使用您想要用於診斷工具之 Azure 訂用帳戶擁有者或參與者許可權的帳戶登入 Azure：
   ```powershell
   Login-AzAccount
   ```
3. 使用相同的帳戶登入 Azure AD：
   ```powershell
   Connect-AzureAD
   ```
4. 移至 [RDS 範本 GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) 存放庫，並在 PowerShell 中執行 **CreateADAppRegistrationforDiagnostics.ps1** 腳本。
5.  當腳本要求您為應用程式命名時，請輸入唯一的應用程式名稱。


腳本順利執行之後，它應該會在輸出中顯示下列內容：

-  確認您的應用程式現在有服務主體角色指派的訊息。
-  您在部署診斷工具時需要的用戶端識別碼和用戶端秘密金鑰。

現在您已註冊您的應用程式，接下來就是設定 Log Analytics 工作區的時間。

## <a name="configure-your-log-analytics-workspace"></a>設定您的 Log Analytics 工作區

為了獲得最佳體驗，我們建議您使用下列效能計數器來設定 Log Analytics 工作區，讓您可以在遠端會話中衍生使用者體驗的聲明。 如需建議的計數器清單以及建議的閾值，請參閱 [Windows 效能計數器閾值](deploy-diagnostics.md#windows-performance-counter-thresholds)。

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>使用 PowerShell 建立 Azure Log Analytics 工作區

您可以執行 PowerShell 腳本來建立 Log Analytics 工作區，並設定建議的 Windows 效能計數器，以監視使用者體驗和應用程式效能。

>[!NOTE]
>如果您已經有現有的 Log Analytics 工作區，但沒有您想要使用的 PowerShell 腳本，請直接跳到 [Azure 入口網站中驗證腳本結果](#validate-the-script-results-in-the-azure-portal)。

若要執行 PowerShell 腳本：

1.  以系統管理員身分開啟 PowerShell。
2.  移至 [RDS 範本 GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) 存放庫，並在 PowerShell 中執行 **CreateLogAnalyticsWorkspaceforDiagnostics.ps1** 腳本。
3. 為各個參數輸入下列值︰

    - 針對 [ **ResourceGroupName**]，輸入資源群組的名稱。
    - 針對 [ **LogAnalyticsWorkspaceName**]，輸入 Log Analytics 工作區的唯一名稱。
    - 針對 [ **位置**]，輸入您要使用的 Azure 區域。
    - 輸入 **Azure 訂**用帳戶識別碼，您可以在 [訂 **用帳戶**] 下的 Azure 入口網站中找到此識別碼。

4. 輸入具有委派系統管理員存取權的使用者認證。
5. 使用相同的使用者認證登入 Azure 入口網站。
6. 寫下或記住 LogAnalyticsWorkspace 識別碼，以供稍後之用。
7. 如果您使用 PowerShell 腳本來設定 Log Analytics 工作區，則您的效能計數器應已設定，而且您可以直接跳過以 [驗證 Azure 入口網站中的腳本結果](#validate-the-script-results-in-the-azure-portal)。 否則，請繼續下一節。

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>在現有的 Log Analytics 工作區中設定 Windows 效能計數器

本節適用于想要使用現有 Azure Log Analytics 工作區的使用者，而該工作區在上一節沒有 PowerShell 腳本的情況下建立。 如果您還沒有使用腳本，則必須手動設定建議的 Windows 效能計數器。

以下說明如何手動設定建議的效能計數器：

1. 開啟您的網際網路瀏覽器，並使用您的系統管理帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。
2. 接下來，請移至 **Log Analytics 工作區** ，以檢查已設定的 Windows 效能計數器。
3. 在 [ **設定** ] 區段中，選取 [  **Advanced Settings**]。
4. 之後，流覽至 [**資料**  >  **Windows 效能計數器**]，並新增下列計數器：

    -   LogicalDisk (\*) \\ % 可用空間
    -   LogicalDisk (C： ) \\ 平均磁片佇列長度
    -   記憶體 (\*) \\ 可用 mb
    -   處理器資訊 (\*) \\ 處理器時間
    -   每個會話的使用者輸入延遲 (\*) \\ 最大輸入延遲

[在 Azure 監視器中深入瞭解 Windows 與 Linux 效能資料來源](/azure/azure-monitor/platform/data-sources-performance-counters)的效能計數器。

>[!NOTE]
>您所設定的任何其他計數器都不會顯示在診斷工具本身。 若要讓它顯示在診斷工具中，您必須設定工具的設定檔。 在 GitHub 中，如何使用 advanced administration 來進行這項操作的指示，將于稍後的日期提供。

## <a name="validate-the-script-results-in-the-azure-portal"></a>驗證 Azure 入口網站中的腳本結果

在您繼續部署診斷工具之前，建議您先確認您的 Azure Active Directory 應用程式具有 API 許可權，而且您的 Log Analytics 工作區具有預先設定的 Windows 效能計數器。

### <a name="review-your-app-registration"></a>檢查您的應用程式註冊

若要確認您的應用程式註冊有 API 許可權：

1. 開啟瀏覽器，並使用您的系統管理帳戶連接到 [Azure 入口網站](https://portal.azure.com/) 。
2. 移至 **Azure Active Directory**。
3. 移至 **應用程式註冊** ，然後選取 [ **所有應用程式**]。
4. 使用您在步驟5中輸入的相同應用程式名稱來尋找您的 Azure AD 應用程式註冊，以 [建立 Azure Active Directory 應用程式註冊](deploy-diagnostics.md#create-an-azure-active-directory-app-registration)。

### <a name="review-your-log-analytics-workspace"></a>檢查您的 Log Analytics 工作區

若要確認您的 Log Analytics 工作區具有預先設定的 Windows 效能計數器：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，移至 **Log Analytics 工作區** 以檢查已設定的 Windows 效能計數器。
2. 在 [ **設定**] 底下，選取 [ **Advanced Settings**]。
3. 之後，請移至**資料**  >  **Windows 效能計數器**。
4. 請確定已預先設定下列計數器：

   - LogicalDisk (\*) \\ % 可用空間：顯示磁片上總可用空間的可用空間量（以百分比表示）。
   - LogicalDisk (C： ) \\ Avg. Disk Queue Length： C 磁片磁碟機的磁片傳輸要求長度。 值的長度不應超過2次。
   - 記憶體 (\*) \\ 可用 mb 數：系統的可用記憶體（以 mb 為單位）。
   - 處理器資訊 (\*) \\ 處理器時間：處理器花費在執行非閒置執行緒所經過時間的百分比。
   - 每個會話的使用者輸入延遲 (\*) \\ 最大輸入延遲

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>連接至 Log Analytics 工作區中的 Vm

為了能夠查看 Vm 的健康情況，您必須啟用 Log Analytics 連線。 遵循下列步驟來連接您的 Vm：

1. 開啟瀏覽器，並使用您的系統管理帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。
2. 移至您的 Log Analytics 工作區。
3. 在左面板的 [工作區資料來源] 底下，選取 [ **虛擬機器**]。
4. 選取您要連接的 VM 名稱。
5. 選取 [連接]。

## <a name="deploy-the-diagnostics-tool"></a>部署診斷工具

若要部署適用于診斷工具的 Azure 資源管理範本：

1.  移至 [GitHub Azure RDS 範本頁面](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy) \(英文\)。
2.  將範本部署至 Azure，並遵循範本中的指示。 請確定您有下列可用資訊：

    -   Client-Id
    -   Client-Secret
    -   Log Analytics 工作區識別碼

3.  提供輸入參數之後，請接受條款及條件，然後選取 [ **購買**]。

部署需要2到3分鐘。 成功部署之後，請移至資源群組，並確定有 web 應用程式和 app service 方案資源。

之後，您必須設定重新導向 URI。

### <a name="set-the-redirect-uri"></a>設定重新導向 URI

若要設定重新導向 URI：

1.  在 [Azure 入口網站](https://portal.azure.com/)中，移至 [ **應用程式服務** ]，並找出您所建立的應用程式。
2.  移至 [總覽] 頁面，並複製您在該處找到的 URL。
3.  流覽至 [ **應用程式註冊** ]，然後選取您想要部署的應用程式。
4.  在左面板的 [管理] 區段下，選取 [ **驗證**]。
5.  在 [重新 **導向 uri** ] 文字方塊中輸入所需的重新導向 uri，然後選取功能表左上角的 [ **儲存** ]。
6. 在 [類型] 下方的下拉式功能表中選取 [ **Web** ]。
7. 從應用程式總覽頁面輸入 URL，並將 **/security/signin-callback** 新增至其結尾。 例如： `https://<yourappname>.azurewebsites.net/security/signin-callback` 。

   > [!div class="mx-imgBorder"]
   > ![重新導向 URI 頁面](../media/redirect-uri-page.png)

8. 現在，移至您的 Azure 資源，選取您在範本中提供之名稱的 Azure App Services 資源，然後流覽至與其相關聯的 URL。  (例如，如果您在範本中使用的應用程式名稱是 `contosoapp45` ，則會) 相關聯的 URL <http://contoso.azurewebsites.net> 。
9. 使用適當的 Azure Active Directory 使用者帳戶登入。
10.   選取 [接受]。

## <a name="distribute-the-diagnostics-tool"></a>散發診斷工具

在您將診斷工具提供給使用者之前，請確定他們有下列許可權：

- 使用者需要 log analytics 的讀取存取權。 如需詳細資訊，請參閱 [Azure 監視器的角色、許可權和安全性](/azure/azure-monitor/platform/roles-permissions-security)入門。
-  使用者也需要 Windows 虛擬桌面租使用者的讀取權限 (RDS 讀者角色) 。 如需詳細資訊，請參閱 [Windows 虛擬桌面中的委派存取權](delegated-access-virtual-desktop-2019.md)。

您也必須為使用者提供下列資訊：

- 應用程式的 URL
- 可以存取的租使用者群組個別租使用者名稱。

## <a name="use-the-diagnostics-tool"></a>使用診斷工具

使用您從組織收到的資訊登入帳戶之後，您就可以為您想要查詢活動的使用者準備好 UPN。 搜尋會為您提供在過去一周內發生之指定活動類型下的所有活動。

### <a name="how-to-read-activity-search-results"></a>如何讀取活動搜尋結果

活動會依時間戳記排序，最新的活動優先。 如果結果傳回錯誤，請先檢查是否為服務錯誤。 針對服務錯誤，請使用活動資訊建立支援票證，以協助我們進行問題的偵測。 使用者或系統管理員通常可以解決所有其他錯誤類型。 如需最常見的錯誤案例清單和解決方法，請參閱 [識別和診斷問題](diagnostics-role-service-2019.md#common-error-scenarios)。

>[!NOTE]
>服務錯誤在連結的檔中稱為「外部錯誤」。 當我們更新 PowerShell 參考時，這會有所變更。

連接活動可能有一個以上的錯誤。 您可以展開活動類型，以查看使用者所遇到的任何其他錯誤。 選取錯誤碼的名稱以開啟對話方塊，以查看其詳細資訊。

### <a name="investigate-the-session-host"></a>調查工作階段主機

在搜尋結果中，尋找並選取您想要取得相關資訊的工作階段主機。

您可以分析工作階段主機健全狀況：

- 根據預先定義的閾值，您可以取得 Log Analytics 查詢的工作階段主機健全狀況資訊。
- 當沒有任何活動或工作階段主機未連線到 Log Analytics 時，就無法使用該資訊。

您也可以與會話主機上的使用者互動：

- 您可以登出或傳送訊息給已登入的使用者。
- 預設會選取您原本搜尋的使用者，但您也可以選取 [其他使用者] 來傳送訊息，或一次登出多個使用者。

### <a name="windows-performance-counter-thresholds"></a>Windows 效能計數器閾值

- LogicalDisk (\*) \\ % 可用空間：

    - 顯示邏輯磁片上可用空間的總可用空間百分比。
    - 閾值：小於20% 標示為狀況不良。

- LogicalDisk (C： ) \\ Avg. Disk Queue Length：

    - 表示儲存系統條件。
    - 閾值：高於5的標記為狀況不良。

- 記憶體 (\*) \\ 可用 mb：

    - 系統可用的記憶體。
    - 閾值：小於 500 mb，標示為狀況不良。

- 處理器資訊 (\*) \\ 處理器時間：

    - 閾值：高於80% 會標示為狀況不良。

- [每個會話的使用者輸入延遲 (\*) \\ 最大輸入延遲](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)：

    - 閾值：高於2000毫秒會標示為狀況不良。

## <a name="next-steps"></a>接下來的步驟

- 瞭解如何使用 [Log Analytics 監視使用診斷](diagnostics-log-analytics-2019.md)的活動記錄。
- 請參閱常見的錯誤案例，以及如何在 [找出及診斷問題](diagnostics-role-service-2019.md)時加以修正。
