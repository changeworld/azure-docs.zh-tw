---
title: 安裝和配置 Windows Azure 診斷擴展 （WAD）
description: 瞭解如何在 Azure 存儲帳戶中收集 Azure 診斷資料，以便可以使用多個可用工具之一查看資料。
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 929ab4109eb8d0e90b6c561a2135c0b7dd4205bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672254"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>安裝和配置 Windows Azure 診斷擴展 （WAD）
Azure 診斷擴展是 Azure 監視器中的代理，用於從 Azure 虛擬機器和其他計算資源的客體作業系統和工作負荷收集監視資料。 本文提供有關安裝和配置 Windows 診斷擴展的詳細資訊，以及資料在和 Azure 存儲帳戶中的存儲方式的說明。

診斷擴展在 Azure 中作為[虛擬機器擴展](../../virtual-machines/extensions/overview.md)實現，因此它支援使用資源管理器範本、PowerShell 和 CLI 的相同安裝選項。 有關安裝和維護虛擬機器擴展的詳細資訊，請參閱[Windows 的虛擬機器擴展和功能](../../virtual-machines/extensions/features-windows.md)。

## <a name="install-with-azure-portal"></a>使用 Azure 門戶安裝
您可以在 Azure 門戶中的單個虛擬機器上安裝和配置診斷擴展，該虛擬機器提供介面，而不是直接使用配置。 啟用診斷擴展時，它將自動使用預設配置與最常見的效能計數器和事件。 您可以根據您的特定要求修改此預設配置。

> [!NOTE]
> 無法使用 Azure 門戶配置診斷擴展設置，包括將資料發送到 Azure 事件中心。 您必須對這些設置使用其他配置方法之一。

1. 打開 Azure 門戶中的虛擬機器的功能表。
2. 按一下 VM 功能表的 **"監視**"部分中的 **"診斷設置**"。
3. 如果診斷擴展尚未啟用，請按一下**啟用來賓級監視**。
4. 將為具有該名稱的 VM 創建新的 Azure 存儲帳戶，該帳戶的名稱將基於 VM 的資源組的名稱。 您可以通過選擇 **"代理"** 選項卡將 VM 附加到其他存儲帳戶。

![診斷設定](media/diagnostics-extension-windows-install/diagnostic-settings.png)


啟用診斷擴展後，可以修改預設配置。 下表描述了可以在不同的選項卡中修改的選項。 某些選項具有 **"自訂"** 命令，允許您指定更詳細的配置;有關不同設置的詳細資訊，請參閱[Windows 診斷擴展架構](diagnostics-extension-schema-windows.md)。

| 索引標籤 | 描述 |
|:---|:---|
| 總覽 | 顯示當前配置，並帶有指向其他選項卡的連結。 |
| 效能計數器 | 選擇要收集的效能計數器以及每個計數器的取樣速率。  |
| 記錄 | 選擇要收集的日誌資料。 這包括 Windows 事件日誌、IIS 日誌、.NET 應用程式日誌和 ETW 事件。  |
| 損毀傾印 | 為不同的進程啟用崩潰轉儲。 |
| 接收 | 使資料接收器將資料發送到除 Azure 存儲之外的目標。<br>Azure 監視器 - 將效能資料發送到 Azure 監視器指標。<br>應用程式見解 - 將資料發送到應用程式見解應用程式。 |
| 代理程式 | 修改代理的以下配置：<br>- 更改存儲帳戶。<br>- 指定用於代理的最大本地磁片。<br>- 為代理本身的運行狀況配置日誌。|


> [!NOTE]
> 雖然診斷擴展的配置可以在 JSON 或 XML 中格式化，但在 Azure 門戶中完成的任何配置將始終存儲為 JSON。 如果將 XML 與其他配置方法一起使用，然後使用 Azure 門戶更改配置，則設置將更改為 JSON。

## <a name="resource-manager-template"></a>Resource Manager 範本
有關使用 Azure 資源管理器範本部署診斷擴展[，請參閱將監視和診斷與 Windows VM 和 Azure 資源管理器範本](../../virtual-machines/extensions/diagnostics-template.md)一起使用。 

## <a name="azure-cli-deployment"></a>Azure CLI 部署
Azure CLI 可用於使用[az vm 擴展集](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set)將 Azure 診斷擴展部署到現有虛擬機器，如以下示例所示。 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

受保護的設置在配置架構的[私有配置元素](diagnostics-extension-schema-windows.md#privateconfig-element)中定義。 下面是定義存儲帳戶的受保護設置檔的最小示例。 有關私有設置的完整詳細資訊，請參閱[示例配置](diagnostics-extension-schema-windows.md#privateconfig-element)。

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
公共設置在配置架構的公共[元素](diagnostics-extension-schema-windows.md#publicconfig-element)中定義。 下面是公共設置檔的最小示例，該檔支援收集診斷基礎結構日誌、單個效能計數器和單個事件日誌。 有關公共設置的完整詳細資訊，請參閱[示例配置](diagnostics-extension-schema-windows.md#publicconfig-element)。

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>PowerShell 部署
PowerShell 可用於使用[Set-AzVM 診斷擴展](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension)將 Azure 診斷擴展部署到現有虛擬機器，如以下示例所示。 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

私有設置在[Private Config 元素](diagnostics-extension-schema-windows.md#privateconfig-element)中定義，而公共設置在配置架構的[Public 元素](diagnostics-extension-schema-windows.md#publicconfig-element)中定義。 您還可以選擇將存儲帳戶的詳細資訊指定為 Set-AzVM 診斷擴展 Cmdlet 的參數，而不是將它們包括在私有設置中。

下面是一個設定檔的最小示例，該檔支援診斷基礎結構日誌、單個效能計數器和單個事件日誌的集合。 有關私有和公共設置的完整詳細資訊，請參閱[示例配置](diagnostics-extension-schema-windows.md#publicconfig-element)。 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000
            },
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M",
                        "unit": "percent"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

另請參閱[使用 PowerShell 在執行 Windows 的虛擬機器中啟用 Azure 診斷](../../virtual-machines/extensions/diagnostics-windows.md)。

## <a name="data-storage"></a>資料儲存體
下表列出了從診斷擴展中收集的不同類型的資料，以及這些資料是作為表存儲還是 Blob 存儲。 存儲在表中的資料也可以存儲在 blob 中，具體取決於公共配置中的[StorageType 設置](diagnostics-extension-schema-windows.md#publicconfig-element)。


| 資料 | 儲存體類型 | 描述 |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Table | 診斷監視器和配置更改。 |
| WAD 目錄表 | Table | 診斷監視器正在監視的目錄。  這包括 IIS 記錄、IIS 失敗要求記錄和自訂目錄。  Blob 記錄檔的位置是在 [容器] 欄位中指定，而 Blob 的名稱則是在 RelativePath 欄位中指定。  AbsolutePath 欄位會指出檔案存在於 Azure 虛擬機器上的位置和名稱。 |
| WadLogsTable | Table | 使用跟蹤攔截器在代碼中編寫的日誌。 |
| WADPerformanceCountersTable | Table | 效能計數器。 |
| WADWindowsEventLogsTable | Table | Windows 事件記錄檔。 |
| wad-iis-失敗重新日誌檔 | Blob | 包含來自 IIS 失敗請求日誌的資訊。 |
| wad-iis-logfiles | Blob | 包含有關 IIS 日誌的資訊。 |
| "自訂" | Blob | 基於配置由診斷監視器監視的目錄的自訂容器。  將會在 WADDirectoriesTable 中指定此 Blob 容器的名稱。 |

## <a name="tools-to-view-diagnostic-data"></a>用來檢視診斷資料的工具
有數個工具可用來檢視傳輸至儲存體後的資料。 例如：

* Visual Studio 中的伺服器總管 - 如果您已安裝 Azure Tools for Microsoft Visual Studio，您可以在伺服器總管中使用 Azure 儲存體節點，從您的 Azure 儲存體帳戶檢視唯讀的 Blob 和資料表資料。 您可以從您的本機儲存體模擬器帳戶顯示資料，也可以從您為 Azure 建立的儲存體帳戶顯示資料。 如需詳細資訊，請參閱 [使用伺服器總管瀏覽和管理儲存體資源](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)。
* [Microsoft Azure 儲存體 Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) 是一個獨立應用程式，可讓您在 Windows、OSX 和 Linux 上輕鬆使用 Azure 儲存體資料。
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) 包含 Azure 診斷管理員，可讓您檢視、下載及管理在 Azure 上執行的應用程式所收集的診斷資料。

## <a name="next-steps"></a>後續步驟
- 有關將監視資料轉發到 Azure 事件中心的詳細資訊，請參閱[將資料從 Windows Azure 診斷擴展發送到事件中心](diagnostics-extension-stream-event-hubs.md)。
