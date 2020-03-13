---
title: 將 Windows 電腦連接到 Azure 監視器 |Microsoft Docs
description: 本文說明如何將裝載于其他雲端或內部部署環境的 Windows 電腦，與適用于 Windows 的 Log Analytics 代理程式連線 Azure 監視器。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: 21efb16cf519d4bcad520af1c7d8818f36a77218
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275031"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>將 Windows 電腦連線至 Azure 監視器

若要使用 Azure 監視器來監視及管理本機資料中心或其他雲端環境中的虛擬機器或實體電腦，您需要部署 Log Analytics 代理程式（也稱為 Microsoft Monitoring Agent （MMA）），並將它設定為向一或多個 Log Analytics 工作區報告。 此代理程式也支援 Azure 自動化的混合式 Runbook 背景工作角色。  

在受監視的 Windows 電腦上，會將此代理程式列為 Microsoft Monitoring Agent 服務。 Microsoft Monitoring Agent 服務會從記錄檔和 Windows 事件記錄檔、效能資料及其他遙測收集事件。 即使代理程式無法與報告的 Azure 監視器通訊，代理程式仍會繼續執行，並將收集到的資料排入受監視電腦的磁片上。 當連線恢復時，Microsoft Monitoring Agent 服務會將收集的資料傳送給服務。

可以使用下列其中一種方法安裝代理程式。 大部分安裝會使用這些方法的組合，視需要為不同的電腦集合進行安裝。  使用每種方法的詳細資料會在本文稍後提供。

* 手動安裝。 在電腦上執行手動安裝可使用安裝精靈、從命令列、或使用現有的軟體發佈工具部署。
* Azure 自動化期望狀態設定 (DSC)。 針對已在環境中部署的 Windows 電腦，利用指令碼使用 Azure 自動化中的 DSC。  
* PowerShell 指令碼。
* 針對 Azure Stack 中執行內部部署 Windows 的虛擬機器，使用 Resource Manager 範本。 

>[!NOTE]
>Azure 資訊安全中心（ASC）取決於 Microsoft Monitoring Agent （也稱為 Log Analytics Windows 代理程式），並會安裝並設定它，以在其部署過程中向 Log Analytics 工作區報告。 ASC 包含自動布建選項，可讓您在訂用帳戶中的所有 Vm 上自動安裝 Log Analytics Windows 代理程式，並將其設定為向特定工作區報告。 如需此選項的詳細資訊，請參閱[啟用 Log Analytics 代理程式的自動](../../security-center/security-center-enable-data-collection.md#auto-provision-mma)布建。
>

如果您需要將代理程式設定為向多個工作區報告，這在初始設定期間無法執行，只有在更新 [控制台] 或 PowerShell 中的設定（如[新增或移除工作區](agent-manage.md#adding-or-removing-a-workspace)中所述）之後。  

若要了解支援的組態，請檢閱[支援的 Windows 作業系統](log-analytics-agent.md#supported-windows-operating-systems)和[網路防火牆組態](log-analytics-agent.md#network-firewall-requirements)。

## <a name="obtain-workspace-id-and-key"></a>取得工作區識別碼和金鑰
安裝適用於 Windows 的 Log Analytics 代理程式之前，您需要 Log Analytics 工作區的工作區識別碼和金鑰。  在安裝過程中，從每個安裝方法都需要這項資訊，才能正確設定代理程式，並確保它可以與 Azure 商業和美國政府雲端中的 Azure 監視器成功通訊。 

1. 在 Azure 入口網站中，搜尋並選取  **Log Analytics 工作區**。
2. 在您的 Log Analytics 工作區清單中，選取您要設定讓代理程式向哪個工作區報告。
3. 選取 [進階設定]。<br><br> ![Log Analytics 進階設定](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. 選取 [連接的來源]，然後選取 [Windows 伺服器]。   
5. 將 [工作區識別碼] 和 [主要金鑰] 複製並貼到您最愛的編輯器。    
   
## <a name="configure-agent-to-use-tls-12"></a>設定代理程式以使用 TLS 1.2
若要設定將 [TLS 1.2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) 通訊協定用於 Windows 代理程式和 Log Analytics 服務之間的通訊，您可以遵循下列步驟，以在將代理程式安裝於虛擬機器之前或之後啟用此功能。

>[!NOTE]
>如果您要將執行 Windows Server 2008 SP2 x64 的 VM 設定為使用 TLS 1.2，您必須先安裝下列[sha-1 程式碼簽署支援更新](https://support.microsoft.com/help/4474419/sha-2-code-signing-support-update)，然後再執行下列步驟。 
>

1. 找出下列登錄子機碼：**HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. 在 TLS 1.2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2**的**通訊協定**底下建立子機碼
3. 在您先前所建立的 TLS 1.2 通訊協定版本子機碼底下，建立 **Client** 子機碼。 例如， **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ Client**。
4. 在**HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ 用戶端**底下建立下列 DWORD 值：

    * **Enabled** [Value = 1]
    * **DisabledByDefault** [Value = 0]  

設定 .NET Framework 4.6 或更新版本以支援安全密碼編譯，因為它預設為停用。 [強式密碼編譯](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto)會使用如 TLS 1.2 等更加安全的網路通訊協定，並封鎖不安全的通訊協定。 

1. 找出下列登錄子機碼：**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319**。  
2. 在此子機碼底下建立 DWORD 值 **SchUseStrongCrypto**，並使其值為 **1**。  
3. 找出下列登錄子機碼：**HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319**。  
4. 在此子機碼底下建立 DWORD 值 **SchUseStrongCrypto**，並使其值為 **1**。 
5. 重新啟動系統來使設定生效。 

## <a name="install-the-agent-using-setup-wizard"></a>使用安裝精靈安裝代理程式
下列步驟會在您的電腦上，使用安裝程式，在 Azure 中安裝和設定 Log Analytics 代理程式，並為您的代理程式 Azure Government 雲端。 如果您想要了解如何設定代理程式，以同時回報至 System Center Operations Manager 管理群組，請參閱[使用代理程式安裝精靈部署 Operations Manager 代理程式](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard)。

1. 在 Log Analyics 工作區中，從您稍早瀏覽的 [Windows 伺服器] 頁面，根據 Windows 作業系統的處理器架構，選取適當的 [下載 Windows 代理程式] 版本來下載。   
2. 執行安裝程式以在您的電腦上安裝代理程式。
2. 在 [歡迎] 頁面中按 [下一步]。
3. 閱讀 [授權條款] 頁面上的授權，然後按一下 [我接受]。
4. 在 [目的資料夾] 頁面上，變更或保留預設的安裝資料夾，然後按 [下一步]。
5. 在 [代理程式安裝選項] 頁面上，選擇將代理程式連線到 Azure Log Analytics，然後按 [下一步]。   
6. 在 [Azure Log Analytics] 頁面上，執行下列操作：
   1. 貼上您先前複製的**工作區識別碼**和**工作區金鑰 (主要金鑰)** 。  如果電腦應該向 Azure Government Cloud 中的 Log Analytics 工作區回報，請從 [Azure 雲端] 下拉式清單中選取 [Azure US Gov]。  
   2. 如果電腦需要透過 Proxy 伺服器與 Log Analytics 服務進行通訊，請按一下 [進階]，然後提供 Proxy 伺服器的 URL 和連接埠號碼。  如果您的 Proxy 伺服器會要求驗證，請輸入要向 Proxy 伺服器進行驗證的使用者名稱和密碼，然後按 [下一步]。  
7. 提供完必要的組態設定之後，按 [下一步]。<br><br> ![貼上工作區識別碼和主索引鍵](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. 在 [安裝準備就緒] 頁面上，檢閱您的選擇，然後按一下 [安裝]。
9. 在 [設定成功完成] 頁面上，按一下 [完成]。

完成時，[Microsoft 監視代理程式] 會出現在 [控制台] 中。 若要確認它有向 Log Analytics 報告，請檢閱[確認代理程式能夠連線到 Log Analytics](#verify-agent-connectivity-to-log-analytics)。 

## <a name="install-the-agent-using-the-command-line"></a>使用命令列安裝代理程式
為代理程式下載的檔案是獨立安裝套件。  套件中包含代理程式的安裝程式和支援檔案，這些檔案需要先解壓縮，才能使用命令列正確安裝，如下列範例所示。    

>[!NOTE]
>如果您想要升級代理程式，您需要使用 Log Analytics 指令碼 API。 如需更多資訊，請參閱[管理和維護適用於 Windows 和 Linux 的 Log Analytics 代理程式](agent-manage.md)。

下表特別列出代理程式安裝支援的特定參數，包括使用 Automation DSC 部署時的參數。

|MMA 專屬選項                   |注意         |
|---------------------------------------|--------------|
| NOAPM=1                               | 選擇性參數。 安裝代理程式但不包括 .NET 應用程式效能監控。|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = 將代理程式設定為向工作區報告                |
|OPINSIGHTS_WORKSPACE_ID                | 要新增之工作區的工作區識別碼 (guid)                    |
|OPINSIGHTS_WORKSPACE_KEY               | 用來向工作區進行初始驗證的工作區金鑰 |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | 指定工作區所在的雲端環境 <br> 0 = Azure 商業雲端 (預設值) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | 要使用的 Proxy URI |
|OPINSIGHTS_PROXY_USERNAME               | 要存取已驗證 Proxy 的使用者名稱 |
|OPINSIGHTS_PROXY_PASSWORD               | 要存取已驗證 Proxy 的密碼 |

1. 若要解壓縮代理程式安裝檔案，請從提升權限的命令提示字元執行 `MMASetup-<platform>.exe /c`，它會提示檔案解壓縮的路徑。  或者，您可以傳遞 `MMASetup-<platform>.exe /c /t:<Full Path>` 引數來指定路徑。  
2. 若要以無訊息方式安裝代理程式，並設定它向 Azure 商業雲端中的工作區報告，在您解壓縮安裝檔案的資料夾中輸入： 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   或者，若要設定代理程式向 Azure 美國政府雲端報告，請輸入： 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >*OPINSIGHTS_WORKSPACE_ID*和*OPINSIGHTS_WORKSPACE_KEY*參數的字串值必須以雙引號封裝，以指示 Windows Installer interprit 為封裝的有效選項。 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>使用 Azure 自動化中的 DSC 安裝代理程式

您可以用下列指令碼範例，使用 Azure Automation DSC 安裝代理程式。   如果您沒有自動化帳戶，請參閱[開始使用 Azure 自動化](/azure/automation/)，以了解使用 Automation DSC 所需的自動化帳戶的建立需求和步驟。  如果您不熟悉 Automation DSC，請檢閱[開始使用 Automation DSC](../../automation/automation-dsc-getting-started.md)。

以下範例會安裝 64 位元代理程式，可由 `URI` 值識別。 您也可以取代 URI 值改為使用 32 位元版本。 這兩個版本的 URI 分別是︰

- Windows 64 位元代理程式： https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32 位元代理程式： https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>此程序和指令碼範例不支援將已部署到 Windows 電腦的代理程式升級。

代理程式套件的 32 位元和 64 位元版本具有不同的產品代碼，新發行的版本也都會有唯一代碼值。  產品代碼是唯一識別碼，為應用程式或產品的主要識別，在 Windows 安裝程式中以 **ProductCode** 屬性表示。  `ProductId`MMAgent.ps1**指令碼中的** 值必須符合 32 位元或 64 位元代理程式安裝程式套件的產品代碼。

若要直接從代理程式安裝套件擷取產品代碼，您可以使用[適用於 Windows Installer 開發人員的 Windows SDK 元件](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) (Windows 軟體開發套件的元件之一) 中的 Orca.exe，或依循 Microsoft Valuable Professional (MVP) 撰寫的[範例指令碼](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/)使用 PowerShell。  針對任一方法，您都必須先從 MMASetup 安裝套件擷取 **MOMagent.msi** 檔案。  這在前面[使用命令列安裝代理程式](#install-the-agent-using-the-command-line)一節底下的第一個步驟中有所敘述。  

1. 將 xPSDesiredStateConfiguration DSC 模組從 [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) 匯入 Azure 自動化。  
2.  建立 Azure 自動化的 *OPSINSIGHTS_WS_ID* 和 *OPSINSIGHTS_WS_KEY* 變數資產。 將 OPSINSIGHTS_WS_ID 設定為您的 Log Analytics 工作區識別碼，將 OPSINSIGHTS_WS_KEY 設定為您的工作區主索引鍵。
3.  複製指令碼，並將其儲存為 MMAgent.ps1。

    ```powershell
    Configuration MMAgent
    {
        $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
        $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
        $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

        Import-DscResource -ModuleName xPSDesiredStateConfiguration
        Import-DscResource -ModuleName PSDesiredStateConfiguration

        Node OMSnode {
            Service OIService
            {
                Name = "HealthService"
                State = "Running"
                DependsOn = "[Package]OI"
            }

            xRemoteFile OIPackage {
                Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
                DestinationPath = $OIPackageLocalPath
            }

            Package OI {
                Ensure = "Present"
                Path  = $OIPackageLocalPath
                Name = "Microsoft Monitoring Agent"
                ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
                Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
                DependsOn = "[xRemoteFile]OIPackage"
            }
        }
    }

    ```

4. 使用稍早建議的方法，將指令碼中的 `ProductId` 值更新為從最新版代理程式安裝套件擷取的產品代碼。 
5. [將 MMAgent.ps1 設定指令碼匯入](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation)您的自動化帳戶。 
5. 為設定[指派 Windows 電腦或節點](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration)。 在 15 分鐘內，節點會檢查其設定，然後系統會將代理程式推送至節點。

## <a name="verify-agent-connectivity-to-log-analytics"></a>確認代理程式能夠連線到 Log Analytics

代理程式的安裝一旦完成，可以兩種方式確認其連線成功和報告。  

在電腦的 [控制台] 中，找到 [Microsoft Monitoring Agent]。  選取它，在 [Azure Log Analytics] 索引標籤上，代理程式應該會顯示下列訊息︰**Microsoft Monitoring Agent 已成功連線到 Microsoft Operations Management Suite 服務。**<br><br> ![MMA 對 Log Analytics 的連線狀態](media/agent-windows/log-analytics-mma-laworkspace-status.png)

您也可以在 Azure 入口網站中執行簡單的記錄查詢。  

1. 在 Azure 入口網站中，搜尋並選取 **監視**。
1. 選取功能表中的 [**記錄**]。
1. 在 [**記錄**檔] 窗格的 [查詢] 欄位中，輸入：  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

在傳回的搜尋結果中，您應該會看到電腦的活動訊號記錄，指出它已連線並且向服務報告。   

## <a name="next-steps"></a>後續步驟

- 請參閱[管理和維護適用于 Windows 和 Linux 的 Log Analytics 代理程式](agent-manage.md)，以瞭解如何從虛擬機器重新設定、升級或移除代理程式。

- 如果您在安裝或管理代理程式時遇到問題，請參閱針對[Windows 代理程式進行疑難排解](agent-windows-troubleshoot.md)。
