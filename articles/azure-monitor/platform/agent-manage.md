---
title: 管理 Azure 日誌分析代理
description: 本文介紹了在電腦上部署的日誌分析 Windows 或 Linux 代理的生命週期中通常要執行的不同管理工作。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/14/2019
ms.openlocfilehash: 4d0ceacd37748e9761903d02fd7e052d70b10e15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275096"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>管理和維護適用於 Windows 和 Linux 的 Log Analytics 代理程式

在 Azure 監視器中初始部署日誌分析 Windows 或 Linux 代理後，如果代理在其生命週期中已達到停用階段，則可能需要重新配置代理、升級代理或將其從電腦中刪除。 您可以手動或自動的方式輕鬆地管理這些例行維護工作，後者可以降低操作錯誤和費用。

## <a name="upgrading-agent"></a>升級代理

Windows 和 Linux 的日誌分析代理可以手動或自動升級到最新版本，具體取決於 VM 運行的部署方案和環境。 以下方法可用於升級代理。

| 環境 | 安裝方式 | 升級方法 |
|--------|----------|-------------|
| Azure VM | 用於 Windows/Linux 的日誌分析代理 VM 擴展 | 預設情況下，代理會自動升級，除非您將 Azure 資源管理器範本配置為通過將屬性*自動升級 MinorVersion*設置為**false**來退出宣告。 |
| 自訂 Azure VM 映射 | 手動安裝適用于 Windows/Linux 的日誌分析代理 | 需要從運行 Windows 安裝程式包或 Linux 自提取和可安裝的 shell 腳本包的命令列執行將 VM 更新到最新版本的代理。|
| 非 Azure VM | 手動安裝適用于 Windows/Linux 的日誌分析代理 | 需要從運行 Windows 安裝程式包或 Linux 自提取和可安裝的 shell 腳本包的命令列執行將 VM 更新到最新版本的代理。 |

### <a name="upgrade-windows-agent"></a>升級 Windows 代理 

要將 Windows VM 上的代理更新到未使用日誌分析 VM 擴展安裝的最新版本，請從命令提示符、腳本或其他自動化解決方案運行，或者使用 MMASetup-\<平臺\>.msi 安裝程式嚮導運行。  

您可以通過執行以下步驟，從日誌分析工作區下載最新版本的 Windows 代理。

1. 登錄到 Azure[門戶](https://portal.azure.com)。

2. 在 Azure 門戶中，按一下 **"所有服務**"。 在資源清單中，鍵入**日誌分析**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics 工作區]****。

3. 在日誌分析工作區清單中，選擇工作區。

4. 在日誌分析工作區中，選擇 **"高級設置**"，然後選擇 **"連接的源**"，最後選擇**Windows 伺服器**。

5. 在**Windows 伺服器**頁面中，根據 Windows 作業系統的處理器體系結構選擇要下載的相應**下載 Windows 代理**版本。

>[!NOTE]
>在升級 Windows 的日誌分析代理期間，它不支援配置或重新配置要報告的工作區。 要配置代理，您需要遵循["添加或刪除工作區](#adding-or-removing-a-workspace)"下列出的受支援方法之一。
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>使用設置嚮導進行升級

1. 以具有系統管理權限的帳戶登入電腦。

2. 執行**\<MMASetup-\>平臺 .exe**以啟動設置嚮導。

3. 在"設置嚮導"的第一頁上，按一下 **"下一步**"。

4. 在**Microsoft 監視代理設置對話方塊**中，按一下 **"我同意**接受授權合約"。

5. 在 [Microsoft Monitoring Agent 安裝程式] **** 對話方塊中，按一下 [升級] ****。 狀態頁面會顯示升級進度。

6. 當**Microsoft 監視代理配置成功完成時。** 頁面出現，按一下"**完成**"。

#### <a name="to-upgrade-from-the-command-line"></a>從命令列升級

1. 以具有系統管理權限的帳戶登入電腦。

2. 若要解壓縮代理程式安裝檔案，請從提升權限的命令提示字元執行 `MMASetup-<platform>.exe /c`，它會提示檔案解壓縮的路徑。 或者，您可以傳遞 `MMASetup-<platform>.exe /c /t:<Full Path>` 引數來指定路徑。

3. 執行下列命令，其中 D:\ 是升級記錄檔的位置。

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>升級 Linux 代理 

支援從以前的版本（>1.0.0-47）升級。 使用 `--upgrade` 命令執行安裝，會將代理程式的所有元件升級為最新版本。

運行以下命令以升級代理。

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>新增或移除工作區

### <a name="windows-agent"></a>Windows 代理程式
當您不僅要重新配置 Windows 代理以報告到其他工作區或從其配置中刪除工作區時，以及希望將代理配置為向多個工作區報告時，此部分中的步驟是必需的（通常稱為多宿主）。 將 Windows 代理配置為向多個工作區報告只能在代理的初始設置和使用下面描述的方法後執行。    

#### <a name="update-settings-from-control-panel"></a>從控制台更新設定

1. 以具有系統管理權限的帳戶登入電腦。

2. 開啟 [控制台]****。

3. 選取 [Microsoft Monitoring Agent]****，然後按一下 [Azure Log Analytics]**** 索引標籤。

4. 若要移除工作區，請選取工作區，然後按一下 [移除]****。 針對您希望代理程式停止向其報告的任何其他工作區，重複此步驟。

5. 若要新增工作區，請按一下 [新增]****，然後在 [新增 Log Analytics 工作區]**** 對話方塊中，貼上工作區識別碼和工作區索引鍵 (主索引鍵)。 如果電腦應該向 Azure Government 雲端中的 Log Analytics 工作區報告，請從 Azure 雲端下拉式清單中選取 [Azure US Government]。

6. 按一下 [確定]**** 以儲存您的變更。

#### <a name="remove-a-workspace-using-powershell"></a>使用 PowerShell 移除工作區

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>使用 PowerShell 在 Azure Commercial 中新增工作區

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>使用 PowerShell 在 Azure US Government 中新增工作區

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>如果您先前使用過命令列或指令碼來安裝或設定代理程式，`EnableAzureOperationalInsights` 會換成 `AddCloudWorkspace` 和 `RemoveCloudWorkspace`。
>

### <a name="linux-agent"></a>Linux 代理程式
以下步驟演示如何重新配置 Linux 代理（如果您決定將其註冊到其他工作區或從其配置中刪除工作區）。

1. 若要確認已向工作區註冊該代理程式，請執行下列命令：

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    它應該會傳回類似下列範例的狀態：

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    請務必確認狀態也會顯示代理程式正在執行，否則，重新設定代理程式的後續步驟將無法順利完成。

2. 如果已向工作區註冊，執行下列命令來移除已註冊的工作區。 否則，如果尚未註冊，請繼續進行下一個步驟。

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. 若要向不同工作區註冊，請執行下列命令：

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. 要驗證所做的更改生效，請運行以下命令：

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    它應該會傳回類似下列範例的狀態：

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

代理程式服務不需重新啟動，就能使變更生效。

## <a name="update-proxy-settings"></a>更新 Proxy 設定
若要在部署之後將代理程式設定為透過 Proxy 伺服器或 [Log Analytics 閘道](gateway.md)來與服務通訊，可使用下列其中一種方法來完成這項工作。

### <a name="windows-agent"></a>Windows 代理程式

#### <a name="update-settings-using-control-panel"></a>使用控制台更新設定

1. 以具有系統管理權限的帳戶登入電腦。

2. 開啟 [控制台]****。

3. 選擇**Microsoft 監視代理**，然後按一下 **"代理設置"** 選項卡。

4. 按一下 [使用 Proxy 伺服器]****，並提供 Proxy 伺服器或閘道的 URL 和連接埠號碼。 如果您的 Proxy 伺服器或 Log Analytics 閘道要求驗證，請輸入要驗證的使用者名稱和密碼，然後按一下 [確定]****。

#### <a name="update-settings-using-powershell"></a>使用 PowerShell 更新設定

複製下列 PowerShell 程式碼範例，以您環境的特定資訊加以更新，並使用 PS1 副檔名進行儲存。 在直接連接到 Azure 監視器中的日誌分析工作區的每台電腦上運行腳本。

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

### <a name="linux-agent"></a>Linux 代理程式
如果您的 Linux 電腦需要透過 Proxy 伺服器或 Log Analytics 閘道來通訊，請執行下列步驟。 Proxy 組態值的語法如下：`[protocol://][user:password@]proxyhost[:port]`。 *proxyhost* 屬性接受 Proxy 伺服器的完整網域名稱或 IP 位址。

1. 藉由執行下列命令來編輯檔案 `/etc/opt/microsoft/omsagent/proxy.conf`，並將值變更為您的特定設定。

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
    ```

2. 執行下列命令來重新啟動代理程式：

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ```

## <a name="uninstall-agent"></a>解除安裝代理程式
使用以下過程之一使用命令列或設置嚮導卸載 Windows 或 Linux 代理。

### <a name="windows-agent"></a>Windows 代理程式

#### <a name="uninstall-from-control-panel"></a>從控制台解除安裝
1. 以具有系統管理權限的帳戶登入電腦。

2. 在 **[控制台]** 中，按一下 **[程式和功能]**。

3. 在 [程式和功能]**** 中，依序按一下 [Microsoft Monitoring Agent]****、[解除安裝]**** 和 [是]****。

>[!NOTE]
>按兩下 **MMASetup-\<platform\>.exe** 也可以執行代理程式安裝精靈，您可從 Azure 入口網站中的工作區下載此檔案。

#### <a name="uninstall-from-the-command-line"></a>從命令列解除安裝
為代理程式下載的檔案是使用 IExpress 建立的獨立安裝套件。 套件中包含代理程式的安裝程式和支援檔案，這些檔案需要先解壓縮，才能使用命令列正確解除安裝，如下列範例所示。

1. 以具有系統管理權限的帳戶登入電腦。

2. 若要解壓縮代理程式安裝檔案，請從提升權限的命令提示字元執行 `extract MMASetup-<platform>.exe`，它會提示檔案解壓縮的路徑。 或者，您可以傳遞 `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>` 引數來指定路徑。 如需 IExpress 支援的命令列參數詳細資訊，請參閱 [IExpress 的命令列參數](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages)，然後更新範例以符合您的需求。

3. 在出現提示時輸入 `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`。

### <a name="linux-agent"></a>Linux 代理程式
若要移除代理程式，請在 Linux 電腦上執行下列命令。 *--purge* 引數可將代理程式及其組態完全移除。

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>將代理程式設定為向 Operations Manager 管理群組報告

### <a name="windows-agent"></a>Windows 代理程式
執行下列步驟，將適用於 Windows 的 Log Analytics 代理程式設定為向 System Center Operations Manager 管理群組報告。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. 以具有系統管理權限的帳戶登入電腦。

2. 開啟 [控制台]****。

3. 按一下 [Microsoft Monitoring Agent]****，然後按一下 [Operations Manager]**** 索引標籤。

4. 如果 Operations Manager 伺服器已與 Active Directory 整合，請按一下 [自動更新來自 AD DS 的管理群組指派] ****。

5. 按一下 [新增]**** 以開啟 [新增管理群組]**** 對話方塊。

6. 在 [管理群組名稱]**** 欄位中，輸入您的管理群組名稱。

7. 在 [主要管理伺服器]**** 欄位中，輸入主要管理伺服器的電腦名稱。

8. 在 [管理伺服器連接埠] **** 欄位中，輸入 TCP 連接埠號碼。

9. 在 [代理程式動作帳戶] **** 頁面下，選擇本機系統帳戶或本機網域帳戶。

10. 按一下 [確定]**** 關閉 [新增管理群組]**** 對話方塊，然後按一下 [確定]**** 關閉 [Microsoft 監視代理程式內容]**** 對話方塊。

### <a name="linux-agent"></a>Linux 代理程式
執行下列步驟，將適用於 Linux 的 Log Analytics 代理程式設定為向 System Center Operations Manager 管理群組報告。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. 編輯 `/etc/opt/omi/conf/omiserver.conf`

2. 確認以 `httpsport=` 開頭的這一行會定義連接埠 1270。 例如：`httpsport=1270`

3. 重新啟動 OMI 伺服器：`sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>後續步驟

- 如果在安裝或管理 Linux 代理時遇到問題[，請查看對 Linux 代理進行故障排除](agent-linux-troubleshoot.md)。

- 如果在安裝或管理 Windows 代理時遇到問題[，請查看對 Windows 代理進行故障排除](agent-windows-troubleshoot.md)。
