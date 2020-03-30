---
title: 為混合環境啟用 Azure 監視器 |微軟文檔
description: 本文介紹如何為包含一個或多個虛擬機器的混合雲環境啟用 VM 的 Azure 監視器。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 734f61c2e96002516e9e15af88d2c6b0fce00e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480737"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-environment"></a>為混合環境啟用 VM 的 Azure 監視器

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

本文介紹如何為虛擬機器或資料中心或其他雲環境中託管的物理電腦啟用虛擬機器或物理電腦的 Azure 監視器。 此過程結束時，您將成功開始監視環境中的虛擬機器，並瞭解虛擬機器是否遇到任何性能或可用性問題。

開始之前，請務必檢閱[必要條件](vminsights-enable-overview.md)並確認訂用帳戶和資源已符合需求。 檢閱適用於 [Log Analytics Linux 和 Windows 代理程式](../../log-analytics/log-analytics-agent-overview.md)的需求和部署方法。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>適用於 VM 的 Azure 監視器對應 Dependency Agent 本身不會傳輸任何資料，因此不需變更防火牆或連接埠。 如果 IT 安全性原則不允許網路上的電腦連接到 Internet，則地圖資料始終由日誌分析代理直接或通過[操作管理套件閘道](../../azure-monitor/platform/gateway.md)傳輸到 Azure 監視器服務。

完成此任務的步驟總結如下：

1. 安裝適用于 Windows 或 Linux 的日誌分析代理。 在安裝代理之前，請查看[日誌分析代理概述](../platform/log-analytics-agent.md)文章以瞭解系統先決條件和部署方法。

2. 針對 [Windows](https://aka.ms/dependencyagentwindows) 或 [Linux](https://aka.ms/dependencyagentlinux)，下載並安裝適用於 VM 的 Azure 監視器對應 Dependency Agent。

3. 啟用收集效能計數器。

4. 部署適用於 VM 的 Azure 監視器。

>[!NOTE]
>本文中描述的用於部署依賴項代理的資訊也適用于[服務映射解決方案](service-map.md)。  

## <a name="install-the-dependency-agent-on-windows"></a>在 Windows 上安裝 Dependency Agent

您可以執行 `InstallDependencyAgent-Windows.exe` 以手動將 Dependency Agent 安裝在 Windows 電腦上。 如果您在執行此可執行檔時未使用任何選項，則會啟動以互動方式安裝代理程式的安裝精靈。

>[!NOTE]
>安裝或卸載代理需要*管理員*許可權。

下表將強調說明命令列中適用於代理程式的安裝程式所支援的參數。

| 參數 | 描述 |
|:--|:--|
| /? | 傳回命令列選項的清單。 |
| /S | 執行無訊息安裝，而不需要與使用者互動。 |

例如，要使用`/?`參數運行安裝程式，請輸入**安裝獨立代理-Windows.exe /？**

Windows Dependency Agent 的檔案預設安裝在 *C:\Program Files\Microsoft Dependency Agent* 中。 如果依賴項代理在設置完成後無法啟動，請檢查日誌以瞭解詳細的錯誤資訊。 記錄目錄是 *%Programfiles%\Microsoft Dependency Agent\logs*。

## <a name="install-the-dependency-agent-on-linux"></a>在 Linux 上安裝 Dependency Agent

將 Dependency Agent 從 *InstallDependencyAgent-Linux64.bin* (具有自我解壓縮二進位檔的殼層指令碼) 安裝在 Linux 伺服器上。 您可以使用 `sh` 來執行檔案，或對檔案本身新增執行權限。

>[!NOTE]
> 必須有 root 權限，以便安裝或設定代理程式。
>

| 參數 | 描述 |
|:--|:--|
| -help | 取得命令列選項的清單。 |
| -S | 執行無訊息安裝，不會出現任何使用者提示。 |
| --check | 檢查權限和作業系統，但不會安裝代理程式。 |

例如，要使用`-help`參數運行安裝程式，請輸入**安裝獨立代理-Linux64.bin-help**。

通過運行命令`sh InstallDependencyAgent-Linux64.bin`將 Linux 依賴項代理安裝為根。

如果 Dependency Agent 無法啟動，請檢查記錄以取得詳細的錯誤資訊。 在 Linux 代理上，日誌目錄是 */var/opt/microsoft/依賴代理/日誌*。

Dependency Agent 的檔案位於下列目錄：

| 檔案 | Location |
|:--|:--|
| 核心檔案 | /opt/microsoft/dependency-agent |
| 記錄檔 | /var/opt/microsoft/dependency-agent/log |
| 組態檔 | /etc/opt/microsoft/dependency-agent/config |
| 服務可執行檔 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 二進位儲存體檔案 | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>安裝指令碼範例

若要同時在許多伺服器上輕鬆部署 Dependency Agent ，請使用下列指令碼範例，在 Windows 或 Linux 上下載並安裝 Dependency Agent。

### <a name="powershell-script-for-windows"></a>適用於 Windows 的 PowerShell 指令碼

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>適用於 Linux 的殼層指令碼

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

## <a name="enable-performance-counters"></a>啟用效能計數器

如果解決方案所參考的 Log Analytics 工作區還未設定為收集此解決方案所需的效能計數器，則必須啟用這些效能計數器。 您可以通過兩種方式之一執行此操作：
* 手動，如 [Log Analytics 中的 Windows 和 Linux 效能資料來源](../../azure-monitor/platform/data-sources-performance-counters.md)中所述
* 通過下載並運行可從[Azure PowerShell 庫](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)獲取的 PowerShell 腳本

## <a name="deploy-azure-monitor-for-vms"></a>部署適用於 VM 的 Azure 監視器

此方法包含一個 JSON 範本，其會指定在 Log Analytics 工作區中啟用解決方案元件的設定。

如果您不知道如何使用範本部署資源，請參閱：
* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../../azure-resource-manager/templates/deploy-powershell.md)
* [使用 Resource Manager 範本與 Azure CLI 部署資源](../../azure-resource-manager/templates/deploy-cli.md)

要使用 Azure CLI，首先需要在本地安裝和使用 CLI。 您必須執行 Azure CLI 2.0.27 版或更新版本。 若要知道您使用的版本，請執行 `az --version`。 要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="create-and-execute-a-template"></a>建立並執行範本

1. 複製以下 JSON 語法並貼到您的檔案中：

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. 在本機資料夾中將此檔案儲存為 *installsolutionsforvminsights.json*。

1. 擷取 *WorkspaceName*、*ResourceGroupName* 和 *WorkspaceLocation* 的值。 *WorkspaceName* 的值是您 Log Analytics 工作區的名稱。 *WorkspaceLocation* 的值是定義工作區所在的區域。

1. 您已經準備好使用下列 PowerShell 命令部署此範本：

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    配置更改可能需要幾分鐘才能完成。 完成後，將顯示類似于以下內容的消息，其中包含結果：

    ```powershell
    provisioningState       : Succeeded
    ```
   啟用監視之後，可能需要約 10 分鐘的時間才能檢視混合式電腦的健康狀態和計量。

## <a name="troubleshooting"></a>疑難排解

### <a name="vm-doesnt-appear-on-the-map"></a>VM 未顯示在地圖上

如果依賴項代理安裝成功，但在地圖上看不到電腦，請按照以下步驟診斷問題。

1. Dependency Agent 是否安裝成功？ 您可以查看是否已安裝服務並且執行，以便驗證。

    **Windows**︰尋找名稱為「Microsoft Dependency Agent」的服務。

    **Linux**：尋找正在運行的進程"微軟依賴代理"。

2. 您是否在[日誌分析的免費定價層](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)？ 免費計畫允許最多使用五台獨特的電腦。 任何後續電腦將不會顯示在地圖上，即使前五台電腦不再發送資料。

3. 電腦是否向 Azure 監視器日誌發送日誌和 perf 資料？ 為您的電腦執行以下查詢：

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    是否返回一個或多個結果？ 是否為最新的資料？ 如果是這樣，您的日誌分析代理操作正常並與服務通信。 如果不是，請檢查您伺服器上的代理程式︰[適用於 Windows 的 Log Analytics 代理程式疑難排解](../platform/agent-windows-troubleshoot.md)或[適用於 Linux 的 Log Analytics 代理程式疑難排解](../platform/agent-linux-troubleshoot.md)。

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>電腦顯示在地圖上，但沒有進程

如果在地圖上看到伺服器，但它沒有進程或連接資料，則表示已安裝並運行依賴項代理，但內核驅動程式未載入。

請檢查 C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file (Windows) 檔案或 /var/opt/microsoft/dependency-agent/log/service.log 檔案 (Linux)。 檔案的最後幾行應該會指出未載入核心的原因。 例如，若您更新過核心，在 Linux 上可能會不受支援。


## <a name="next-steps"></a>後續步驟

現在，已為虛擬機器啟用了監視功能，此資訊可用於使用針對 VM 的 Azure 監視器進行分析。

- 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。

- 要根據 VM 的性能識別瓶頸和總體利用率，請參閱查看[Azure VM 性能](vminsights-performance.md)。
