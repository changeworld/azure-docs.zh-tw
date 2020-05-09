---
title: 安裝和設定 Windows Azure 診斷擴充功能（WAD）
description: 瞭解如何收集 Azure 儲存體帳戶中的 Azure 診斷資料，讓您可以使用數個可用工具的其中一種來進行查看。
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: dd18fd484ac456f0c38cd6d9b73a2395a08ad5d0
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82883102"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>安裝和設定 Windows Azure 診斷擴充功能（WAD）
Azure 診斷擴充功能是 Azure 監視器中的代理程式，會收集來自客體作業系統的監視資料，以及 Azure 虛擬機器和其他計算資源的工作負載。 本文提供有關安裝和設定 Windows 診斷擴充功能，以及如何將資料儲存在和 Azure 儲存體帳戶中的描述的詳細資訊。

診斷延伸模組會實作為 Azure 中的[虛擬機器擴充](../../virtual-machines/extensions/overview.md)功能，因此它支援使用 Resource Manager 範本、POWERSHELL 和 CLI 的相同安裝選項。 如需安裝和維護虛擬機器擴充功能的詳細資訊，請參閱[適用于 Windows 的虛擬機器延伸模組和功能](../../virtual-machines/extensions/features-windows.md)。

## <a name="install-with-azure-portal"></a>使用 Azure 入口網站安裝
您可以在 Azure 入口網站中的個別虛擬機器上安裝及設定診斷擴充功能，以提供介面，而不是直接使用設定。 當您啟用診斷擴充功能時，它會自動使用預設設定，其中包含最常見的效能計數器和事件。 您可以根據您的特定需求來修改此預設設定。

> [!NOTE]
> 您無法使用 Azure 入口網站設定診斷延伸模組設定，包括將資料傳送至 Azure 事件中樞。 您必須針對這些設定使用其中一種其他的 configuration 方法。

1. 在 Azure 入口網站中，開啟虛擬機器的功能表。
2. 在 VM 功能表的 [**監視**] 區段中，按一下 [**診斷設定**]。
3. 如果診斷延伸模組尚未啟用，請按一下 [**啟用來賓層級監視**]。
4. 系統會為 VM 建立新的 Azure 儲存體帳戶，其名稱將以 VM 的資源組名為基礎。 您可以藉由選取 [**代理程式**] 索引標籤，將 VM 連結至另一個儲存體帳戶。

![診斷設定](media/diagnostics-extension-windows-install/diagnostic-settings.png)


一旦啟用診斷延伸模組之後，您就可以修改預設設定。 下表描述您可以在不同索引標籤中修改的選項。 有些選項具有**自訂**命令，可讓您指定更詳細的設定;如需不同設定的詳細資訊，請參閱[Windows 診斷擴充功能架構](diagnostics-extension-schema-windows.md)。

| 索引標籤 | 描述 |
|:---|:---|
| 總覽 | 顯示目前的設定，以及其他索引標籤的連結。 |
| 效能計數器 | 選取要收集的效能計數器，以及每個的取樣率。  |
| 記錄 | 選取要收集的記錄檔資料。 這包括 Windows 事件記錄檔、IIS 記錄檔、.NET 應用程式記錄檔和 ETW 事件。  |
| 損毀傾印 | 為不同的進程啟用損毀傾印。 |
| 接收 | 除了 Azure 儲存體以外，讓資料接收能夠將資料傳送至目的地。<br>Azure 監視器-將效能資料傳送至 Azure 監視器計量。<br>Application Insights-將資料傳送至 Application Insights 應用程式。 |
| 代理程式 | 修改代理程式的下列設定：<br>-變更儲存體帳戶。<br>-指定用於代理程式的本機磁片上限。<br>-設定代理程式本身健全狀況的記錄檔。|


> [!NOTE]
> 雖然診斷擴充功能的設定可以用 JSON 或 XML 格式進行格式化，但在 Azure 入口網站中完成的任何設定一律會儲存為 JSON。 如果您使用 XML 搭配另一個設定方法，然後使用 Azure 入口網站變更您的設定，則設定會變更為 JSON。

## <a name="resource-manager-template"></a>Resource Manager 範本
請參閱在[WINDOWS VM 上使用監視和診斷和](../../virtual-machines/extensions/diagnostics-template.md)在使用 Azure Resource Manager 範本部署診斷擴充功能 Azure Resource Manager 範本。 

## <a name="azure-cli-deployment"></a>Azure CLI 部署
Azure CLI 可以使用[az vm extension set](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) ，將 Azure 診斷延伸模組部署到現有的虛擬機器，如下列範例所示。 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

受保護的設定會定義在設定架構的[PrivateConfig 元素](diagnostics-extension-schema-windows.md#privateconfig-element)中。 以下是定義儲存體帳戶之受保護設定檔的基本範例。 如需私用設定的完整詳細資料，請參閱設定[範例](diagnostics-extension-schema-windows.md#privateconfig-element)。

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
公用設定會定義在設定架構的[public 元素](diagnostics-extension-schema-windows.md#publicconfig-element)中。 以下是公用設定檔案的基本範例，可讓您收集診斷基礎結構記錄、單一效能計數器和單一事件記錄檔。 如需公用設定的完整詳細資料，請參閱設定[範例](diagnostics-extension-schema-windows.md#publicconfig-element)。

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
PowerShell 可用來將 Azure 診斷擴充功能部署至現有的虛擬機器，使用[set-azvmdiagnosticsextension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) ，如下列範例所示。 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

私用設定定義于[PrivateConfig 元素](diagnostics-extension-schema-windows.md#privateconfig-element)中，而公用設定則定義于設定架構的[public 元素](diagnostics-extension-schema-windows.md#publicconfig-element)中。 您也可以選擇將儲存體帳戶的詳細資料指定為 Set-azvmdiagnosticsextension Cmdlet 的參數，而不是將它們包含在私人設定中。

以下是設定檔的基本範例，可讓您收集診斷基礎結構記錄、單一效能計數器和單一事件記錄檔。 如需私用和公用設定的完整詳細資料，請參閱[範例](diagnostics-extension-schema-windows.md#publicconfig-element)設定。 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000,
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
下表列出從診斷擴充功能收集而來的不同資料類型，以及它們是儲存為數據表或 blob。 儲存在資料表中的資料也可以儲存在 blob 中，視公用設定中的[StorageType 設定](diagnostics-extension-schema-windows.md#publicconfig-element)而定。


| 資料 | 儲存體類型 | 描述 |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Table | 診斷監視器和設定變更。 |
| WADDirectoriesTable | Table | 診斷監視器正在監視的目錄。  這包括 IIS 記錄、IIS 失敗要求記錄和自訂目錄。  Blob 記錄檔的位置是在 [容器] 欄位中指定，而 Blob 的名稱則是在 RelativePath 欄位中指定。  AbsolutePath 欄位會指出檔案存在於 Azure 虛擬機器上的位置和名稱。 |
| WadLogsTable | Table | 使用追蹤接聽程式在程式碼中寫入的記錄。 |
| WADPerformanceCountersTable | Table | 效能計數器。 |
| WADWindowsEventLogsTable | Table | Windows 事件記錄檔。 |
| wad-iis-wad-iis-failedreqlogfiles | Blob | 包含 IIS 失敗要求記錄檔中的資訊。 |
| wad-iis-logfiles | Blob | 包含 IIS 記錄檔的相關資訊。 |
| 客戶 | Blob | 以設定診斷監視器所監視的目錄為基礎的自訂容器。  將會在 WADDirectoriesTable 中指定此 Blob 容器的名稱。 |

## <a name="tools-to-view-diagnostic-data"></a>用來檢視診斷資料的工具
有數個工具可用來檢視傳輸至儲存體後的資料。 例如：

* Visual Studio 中的伺服器總管 - 如果您已安裝 Azure Tools for Microsoft Visual Studio，您可以在伺服器總管中使用 Azure 儲存體節點，從您的 Azure 儲存體帳戶檢視唯讀的 Blob 和資料表資料。 您可以從您的本機儲存體模擬器帳戶顯示資料，也可以從您為 Azure 建立的儲存體帳戶顯示資料。 如需詳細資訊，請參閱 [使用伺服器總管瀏覽和管理儲存體資源](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)。
* [Microsoft Azure 儲存體 Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) 是一個獨立應用程式，可讓您在 Windows、OSX 和 Linux 上輕鬆使用 Azure 儲存體資料。
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) 包含 Azure 診斷管理員，可讓您檢視、下載及管理在 Azure 上執行的應用程式所收集的診斷資料。

## <a name="next-steps"></a>後續步驟
- 如需將監視資料轉送至 Azure 事件中樞的詳細資訊，請參閱[將資料從 Windows Azure 診斷擴充功能傳送至事件中樞](diagnostics-extension-stream-event-hubs.md)。
