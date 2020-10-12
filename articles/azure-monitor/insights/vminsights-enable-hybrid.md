---
title: 針對混合式環境啟用 Azure 監視器
description: 本文說明如何針對包含一或多部虛擬機器的混合式雲端環境啟用適用於 VM 的 Azure 監視器。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: d994df4d56f4958784256ff9cd92ce1e6f3b3e50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88642158"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-virtual-machine"></a>啟用混合式虛擬機器的適用於 VM 的 Azure 監視器
本文說明如何為 Azure 外部的虛擬機器啟用適用於 VM 的 Azure 監視器，包括內部部署和其他雲端環境。

> [!IMPORTANT]
> 啟用混合式 Vm 的建議方法是先啟用 [適用於伺服器的 Azure Arc](../../azure-arc/servers/overview.md) ，讓 vm 可以使用與 Azure vm 類似的進程來適用於 VM 的 Azure 監視器啟用。 本文說明如何在您選擇不使用 Azure Arc 時，將混合式 Vm 上線。

## <a name="prerequisites"></a>Prerequisites

- [建立並設定 Log Analytics 工作區](vminsights-configure-workspace.md)。
- 請參閱 [支援的作業系統](vminsights-enable-overview.md#supported-operating-systems) ，以確保支援您所啟用之虛擬機器或虛擬機器擴展集的作業系統。 


## <a name="overview"></a>概觀
Azure 外部的虛擬機器需要相同的 Log Analytics 代理程式和用於 Azure Vm 的相依性代理程式。 由於您無法使用 VM 擴充功能來安裝代理程式，因此您必須手動將其安裝在客體作業系統中，或透過其他方法來安裝它們。 

如需部署 Log Analytics 代理程式的詳細資訊，請參閱 [將 Windows 電腦連線到 Azure 監視器](../platform/agent-windows.md) 或 [將 Linux 電腦連線到 Azure 監視器](../platform/agent-linux.md) 。 本文提供相依性代理程式的詳細資料。 

## <a name="firewall-requirements"></a>防火牆需求
Log analytics [代理程式總覽](../platform/log-analytics-agent.md#network-requirements)中提供了 log analytics 代理程式的防火牆需求。 適用於 VM 的 Azure 監視器對應 Dependency Agent 本身不會傳輸任何資料，因此不需變更防火牆或連接埠。 [如果您](../../azure-monitor/platform/gateway.md)的 IT 安全性原則不允許網路上的電腦連線到網際網路，則對應資料一律會由 Log Analytics 代理程式直接傳輸到 Azure 監視器服務。


## <a name="dependency-agent"></a>相依性代理程式

>[!NOTE]
>本節所述的下列資訊也適用于 [服務對應的解決方案](service-map.md)。  

您可以從下列位置下載 Dependency agent：

| 檔案 | OS | 版本 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.5.10940 | C27A56D0BE9CF162DF73292DFBB2083F5FF749F2B80FCAD2545BC8B14B64A8D7  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.5.10940 | 71B4E1DA5116E61E03317C49C6702B5069F01A0C9A7CB860F6ACFAF5C198740E |


## <a name="install-the-dependency-agent-on-windows"></a>在 Windows 上安裝 Dependency Agent

您可以執行 `InstallDependencyAgent-Windows.exe` 以手動將 Dependency Agent 安裝在 Windows 電腦上。 如果您在執行此可執行檔時未使用任何選項，則會啟動以互動方式安裝代理程式的安裝精靈。 您需要來賓 OS 的 *系統管理員* 許可權，才能安裝或卸載代理程式。

下表將強調說明命令列中適用於代理程式的安裝程式所支援的參數。

| 參數 | 描述 |
|:--|:--|
| /? | 傳回命令列選項的清單。 |
| /S | 執行無訊息安裝，而不需要與使用者互動。 |

例如，若要使用參數來執行安裝程式 `/?` ，請輸入 **InstallDependencyAgent-Windows.exe/？**。

Windows Dependency Agent 的檔案預設安裝在 *C:\Program Files\Microsoft Dependency Agent* 中。 如果相依性代理程式在安裝程式完成後無法啟動，請檢查記錄檔以取得詳細的錯誤資訊。 記錄目錄是 *%Programfiles%\Microsoft Dependency Agent\logs*。

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

例如，若要使用參數來執行安裝程式 `-help` ，請輸入 **installdependencyagent-linux64.bin (。 bin-help**。 執行命令，以 root 身份安裝 Linux Dependency agent `sh InstallDependencyAgent-Linux64.bin` 。

如果 Dependency Agent 無法啟動，請檢查記錄以取得詳細的錯誤資訊。 在 Linux 代理程式上，記錄檔目錄是 */var/opt/microsoft/dependency-agent/log*。

Dependency Agent 的檔案位於下列目錄：

| 檔案儲存體 | Location |
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

### <a name="vm-doesnt-appear-on-the-map"></a>VM 未出現在地圖上

如果相依性代理程式安裝成功，但您在對應上看不到您的電腦，請遵循下列步驟來診斷問題。

1. Dependency Agent 是否安裝成功？ 您可以查看是否已安裝服務並且執行，以便驗證。

    **Windows**：尋找名為「Microsoft Dependency agent」的服務。

    **Linux**：尋找正在執行的進程 "microsoft-dependency-agent"。

2. 您是在 [Log Analytics 免費定價層](./solutions.md)嗎？ 免費方案允許最多五部唯一的電腦。 即使先前的五個電腦不再傳送資料，任何後續的電腦也不會顯示在地圖上。

3. 電腦是否將記錄和效能資料傳送至 Azure 監視器記錄？ 針對您的電腦執行下列查詢：

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    它會傳回一或多個結果嗎？ 是否為最新的資料？ 如果是，您的 Log Analytics 代理程式會正常運作，並與服務通訊。 如果不是，請檢查您伺服器上的代理程式︰[適用於 Windows 的 Log Analytics 代理程式疑難排解](../platform/agent-windows-troubleshoot.md)或[適用於 Linux 的 Log Analytics 代理程式疑難排解](../platform/agent-linux-troubleshoot.md)。

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>電腦出現在地圖上，但是沒有任何進程

如果您在地圖上看到您的伺服器，但它沒有進程或連線資料，則表示相依性代理程式已安裝且正在執行，但未載入核心驅動程式。

請檢查 C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file (Windows) 檔案或 /var/opt/microsoft/dependency-agent/log/service.log 檔案 (Linux)。 檔案的最後幾行應該會指出未載入核心的原因。 例如，若您更新過核心，在 Linux 上可能會不受支援。


## <a name="next-steps"></a>接下來的步驟

現在已為您的虛擬機器啟用監視，此資訊可透過適用於 VM 的 Azure 監視器進行分析。

- 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。

- 若要找出 VM 效能的瓶頸和整體使用率，請參閱 [View AZURE VM performance](vminsights-performance.md)。