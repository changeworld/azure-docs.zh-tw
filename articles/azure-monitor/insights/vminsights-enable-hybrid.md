---
title: 針對混合式環境啟用 Azure 監視器
description: 本文說明如何針對包含一或多部虛擬機器的混合式雲端環境，啟用適用於 VM 的 Azure 監視器。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 7a6105e8742a4cb3d2f113c6ef723f6171baf4d9
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87328198"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-virtual-machine"></a>啟用混合式虛擬機器的適用於 VM 的 Azure 監視器
本文說明如何針對 Azure 外部的虛擬機器（包括內部部署和其他雲端環境）啟用適用於 VM 的 Azure 監視器。

> [!IMPORTANT]
> 啟用混合式 Vm 的建議方法是先啟用[伺服器的 Azure Arc](/azure-arc/servers/overview.md) ，讓 vm 可以使用類似 Azure vm 的程式來啟用適用於 VM 的 Azure 監視器。 本文說明如何在您選擇不要使用 Azure Arc 的情況下，將混合式 Vm 上架。

## <a name="prerequisites"></a>必要條件

- [建立和設定 Log Analytics 工作區](vminsights-configure-workspace.md)。
- 請參閱[支援的作業系統](vminsights-enable-overview.md#supported-operating-systems)，以確保支援您要啟用之虛擬機器或虛擬機器擴展集的作業系統。 


## <a name="overview"></a>概觀
Azure 外部的虛擬機器需要與適用于 Azure Vm 的相同 Log Analytics 代理程式和相依性代理程式。 由於您無法使用 VM 擴充功能來安裝代理程式，因此您必須手動將它們安裝在客體作業系統中，或透過其他方法來安裝它們。 

如需部署 Log Analytics 代理程式的詳細資訊，請參閱[將 Windows 電腦連線至 Azure 監視器](../platform/agent-windows.md)或[將 Linux 電腦連接到 Azure 監視器](../platform/agent-linux.md)。 如需相依性代理程式的詳細資料，請參閱這篇文章。 

## <a name="firewall-requirements"></a>防火牆需求
Log Analytics 代理程式[總覽](..//platform/log-analytics-agent.md#network-requirements)中提供 log analytics 代理程式的防火牆需求。 適用於 VM 的 Azure 監視器對應 Dependency Agent 本身不會傳輸任何資料，因此不需變更防火牆或連接埠。 如果您的 IT 安全性原則不允許網路上的電腦連線到網際網路，則對應資料一律會由 Log Analytics 代理程式直接傳輸到 Azure 監視器服務，或是透過[Operations Management Suite 閘道](../../azure-monitor/platform/gateway.md)來傳送。


## <a name="dependency-agent"></a>相依性代理程式

>[!NOTE]
>本節所述的下列資訊也適用于[服務對應解決方案](service-map.md)。  

您可以從下列位置下載 Dependency agent：

| 檔案 | OS | 版本 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.4.10090 | B4E1FF9C1E5CD254AA709AEF9723A81F04EC0763C327567C582CE99C0C5A0BAE  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.4.10090 | A56E310D297CE3B343AE8F4A6F72980F1C3173862D6169F1C713C2CA09660A9F |


## <a name="install-the-dependency-agent-on-windows"></a>在 Windows 上安裝 Dependency Agent

您可以執行 `InstallDependencyAgent-Windows.exe` 以手動將 Dependency Agent 安裝在 Windows 電腦上。 如果您在執行此可執行檔時未使用任何選項，則會啟動以互動方式安裝代理程式的安裝精靈。 您需要來賓 OS 上的*系統管理員*許可權，才能安裝或卸載代理程式。

下表將強調說明命令列中適用於代理程式的安裝程式所支援的參數。

| 參數 | 描述 |
|:--|:--|
| /? | 傳回命令列選項的清單。 |
| /S | 執行無訊息安裝，而不需要與使用者互動。 |

例如，若要使用參數來執行安裝程式 `/?` ，請輸入**InstallDependencyAgent-Windows.exe/？**。

Windows Dependency Agent 的檔案預設安裝在 *C:\Program Files\Microsoft Dependency Agent* 中。 如果在安裝程式完成之後，Dependency agent 無法啟動，請檢查記錄以取得詳細的錯誤資訊。 記錄目錄是 *%Programfiles%\Microsoft Dependency Agent\logs*。

### <a name="powershell-script"></a>PowerShell 指令碼
使用下列範例 PowerShell 腳本來下載並安裝代理程式：

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```


## <a name="install-the-dependency-agent-on-linux"></a>在 Linux 上安裝 Dependency Agent

將 Dependency Agent 從 *InstallDependencyAgent-Linux64.bin* (具有自我解壓縮二進位檔的殼層指令碼) 安裝在 Linux 伺服器上。 您可以使用 `sh` 來執行檔案，或對檔案本身新增執行權限。

>[!NOTE]
> 必須有 root 權限，以便安裝或設定代理程式。
>

| 參數 | 說明 |
|:--|:--|
| -help | 取得命令列選項的清單。 |
| -S | 執行無訊息安裝，不會出現任何使用者提示。 |
| --check | 檢查權限和作業系統，但不會安裝代理程式。 |

例如，若要使用參數來執行安裝程式 `-help` ，請輸入**installdependencyagent-linux64.bin (**。 執行命令，以 root 身分安裝 Linux Dependency agent `sh InstallDependencyAgent-Linux64.bin` 。

如果 Dependency Agent 無法啟動，請檢查記錄以取得詳細的錯誤資訊。 在 Linux 代理程式上，記錄檔目錄是 */var/opt/microsoft/dependency-agent/log*。

Dependency Agent 的檔案位於下列目錄：

| 檔案 | 位置 |
|:--|:--|
| 核心檔案 | /opt/microsoft/dependency-agent |
| 記錄檔 | /var/opt/microsoft/dependency-agent/log |
| 組態檔 | /etc/opt/microsoft/dependency-agent/config |
| 服務可執行檔 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 二進位儲存體檔案 | /var/opt/microsoft/dependency-agent/storage |

### <a name="shell-script"></a>Shell 指令碼 
使用下列範例 shell 腳本來下載並安裝代理程式：

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Desired State Configuration

若要使用預期狀態設定 (DSC) 部署 Dependency Agent，您可以使用 xPSDesiredStateConfiguration 模組和下列範例程式碼：

```powershell
configuration VMInsights {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```



## <a name="troubleshooting"></a>疑難排解

### <a name="vm-doesnt-appear-on-the-map"></a>VM 不會出現在對應上

如果您的相依性代理程式安裝成功，但您在地圖上看不到您的電腦，請遵循下列步驟來診斷問題。

1. Dependency Agent 是否安裝成功？ 您可以查看是否已安裝服務並且執行，以便驗證。

    **Windows**：尋找名為「Microsoft Dependency agent」的服務。

    **Linux**：尋找執行中的進程「microsoft dependency 代理程式」。

2. 您在[Log Analytics 的免費定價層](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)嗎？ 免費方案最多允許五部唯一的電腦。 任何後續的電腦都不會顯示在地圖上，即使先前的五個已不再傳送資料也一樣。

3. 電腦會將記錄檔和效能資料傳送給 Azure 監視器記錄嗎？ 針對您的電腦執行下列查詢：

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    它是否會傳回一或多個結果？ 是否為最新的資料？ 若是如此，您的 Log Analytics 代理程式會正常運作，並與服務通訊。 如果不是，請檢查您伺服器上的代理程式︰[適用於 Windows 的 Log Analytics 代理程式疑難排解](../platform/agent-windows-troubleshoot.md)或[適用於 Linux 的 Log Analytics 代理程式疑難排解](../platform/agent-linux-troubleshoot.md)。

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>電腦出現在地圖上，但是沒有任何進程

如果您在對應上看到您的伺服器，但它沒有進程或連接資料，則表示相依性代理程式已安裝且正在執行，但未載入核心驅動程式。

請檢查 C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file (Windows) 檔案或 /var/opt/microsoft/dependency-agent/log/service.log 檔案 (Linux)。 檔案的最後幾行應該會指出未載入核心的原因。 例如，若您更新過核心，在 Linux 上可能會不受支援。


## <a name="next-steps"></a>後續步驟

現在已針對您的虛擬機器啟用監視，此資訊可透過適用於 VM 的 Azure 監視器進行分析。

- 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。

- 若要找出 VM 效能的瓶頸和整體使用率，請參閱[查看 AZURE VM 效能](vminsights-performance.md)。
