---
title: 針對 Windows Azure 來賓代理程式進行疑難排解
description: 疑難排解 Windows Azure 來賓代理程式無法運作的問題
services: virtual-machines-windows
ms.service: virtual-machines-windows
author: dkdiksha
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/15/2020
ms.author: genli
ms.openlocfilehash: 597ea6e7ff7dbcfcb8a99d4e4de3c1b82915ee07
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90561256"
---
# <a name="troubleshooting-windows-azure-guest-agent"></a>針對 Windows Azure 來賓代理程式進行疑難排解

Windows Azure 來賓代理程式是虛擬機器) 代理程式 (VM。 它可讓 VM (IP 位址168.63.129.16 上裝載 VM) 基礎實體伺服器，與網狀架構控制器通訊。 這是有助於進行通訊的虛擬公用 IP 位址。 如需詳細資訊，請參閱 [什麼是 IP 位址 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16)。

 從內部部署遷移至 Azure，或使用自訂映射建立的 VM 未安裝 Windows Azure 來賓代理程式。 在這些情況下，您必須手動安裝 VM 代理程式。 如需有關如何安裝 VM 代理程式的詳細資訊，請參閱 [Azure 虛擬機器代理程式總覽](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)。

成功安裝 Windows Azure 來賓代理程式之後，您可以在 VM 上看到 services.msc 中列出下列服務：
 
- Windows Azure 來賓代理程式服務
- 遙測服務
- RD 代理程式服務

**Windows Azure 來賓代理程式服務**：此服務是負責 WAppAgent 中所有記錄的服務。 此服務負責設定不同的擴充功能，以及從來賓至主機的通訊。 

**遙測服務**：此服務負責將 VM 的遙測資料傳送至後端伺服器。

**RD 代理程式服務**：此服務負責安裝來賓代理程式。 透明安裝程式也是 Rd 代理程式的元件，可協助升級來賓代理程式的其他元件和服務。 RDAgent 也負責將來自來賓 VM 的心跳送至實體伺服器上的主機代理程式。

> [!NOTE]
> 從 VM 來賓代理程式的版本2.7.41491.971 開始，遙測元件包含在 RDAgent 服務中，因此您可能不會在新建立的 Vm 中看到此遙測服務。

## <a name="checking-agent-status-and-version"></a>檢查代理程式狀態和版本

移至 Azure 入口網站中的 [VM 屬性] 頁面，並檢查 **代理程式狀態**。 如果 Windows Azure 來賓代理程式正常運作，狀態就會顯示為 [ **就緒**]。 如果 VM 代理程式處於 [ **未就緒** ] 狀態，Azure 入口網站上的 [擴充功能] 和 [ **執行] 命令** 將無法運作。

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>針對處於未就緒狀態的 VM 代理程式進行疑難排解

### <a name="step-1-check-whether-the-windows-azure-guest-agent-service-is-installed"></a>步驟1檢查是否已安裝 Windows Azure 來賓代理程式服務

- 檢查套件

    找出 [C:\WindowsAzure] 資料夾。 如果您看到 GuestAgent 資料夾顯示版本號碼，這表示已在 VM 上安裝 Windows Azure 來賓代理程式。 您也可以尋找已安裝的套件。  如果 VM 上已安裝 Windows Azure 來賓代理程式，則套件將會儲存在下列位置： `C:\windows\OEM\GuestAgent\VMAgentPackage.zip` 。
    
    您可以執行下列 PowerShell 命令來檢查 VM 代理程式是否已部署至 VM：
    
    `Get-AzVM -ResourceGroupName "RGNAME" -Name "VMNAME" -DisplayHint expand`
    
    在輸出中，找出 [ **ProvisionVMAgent** ] 屬性，然後檢查此值是否設定為 **True**。 如果是，這表示代理程式已安裝在 VM 上。
    
- 檢查服務和流程

   移至 [服務] 主控台 (services.msc) 並檢查下列服務的狀態： Windows Azure 來賓代理程式服務、RDAgent 服務、Windows Azure 遙測服務和 Windows Azure 網路代理程式服務。
 
    您也可以檢查下列進程的工作管理員，檢查這些服務是否正在執行：
       
    - WindowsAzureGuestAgent.exe： Windows Azure 來賓代理程式服務
    - WaAppAgent.exe： RDAgent 服務
    - WindowsAzureNetAgent.exe： Windows Azure 網路代理程式服務
    - WindowsAzureTelemetryService.exe： Windows Azure 遙測服務

   如果您找不到這些處理常式和服務，這表示您未安裝 Windows Azure 來賓代理程式。

- 檢查程式和功能

    在主控台中，移至 [ **程式和功能** ]，以判斷是否已安裝 Windows Azure 來賓代理程式服務。

如果您找不到任何套件、服務和進程，而且沒有看到安裝在 [程式和功能] 下的 Windows Azure 來賓代理程式，請嘗試 [安裝 Windows Azure 來賓代理程式服務](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)。 如果來賓代理程式未正確安裝，您可以 [離線安裝 VM 代理程式](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline)。

如果您可以看到正在執行的服務，請重新開機服務，以查看問題是否已解決。 如果服務已停止，請將其啟動並等候幾分鐘。 然後檢查 **代理程式狀態** 是否已報告為 [ **就緒**]。 如果您發現這些服務損毀，某些協力廠商進程可能會導致這些服務損毀。 若要進一步疑難排解這些問題，請聯絡 [Microsoft 支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="step-2-check-whether-auto-update-is-working"></a>步驟2：檢查自動更新是否正常運作

Windows Azure 來賓代理程式具有自動更新功能。 它會自動檢查是否有新的更新，並加以安裝。 如果自動更新功能無法正常運作，請嘗試使用下列步驟卸載並安裝 Windows Azure 來賓代理程式：

1. 如果 Windows Azure 來賓代理程式出現在 [ **程式和功能**] 中，請卸載 Windows Azure 來賓代理程式。

2. 開啟具有系統管理員許可權的 [命令提示字元] 視窗。

3. 停止來賓代理程式服務。 如果服務未停止，您必須將服務設定為 **手動啟動** ，然後重新開機 VM。
    ```
    net stop rdagent
    net stop WindowsAzureGuestAgent
    net stop WindowsAzureTelemetryService
    ```
1. 刪除來賓代理程式服務：
    ```
    sc delete rdagent
    sc delete WindowsAzureGuestAgent
    sc delete WindowsAzureTelemetryService
    ```
1. 在 [ `C:\WindowsAzure` 建立名為 OLD 的資料夾] 下。

1. 將任何名為封裝或 GuestAgent 的資料夾移到舊資料夾中。

1. 從 [這裡](https://go.microsoft.com/fwlink/?linkid=394789&clcid=0x409)下載並安裝最新版的代理程式安裝套件。 您必須擁有系統管理員權限，才能完成安裝。

1. 使用下列命令來安裝來賓代理程式：

    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log
    ```
    然後檢查來賓代理程式服務是否正確啟動。
 
    在不正確安裝來賓代理程式的罕見情況下，您可以 [離線安裝 VM 代理程式](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline)。
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>步驟3檢查 VM 是否可以連線到網狀架構控制器

使用 PsPing 之類的工具來測試 VM 是否可以連接到埠80、32526和443上的168.63.129.16。 如果 VM 未如預期般連線，請檢查是否已在 VM 的本機防火牆中開啟埠80、443和32526的輸出通訊。 如果此 IP 位址遭到封鎖，VM 代理程式可能會在各種情況下顯示非預期的行為。

## <a name="advanced-troubleshooting"></a>進階疑難排解

針對 Windows Azure 來賓代理程式進行疑難排解的事件會記錄在下列記錄檔中：

- C:\WindowsAzure\Logs\WaAppAgent.log
- C:\WindowsAzure\Logs\TransparentInstaller.log

以下是 Windows Azure 來賓代理程式可進入 **未就緒** 狀態或停止如預期般運作的一些常見案例。

### <a name="agent-stuck-on-starting"></a>代理程式卡在「啟動中」

在 WaAppAgent 記錄中，您可以看到代理程式停滯于啟動進程，而且無法啟動。

**記錄資訊**
 
[00000007] [05/28/2019 12：58： 50.90] [INFO] WindowsAzureGuestAgent 開始。 版本2.7.41491.901

**分析**
 
VM 仍在執行較舊版本的 Windows Azure 來賓代理程式。 在 C:\WindowsAzure 資料夾中，您可能會注意到已安裝多個 Windows Azure 來賓代理程式實例，包括數個相同的版本。 因為已安裝多個代理程式實例，所以 VM 不會啟動最新版本的 Windows Azure 來賓代理程式。

**方案**

手動卸載 Windows Azure 來賓代理程式，然後依照下列步驟來重新安裝：

1. 開啟主控台 > 的 [程式和功能]，然後卸載 Windows Azure 來賓代理程式。
1. 開啟工作管理員，並停止下列服務： Windows Azure 來賓代理程式服務、RDAgent 服務、Windows Azure 遙測服務和 Windows Azure 網路代理程式服務。
1. 在 [C:\WindowsAzure] 底下，建立名為 OLD 的資料夾。
1. 將任何名為封裝或 GuestAgent 的資料夾移到舊資料夾中。 此外，請將以 GuestAgent_x 的 C:\WindowsAzure\logs 中的任何 GuestAgent 資料夾移至舊資料夾。
1. 下載並安裝最新版本的 MSI 代理程式。 您必須擁有系統管理員許可權才能完成安裝。
1. 使用下列 MSI 命令安裝來賓代理程式：
    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log`
    ```
1. 確認 RDAgent、Windows Azure 來賓代理程式和 Windows Azure 遙測服務現在正在執行。
 
1. 檢查 WaAppAgent 以確定最新版本的 Windows Azure 來賓代理程式正在執行。
 
1. 刪除 C:\WindowsAzure. 下的舊資料夾
  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>無法連接到 WireServer IP (主機 IP)  

您會注意到 WaAppAgent 中的下列錯誤專案，以及遙測記錄：

**記錄資訊**

```Log sample
[ERROR] GetVersions() failed with exception: Microsoft.ServiceModel.Web.WebProtocolException: Server Error: Service Unavailable (ServiceUnavailable) ---> 
System.Net.WebException: The remote server returned an error: (503) Server Unavailable.
```
```Log sample
[00000011] [12/11/2018 06:27:55.66] [WARN]  (Ignoring) Exception while fetching supported versions from HostGAPlugin: System.Net.WebException: Unable to connect to the remote server 
---> System.Net.Sockets.SocketException: An attempt was made to access a socket in a way forbidden by its access permissions 168.63.129.16:32526
at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
at System.Net.ServicePoint.ConnectSocketInternal(Boolean connectFailure, Socket s4, Socket s6, Socket& socket, IPAddress& address, ConnectSocketState status, IAsyncResult asyncResult, Exception& exception)
--- End of inner exception stack trace ---
at System.Net.WebClient.DownloadDataInternal(Uri address, WebRequest& request)
at System.Net.WebClient.DownloadString(Uri address)
at Microsoft.GuestAgentHostPlugin.Client.GuestInformationServiceClient.GetVersions()
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.HostGAPluginUtility.UpdateHostGAPluginAvailability()`
```
**分析**

VM 無法連線到 wireserver 主機伺服器。

**方案**

1. 因為無法連線到 wireserver，請使用遠端桌面連線到 VM，然後嘗試從網際網路瀏覽器存取下列 URL： http://168.63.129.16/?comp=versions 
1. 如果您無法連線到步驟1中的 URL，請檢查網路介面，以判斷它是否設定為啟用 DHCP 且有 DNS。 若要檢查網路介面的 DHCP 狀態，請執行下列命令：  `netsh interface ip show config` 。
1. 如果已停用 DHCP，請執行下列動作，並確定您將黃色的值變更為您的介面名稱： `netsh interface ip set address name="Name of the interface" source=dhcp` 。
1. 檢查可能由防火牆、proxy 或其他可能封鎖存取 IP 位址168.63.129.16 的來源所造成的任何問題。
1. 檢查 Windows 防火牆或協力廠商防火牆是否封鎖埠80、443和32526的存取。 如需為何無法封鎖此位址的詳細資訊，請參閱 [什麼是 IP 位址 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16)。

### <a name="guest-agent-is-stuck-stopping"></a>來賓代理程式停滯「正在停止」  

您會注意到 WaAppAgent 中有下列錯誤專案：

**記錄資訊** 

```Log sample
[00000007] [07/18/2019 14:46:28.87] [WARN] WindowsAzureGuestAgent stopping.
[00000007] [07/18/2019 14:46:28.89] [INFO] Uninitializing StateExecutor with WaitForTerminalStateReachedOnEnd : True
[00000004] [07/18/2019 14:46:28.89] [WARN] WindowsAzureGuestAgent could not be stopped. Exception: System.NullReferenceException: Object reference not set to an instance of an object.
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.WaitForExtensionWorkflowComplete(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorForCloud.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentCore.Stop(Boolean waitForTerminalState)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.DoStopService()
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.<>c__DisplayClass2.<OnStopProcessing>b__1()
```
**分析**

Windows Azure 來賓代理程式停滯于停止進程。

**方案**

1. 確定 WaAppAgent.exe 正在 VM 上執行。 如果未執行，請重新開機 rdgagent 服務，然後等候五分鐘。 當 WaAppAgent.exe 正在執行時，請結束 WindowsAzureGuest.exe 進程。
2. 如果步驟1無法解決問題，請移除目前安裝的版本，並手動安裝最新版的代理程式。

### <a name="npcap-loopback-adapter"></a>Npcap 回送介面卡 

您會注意到 WaAppAgent 中有下列錯誤專案：
 
```Log sample
[00000006] [06/20/2019 07:44:28.93] [INFO]  Attempting to discover fabric address on interface Npcap Loopback Adapter.
[00000024] [06/20/2019 07:44:28.93] [WARN]  Empty DHCP option data returned
[00000006] [06/20/2019 07:44:28.93] [ERROR] Did not discover fabric address on interface Npcap Loopback Adapter
```
**分析**

Wireshark 會在 VM 上安裝 Npcap 回送介面卡。 Wireshark 是一種開放原始碼工具，可用於分析網路流量及分析封包。 這類工具通常稱為「網路分析器」、「網路通訊協定分析器」或「探查」。

**方案**

WireShark 可能會安裝 Npcap 回送介面卡。 請嘗試停用它，然後檢查問題是否已解決。

## <a name="next-steps"></a>後續步驟

若要進一步疑難排解「Windows Azure 來賓代理程式無法運作」問題，請 [洽詢 Microsoft 支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
