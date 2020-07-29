---
title: 探索您的 Azure 資源
description: 了解使用 Resource Graph 查詢語言來瀏覽您的資源，並探索其連線的方式。
ms.date: 05/20/2020
ms.topic: conceptual
ms.openlocfilehash: 33bf457a57f7e62b9c99471bcb7676f62046f61d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654494"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>使用 Resource Graph 探索您的 Azure 資源

Azure Resource Graph 提供了快速且大規模地探索與找出 Azure 資源的功能。 專為快速回應而設計，很適合用來了解您的環境以及組成您的 Azure 資源的屬性。

## <a name="explore-virtual-machines"></a>探索虛擬機器

Azure 中的常見資源是虛擬機器。 作為一種資源類型，虛擬機器具有許多可查詢的屬性。 每個屬性都提供了一個篩選或尋找您正在尋找之資源的選項。

### <a name="virtual-machine-discovery"></a>虛擬機器探索

讓我們從一個簡單的查詢開始，以便從我們的環境中取得單一 VM，並查看傳回的屬性。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> 根據預設，Azure PowerShell `Search-AzGraph` Cmdlet 會傳回 **PSCustomObject**。 若要讓輸出看起來與 Azure CLI 所傳回的內容相同，可使用 `ConvertTo-Json` Cmdlet。 **Depth** 的預設值是 _2_。 若將其設定為 _100_，則應該會轉換所有傳回的層級。

JSON 結果的結構類似於下列範例：

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

屬性告訴我們有關虛擬機器資源本身的其他資訊，包括 SKU、作業系統、磁碟，標記以及它所屬的資源群組和訂用帳戶。

### <a name="virtual-machines-by-location"></a>依位置劃分的虛擬機器

根據我們對虛擬機器資源的了解，讓我們使用 **location** 屬性依位置計算所有虛擬機器。 若要更新查詢，我們將移除限制並摘要說明位置值的計數。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

JSON 結果的結構類似於下列範例：

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

我們現在可以看到每個 Azure 區域中有多少虛擬機器。

### <a name="virtual-machines-by-sku"></a>依 SKU 列出的虛擬機器

回到原始的虛擬機器屬性，讓我們嘗試尋找 SKU 大小為 **Standard_B2s** 的所有虛擬機器。 查看傳回的 JSON，我們會看到它儲存在 **properties.hardwareprofile.vmsize** 中。 我們將更新查詢以尋找符合這個大小的所有 VM，並僅傳回 VM 和區域的名稱。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>連線至進階受控磁碟的虛擬機器

如果我們想要取得連結到這些 **Standard_B2s** 虛擬機器的進階受控磁碟詳細資訊，可以展開查詢以提供我們這些受控磁碟的資源識別碼。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> 取得 SKU 的另一種方法是使用 **aliases** 屬性 **Microsoft.Compute/virtualMachines/sku.name**。 請參閱[顯示別名](../samples/starter.md#show-aliases)和[顯示不同的別名值](../samples/starter.md#distinct-alias-values)範例。

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

結果是一份磁碟識別碼清單。

### <a name="managed-disk-discovery"></a>受控磁碟探索

透過上一個查詢中的第一筆記錄，我們將探索連結到第一個虛擬機器的受控磁碟上存在的屬性。 更新的查詢使用磁碟識別碼並變更類型。

上一個查詢的範例輸出，例如：

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
Resources
| where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

在執行查詢之前，我們怎麼知道 **type** 現在應該是 **Microsoft.Compute/disks**？
如果您查看完整的識別碼，您會看到 **/providers/Microsoft.Compute/disks/** 作為字串的一部分。 此字串片段會為您提供要搜尋之類型的提示。 替代方法是依類型移除限制，而只依 [識別碼] 欄位搜尋。 由於識別碼是唯一的，因此只會傳回一筆記錄，並且其上的 **type** 屬性會提供詳細資料。

> [!NOTE]
> 若要使此範例正常運作，必須使用您自己的環境中的結果取代 [識別碼] 欄位。

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

JSON 結果的結構類似於下列範例：

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>瀏覽虛擬機器以尋找公用 IP 位址

這組查詢會先尋找及儲存所有連線到虛擬機器的網路介面 (NIC) 資源。 然後查詢會使用 NIC 清單來尋找每個屬於公用 IP 位址的 IP 位址資源，並儲存這些值。 最後，查詢會提供公用 IP 位址清單。

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nics.txt' file
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

```azurepowershell-interactive
# Use Resource Graph to get all NICs and store in the $nics variable
$nics = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20"

# Review the output of the query stored in the variable
$nics.nic
```

在下一個查詢中使用檔案 (Azure CLI) 或變數 (Azure PowerShell) 來取得相關的網路介面資源詳細資料，其中有一個公用 IP 位址附加至 NIC。

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

```azurepowershell-interactive
# Use Resource Graph  with the $nics variable to get all related public IP addresses and store in $ips variable
$ips = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$($nics.nic -join "','")') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp"

# Review the output of the query stored in the variable
$ips.publicIp
```

最後，使用檔案 (Azure CLI) 或變數 (Azure PowerShell) 中儲存的公用 IP 位址資源清單，從相關的物件中取得實際的公用 IP 位址並顯示。

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip"
```

若要查看如何使用 `join` 運算子在單一查詢中完成這些步驟，請參閱[列出虛擬機器與其網路介面及公用 IP](../samples/advanced.md#join-vmpip) 範例。

## <a name="next-steps"></a>後續步驟

- 深入了解[查詢語言](query-language.md)。
- 請參閱[入門查詢](../samples/starter.md)中使用的語言。
- 請參閱[進階查詢](../samples/advanced.md)中的進階使用方式。
