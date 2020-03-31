---
title: 使用 Azure PowerShell 在 DevTest 實驗室中創建虛擬機器
description: 瞭解如何使用 Azure 開發人員測試實驗室使用 Azure PowerShell 創建和管理虛擬機器。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 13014c39641203bddadf858c34cff67462b3a4b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167101"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>使用 Azure PowerShell 使用開發人員測試實驗室創建虛擬機器
本文介紹如何使用 Azure PowerShell 在 Azure 開發人員測試實驗室中創建虛擬機器。 您可以使用 PowerShell 腳本在 Azure 開發人員測試實驗室的實驗室中自動創建虛擬機器。 

## <a name="prerequisites"></a>Prerequisites
開始之前：

- 如果不想使用現有實驗室來測試本文中的腳本或命令，[請創建一個實驗室](devtest-lab-create-lab.md)。 
- [安裝 Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0)或使用集成到 Azure 門戶中的 Azure 雲外殼。 

## <a name="powershell-script"></a>PowerShell 指令碼
本節中的示例腳本使用[Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) Cmdlet。  此 Cmdlet 獲取實驗室的資源識別碼、要執行的操作的名稱 （`createEnvironment`），以及執行該操作所需的參數。 參數位於包含所有虛擬機器描述屬性的雜湊表中。 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "expirationDate"          = "2019-12-01"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

上述腳本中虛擬機器的屬性允許我們創建一個虛擬機器，該虛擬機器以 Windows Server 2016 資料中心為作業系統。 對於每種類型的虛擬機器，這些屬性將略有不同。 "[定義虛擬機器](#define-virtual-machine)"部分演示如何確定在此腳本中使用哪些屬性。

以下命令提供了運行保存在檔案名中的腳本的示例：Create-LabVirtualMachine.ps1。 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>定義虛擬機器
本節介紹如何獲取特定于要創建的虛擬機器類型的屬性。 

### <a name="use-azure-portal"></a>使用 Azure 入口網站
在 Azure 門戶中創建 VM 時，可以生成 Azure 資源管理器範本。 您無需完成創建 VM 的過程。 您只按照這些步驟操作，直到看到範本。 如果尚未創建實驗室 VM，則這是獲取必要 JSON 說明的最佳方式。 

1. 導航到[Azure 門戶](https://portal.azure.com)。
2. 選擇左側導航功能表上**的所有服務**。
3. 從服務清單中搜索並選擇**DevTest 實驗室**。 
4. 在**DevTest 實驗室**頁面上，在實驗室清單中選擇您的實驗室。
5. 在實驗室的主頁上，選擇 **"添加"** 在工具列上。 
6. 為 VM 選擇**基本映射**。 
7. 選擇 **"提交"** 按鈕上方的頁面底部的**自動化選項**。 
8. 您將看到用於創建虛擬機器的**Azure 資源管理器範本**。 
9. **資源**部分中的 JSON 段具有您之前選擇的圖像類型的定義。 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

在此示例中，您將瞭解如何獲取 Azure 市場位置圖像的定義。 您可以以同樣的方式獲取自訂圖像、公式或環境的定義。 添加虛擬機器所需的任何工件，並設置所需的任何高級設置。 在選擇 **"自動化"選項**按鈕之前，為所需欄位和任何可選欄位提供值。

### <a name="use-azure-rest-api"></a>使用 Azure REST API
以下過程提供了使用 REST API 獲取映射屬性的步驟：這些步驟僅適用于實驗室中的現有 VM。 

1. 導航到[虛擬機器 - 清單](/rest/api/dtl/virtualmachines/list)頁，選擇 **"試用"** 按鈕。 
2. 選擇**Azure 訂閱**。
3. 輸入**實驗室的資源組**。
4. 輸入**實驗室的名稱**。 
5. 選擇 **"運行**"。
6. 您將看到基於創建 VM 的圖像**的屬性**。 

## <a name="set-expiration-date"></a>設定到期日期
在培訓、演示和試用等方案中，您可能希望創建虛擬機器，並在固定持續時間後自動刪除虛擬機器，這樣您就不會產生不必要的成本。 您可以使用 PowerShell[腳本示例部分](#powershell-script)所示，在使用 PowerShell 創建 VM 時設置到期日期。

下面是一個示例 PowerShell 腳本，該腳本設置實驗室中所有現有 VM 的到期日期：

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>後續步驟
請參閱以下內容[：Azure 開發人員測試實驗室的 Azure PowerShell 文檔](/powershell/module/az.devtestlabs/)
