---
title: 安裝和設定 Windows Azure 診斷擴充功能 (WAD)
description: 瞭解如何安裝和設定 Windows 診斷延伸模組。 此外，瞭解如何將資料儲存在和 Azure 儲存體帳戶的說明。
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e6ccba27fb599cb26da86e94d3500f4f806ecb76
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328865"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>安裝和設定 Windows Azure 診斷擴充功能 (WAD)
[Azure 診斷擴充功能](diagnostics-extension-overview.md)是 Azure 監視器中的代理程式，可收集來自客體作業系統的監視資料，以及 Azure 虛擬機器和其他計算資源的工作負載。 本文提供有關安裝和設定 Windows 診斷擴充功能的詳細資訊，以及說明如何在 Azure 儲存體帳戶中儲存資料。

在 Azure 中診斷擴充功能會當作[虛擬機器擴充功能](../../virtual-machines/extensions/overview.md)實作，因此支援使用 Resource Manager 範本、PowerShell 和 CLI 的相同安裝選項。 請參閱[虛擬機器擴充功能和功能 (Windows)](../../virtual-machines/extensions/features-windows.md)，以取得安裝及維護虛擬機器擴充功能的詳細資訊。

## <a name="overview"></a>概觀
當您設定 Windows Azure 診斷擴充功能時，必須指定儲存體帳戶，所有指定的資料都會傳送到該帳戶。 您可以選擇性地新增一或多個 *資料接收器* ，以將資料傳送至不同的位置。

- Azure 監視器接收器 - 將來賓效能資料傳送至 Azure 監視器計量。
- 事件中樞接收器 - 將來賓效能和記錄資料傳送至 Azure 事件中樞，以在 Azure 外部轉送。 無法在 Azure 入口網站中設定此接收器。


## <a name="install-with-azure-portal"></a>使用 Azure 入口網站進行安裝
您可以在 Azure 入口網站中的個別虛擬機器上安裝及設定診斷擴充功能，以提供一個介面，而不是直接使用設定。 當您啟用診斷擴充功能時，其會自動使用包含最常見效能計數器和事件的預設設定。 您可以根據您的特定需求來修改此預設設定。

> [!NOTE]
> 以下說明診斷擴充功能的最常見設定。 如需所有設定選項的詳細資訊，請參閱 [Windows 診斷擴充功能結構描述](diagnostics-extension-schema-windows.md)。

1. 在 Azure 入口網站中開啟虛擬機器的功能表。

2. 在 VM 功能表的 [監視] 區段中，按一下 [診斷設定]。

3. 如果尚未啟用診斷擴充功能，請按一下 [啟用來賓層級監視]。

   ![啟用監視](media/diagnostics-extension-windows-install/enable-monitoring.png)

4. 系統會為 VM 建立新的 Azure 儲存體帳戶，其名稱將以 VM 的資源組名稱為基礎，並將選取一組預設的來賓效能計數器和記錄。

   ![診斷設定](media/diagnostics-extension-windows-install/diagnostic-settings.png)

5. 在 [效能計數器] 索引標籤中，選取您想要從此虛擬機器收集的來賓計量。 使用 [自訂] 設定，以進行更進階的選取。

   ![效能計數器](media/diagnostics-extension-windows-install/performance-counters.png)

6. 在 [記錄] 索引標籤中，選取要從虛擬機器收集的記錄。 記錄可以傳送至儲存體或事件中樞，但不能傳送至 Azure 監視器。 使用 [Log Analytics 代理程式](log-analytics-agent.md)，將來賓記錄收集至 Azure 監視器。

   ![螢幕擷取畫面顯示已為虛擬機器選取不同記錄的 [記錄] 索引標籤。](media/diagnostics-extension-windows-install/logs.png)

7. 在 [損毀傾印] 索引標籤中，指定在損毀後用於收集記憶體傾印的程序。 資料將會寫入至儲存體帳戶以進行診斷設定，而且您可選擇性地指定 Blob 容器。

   ![損毀傾印](media/diagnostics-extension-windows-install/crash-dumps.png)

8. 在 [接收器] 索引標籤中，指定是否要將資料傳送至 Azure 儲存體以外的位置。 如果您選取 [Azure 監視器]，系統會將來賓效能資料傳送至 Azure 監視器計量。 您無法使用 Azure 入口網站來設定事件中樞接收器。

   ![螢幕擷取畫面顯示已啟用 [將診斷資料傳送至 Azure 監視器] 選項的 [接收] 索引標籤。](media/diagnostics-extension-windows-install/sinks.png)
   
   如果您尚未針對虛擬機器啟用系統指派的身分識別，當您使用 Azure 監視器接收器儲存設定時，可能會看到以下警告。 按一下橫幅可啟用系統指派的身分識別。
   
   ![受控實體](media/diagnostics-extension-windows-install/managed-entity.png)

9. 在 [代理程式]中，您可以變更儲存體帳戶、設定磁碟配額，以及指定是否要收集診斷基礎結構記錄。  

   ![螢幕擷取畫面顯示 [代理程式] 索引標籤，其中包含設定儲存體帳戶的選項。](media/diagnostics-extension-windows-install/agent.png)

10. 按一下 **[儲存]** 儲存組態。 

> [!NOTE]
> 雖然診斷擴充功能的設定可以用 JSON 或 XML 進行格式化，但是在 Azure 入口網站中完成的任何設定一律會以 JSON 形式儲存。 如果您使用 XML 搭配另一個設定方法，然後透過 Azure 入口網站變更您的組態，則設定會變更為 JSON。

## <a name="resource-manager-template"></a>Resource Manager 範本
如需使用 Azure Resource Manager 範本佈署診斷擴充功能的相關資訊，請參閱[使用 Windows VM 和 Azure Resource Manager 範本進行監視和診斷](../../virtual-machines/extensions/diagnostics-template.md)。 

## <a name="azure-cli-deployment"></a>Azure CLI 部署
Azure CLI 可使用 [az vm extension set](/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) 將Azure 診斷擴充功能部署至現有的虛擬機器，如下列範例所示。 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

受保護的設定會定義於設定結構描述的 [PrivateConfig 元素](diagnostics-extension-schema-windows.md#privateconfig-element)中。 以下是定義儲存體帳戶之受保護設定檔案的基本範例。 如需私人設定的完整詳細資料，請參閱[設定範例](diagnostics-extension-schema-windows.md#privateconfig-element)。

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```

公用設定會定義於設定結構描述的 [Public 元素](diagnostics-extension-schema-windows.md#publicconfig-element)中。 以下是公用設定檔案的基本範例，其可收集診斷基礎結構記錄、單一效能計數器和單一事件記錄。 如需公用設定的完整詳細資料，請參閱[設定範例](diagnostics-extension-schema-windows.md#publicconfig-element)。

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
PowerShell 可使用 [Set-AzVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) 將 Azure 診斷擴充功能部署至現有的虛擬機器，如下列範例所示。 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

私人設定定義於 [PrivateConfig 元素](diagnostics-extension-schema-windows.md#privateconfig-element)中，而公用設定則定義於設定結構描述的 [Public 元素](diagnostics-extension-schema-windows.md#publicconfig-element)中。 您也可以選擇將儲存體帳戶的詳細資料指定為 Set-AzVMDiagnosticsExtension Cmdlet 的參數，而不是將其包含在私人設定中。

以下是組態檔案的基本範例，其可收集診斷基礎結構記錄、單一效能計數器和單一事件記錄。 如需私人和公用設定的完整詳細資料，請參閱[設定範例](diagnostics-extension-schema-windows.md#publicconfig-element)。 

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
下表列出從診斷擴充功能收集而來的不同資料類型，以及其是以資料表或 Blob 的形式儲存。 資料表中儲存的資料也可以儲存在 Blob 中，視您的公用設定中的 [StorageType 設定](diagnostics-extension-schema-windows.md#publicconfig-element)而定。


| 資料 | 儲存體類型 | 描述 |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Table | 診斷監視器和設定變更。 |
| WADDirectoriesTable | Table | 診斷監視器所監視的目錄。  這包括 IIS 記錄、IIS 失敗要求記錄和自訂目錄。  Blob 記錄檔的位置是在 [容器] 欄位中指定，而 Blob 的名稱則是在 RelativePath 欄位中指定。  AbsolutePath 欄位會指出檔案存在於 Azure 虛擬機器上的位置和名稱。 |
| WadLogsTable | Table | 使用追蹤接聽程式在程式碼中寫入的記錄。 |
| WADPerformanceCountersTable | Table | 效能計數器。 |
| WADWindowsEventLogsTable | Table | Windows 事件記錄。 |
| wad-iis-failedreqlogfiles | Blob | 包含 IIS 失敗要求記錄中的資訊。 |
| wad-iis-logfiles | Blob | 包含有關 IIS 的資訊。 |
| "custom" | Blob | 自訂容器，該容器是以設定診斷監視器所監視的目錄為基礎。  將會在 WADDirectoriesTable 中指定此 Blob 容器的名稱。 |

## <a name="tools-to-view-diagnostic-data"></a>用來檢視診斷資料的工具
有數個工具可用來檢視傳輸至儲存體後的資料。 例如：

* Visual Studio 中的伺服器總管 - 如果您已安裝 Azure Tools for Microsoft Visual Studio，您可以在伺服器總管中使用 Azure 儲存體節點，從您的 Azure 儲存體帳戶檢視唯讀的 Blob 和資料表資料。 您可以從您的本機儲存體模擬器帳戶顯示資料，也可以從您為 Azure 建立的儲存體帳戶顯示資料。 如需詳細資訊，請參閱 [使用伺服器總管瀏覽和管理儲存體資源](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)。
* [Microsoft Azure 儲存體 Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) 是一個獨立應用程式，可讓您在 Windows、OSX 和 Linux 上輕鬆使用 Azure 儲存體資料。
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) 包含 Azure 診斷管理員，可讓您檢視、下載及管理在 Azure 上執行的應用程式所收集的診斷資料。

## <a name="next-steps"></a>後續步驟
- 如需將監視資料轉送至 Azure 事件中樞的詳細資訊，請參閱[將資料從 Windows Azure 診斷擴充功能傳送至事件中樞](diagnostics-extension-stream-event-hubs.md)。
