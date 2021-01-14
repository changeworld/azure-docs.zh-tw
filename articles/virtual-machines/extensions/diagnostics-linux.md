---
title: Azure 計算 - Linux 診斷擴充功能
description: 如何設定 Azure Linux 診斷擴充功能 (LAD) 從 Azure 中執行的 Linux VM 中收集計量並記錄事件。
services: virtual-machines-linux
author: axayjo
manager: gwallace
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: akjosh
ms.openlocfilehash: 2e831b3c091b18a5c739275e4c932094ce088ba4
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202601"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>使用 Linux 診斷擴充功能監視計量與記錄

本文件說明 3.0 版與更新版本的 Linux 診斷擴充功能。

> [!IMPORTANT]
> 如需 2.3 版與更舊版本的資訊，請參閱[本文件](/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2)。

## <a name="introduction"></a>簡介

Linux 診斷擴充功能可協助使用者監視在 Microsoft Azure 上執行的 Linux VM 其健康情況。 它可提供下列功能：

* 從 VM 收集系統效能計量，並儲存在所指定儲存體帳戶的特定資料表中。
* 從 Syslog 擷取記錄事件，並儲存在所指定儲存體帳戶的特定資料表中。
* 讓使用者自訂要收集並上傳的資料計量。
* 可讓使用者自訂 Syslog 設備，以及要收集並上傳的事件其嚴重性層級。
* 讓使用者將指定的記錄檔上傳至指定的儲存體資料表。
* 支援傳送計量與事件記錄至所指定儲存體帳戶中的任意 EventHub 端點與 JSON 格式的 blob。

此擴充功能適用於這兩個 Azure 部署模型。

## <a name="installing-the-extension-in-your-vm"></a>在 VM 中安裝擴充功能

您可以使用 Azure PowerShell Cmdlet、Azure CLI 指令碼、ARM 範本或 Azure 入口網站啟用此擴充功能。 如需詳細資訊，請參閱[擴充功能](features-linux.md)。

>[!NOTE]
>診斷 VM 擴充功能的某些元件也會隨附于 [Log ANALYTICS vm 擴充](./oms-linux.md)功能。 由於此架構的緣故，如果兩個延伸模組都在相同的 ARM 範本中具現化，則可能會發生衝突。 若要避免這些安裝時期衝突，請使用指示[ `dependsOn` 詞來確定](../../azure-resource-manager/templates/define-resource-dependency.md#dependson)是否依序安裝延伸模組。 延伸模組可以依照任何順序安裝。

這些安裝指示與[可下載範例組態](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json)可設定 LAD 3.0 以：

* 擷取與儲存 LAD 2.3 所提供的相同計量；
* 擷取一組實用的檔案系統計量，這是 LAD 3.0 的新功能；
* 擷取 LAD 2.3 啟用的預設 Syslog 集合；
* 讓 Azure 入口網站能夠對 VM 計量繪製圖表與發出警示。

可下載組態只是範例，可修改以符合您的需求。

### <a name="supported-linux-distributions"></a>支援的 Linux 散發套件

Linux 診斷擴充功能支援下列散發套件和版本。 散發套件和版本的清單僅適用於 Azure 背書的 Linux 廠商映像。 Linux 診斷擴充功能通常不支援第三方 BYOL 和 BYOS 映像，例如設備。

所有次要版本也都支援只列出主要版本 (例如 Debian 7) 的散發套件。 如果指定了特定次要版本，則只支援該特定版本；如果附加 "+"，則支援等於或大於指定版本的次要版本。

支援的發佈和版本：

- Ubuntu 18.04、16.04、14.04
- CentOS 7、6.5+
- Oracle Linux 7、6.4+
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9、8、7
- RHEL 7、6.7+

### <a name="prerequisites"></a>必要條件

* **Azure Linux Agent 2.2.0 版或更新版本**。 大部分的 Azure VM Linux 資源庫映像包含版本 2.2.7 或更新版本。 執行 `/usr/sbin/waagent -version` 以確認安裝在 VM 上的版本。 如果 VM 執行的是舊版客體代理程式，請依照[這些指示](./update-linux-agent.md)更新。
* **Azure CLI**。 在您的電腦上[設定 Azure CLI](/cli/azure/install-azure-cli) 環境。
* Wget 命令，如果您沒有：執行 `sudo apt-get install wget`。
* 現有的 Azure 訂用帳戶和現有的一般用途儲存體帳戶，用來儲存資料。  一般目的儲存體帳戶支援需要的資料表儲存體。  Blob 儲存體帳戶將無法運作。
* Python 2

### <a name="python-requirement"></a>Python 需求

Linux 診斷擴充功能需要 Python 2。 如果您的虛擬機器預設使用未包含 Python 2 的發行版本，則您必須安裝它。 下列範例命令會在不同的散發版本上安裝 Python 2。    

 - Red Hat、CentOS、Oracle： `yum install -y python2`
 - Ubuntu、Debian： `apt-get install -y python2`
 - SUSE：`zypper install -y python2`

Python2 可執行檔必須以 *python* 為別名。 以下是您可以用來設定此別名的其中一種方法：

1. 執行下列命令以移除任何現有的別名。
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. 執行下列命令以建立別名。

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>範例安裝

> [!NOTE]
> 針對其中一個範例，請在執行之前，為第一個區段中的變數填入正確的值。 

在這些範例中下載的範例設定會收集一組標準資料，並將資料傳送到資料表儲存體。 範例組態的 URL 及其內容可能會變更。 在大部分情況下，請下載一份入口網站設定 JSON 檔案，並依據您的需求進行自訂，然後讓您建構的任何範本或自動化使用您自己的設定檔版本，而不是每次都下載該 URL。

#### <a name="azure-cli-sample"></a>Azure CLI 範例

```azurecli
# Set your Azure VM diagnostic variables correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-for-installing-lad-30-extension-on-the-vmss-instance"></a>在 VMSS 實例上安裝 LAD 3.0 擴充功能的 Azure CLI 範例

```azurecli
#Set your Azure VMSS diagnostic variables correctly below
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VMSS resource ID. Replace storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>PowerShell 範例

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the template above: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally install the extension with the settings built above
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="updating-the-extension-settings"></a>更新擴充功能設定

在您變更自己的受保護或公開設定後，請執行相同的命令將設定部署到 VM。 如果設定有任何的變更，系統會將更新後設定傳送至擴充功能。 LAD 會重新載入組態並自行重新啟動。

### <a name="migration-from-previous-versions-of-the-extension"></a>自舊版擴充功能移轉

擴充功能的最新版本是 **3.0**。 **任何舊版 (2.x) 皆已被取代，並會 2018 年 7 月 31 日停止發行**。

> [!IMPORTANT]
> 此擴充功能為擴充功能組態帶來突破性的改變。 這一項改變可提升擴充功能安全性，也因此不會再維持與 2.x 的回溯相容性。 此外，此擴充功能的擴充功能發行者與 2.x 版的發行者不同。
>
> 若要從 2.x 移轉至新版的擴充功能，您必須解除安裝舊版擴充功能 (在舊發行者名稱下)，然後安裝 3 版的擴充功能。

建議：

* 透過啟用的自動次要版本升級安裝擴充功能。
  * 如果您正透過 Azure XPLAT CLI 或 Powershell 安裝擴充功能，請在傳統部署模型 VM 上指定版本為「3.*」。
  * 在 Azure Resource Manager 部署模型 VM 上的 VM 部署範本中包含「"autoUpgradeMinorVersion": true」。
* 為 LAD 3.0 使用新/不同的儲存體帳戶。 LAD 2.3 與 LAD 3.0 之間些許不相容，讓共用帳戶變得麻煩：
  * LAD 3.0 會將 Syslog 事件儲存在採用不同名稱的資料表中。
  * `builtin` 計量的 counterSpecifier 字串在 LAD 3.0 中不同。

## <a name="protected-settings"></a>受保護的設定

這一組的組態資訊包含敏感性資訊，應加以保護以防遭公開檢視，例如儲存體認證。 這些設定會以加密的形式傳輸到擴充功能並加以儲存。

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

名稱 | 值
---- | -----
storageAccountName | 擴充功能寫入資料的儲存體帳戶名稱。
storageAccountEndPoint | (選擇性) 可識別儲存體帳戶所在雲端的端點。 如果沒有此設定，LAD 會預設為 Azure 公用雲端，`https://core.windows.net`。 若要使用 Azure 德國、Azure Government 或 Azure 中國中的儲存體帳戶，請相應地設定此值。
storageAccountSasToken | Blob 與資料表服務 (`ss='bt'`) 的 [帳戶 SAS 權杖](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/)，適用於容器與物件 (`srt='co'`)，可授與新增、建立、列示、更新與寫入權限 (`sp='acluw'`)。 請 *勿* 包含前置問號 (?)。
mdsdHttpProxy | (選擇性) 啟用擴充功能以連線所指定儲存體帳戶和端點時所需的 HTTP proxy 資訊。
sinksConfig | (選擇性) 可將計量與事件傳遞至的替代目的地詳細資料。 以下各節包含擴充功能所支援每個資料接收的特定詳細資料。

若要取得 Resource Manager 範本內的 SAS 權杖，請使用 **listAccountSas** 函式。 如需範例範本，請參閱[列出函式範例](../../azure-resource-manager/templates/template-functions-resource.md#list-example)。

您可以輕鬆地透過 Azure 入口網站建構所需的 SAS 權杖。

1. 選取您要將擴充功能寫入的一般用途儲存體帳戶
1. 從左側功能表的 [設定] 部分中選取 [共用存取簽章]
1. 依前述設定適當的區段
1. 按一下 [產生 SAS] 按鈕。

![螢幕擷取畫面顯示 [共用存取簽章] 頁面，其中包含 [產生 S]。](./media/diagnostics-linux/make_sas.png)

將產生的 SAS 複製到 [storageAccountSasToken] 欄位；移除前置問號 ("?")。

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

此選擇性區段會定義額外的目的地，讓擴充功能可將收集到的資訊傳送到該目的地。 "sink" 陣列包含每個額外資料接收的物件。 "type" 屬性可決定物件中的其他屬性。

元素 | 值
------- | -----
NAME | 用來在擴充功能組態中的其他位置參考此接收的字串。
type | 正在定義的接收類型。 決定此類型執行個體中的其他值 (若有的話)。

3\.0 版的 Linux 診斷擴充功能支援兩種接收類型：EventHub 與 JsonBlob。

#### <a name="the-eventhub-sink"></a>EventHub 接收

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

"sasURL" 項目包含完整的 URL，包括 SAS 權杖，適用於應將資料發佈至的事件中樞。 LAD 需要 SAS 命名可啟用 Send 宣告的原則。 例如：

* 建立名為 `contosohub` 的事件中樞命名空間
* 在名為 `syslogmsgs` 的命名空間建立事件中樞
* 在啟用 Send 宣告且名為 `writer` 的事件中樞上建立共用存取原則

如果您已建立一個在 2018 年 1 月 1 日 UTC 午夜之前會維持良好的 SAS，則 sasURL 值會是：

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

如需如何在事件中樞的 SAS 權杖上產生和擷取資訊的詳細資訊，請參閱[此網頁](/rest/api/eventhub/generate-sas-token#powershell)。

#### <a name="the-jsonblob-sink"></a>JsonBlob 接收

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

導向至 JsonBlob 的資料儲存在 Azure 儲存體的 Blob 中。 LAD 的每個執行個體每小時會為每個接收名稱建立 Blob。 每個 Blob 一律包含物件的語法有效 JSON 陣列。 新項目會自動新增至陣列中。 Blob 會儲存在與接收相同名稱的容器中。 Blob 容器名稱的 Azure 儲存體規則也適用於 JsonBlob 接收的名稱：3 到 63 個小寫英字數 ASCII 字元或破折號。

## <a name="public-settings"></a>公用設定

此結構包含各種設定區段，可控制擴充功能收集的資訊。 每個設定皆為選擇性。 如果您指定 `ladCfg`，則亦須指定 `StorageAccount`。

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

元素 | 值
------- | -----
StorageAccount | 擴充功能寫入資料的儲存體帳戶名稱。 必須與在[保護設定](#protected-settings)中指定的名稱相同。
mdsdHttpProxy | (選擇性) 與[受保護的設定](#protected-settings)相同。 公用值會被私用值 (若有設定) 覆寫。 將包含祕密 (例如密碼) 的 Proxy 設定設置在[受保護的設定](#protected-settings)中。

以下各節會詳細說明其餘的元素。

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

此選擇性結構會控制計量與記錄的收集，以傳遞至 Azure 計量與其他資料接收。 您必須指定 `performanceCounters` 或 `syslogEvents` 或是兩者。 您必須指定 `metrics` 結構。

元素 | 值
------- | -----
eventVolume | (選擇性) 控制在儲存體資料表中建立的分割區數目。 必須是 `"Large"`、`"Medium"` 或 `"Small"` 的其中之一。 若未指定，則預設值為 `"Medium"`。
sampleRateInSeconds | (選擇性) 原始 (未彙總) 計量集合之間的預設間隔。 支援的最小採樣速率為 15 秒。 若未指定，則預設值為 `15`。

#### <a name="metrics"></a>metrics

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

元素 | 值
------- | -----
resourceId | VM 所屬之 VM 或虛擬機器擴展集的 Azure Resource Manager 資源 ID。 如果在組態中使用任何的 JsonBlob 接收，則亦須指定此設定。
scheduledTransferPeriod | 系統會計算彙總計量的頻率並傳輸至 Azure 計量 (以 IS 8601 時間間隔表示)。 最小傳輸期間為 60 秒，亦即 PT1M。 您必須指定至少一個 scheduledTransferPeriod。

系統每隔 15 秒或以為計數器明確定義的採樣速率收集在 performanceCounters 區段中指定的計量樣本。 如果顯示多個 scheduledTransferPeriod 頻率 (如範例所述)，則會獨立計算每個彙總。

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

此選擇性區段會控制計量的收集。 系統會彙總每個 [scheduledTransferPeriod](#metrics) 原始樣本以產生以下的值：

* 平均值
* minimum
* maximum
* 上次收集的值
* 用於計算彙總的原始樣本計數

元素 | 值
------- | -----
sinks | (選擇性) 以逗號分隔的接收名稱清單，LAD 會將彙總的計量結果傳送至此清單。 系統會將所有彙總的計量發佈至每個列出的接收。 請參閱 [sinksConfig](#sinksconfig)。 範例： `"EHsink1, myjsonsink"`.
type | 識別計量的實際提供者。
class | 與 "counter" 一起使用，可識別提供者命名空間內的特定計量。
counter | 與 "class" 一起使用，可識別提供者命名空間內的特定計量。
counterSpecifier | 可識別 Azure 計量命名空間內的特定計量。
condition (條件) | (選擇性) 選取會套用計量之物件的特定執行個體，或選取該物件所有執行個體的彙總。 如需詳細資訊，請參閱 `builtin` 計量定義。
sampleRate | IS 8601 間隔，可設定收集此計量原始樣本的速率。 如果未設定，則會由 [sampleRateInSeconds](#ladcfg) 的值設定收集間隔。 支援的最短採樣速率為 15 秒 (PT15S)。
unit | 應為以下字串之一："Count"、"Bytes"、"Seconds"、"Percent"、"CountPerSecond"、"BytesPerSecond"、"Millisecond"。 定義計量的單位。 收集資料的取用者預期收集的資料值符合這個單位。 LAD 會忽略此欄位。
displayName | 在 Azure 計量中要附加至此資料的標籤 (採用相關聯地區設定所指定的語言)。 LAD 會忽略此欄位。

counterSpecifier 是任意的識別碼。 如 Azure 入口網站的圖表與警示功能等計量的使用者，會使用 counterSpecifier 作為識別計量或計量執行個體的「鑰匙」。 對於 `builtin` 計量，建議您使用開頭為 `/builtin/` 的 counterSpecifier 值。 如果您正在收集計量的特定執行個體，建議您將執行個體的識別碼附加至 counterSpecifier 值。 以下是一些範例：

* `/builtin/Processor/PercentIdleTime` - 所有 vCPU 的平均閒置時間
* `/builtin/Disk/FreeSpace(/mnt)` - /mnt 檔案系統的可用空間
* `/builtin/Disk/FreeSpace` - 所有已掛接檔案系統的平均可用空間

LAD 或 Azure 入口網站皆不預期 counterSpecifier 值符合任何模式。 應與您建構 counterSpecifier 值的方式一致。

當您指定 `performanceCounters` 時，LAD 一律將資料寫入 Azure 儲存體中的資料表。 您可以將相同的資料寫入 JSON Blob 和/或事件中樞，但您無法禁止將資料儲存至資料表。 診斷擴充功能中所有設定為使用相同儲存體帳戶名稱與端點的執行個體，會將其計量與記錄新增至相同的資料表。 如果有過多的 VM 寫入相同的資料表分割區，Azure 會將這些寫入節流至該分割區。 eventVolume 設定會造成這些項目散布在 1 (小)、10 (中) 或 100 (大) 個不同的分割區。 通常「中」便足以確定不會將流量節流。 Azure 入口網站的 Azure 計量功能會使用此資料表中的資料產生圖形或觸發警示。 資料表名稱是這些字串的串連：

* `WADMetrics`
* 儲存在資料表中彙總值的 "scheduledTransferPeriod"
* `P10DV2S`
* 格式為 "YYYYMMDD" 的日期，每 10 天變更一次

例如 `WADMetricsPT1HP10DV2S20170410` 與 `WADMetricsPT1MP10DV2S20170609`。

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

此選擇性區段會控制從 Syslog 收集記錄事件。 如果省略該區段，則完全不會擷取 Syslog 事件。

syslogEventConfiguration 集合對每個感興趣的 Syslog 設備都會一個項目。 如果特定設備的 minSeverity 為 "NONE"，或如果該設備完全未顯示在元素中，則不會從該設備中擷取事件。

元素 | 值
------- | -----
sinks | 系統會將個別記錄事件發佈至的接收名稱清單，以逗號分隔。 符合 syslogEventConfiguration 中限制的所有記錄事件會發佈至每個所列的接收。 範例："EHforsyslog"
facilityName | Syslog 設備名稱 (例如 "LOG\_USER" 或 "LOG\_LOCAL0")。 請參閱 [Syslog 手冊頁](http://man7.org/linux/man-pages/man3/syslog.3.html)中的「設備」(英文) 一節，以取得完整清單。
minSeverity | Syslog 嚴重性層級 (例如 "LOG\_ERR" 或 "LOG\_INFO")。 請參閱 [Syslog 手冊頁](http://man7.org/linux/man-pages/man3/syslog.3.html)中的「層級」(英文) 一節，以取得完整清單。 擴充功能會擷取傳送到在指定層級或以上的設備。

當您指定 `syslogEvents` 時，LAD 一律將資料寫入 Azure 儲存體中的資料表。 您可以將相同的資料寫入 JSON Blob 和/或事件中樞，但您無法禁止將資料儲存至資料表。 此資料表的分割行為如同所述的 `performanceCounters`。 資料表名稱是這些字串的串連：

* `LinuxSyslog`
* 格式為 "YYYYMMDD" 的日期，每 10 天變更一次

例如 `LinuxSyslog20170410` 與 `LinuxSyslog20170609`。

### <a name="perfcfg"></a>perfCfg

此選擇性區段會控制任意 [OMI](https://github.com/Microsoft/omi) 查詢的執行。

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

元素 | 值
------- | -----
命名空間 | (選擇性) 應執行查詢的 OMI 命名空間。 如果未指定，則預設值為 "root/scx"，此值由[系統中心跨平台提供者](https://github.com/Microsoft/SCXcore) (英文) 實作。
查詢 | 欲執行的 OMI 查詢。
資料表 | (選擇性) 所指定儲存體帳戶中的 Azure 儲存體資料表 (請參閱[受保護的設定](#protected-settings))。
frequency | (選擇性) 執行查詢之間的秒數。 預設值為 300 秒 (5 分鐘)；最小值為 15 秒。
sinks | (選擇性) 原始樣本計量結果應發佈至的額外接收名稱，以逗號分隔。 擴充功能或 Azure 計量不會計算這些原始樣本的彙總。

必須指定 "table" 或 "sinks"，或兩者。

### <a name="filelogs"></a>fileLogs

控制記錄檔的擷取。 LAD 會在新文字行寫入檔案時擷取這些文字行，並寫入資料表資料列和/或任何指定的接收 (JsonBlob 或 EventHub)。

> [!NOTE]
> fileLogs 是由名為 LAD 的子元件所捕捉 `omsagent` 。 若要收集 fileLogs，您必須確定 `omsagent` 使用者具有您指定之檔案的讀取權限，以及該檔案之路徑中所有目錄的執行許可權。 您可以在 `sudo su omsagent -c 'cat /path/to/file'` 安裝 LAD 之後執行此檢查。

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

元素 | 值
------- | -----
檔案 | 欲監看與擷取的記錄檔完整路徑名稱。 路徑名稱必須命名單一檔案，不能命名目錄或包含萬用字元。 ' Omsagent ' 使用者帳戶必須具有檔案路徑的讀取權限。
資料表 | (選擇性) 所指定儲存體帳戶中的 Azure 儲存體資料表 (如受保護組態中指定)，檔案「尾端」的新行會寫入至此資料表。
sinks | (選擇性) 將記錄行傳送至的額外接收名稱清單，以逗號分隔。

必須指定 "table" 或 "sinks"，或兩者。

## <a name="metrics-supported-by-the-builtin-provider"></a>內建提供者支援的計量

內建的計量提供者為一組廣泛使用者最感興趣的計量來源。 這些計量分成五大類別：

* 處理器
* 記憶體
* 網路
* Filesystem
* 磁碟

### <a name="builtin-metrics-for-the-processor-class"></a>處理器類別的內建計量

計量的處理器類別會提供 VM 中處理器使用量的相關資訊。 彙總百分比時，結果為所有 CPU 的平均。 在一個雙 vCPU VM 中，如果某個 vCPU 100% 忙碌，而另一個 100% 閒置，則回報的 PercentIdleTime 會是 50。 如果每個 vCPU 在同一期間皆為 50% 忙碌，則回報的結果也會是 50。 在一個四 vCPU VM 中，如果某個 vCPU 100% 忙碌，而其他皆閒置，則回報的 PercentIdleTime 會是 75。

counter | 意義
------- | -------
PercentIdleTime | 彙總時間範圍期間，處理器執行核心閒置迴圈的百分比
PercentProcessorTime | 執行非閒置執行緒的時間百分比
PercentIOWaitTime | 等待 IO 作業完成的時間百分比
PercentInterruptTime | 執行硬體/軟體中斷與 DPC (延遲的程序呼叫) 的時間百分比
PercentUserTime | 在彙總時間範圍期間的非閒置時間中，對一般優先順序以上的使用者花費時間的百分比
PercentNiceTime | 在非閒置時間中，對較低 (好) 的優先順序花費的時間百分比
PercentPrivilegedTime | 在非閒置時間中，在具特殊權限 (核心) 模式中花費的百分比

前四個計數器的總和應為 100%。 最後三個計數器的總和也是 100%，再細分 PercentProcessorTime、PercentIOWaitTime 與 PercentInterruptTime 的總和。

若要取得彙總所有處理器而得的單一計量，請設定 `"condition": "IsAggregate=TRUE"`。 若要取得特定處理器的計量 (例如四 vCPU VM 的第二個邏輯處理器)，請設定 `"condition": "Name=\\"1\\""`。 邏輯處理器數目在範圍 `[0..n-1]` 內。

### <a name="builtin-metrics-for-the-memory-class"></a>記處理器類別的內建計量

計量的記憶體類別提供有關記憶體使用率、分頁及交換的資訊。

counter | 意義
------- | -------
AvailableMemory | 可用的實體記憶體 (MiB)
PercentAvailableMemory | 以總記憶體百分比表示的可用實體記憶體
UsedMemory | 使用中實體記憶體 (MiB)
PercentUsedMemory | 以總記憶體百分比表示的使用中實體記憶體
PagesPerSec | 總分頁數 (讀取/寫入)
PagesReadPerSec | 從備份存放區讀取的分頁數 (分頁檔、程式檔、對應檔等)
PagesWrittenPerSec | 寫入備份存放區的頁面 (分頁檔、對應檔等)
AvailableSwap | 未使用的交換空間 (MiB)
PercentAvailableSwap | 以總交換空間百分比表示的未使用交換空間
UsedSwap | 已使用交換空間 (MiB)
PercentUsedSwap | 以總交換空間表示的使用中交換空間

此計量類別只有單一執行個體。 "condition" 屬性無有用的設定，應予以省略。

### <a name="builtin-metrics-for-the-network-class"></a>網路類別的內建計量

計量的網路類別提供自開機後，個別網路介面上網路活動的相關資訊。 LAD 不會公開頻寬計量，該計量可從主機計量中取出。

counter | 意義
------- | -------
BytesTransmitted | 自開機後傳送的位元組總數
BytesReceived | 自開機後收到的位元組總數
BytesTotal | 自開機後收傳送或收到的位元組總數
PacketsTransmitted | 自開機後傳送的封包總數
PacketsReceived | 自開機後收到的封包總數
TotalRxErrors | 自開機後接收的錯誤數
TotalTxErrors | 自開機後傳輸的錯誤數
TotalCollisions | 自開機後網路連接埠回報的衝突數目

 雖然此類別已執行個體化，但 LAD 不支援擷取從所有網路裝置彙總的網路計量。 若要取得特定介面 (例如 eth0) 的計量，請設定 `"condition": "InstanceID=\\"eth0\\""`。

### <a name="builtin-metrics-for-the-filesystem-class"></a>檔案系統類別的內建計量

計量的檔案系統類別提供檔案系統使用量的相關資訊。 當對一般使用者 (非根使用者) 顯示這些資訊時，系統會回報絕對值與百分比值。

counter | 意義
------- | -------
FreeSpace | 可用磁碟空間 (位元組)
UsedSpace | 已使用的磁碟空間 (位元組)
PercentFreeSpace | 可用空間百分比
PercentUsedSpace | 已使用的空間百分比
PercentFreeInodes | 未使用 inode 的百分比
PercentUsedInodes | 所有檔案系統中已配置 (使用中) inode 總和的百分比
BytesReadPerSecond | 每秒讀取的位元組數
BytesWrittenPerSecond | 每秒寫入的位元組數
每秒位元組 | 每秒讀取或寫入的位元組數
ReadsPerSecond | 每秒的讀取作業數
WritesPerSecond | 每秒的寫入作業數
TransfersPerSecond | 每秒的讀取或寫入作業數

可透過設定 `"condition": "IsAggregate=True"` 取得的所有檔案系統彙總值。 可透過設定 `"condition": 'Name="/mnt"'` 取得的特定已掛接檔案系統 (例如 "/mnt") 的值。 

**注意**：如果使用 Azure 入口網站而非 JSON，則正確的條件欄位形式為 Name='/mnt'

### <a name="builtin-metrics-for-the-disk-class"></a>磁碟類別的內建計量

計量的磁碟類別提供磁碟裝置使用量的相關資訊。 這些統計資料會套用到整個磁碟機。 如果裝置上有多個檔案系統，該裝置的計數器實際上是所有檔案系統的彙總。

counter | 意義
------- | -------
ReadsPerSecond | 每秒的讀取作業數
WritesPerSecond | 每秒的寫入作業數
TransfersPerSecond | 每秒的總作業數
AverageReadTime | 每個讀取作業的平均秒數
AverageWriteTime | 每個寫入作業的平均秒數
AverageTransferTime | 每個作業的平均秒數
AverageDiskQueueLength | 已排入佇列的磁碟作業平均數
ReadBytesPerSecond | 每秒讀取的位元組數
WriteBytesPerSecond | 每秒寫入的位元組數
每秒位元組 | 每秒讀取或寫入的位元組數

可透過設定 `"condition": "IsAggregate=True"` 取得的所有磁碟彙總值。 若要取得特定裝置的資訊 (例如，/dev/sdf1) 請設定 `"condition": "Name=\\"/dev/sdf1\\""`。

## <a name="installing-and-configuring-lad-30"></a>安裝和設定 LAD 3。0

### <a name="azure-cli"></a>Azure CLI

假設受保護的設定位於 ProtectedSettings.js的檔案中，而您的公用設定資訊在 PublicSettings.js中，請執行下列命令：

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

此命令假設您使用 Azure CLI 的 Azure 資源管理模式。 若要為傳統部署模型 (ASM) VM 設定 LAD，請切換成 "asm" 模式 (`azure config mode asm`)，並省略命令中的資源群組名稱。 如需詳細資訊，請參閱[跨平台 CLI 文件](/cli/azure/authenticate-azure-cli)。

### <a name="powershell"></a>PowerShell

假設受保護的設定位於 `$protectedSettings` 變數中，而您的公用設定資訊在 `$publicSettings` 變數中，請執行下列命令：

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="an-example-lad-30-configuration"></a>範例 LAD 3.0 組態

根據前述的定義，以下為範例 LAD 3.0 擴充功能及一些說明。 若要將此範例套用到您的案例，您應使用自己的儲存體帳戶、帳戶 SAS 權杖，以及 EventHubs SAS 權杖。

> [!NOTE]
> 根據您是否使用 Azure CLI 或 PowerShell 安裝 LAD，提供公用和受保護設定的方法會有所不同。 如果使用 Azure CLI，請將下列設定儲存至 ProtectedSettings.js開啟和 PublicSettings.js，以搭配上述的範例命令使用。 如果使用 PowerShell，請執行以將設定儲存至 `$protectedSettings` 和 `$publicSettings` `$protectedSettings = '{ ... }'` 。

### <a name="protected-settings"></a>受保護的設定

這些受保護的設定會設定：

* 儲存體帳戶
* 相符的帳戶 SAS 權杖
* 數個接收 (含 SAS 權杖的 JsonBlob 或 EventHubs)

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="public-settings"></a>公用設定

這些公用設定會造成 LAD：

* 將 percent-processor-time 與 used-disk-space 計量上傳至 `WADMetrics*` 資料表
* 將 Syslog 設備 "user" 與嚴重性 "info" 上傳至 `LinuxSyslog*` 資料表
* 將原始 OMI 查詢結果 (PercentProcessorTime 與 PercentIdleTime) 上傳至名為 `LinuxCPU` 的資料表
* 將檔案 `/var/log/myladtestlog` 中附加的行上傳至 `MyLadTestLog` 資料表

在每個案例中，也會將資料上傳至：

* Azure Blob 儲存體 (容器名稱如同 JsonBlob 接收中定義的名稱)
* EventHubs 端點 (如同在 EventHubs 接收中指定)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

組態中的 `resourceId` 必須與 VM 或虛擬機器擴展集中的相符。

* Azure 平台計量的圖表與警示功能知道您正在使用的 VM 其資源 ID。 該功能應使用查閱索引鍵的 resourceId 尋找您 VM 的資料。
* 如果您使用 Azure 自動調整，則自動調整組態中的 resourceId 必須符合 LAD 使用的 resourceId。
* resourceId 內建在 LAD 所寫入 JsonBlob 的名稱中。

## <a name="view-your-data"></a>檢視資料

使用 Azure 入口網站檢視效能資料或集合警示：

![螢幕擷取畫面顯示選取的計量上已使用的磁碟空間和產生的圖表的 Azure 入口網站。](./media/diagnostics-linux/graph_metrics.png)

`performanceCounters` 資料一律儲存在 Azure 儲存體資料表中。 Azure 儲存體 API 適用於許多語言與平台。

傳送到 JsonBlob 接收的資料會儲存在[受保護的設定](#protected-settings)中所指定儲存體帳戶的 Blob 中。 您可以使用任何 Azure Blob 儲存體 API 取用 Blob 資料。

此外，您可以使用這些 UI 工具存取 Azure 儲存體中的資料：

* Visual Studio 伺服器總管。
* [螢幕擷取畫面顯示 Azure 儲存體總管中的容器和資料表](https://azurestorageexplorer.codeplex.com/ "Azure 儲存體總管")。

Microsoft Azure 儲存體總管工作階段的這個快照顯示從測試 VM 上正確設定的 LAD 3.0 擴充功能產生的 Azure 儲存體資料表及容器。 影像不完全符合[範例 LAD 3.0 組態](#an-example-lad-30-configuration)。

![image](./media/diagnostics-linux/stg_explorer.png)

請參閱相關的 [EventHubs 資訊](../../event-hubs/event-hubs-about.md)，以了解如何取用發佈至 EventHubs 端點的訊息。

## <a name="next-steps"></a>後續步驟

* 在 [Azure 監視器](../../azure-monitor/platform/alerts-classic-portal.md)中為您收集的計量建立計量警示。
* 為您的計量建立[監視圖表](../../azure-monitor/platform/data-platform.md)。
* 了解如何使用計量[建立虛擬機器擴展集](../linux/tutorial-create-vmss.md)以控制自動調整。
