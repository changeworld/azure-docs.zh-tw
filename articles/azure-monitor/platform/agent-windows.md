---
title: 在 Windows 電腦上安裝 Log Analytics 代理程式
description: 本文描述如何使用適用於 Microsoft 的 Log Analytics 代理程式，將其他雲端中託管的 Windows 電腦或內部部署電腦連線至 Azure 監視器。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/03/2020
ms.openlocfilehash: d283c2b2cdbbeb3ef4bc4e25f4288dfd95158552
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89003366"
---
# <a name="install-log-analytics-agent-on-windows-computers"></a>在 Windows 電腦上安裝 Log Analytics 代理程式
本文將詳細說明如何使用下列方法，在 Windows 電腦上安裝 Log Analytics 代理程式：

* 使用 [安裝精靈](#install-agent-using-setup-wizard) 或 [命令列](#install-agent-using-command-line)手動安裝。
* [Azure 自動化 Desired State Configuration (DSC) ](#install-agent-using-dsc-in-azure-automation)。 

>[!IMPORTANT]
> 本文所述的安裝方法通常用於內部部署或其他雲端中的虛擬機器。 請參閱 [安裝選項](log-analytics-agent.md#installation-options) ，以取得可用於 Azure 虛擬機器的更有效率選項。

> [!NOTE]
> 如果需要將代理程式設定為向多個工作區報告，則無法在初始安裝期間執行這項作業，只能在稍後透過更新 [控制台] 或 PowerShell 中的設定來執行 (如[新增或移除工作區](agent-manage.md#adding-or-removing-a-workspace)中所述)。  

## <a name="supported-operating-systems"></a>支援的作業系統

如需 Log Analytics 代理程式所支援的 Windows 版本清單，請參閱 [Azure 監視器代理](agents-overview.md#supported-operating-systems) 程式的總覽。

### <a name="sha-2-code-signing-support-requirement"></a>SHA-2 程式碼簽署支援需求 
Windows 代理程式將在 2020 年 8 月 17 日開始獨佔使用 SHA-2 簽署。 這項變更會影響在舊版 OS 上以任何 Azure 服務 (Azure 監視器、Azure 自動化、Azure 更新管理、Azure 變更追蹤、Azure 資訊安全中心、Azure Sentinel、Windows Defender ATP) 的形式來使用 Log Analytics 代理程式的客戶。 除非客戶在舊版 OS (Windows 7、Windows Server 2008 R2 和 Windows Server 2008) 上執行代理程式，否則此變更不需要其採取任何動作。 在舊版 OS 上執行的客戶必須於 2020 年 8 月 17 日之前在其電腦上採取下列動作，否則其代理程式將會停止將資料傳送到其 Log Analytics 工作區：

1. 安裝適用於 OS 的最新 Service Pack。 所需的 Service Pack 版本如下：
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. 安裝適用於 OS 的 SHA-2 簽署 Windows 更新，如 [Windows 和 WSUS 的 2019 SHA-2 程式碼簽署支援需求](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)所述
3. 更新至最新版的 Windows 代理程式 (10.20.18029 版)。
4. 建議將代理程式設定為[使用 TLS 1.2](agent-windows.md#configure-agent-to-use-tls-12)。 

## <a name="network-requirements"></a>網路需求
如需 Windows 代理程式的網路需求，請參閱 [Log Analytics 代理程式總覽](log-analytics-agent.md#network-requirements) 。


   
## <a name="configure-agent-to-use-tls-12"></a>設定代理程式以使用 TLS 1.2
[TLS 1.2](/windows-server/security/tls/tls-registry-settings#tls-12) 通訊協定可確保傳輸中的資料安全性，以進行 Windows 代理程式與 Log Analytics 服務之間的通訊。 如果您要在 [未啟用 TLS 1.2 的作業系統](data-security.md#sending-data-securely-using-tls-12)上安裝，則應該使用下列步驟來設定 tls 1.2。

1. 找出下列登錄子機碼：**HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. 在 **Protocols** 下針對 TLS 1.2 建立子機碼：**HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2**
3. 在您先前所建立的 TLS 1.2 通訊協定版本子機碼底下，建立 **Client** 子機碼。 例如，**HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**。
4. 在 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client** 下建立下列 DWORD 值：

    * **Enabled** [Value = 1]
    * **DisabledByDefault** [Value = 0]  

設定 .NET Framework 4.6 或更新版本以支援安全密碼編譯，因為它預設為停用。 [強式密碼編譯](/dotnet/framework/network-programming/tls#schusestrongcrypto)會使用如 TLS 1.2 等更加安全的網路通訊協定，並封鎖不安全的通訊協定。 

1. 找出下列登錄子機碼：**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319**。  
2. 在此子機碼底下建立 DWORD 值 **SchUseStrongCrypto**，並使其值為 **1**。  
3. 找出下列登錄子機碼：**HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319**。  
4. 在此子機碼底下建立 DWORD 值 **SchUseStrongCrypto**，並使其值為 **1**。 
5. 重新啟動系統來使設定生效。 

## <a name="install-agent-using-setup-wizard"></a>使用安裝 wizard 安裝代理程式
下列步驟會在電腦上使用代理程式的安裝精靈來安裝並設定 Azure 和 Azure Government 雲端中 Log Analytics 代理程式。 如果您想要了解如何設定代理程式，以同時回報至 System Center Operations Manager 管理群組，請參閱[使用代理程式安裝精靈部署 Operations Manager 代理程式](/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard)。

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

完成時，[Microsoft 監視代理程式] 會出現在 [控制台] 中。 若要確認它有向 Log Analytics 報告，請檢閱[確認代理程式能夠連線到 Log Analytics](#verify-agent-connectivity-to-azure-monitor)。 

## <a name="install-agent-using-command-line"></a>使用命令列安裝代理程式
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
    >*OPINSIGHTS_WORKSPACE_ID* 和 *OPINSIGHTS_WORKSPACE_KEY* 參數的字串值必須以雙引號封裝，以指示 Windows Installer 將其解譯為套件的有效選項。 

## <a name="install-agent-using-dsc-in-azure-automation"></a>使用 DSC 在 Azure 自動化中安裝代理程式

您可以用下列指令碼範例，使用 Azure Automation DSC 安裝代理程式。   如果您沒有自動化帳戶，請參閱[開始使用 Azure 自動化](../../automation/index.yml)，以了解使用 Automation DSC 所需的自動化帳戶的建立需求和步驟。  如果您不熟悉 Automation DSC，請檢閱[開始使用 Automation DSC](../../automation/automation-dsc-getting-started.md)。

以下範例會安裝 64 位元代理程式，可由 `URI` 值識別。 您也可以取代 URI 值改為使用 32 位元版本。 這兩個版本的 URI 分別是︰

- Windows 64 位元代理程式： https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32 位元代理程式： https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>此程序和指令碼範例不支援將已部署到 Windows 電腦的代理程式升級。

代理程式套件的 32 位元和 64 位元版本具有不同的產品代碼，新發行的版本也都會有唯一代碼值。  產品代碼是唯一識別碼，為應用程式或產品的主要識別，在 Windows 安裝程式中以 **ProductCode** 屬性表示。  **MMAgent.ps1** 指令碼中的 `ProductId` 值必須符合 32 位元或 64 位元代理程式安裝程式套件的產品代碼。

若要直接從代理程式安裝套件擷取產品代碼，您可以使用[適用於 Windows Installer 開發人員的 Windows SDK 元件](/windows/win32/msi/platform-sdk-components-for-windows-installer-developers) (Windows 軟體開發套件的元件之一) 中的 Orca.exe，或依循 Microsoft Valuable Professional (MVP) 撰寫的[範例指令碼](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/)使用 PowerShell。  針對任一方法，您都必須先從 MMASetup 安裝套件擷取 **MOMagent.msi** 檔案。  這在前面[使用命令列安裝代理程式](#install-agent-using-command-line)一節底下的第一個步驟中有所敘述。  

1. 將 xPSDesiredStateConfiguration DSC 模組從 [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) 匯入 Azure 自動化。  
2.    建立 Azure 自動化的 *OPSINSIGHTS_WS_ID* 和 *OPSINSIGHTS_WS_KEY* 變數資產。 將 OPSINSIGHTS_WS_ID 設定為您的 Log Analytics 工作區識別碼，將 OPSINSIGHTS_WS_KEY 設定為您的工作區主索引鍵。
3.    複製指令碼，並將其儲存為 MMAgent.ps1。

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
5. [將 MMAgent.ps1 設定指令碼匯入](../../automation/automation-dsc-getting-started.md#import-a-configuration-into-azure-automation)您的自動化帳戶。 
6. 為設定[指派 Windows 電腦或節點](../../automation/automation-dsc-getting-started.md#enable-an-azure-resource-manager-vm-for-management-with-state-configuration)。 在 15 分鐘內，節點會檢查其設定，然後系統會將代理程式推送至節點。

## <a name="verify-agent-connectivity-to-azure-monitor"></a>確認代理程式與 Azure 監視器的連線能力

代理程式的安裝一旦完成，可以兩種方式確認其連線成功和報告。  

在電腦的 [控制台] 中，找到 [Microsoft Monitoring Agent]。  選取它，然後代理程式應該會在 [Azure Log Analytics] 索引標籤上顯示訊息，指出：[Microsoft Monitoring Agent 已成功與 Microsoft Operations Management Suite 服務連線。]<br><br> ![MMA 對 Log Analytics 的連線狀態](media/agent-windows/log-analytics-mma-laworkspace-status.png)

您也可以在 Azure 入口網站中執行簡單的記錄查詢。  

1. 在 Azure 入口網站中，搜尋並選取 [監視器]。
1. 在功能表中選取 [記錄]。
1. 在 [記錄] 窗格的查詢欄位中鍵入：  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

在傳回的搜尋結果中，您應該會看到電腦的活動訊號記錄，指出它已連線並且向服務報告。

## <a name="cache-information"></a>快取資訊

Log Analytics 代理程式中的資料會快取到本機電腦上的 *C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service 狀態* ，然後再傳送至 Azure 監視器。 代理程式每隔20秒會嘗試上傳一次。 如果失敗，則會以指數方式增加持續時間，直到成功為止。 它會在第二次嘗試之前等候30秒，在接下來的60秒之前、120秒，8.5 依此類推，直到再次成功連接為止。 這段等候時間會稍微隨機化，以避免所有代理程式同時嘗試連接。 到達最大緩衝區時，會捨棄最舊的資料。

預設的快取大小為 50 MB，但最少可設定為 5 MB，最大值為 1.5 GB。 它會儲存在登錄機碼 *HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Persistence Cache Maximum*中。 值代表頁面數目，每頁 8 KB。


## <a name="next-steps"></a>後續步驟

- 檢閱[管理及維護適用於 Windows 和 Linux 的 Log Analytics 代理程式](agent-manage.md)，以了解如何重新設定、升級或移除虛擬機器中的代理程式。

- 如果在安裝或管理代理程式時遇到問題，請檢閱[針對 Windows 代理程式進行疑難排解](agent-windows-troubleshoot.md)。
