---
title: 將操作管理器連接到 Azure 監視器 |微軟文檔
description: 若要維護 System Center Operations Manager 中的現有投資，並使用 Log Analytics 的延伸功能，您可以整合 Operations Manager 與工作區。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 92b6737f48d8d8704f461c9adac92284b323b05f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274342"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>將操作管理器連接到 Azure 監視器

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

要維護[系統中心操作管理器](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807)的現有投資，並將擴展功能與 Azure 監視器一起使用，可以將操作管理器與日誌分析工作區集成。 這允許您利用 Azure 監視器中的日誌機會，同時繼續使用操作管理器：

* 使用 Operations Manager 監視 IT 服務的健全狀況
* 維護與支援事件和問題管理之 ITSM 解決方案的整合
* 管理代理程式的生命週期，這些代理程式部署到內部部署以及您使用 Operations Manager 所監視的公用雲端 IaaS 虛擬機器

與系統中心操作管理器集成，使用 Azure 監視器的速度和效率從操作管理器收集、存儲和分析日誌資料，從而為服務運營策略增加價值。 Azure 監視器日誌查詢有助於關聯並致力於識別問題故障和顯示重複，以支援現有問題管理過程。 查詢引擎能夠靈活地檢查性能、事件和警報資料，具有豐富的儀表板和報告功能，以有意義的方式公開此資料，這顯示了 Azure 監視器在恭維操作管理器中帶來的優勢。

向操作經理管理組報告的代理會根據工作區中啟用的[日誌分析資料源](agent-data-sources.md)和解決方案從伺服器收集資料。 根據啟用的解決方案，它們的資料要麼直接從操作管理器管理伺服器發送到服務，要麼由於在代理託管系統上收集的資料量，直接從代理髮送到日誌分析工作區。 管理伺服器會直接將資料轉送到服務，永遠不會寫入作業或資料倉儲資料庫。 當管理伺服器失去與 Azure 監視器的連接時，它將在本機快取資料，直到重新建立通信。 如果管理伺服器由於計畫維護或計畫外停機而離線，則管理組中的另一個管理伺服器將恢復與 Azure 監視器的連接。  

下圖顯示了系統中心操作管理器管理組中的管理伺服器和代理之間的連接，包括方向和埠。

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

如果 IT 安全性原則不允許您網路上的電腦連線到網際網路，則可以將管理伺服器設定為連線到 Log Analytics 閘道，以根據已啟用的解決方案來接收組態資訊和傳送收集到的資料。 有關如何配置操作管理器管理組以通過日誌分析閘道與 Azure 監視器進行通信的詳細資訊和步驟，請參閱[使用日誌分析閘道將電腦連接到 Azure 監視器](../../azure-monitor/platform/gateway.md)。  

## <a name="prerequisites"></a>Prerequisites

開始之前，請檢閱下列需求。

* Azure 監視器僅支援系統中心操作管理器 2016 或更高版本、操作管理器 2012 SP1 UR6 或更高版本，以及操作管理器 2012 R2 UR2 或更高版本。 Operations Manager 2012 SP1 UR7 和 Operations Manager 2012 R2 UR3 中已加入 Proxy 支援。
* 將系統中心運營管理器 2016 與美國政府雲集成需要更新的"顧問管理包"，包括更新彙總套件 2 或更高版本。 系統中心運營管理器 2012 R2 需要更新的 Advisor 管理包，包括更新彙總套件 3 或更高版本。
* 所有 Operations Manager 代理程式必須符合最低支援需求。 請確定代理程式已安裝最低更新版本，否則 Windows 代理程式通訊可能會失敗，而且會在 Operations Manager 事件記錄中產生錯誤。
* Log Analytics 工作區。 如需進一步資訊，請檢閱 [Log Analytics 工作區概觀](design-logs-deployment.md)。 
* 您必須使用屬於 [Log Analytics 參與者角色](manage-access.md#manage-access-using-azure-permissions)向 Azure 驗證。

* 支援的區域 - 系統中心操作管理器僅支援以下 Azure 區域以連接到日誌分析工作區：
    - 美國中西部
    - 澳大利亞東南部
    - 西歐
    - 美國東部
    - 東南亞
    - 日本東部
    - 英國南部
    - 印度中部
    - 加拿大中部
    - 美國西部 2

>[!NOTE]
>最近對 Azure API 的更改將阻止客戶首次成功配置其管理組和 Azure 監視器之間的集成。 已將其管理群組與該服務整合的客戶則不會受影響，除非您需要重新設定您的現有連線。  
>我們已針對下列版本的 Operations Manager 發行新的管理組件：
> - 對於系統中心操作管理器 2019，此管理包包含在源介質中，並在設置新管理組期間或升級期間安裝。
>- 運營管理器 1801 管理包也適用于運營管理器 1807。
>- 對於系統中心操作管理器 1801，[請在此處](https://www.microsoft.com/download/details.aspx?id=57173)下載管理包。
>- 對於系統中心 2016 - 運營管理器，[請在此處](https://www.microsoft.com/download/details.aspx?id=57172)下載管理包。  
>- 對於系統中心操作管理器 2012 R2，[請在此處](https://www.microsoft.com/download/details.aspx?id=57171)下載管理包。  


### <a name="network"></a>網路

以下資訊列出了操作管理器代理、管理伺服器和操作主控台與 Azure 監視器通信所需的代理和防火牆配置資訊。 從每個元件的流量從網路向 Azure 監視器出站。

|資源 | 連接埠號碼| 略過 HTTPS 檢查|  
|---------|------|-----------------------|  
|**代理**|||  
|\*.ods.opinsights.azure.com| 443 |是|  
|\*.oms.opinsights.azure.com| 443|是|  
|\*.blob.core.windows.net| 443|是|  
|\*.azure-automation.net| 443|是|  
|**管理伺服器**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| 是|  
|\*.ods.opinsights.azure.com| 443| 是|  
|*.azure-automation.net | 443| 是|  
|**操作管理器主控台到 Azure 監視器**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 和 443||  
|\*.microsoft.com| 80 和 443||  
|\*.microsoftonline.com| 80 和 443||  
|\*.mms.microsoft.com| 80 和 443||  
|login.windows.net| 80 和 443||  
|portal.loganalytics.io| 80 和 443||
|api.loganalytics.io| 80 和 443||
|docs.loganalytics.io| 80 和 443||  

### <a name="tls-12-protocol"></a>TLS 1.2 通訊協定

為了確保傳輸到 Azure 監視器的資料的安全性，我們強烈建議您配置代理和管理組至少使用傳輸層安全 （TLS） 1.2。 我們已發現較舊版本的 TLS/安全通訊端層 (SSL) 較易受到攻擊，而且在其目前的運作中仍允許回溯相容性，因此並**不建議使用**這些版本。 如需其它資訊，請檢閱[使用 TLS 1.2 安全地傳送](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)。

## <a name="connecting-operations-manager-to-azure-monitor"></a>將操作管理器連接到 Azure 監視器

執行下列一系列的步驟，設定 Operations Manager 管理群組來連接到其中一個 Log Analytics 工作區。

在 Operations Manager 管理群組第一次向 Log Analytics 工作區進行登錄期間，在 Operations 主控台中為管理群組指定 Proxy 設定的選項無法使用。  必須先向服務成功登錄該管理群組，之後此選項才可供使用。  若要因應此問題，您需要在執行 Operations 主控台的系統上使用 Netsh 更新系統 Proxy 設定，以在管理群組中設定整合，以及所有管理伺服器。  

1. 開啟提升權限的命令提示字元。
   a. 移至 [開始]**** 並輸入 **cmd**。
   b. 按右鍵**命令提示**並選擇"以管理員身份運行"*。
1. 輸入下列命令並按 **Enter** 鍵：

    `netsh winhttp set proxy <proxy>:<port>`

完成與 Azure 監視器集成的以下步驟後，可以通過運行`netsh winhttp reset proxy`來刪除配置，然後在操作主控台中使用 **"配置代理伺服器"** 選項指定代理或日誌分析閘道伺服器。

1. 在 Operations Manager 主控台中，選取 [管理] **** 工作區。
1. 展開 Operations Management Suite 節點，然後按一下 [連接] ****。
1. 按一下 [註冊到 Operations Management Suite] **** 連結。
1. 在 [Operations Management Suite 登入精靈：驗證]**** 頁面上，輸入與 OMS 訂用帳戶相關聯之系統管理員帳戶的電子郵件地址或電話號碼和密碼，然後按一下 [登入]****。

   >[!NOTE]
   >操作管理套件名稱已停用。

1. 成功通過驗證之後，在 [Operations Management Suite 登入精靈: 選取工作區]**** 頁面上，系統會提示您選取 Azure 租用戶、訂用帳戶與 Log Analytics 工作區。 如果您有多個工作區，請從下拉式清單中選取您想要向 Operations Manager 管理群組註冊的工作區，然後按 [下一步] ****。

   > [!NOTE]
   > Operations Manager 一次只支援一個 Log Analytics 工作區。 連接和已註冊到 Azure 監視器的電腦與以前的工作區將從 Azure 監視器中刪除。
   >
   >
1. 在 [Operations Management Suite 登入精靈：摘要]**** 頁面上，確認您的設定，如果正確無誤，請按一下 [建立]****。
1. 在 [Operations Management Suite 登入精靈：完成]**** 頁面上，按一下 [關閉]****。

### <a name="add-agent-managed-computers"></a>加入代理程式的受控電腦

設定與 Log Analytics 工作區的整合之後，只會建立與 Log Analytics 的連線，並不會從向管理群組回報的代理程式收集任何資料。 在配置哪個特定代理管理的電腦為 Azure 監視器收集日誌資料之前，才會發生這種情況。 您可以個別選取電腦物件，也可以選取包含 Windows 電腦物件的群組。 您無法選取包含另一個類別之執行個體 (例如邏輯磁碟或 SQL 資料庫) 的群組。

1. 開啟 Operations Manager 主控台，然後選取 [管理]**** 工作區。
1. 展開 Operations Management Suite 節點，然後按一下 [連接] ****。
1. 按一下窗格右側之 [執行] 標題下方的 [加入電腦/群組] **** 連結。
1. 在 [電腦搜尋]**** 對話方塊中，您可以搜尋 Operations Manager 監視的電腦或群組。 選擇要將操作管理器管理伺服器（包括操作管理器管理伺服器）的電腦或組添加到 Azure 監視器，然後按一下"**添加**"，然後按一下"**確定**"。

在 Operations 主控台的 [管理] **** 工作區中，您可以檢視電腦和群組，這些電腦和群組設定成收集來自 Operations Management Suite 下方之 [受控電腦] 節點的資料。 您可以視需要在這裡新增或移除電腦和群組。

### <a name="configure-proxy-settings-in-the-operations-console"></a>在 Operations 主控台中設定 Proxy 設定

如果內部代理伺服器位於管理組和 Azure 監視器之間，則執行以下步驟。 這些設置從管理組集中管理，並分發到包含在作用域中以收集 Azure 監視器的日誌資料的代理託管系統。  特定解決方案略過管理伺服器並將資料直接傳送給服務時，這十分有幫助。

1. 開啟 Operations Manager 主控台，然後選取 [管理]**** 工作區。
1. 展開 Operations Management Suite，然後按一下 [連接] ****。
1. 在 [OMS 連線] 檢視中，按一下 [設定 Proxy 伺服器] ****。
1. 在 [Operations Management Suite 精靈：Proxy 伺服器]**** 頁面上，選取 [使用 Proxy 伺服器來存取 Operations Management Suite]****，然後輸入具有連接埠號碼的 URL (例如， http://corpproxy:80 )，然後按一下 [完成]****。

如果代理伺服器需要身份驗證，請執行以下步驟來配置需要傳播到管理組中向 Azure 監視器報告的託管電腦的憑據和設置。

1. 開啟 Operations Manager 主控台，然後選取 [管理]**** 工作區。
1. 在 [RunAs 設定]**** 下，選取 [設定檔]****。
1. 開啟 [ **System Center Advisor 執行身份設定檔 Proxy** ] 設定檔。
1. 在 [執行身分設定檔精靈] 中，按一下 [加入] 使用執行身分帳戶。 您可以建立[執行身分帳戶](https://technet.microsoft.com/library/hh321655.aspx)，或使用現有的帳戶。 此帳戶必須有足夠的權限，才能通過 Proxy 伺服器。
1. 若要設定帳戶來管理，請選擇 [選取的類別、群組或物件]****，按一下 [選取…]**** 然後按一下 [群組…]**** 開啟 [群組搜尋]**** 方塊。
1. 搜尋，然後選取 [Microsoft System Center Advisor 監控伺服器群組] ****。 選取群組之後，按一下 [確定]**** 關閉 [群組搜尋]**** 方塊。
1. 按一下 [確定]**** 以關閉 [新增執行身分帳戶]**** 方塊。
1. 按一下 [儲存] **** 完成精靈並儲存變更。

創建連接並配置哪些代理將收集和向 Azure 監視器報告日誌資料後，管理組中將應用以下配置，不一定按循序執行：

* 建立執行身分帳戶 **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** 。 它是與執行身分設定檔 **Microsoft System Center Advisor Run As Profile Blob** 相關聯，而且將目標設為兩個類別：[收集伺服器]**** 和 [Operations Manager 管理群組]****。
* 會建立兩個連接器。  第一個名稱為**Microsoft.SystemCenter.Advisor.DataConnector，** 並自動設定了訂閱，該訂閱將管理組中所有類的實例生成的所有警報轉發到 Azure 監視器。 第二個連接器是**Advisor 連接器**，它負責與 Azure 監視器通信並共用資料。
* 您在管理群組中已選擇來收集資料的代理程式和群組，將會新增至 [Microsoft System Center Advisor 監控伺服器群組]****。

## <a name="management-pack-updates"></a>管理組件更新

配置完成後，操作管理器管理組將建立與 Azure 監視器的連接。 針對您已啟用且與 Operations Manager 整合的解決方案，管理伺服器會與 Web 服務同步處理，並以管理組件的形式接收更新的組態資訊。 Operations Manager 會檢查這些管理組件的更新，如果有更新，就會自動下載並匯入。 有兩個規則特別可控制這個行為︰

* **微軟.SystemCenter.Advisor.MPUpdate** - 更新基本 Azure 監視器管理包。 預設會每 12 小時執行一次。
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - 更新工作區中所啟用的解決方案管理組件。 預設會每五 (5) 分鐘執行一次。

可以重寫這兩個規則，以防止通過禁用它們自動下載，或者修改管理伺服器與 Azure 監視器同步的頻率，以確定新的管理包是否可用，並且是否應下載。 遵循[如何覆寫規則或監視器](https://technet.microsoft.com/library/hh212869.aspx)步驟，使用值 (秒) 修改 [頻率]**** 參數來變更同步處理排程，或修改 [已啟用]**** 參數來停用規則。 將目標設為覆寫 [Operations Manager 管理群組] 類別的所有物件。

若要繼續遵循現有的變更控制程序來控制生產管理群組中的管理組件發行版本，則可以停用規則，並在允許更新時於特定期間啟用它們。 如果您的環境中有開發或 QA 管理群組，而且該管理群組連接到網際網路，則可以設定該管理群組與 Log Analytics 工作區，以支援此案例。 這允許您在將 Azure 監視器管理包發佈到生產管理組中之前查看和評估它們反覆運算版本。

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>將 Operations Manager 群組切換到新的 Log Analytics 工作區

1. 登錄到 中的[https://portal.azure.com](https://portal.azure.com)Azure 門戶。
1. 在 Azure 入口網站中，按一下左下角的 [更多服務]****。 在資源清單中，鍵入**日誌分析**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics]****，然後建立工作區。  
1. 使用身為 Operations Manager 系統管理員角色成員的帳戶開啟 Operations Manager 主控台，然後選取 [管理] **** 工作區。
1. 展開 Log Analytics，然後選取 [連線]****。
1. 選取窗格中間的 [重新設定 Operation Management Suite] **** 連結。
1. 遵循 [Log Analytics 上架精靈]****，然後輸入與新的 Log Analytics 工作區相關聯之系統管理員帳戶的電子郵件地址或電話號碼和密碼。

   > [!NOTE]
   > [Operations Management Suite 登入精靈：選取工作區]**** 頁面會顯示使用中的現有工作區。
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>驗證操作管理器與 Azure 監視器的集成

有幾種不同的方法可以驗證 Azure 監視器到操作管理器集成是否成功。

### <a name="to-confirm-integration-from-the-azure-portal"></a>從 Azure 入口網站確認整合

1. 在 Azure 入口網站中，按一下左下角的 [更多服務]****。 在資源清單中，鍵入**日誌分析**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。
1. 在 Log Analytics 工作區清單中，選取適用的工作區。  
1. 選取 [進階設定]****，選取 [連接的來源]**** 然後選取 [系統中心]****。
1. 在 [System Center Operations Manager] 區段下方的表格中，您應該會看到管理群組名稱，還會列出上次收到資料時的代理程式數目和狀態。

   ![oms-settings-connectedsources](./media/om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>從 Operations 主控台確認整合

1. 開啟 Operations Manager 主控台，然後選取 [管理]**** 工作區。
1. 選取 [管理組件]****，並在 [尋找:]**** 文字方塊中輸入 **Advisor** 或 **Intelligence**。
1. 根據您已啟用的解決方案，您會看到搜尋結果中列出對應的管理組件。  例如，如果您已啟用警示管理解決方案，則 [Microsoft System Center Advisor 警示管理] 管理組件會在清單中。
1. 從 [監視]**** 檢視中，瀏覽至 [Operations Management Suite\健全狀況狀態]**** 檢視。  在 [管理伺服器狀態]**** 窗格下選取管理伺服器，然後在 [詳細資料檢視]**** 窗格中，確認 [驗證服務 URI]**** 屬性的值符合 Log Analytics 工作區識別碼。

   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-azure-monitor"></a>刪除與 Azure 監視器的集成

當您不再需要整合 Operations Manager 管理群組和 Log Analytics 工作區時，需要執行幾個步驟，才能適當移除管理群組中的連接和組態。 以下過程通過刪除管理組的引用、刪除 Azure 監視器連接器以及刪除支援與服務集成的管理包來更新日誌分析工作區。  

不能輕鬆從管理組中刪除已啟用的解決方案的管理包，這些解決方案與操作管理器集成，以及支援與 Azure 監視器集成所需的管理包。 這是因為某些 Azure 監視器管理包依賴于其他相關管理包。 若要刪除與其他管理組件相依的管理組件，請從 TechNet 指令碼中心下載[移除具有相依性的管理組件](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) (英文) 指令碼。  

1. 使用身為 Operations Manager 系統管理員角色成員的帳戶開啟 Operations Manager 命令殼層。

    > [!WARNING]
    > 繼續之前，請確認您的任何自訂管理組件名稱中沒有 Advisor 或 IntelligencePack 這個字，否則下列步驟會從管理群組中刪除它們。
    >

1. 從命令殼層提示字元中，輸入 `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. 接著輸入 `Get-SCOMManagementPack -name "*IntelligencePack*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. 若要移除與其他 System Center Advisor 管理組件具有相依性的任何其餘管理組件，請使用您稍早從 TechNet 指令碼中心下載的 *RecursiveRemove.ps1* 指令碼。  

    > [!NOTE]
    > 使用 PowerShell 移除 Advisor 管理組件的步驟將不會自動刪除 Microsoft System Center Advisor 或 Microsoft System Center Advisor 內部管理組件。  不要嘗試刪除它們。  
    >  

1. 使用身為 Operations Manager 系統管理員角色成員的帳戶開啟 Operations Manager Operations 主控台。
1. 在 [管理]**** 下，選取 [管理組件]**** 節點，然後在 [尋找:]**** 方塊中輸入 **Advisor**，並確認下列管理組件仍匯入到管理群組中︰

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. 在 Azure 入口網站中，按一下 [設定]**** 圖格。
1. 選擇**連接源**。
1. 在 [System Center Operations Manager] 區段下方的表格中，您應該會看到想要從工作區移除的管理群組名稱。 在 [最後一筆資料]**** 資料行之下，按一下 [移除]****。  

    > [!NOTE]
    > 在偵測到已連線的管理群組有 14 天沒有任何活動之後，[移除]**** 連結才能使用。  
    >

1. 將出現視窗，要求您確認想要繼續移除。  按一下 [是]**** 繼續進行。

若要刪除兩個連接器 - Microsoft.SystemCenter.Advisor.DataConnector 和 Advisor 連接器，請將以下 PowerShell 指令碼儲存至您的電腦，並使用下列範例來執行：

```
    .\OM2012_DeleteConnectors.ps1 "Advisor Connector" <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 "Microsoft.SystemCenter.Advisor.DataConnector" <ManagementServerName>
```

> [!NOTE]
> 您執行此指令碼的電腦 (如果不是管理伺服器) 應該已安裝 Operations Manager 命令殼層，視您的管理群組版本而定。
>
>

```powershell
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

未來，如果您打算將管理群組重新連接至 Log Analytics 工作區，您必須重新匯入 `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` 管理組件檔案。 視部署在您環境中的 System Center Operations Manager 版本而定，您可以在下列位置中找到此檔案：

* 在 System Center 2016 - Operations Manager 或更新版本的 `\ManagementPacks` 資料夾下方的來源媒體上。
* 從套用到您管理群組的最新更新彙總。 對於操作管理器 2012，源資料夾為`%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups`和 2012 R2，它位於`System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`中。

## <a name="next-steps"></a>後續步驟

要添加功能和收集資料，請參閱[從解決方案庫添加 Azure 監視器解決方案](../../azure-monitor/insights/solutions.md)。
