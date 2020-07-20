---
title: Azure 監視器中的連線資料方案 |Microsoft Docs
description: 連線資料是來自內有 Log Analytics 代理程式電腦的網路和效能的彙總資料。 網路資料結合記錄資料可協助您將資料相互關聯。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/29/2020
ms.openlocfilehash: afcad5df1072f2eb474e54aaeca866735a12c5c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84424460"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Azure 監視器中的 Wire Data 2.0 （預覽）解決方案

![Wire Data 符號](media/wire-data/wire-data2-symbol.png)

連線資料是彙總的網路和效能資料，使用 Log Analytics 代理程式從 Windows 連線和 Linux 連線的電腦收集，包括您的環境中由 Operations Manager 監視的資料。 網路資料結合其他記錄資料可協助您將資料相互關聯。

除了 Log Analytics 代理程式，Wire Data 解決方案還會使用您在 IT 基礎結構的電腦上所安裝的 Microsoft 相依性代理程式。 相依性代理程式會監視往返於您電腦傳送的網路資料 (屬於 [OSI 模型](https://en.wikipedia.org/wiki/OSI_model)中的網路層級 2-3)，包括使用的各種通訊協定和連接埠。 然後使用代理程式將資料傳送至 Azure 監視器。  

>[!NOTE]
>網路資料解決方案已取代為[服務對應解決方案](service-map.md)。  兩者都使用 Log Analytics 代理程式和相依性代理程式，將網路連接資料收集到 Azure 監視器。 
> 
>使用網路資料解決方案的現有客戶可能會繼續使用它。 我們將發佈移至服務對應的遷移時程表指引。
>
>新客戶應安裝[服務對應解決方案](service-map.md)或[適用於 VM 的 Azure 監視器](vminsights-overview.md)。  服務對應資料集相當於網路資料。  適用於 VM 的 Azure 監視器包含服務對應的資料集，其中包含額外的效能資料和分析的功能。 


根據預設，Azure 監視器記錄 Windows 和 Linux 內建計數器的 CPU、記憶體、磁片和網路效能資料的資料，以及您可以指定的其他效能計數器。 針對每個代理程式，都是即時收集網路和其他資料，包括電腦使用的子網路和應用程式層級通訊協定。  Wire Data 會查看應用程式層級的網路資料，而不會往下查看 TCP 傳輸層。 解決方案不會查看個別的 ACK 和 SYN。 交握完成後，即會將它視為即時連接，並且標示為已連接。 只要兩端同意通訊端為開啟，該連接會維持為即時，且可以來回傳遞資料。 一旦任一端關閉連線，它就會標示為已中斷連接。  因此，它只會計算已成功完成封包的頻寬，而不會回報重新傳送或失敗的封包。

如果您曾經使用 [sFlow](http://www.sflow.org/) 或其他軟體來搭配 [Cisco NetFlow 通訊協定](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html)，則會很熟悉從連線資料傳回的統計資料和資料。

幾種內建的記錄搜尋查詢包括︰

- 提供連線資料的代理程式
- 提供連線資料的代理程式的 IP 位址
- 依 IP 位址的輸出通訊
- 應用程式通訊協定傳送的位元組數目
- 應用程式服務傳送的位元組數目
- 不同通訊協定接收的位元組數目
- IP 版本傳送及接收的位元組總數
- 可靠測量的連線平均延遲
- 起始或接收網路流量的電腦處理程序
- 處理程序的網路流量

當您使用連線資料來搜尋時，您可以篩選和分組資料，以檢視最常用的前幾個代理程式和通訊協定的相關資訊。 或者，您可以檢視某些電腦 (IP 位址/MAC 位址) 何時彼此通訊、持續時間，以及已傳送的資料量；基本上，就是在檢視以搜尋為基礎之網路流量的相關中繼資料。

不過，因為是檢視中繼資料，在深入的疑難排解中不見得實用。 Azure 監視器中的連線資料不是完整的網路資料捕獲。  不適用於封包層級的深入疑難排解。 相較于其他收集方法，使用代理程式的優點是您不需要安裝設備、重新設定網路交換器，或執行複雜的設定。 連線資料是以代理程式為基礎—在電腦上安裝代理程式，它就會監視自己的網路流量。 另一個優點是您想要監視雲端提供者、主機服務提供者或 Microsoft Azure 中執行的工作負載，而其中使用者未擁有網狀架構層級。

## <a name="connected-sources"></a>連接的來源

Wire Data 會從 Microsoft 相依性代理程式取得其資料。 Dependency Agent 取決於 Log Analytics 代理程式，以便其連線至 Azure 監視器。 這表示，伺服器必須先安裝 Log Analytics 代理程式，並設定 Dependency Agent。 下表描述 Wire Data 解決方案支援的連線來源。

| **連線的來源** | **支援** | **描述** |
| --- | --- | --- |
| Windows 代理程式 | Yes | Wire Data 會分析並收集來自 Windows 代理程式電腦的資料。 <br><br> 除了[適用于 windows 的 Log Analytics 代理程式](../platform/agent-windows.md)以外，windows 代理程式還需要 Microsoft Dependency agent。 如需作業系統版本的完整清單，請參閱[支援的作業系統](vminsights-enable-overview.md#supported-operating-systems)。 |
| Linux 代理程式 | 是 | Wire Data 會分析並收集來自 Linux 代理程式電腦的資料。<br><br> 除了[適用于 linux 的 Log Analytics 代理程式](../learn/quick-collect-linux-computer.md)之外，linux 代理程式還需要 Microsoft Dependency agent。 如需作業系統版本的完整清單，請參閱[支援的作業系統](vminsights-enable-overview.md#supported-operating-systems)。 |
| System Center Operations Manager 管理群組 | Yes | Wire Data 會在連線的 [System Center Operations Manager 管理群組](../platform/om-agents.md)中，分析並收集來自 Windows 和 Linux 代理程式的資料。 <br><br> 需要從 System Center Operations Manager 代理程式電腦到 Azure 監視器的直接連接。 |
| Azure 儲存體帳戶 | No | Wire Data 會收集來自代理程式電腦的資料，因此沒有要從 Azure 儲存體收集的資料。 |

在 Windows 上，System Center Operations Manager 和 Azure 監視器會使用 Microsoft Monitoring Agent （MMA）來收集和傳送資料。 視內容而定，此代理程式可稱為 System Center Operations Manager 代理程式、Log Analytics 代理程式、MMA 或直接代理程式。 System Center Operations Manager 和 Azure 監視器提供稍微不同的 MMA 版本。 這些版本可以每個報表 System Center Operations Manager、Azure 監視器或兩者。

在 Linux 上，適用于 Linux 的 Log Analytics 代理程式會收集資料，並將其傳送至 Azure 監視器。 當代理程式直接連接到 Azure 監視器，或透過 System Center Operations Manager 管理群組連線到 Azure 監視器的伺服器時，您可以在伺服器上使用網路資料。

Dependency agent 不會傳輸任何資料本身，也不需要對防火牆或埠進行任何變更。 連線資料中的資料一律會由 Log Analytics 代理程式傳輸到 Azure 監視器，不論是直接或透過 Log Analytics 閘道。

![代理程式圖表](./media/wire-data/agents.png)

如果您是管理群組連線到 Azure 監視器的 System Center Operations Manager 使用者：

- 當您的 System Center Operations Manager 代理程式可以存取網際網路以連線到 Azure 監視器時，不需要進行其他設定。
- 當您的 System Center Operations Manager 代理程式無法透過網際網路存取 Azure 監視器時，您必須設定 Log Analytics 閘道以使用 System Center Operations Manager。

如果您的 Windows 或 Linux 電腦無法直接連線至服務，您必須將 Log Analytics 代理程式設定為使用 Log Analytics 閘道連接到 Azure 監視器。 您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=52666)下載 Log Analytics 閘道。

## <a name="prerequisites"></a>必要條件

- 需要[洞察力與分析](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing)解決方案供應項目。
- 如果您使用舊版 Wire Data 解決方案，則必須先將它移除。 不過，您仍然可以在 Wire Data 2.0 和記錄搜尋中使用透過原始 Wire Data 解決方案擷取的所有資料。
- 必須有系統管理員許可權，才能安裝或卸載 Dependency agent。
- 相依性代理程式必須安裝在具有64位作業系統的電腦上。

### <a name="operating-systems"></a>作業系統

下列各節列出 Dependency agent 支援的作業系統。 Wire Data 不支援任何 32 位元架構的作業系統。

#### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Windows 桌面

- Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="supported-linux-operating-systems"></a>支援的 Linux 作業系統
下列各節列出 Linux 上的相依性代理程式所支援的作業系統。  

- 只支援預設版本和 SMP Linux 核心版本。
- 所有 Linux 散發套件皆不支援非標準的核心版本 (例如 PAE 和 Xen)。 舉例來說，版本字串為「2.6.16.21-0.8-xen」的系統就不受支援。
- 不支援自訂核心，包括重新編譯的標準核心。

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| OS 版本 | 核心版本 |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

##### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| OS 版本 | 核心版本 |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

##### <a name="centosplus"></a>CentOSPlus
| OS 版本 | 核心版本 |
|:--|:--|
| 6.9 | 2.6.32-696.18。7<br>2.6.32-696.30。1 |
| 6.10 | 2.6.32-696.30。1<br>2.6.32-754.3。5 |

##### <a name="ubuntu-server"></a>Ubuntu Server

| OS 版本 | 核心版本 |
|:--|:--|
| Ubuntu 18.04 | 核心4.15。\*<br>4.18 * |
| Ubuntu 16.04.3 | 核心 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

##### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| OS 版本 | 核心版本
|:--|:--|
| 11 SP4 | 3.0. * |

##### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS 版本 | 核心版本
|:--|:--|
| 12 SP2 | 4.4.* |
| 12 SP3 | 4.4.* |

### <a name="dependency-agent-downloads"></a>Dependency Agent 下載

| 檔案 | OS | 版本 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |



## <a name="configuration"></a>組態

執行下列步驟來設定您工作區的 Wire Data 解決方案。

1. 從[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview)啟用活動記錄分析解決方案，或使用[從方案庫新增監視解決方案](../../azure-monitor/insights/solutions.md)中所述的程式。
2. 在您要取得資料的每部電腦上安裝 Dependency agent。 Dependency agent 可以監視立即鄰近專案的連線，因此您可能不需要在每部電腦上都有代理程式。

> [!NOTE]
> 您無法將舊版 Wire Data 解決方案新增至新的工作區。 如果您已啟用原始 Wire Data 解決方案，您可以繼續使用。 不過，若要使用 Wire Data 2.0，您必須先移除原始版本。
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>在 Windows 上安裝 Dependency Agent

必須有系統管理員權限，以便安裝或解除安裝代理程式。

相依性代理程式會安裝在透過 InstallDependencyAgent-Windows.exe 執行 Windows 的電腦上。 如果您在執行此可執行檔時不使用任何選項，則會啟動以互動方式安裝的精靈。

使用下列步驟在執行 Windows 的每部電腦上安裝 Dependency agent：

1. 依照[為混合式環境中的 Windows 電腦設定 Log Analytics 代理程式](../../azure-monitor/platform/agent-windows.md)一文中的步驟，安裝 Log Analytics 代理程式。
2. 使用上一節中的連結來下載 Windows Dependency agent，然後使用下列命令來執行它：`InstallDependencyAgent-Windows.exe`
3. 遵循精靈來安裝代理程式。
4. 如果 Dependency Agent 無法啟動，請檢查記錄以取得詳細的錯誤資訊。 針對 Windows 代理程式，記錄的目錄是 %Programfiles%\Microsoft Dependency Agent\logs。

#### <a name="windows-command-line"></a>Windows 命令列

使用下表中的選項以從命令列安裝。 若要查看安裝旗標的清單，請如下所示使用 /? 旗標執行安裝程式 如下所示。

InstallDependencyAgent-Windows.exe /?

| **標識** | **描述** |
| --- | --- |
| <code>/?</code> | 取得命令列選項的清單。 |
| <code>/S</code> | 執行無訊息安裝，不會出現任何使用者提示。 |

Windows Dependency agent 的檔案預設會放在 C:\Program Files\Microsoft Dependency agent 中。

### <a name="install-the-dependency-agent-on-linux"></a>在 Linux 上安裝 Dependency Agent

必須有 root 權限，以便安裝或設定代理程式。

「相依性代理程式」是透過 Installdependencyagent-linux64.bin (安裝在 Linux 電腦上，這是一個具有自我解壓縮二進位檔的 shell 腳本。 您可以使用 _sh_ 來執行檔案，或對檔案本身新增執行權限。

請使用下列步驟在每一部 Linux 電腦上安裝 Dependency Agent：

1. 依照[為混合式環境中的 Linux 電腦設定 Log Analytics 代理程式](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key)一文中的步驟，安裝 Log Analytics 代理程式。
2. 使用上一節中的連結來下載 Linux 相依性代理程式，然後使用下列命令將它安裝為根目錄：sh InstallDependencyAgent-Linux64.bin
3. 如果 Dependency Agent 無法啟動，請檢查記錄以取得詳細的錯誤資訊。 在 Linux 代理程式上，記錄檔的目錄是 /var/opt/microsoft/dependency-agent/log。

若要查看安裝旗標的清單，請如下所示以 `-help` 旗標執行安裝程式。

```
InstallDependencyAgent-Linux64.bin -help
```

| **標識** | **描述** |
| --- | --- |
| <code>-help</code> | 取得命令列選項的清單。 |
| <code>-s</code> | 執行無訊息安裝，不會出現任何使用者提示。 |
| <code>--check</code> | 檢查權限和作業系統，但不會安裝代理程式。 |

Dependency Agent 的檔案位於下列目錄：

| **檔案儲存體** | **位置** |
| --- | --- |
| 核心檔案 | /opt/microsoft/dependency-agent |
| 記錄檔 | /var/opt/microsoft/dependency-agent/log |
| 組態檔 | /etc/opt/microsoft/dependency-agent/config |
| 服務可執行檔 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 二進位儲存體檔案 | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>安裝指令碼範例

若要在許多伺服器上一次輕鬆部署相依性代理程式，可以使用腳本。 您可以使用下列腳本範例，在 Windows 或 Linux 上下載並安裝 Dependency agent。

#### <a name="powershell-script-for-windows"></a>適用於 Windows 的 PowerShell 指令碼

```powershell

Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>適用於 Linux 的殼層指令碼

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Desired State Configuration

若要透過 Desired State Configuration 部署相依性代理程式，您可以使用 xPSDesiredStateConfiguration 模組和如下所示的一些程式碼：

```powershell
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency agent

    xRemoteFile DAPackage

    {

        Uri = "https://aka.ms/dependencyagentwindows"

        DestinationPath = $DAPackageLocalPath

        DependsOn = "[Package]OI"

    }

    xPackage DA

    {

        Ensure = "Present"

        Name = "Dependency Agent"

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = ""

        InstalledCheckRegKey = "HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"

        InstalledCheckRegValueName = "DisplayName"

        InstalledCheckRegValueData = "Dependency Agent"

    }

}

```

### <a name="uninstall-the-dependency-agent"></a>卸載 Dependency agent

使用下列各節可協助您移除 Dependency agent。

#### <a name="uninstall-the-dependency-agent-on-windows"></a>卸載 Windows 上的相依性代理程式

系統管理員可透過控制台解除安裝 Windows Dependency Agent。

系統管理員也可以執行 %Programfiles%\Microsoft Dependency Agent\Uninstall.exe，以解除安裝 Dependency Agent。

#### <a name="uninstall-the-dependency-agent-on-linux"></a>卸載 Linux 上的相依性代理程式

若要完全卸載 Linux 上的相依性代理程式，您必須移除代理程式本身以及隨代理程式自動安裝的連接器。 您可以使用下列單一命令同時解除安裝這兩者：

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>管理組件

在 Log Analytics 工作區中啟動 Wire Data 時，會將 300 KB 的管理組件傳送至該工作區中的所有 Windows 伺服器。 如果您是在[連線的管理群組](../platform/om-agents.md)中使用 System Center Operations Manager 代理程式，則會從 System Center Operations Manager 部署相依性監視管理組件。 如果代理程式是直接連接的，Azure 監視器會傳遞管理元件。

管理組件名稱為 Microsoft.IntelligencePacks.ApplicationDependencyMonitor。 它會寫入至 %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs。 管理組件所使用的資料來源是 %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;自動產生的識別碼&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll。

## <a name="using-the-solution"></a>使用解決方案

請使用下列資訊來安裝和設定方案。

- 連線資料方案會從執行 Windows Server 2012 R2、Windows 8.1 和更新版本作業系統的電腦取得資料。
- 您想要取得連線資料的來源電腦上需要有 Microsoft.NET Framework 4.0 或更新版本。
- 使用[從方案庫新增監視解決方案](solutions.md)中所述的程式，將網路資料解決方案新增至您的 Log Analytics 工作區。 不需要進一步的組態。
- 如果您想要檢視特定解決方案的連線資料，必須先將此解決方案新增至您的工作區。

依序安裝代理程式和解決方案之後，Wire Data 2.0 磚會出現在您的工作區中。

![Wire Data 磚](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>使用 Wire Data 2.0 解決方案

在 Azure 入口網站 Log Analytics 工作區的 [概觀]**** 頁面中，按一下 [Wire Data 2.0]**** 磚以開啟 [Wire Data] 儀表板。 此儀表板包含下表中的刀鋒視窗。 每個刀鋒視窗最多會列出 10 個與該刀鋒視窗中指定範圍和時間範圍的準則相符的項目。 您可以按一下刀鋒視窗底部的 [查看全部]****，或按一下刀鋒視窗標頭，以執行記錄搜尋來傳回所有記錄。

| **刀鋒視窗** | **描述** |
| --- | --- |
| 擷取網路流量的代理程式數 | 顯示擷取網路流量的代理程式數，並列出擷取最多流量的前 10 部電腦。 按一下此數字可執行記錄搜尋，以搜尋 <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>。 按一下清單中的電腦可執行記錄搜尋，以傳回擷取的位元組總數。 |
| 區域子網路數 | 顯示代理程式探索到的區域子網路數。  按一下此數字可執行記錄搜尋，以搜尋 <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code>，其中列出所有子網路及透過每個子網路傳送的位元組數目。 按一下清單中的子網路可執行記錄搜尋，以傳回透過子網路傳送的位元組總數。 |
| 應用程式層級通訊協定數 | 顯示代理程式探索到的使用中應用程式層級通訊協定數。 按一下此數字可執行記錄搜尋，以搜尋 <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>。 按一下通訊協定可執行記錄搜尋，以傳回使用該通訊協定傳送的位元組總數。 |

![Wire Data 儀表板](./media/wire-data/wire-data-dash.png)

您可以使用 [擷取網路流量的代理程式數]**** 刀鋒視窗，來判斷電腦所耗用的網路頻寬量。 此刀鋒視窗可協助您輕鬆找到環境中「通訊量最大」__ 的電腦。 這類電腦可能超載、運作異常，或使用的網路資源不尋常地過多。

![記錄搜尋範例](./media/wire-data/log-search-example01.png)

同樣地，您可以使用 [區域子網路數]**** 刀鋒視窗，來判斷透過子網路移動的網路流量。 使用者通常會為其應用程式定義重要區域周圍的子網路。 此刀鋒視窗會提供這些區域的檢視。

![記錄搜尋範例](./media/wire-data/log-search-example02.png)

[應用程式層級通訊協定數]**** 刀鋒視窗可協助了解使用中的通訊協定，因此很有用。 例如，您可能預期 SSH 不會用於網路環境中。 藉由檢視刀鋒視窗中可用的資訊，即可快速確認或否認您的預期。

![記錄搜尋範例](./media/wire-data/log-search-example03.png)

它也有助於了解通訊協定流量是否隨時間增加或減少。 例如，如果應用程式所傳輸的資料量增加，可能有應該注意或值得關注的問題。

## <a name="input-data"></a>輸入資料

連線資料會使用您已啟用的代理程式，來收集有關網路流量的中繼資料。 每個代理程式約每隔 15 秒傳送一次資料。

## <a name="output-data"></a>輸出資料

對於每種類型的輸入資料，系統會建立 _WireData_ 類型的記錄。 WireData 記錄具有下表所示的屬性：

| 屬性 | 描述 |
|---|---|
| 電腦 | 收集資料所在的電腦名稱 |
| TimeGenerated | 記錄的時間 |
| LocalIP | 本機電腦的 IP 位址 |
| SessionState | 已連線或已中斷連線 |
| ReceivedBytes | 接收的位元組數目 |
| ProtocolName | 使用的網路通訊協定名稱 |
| IPVersion | IP 版本 |
| Direction | 輸入或輸出 |
| MaliciousIP | 已知惡意來源的 IP 位址 |
| Severity | 可疑惡意程式碼嚴重性 |
| RemoteIPCountry | 遠端 IP 位址的國家/地區 |
| ManagementGroupName | Operations Manager 管理群組的名稱 |
| SourceSystem | 收集資料所在的來源 |
| SessionStartTime | 工作階段的開始時間 |
| SessionEndTime | 工作階段的結束時間 |
| LocalSubnet | 收集資料所在的子網路 |
| LocalPortNumber | 本機連接埠號碼 |
| RemoteIP | 遠端電腦所使用的遠端 IP 位址 |
| RemotePortNumber | 遠端 IP 位址所使用的連接埠號碼 |
| SessionID | 識別兩個 IP 位址之間通訊工作階段的唯一值 |
| SentBytes | 傳送的位元組數目 |
| TotalBytes | 工作階段期間傳送的位元組總數 |
| ApplicationProtocol | 使用的網路通訊協定類型   |
| ProcessID | Windows 處理序識別碼 |
| ProcessName | 處理序的路徑和檔案名稱 |
| RemoteIPLongitude | IP 經度值 |
| RemoteIPLatitude | IP 緯度值 |

## <a name="next-steps"></a>後續步驟

- [搜尋記錄](../../azure-monitor/log-query/log-query-overview.md)以檢視詳細的連線資料搜尋記錄。
