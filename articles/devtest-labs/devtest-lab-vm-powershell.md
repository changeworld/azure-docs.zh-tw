---
title: 使用 Azure PowerShell 在 DevTest Labs 中建立虛擬機器
description: 瞭解如何使用 Azure DevTest Labs 來建立和管理具有 Azure PowerShell 的虛擬機器。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6bcc1b8cfe1da7e5eafbee0af008a1defbe6f49d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85484106"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>使用 Azure PowerShell 建立具有 DevTest Labs 的虛擬機器
本文說明如何使用 Azure PowerShell 在 Azure DevTest Labs 中建立虛擬機器。 您可以使用 PowerShell 腳本，在 Azure DevTest Labs 的實驗室中自動建立虛擬機器。 

## <a name="prerequisites"></a>必要條件
開始之前：

- 如果您不想要使用現有的實驗室來測試本文中的腳本或命令，請[建立實驗室](devtest-lab-create-lab.md)。 
- [安裝 Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0)或使用已整合至 Azure 入口網站的 Azure Cloud Shell。 

## <a name="powershell-script"></a>PowerShell 指令碼
本節中的範例腳本會使用[AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) Cmdlet。  此 Cmdlet 會採用實驗室的資源識別碼、要執行的動作名稱（ `createEnvironment` ），以及執行該動作所需的參數。 這些參數是在包含所有虛擬機器描述屬性的雜湊表中。 

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

在上述腳本中，虛擬機器的屬性可讓我們以 Windows Server 2016 DataCenter 作為 OS 來建立虛擬機器。 針對每種類型的虛擬機器，這些屬性會稍有不同。 [定義虛擬機器](#define-virtual-machine)一節說明如何判斷要在此腳本中使用的屬性。

下列命令提供執行儲存在檔案名中之腳本的範例： Create-LabVirtualMachine.ps1。 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>定義虛擬機器
本節說明如何取得您想要建立之虛擬機器類型特有的屬性。 

### <a name="use-azure-portal"></a>使用 Azure 入口網站
在 Azure 入口網站中建立 VM 時，您可以產生 Azure Resource Manager 範本。 您不需要完成建立 VM 的程式。 您只會依照步驟執行，直到您看到範本為止。 如果您還沒有建立實驗室 VM，這是取得必要 JSON 描述的最佳方式。 

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
2. 選取左側導覽功能表上的 [**所有服務**]。
3. 搜尋並從服務清單中選取 [ **DevTest Labs** ]。 
4. 在 [ **DevTest Labs** ] 頁面的實驗室清單中，選取您的實驗室。
5. 在您實驗室的首頁上，選取工具列上的 [ **+ 新增**]。 
6. 選取 VM 的**基底映射**。 
7. 選取 [**提交**] 按鈕上方頁面底部的 [**自動化選項**]。 
8. 您會看到用來建立虛擬機器的**Azure Resource Manager 範本**。 
9. **Resources**區段中的 JSON 區段具有您稍早選取之映射類型的定義。 

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

在此範例中，您會看到如何取得 Azure marketplace 位置映射的定義。 您可以使用相同的方式來取得自訂映射、公式或環境的定義。 新增虛擬機器所需的任何構件，並設定所需的任何 advanced 設定。 在您提供必要欄位的值和任何選擇性欄位之後，再選取 [**自動化選項**] 按鈕。

### <a name="use-azure-rest-api"></a>使用 Azure REST API
下列程式提供使用 REST API 來取得映射屬性的步驟：這些步驟僅適用于實驗室中的現有 VM。 

1. 流覽至 [[虛擬機器清單](/rest/api/dtl/virtualmachines/list)] 頁面，選取 [**試試看**] 按鈕。 
2. 選取您的 **Azure 訂用帳戶**。
3. 輸入**實驗室的資源群組**。
4. 輸入**實驗室的名稱**。 
5. 選取 [執行]****。
6. 您會看到以 VM 為基礎所建立之**映射的屬性**。 

## <a name="set-expiration-date"></a>設定到期日期
在訓練、示範和試用等案例中，您可能會想要建立虛擬機器，並在固定期間之後自動加以刪除，這樣就不會產生不必要的成本。 您可以使用 PowerShell 來建立 VM 的到期日，如範例[powershell 腳本](#powershell-script)一節所示。

以下 PowerShell 腳本範例會針對實驗室中的所有現有 Vm 設定到期日：

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
請參閱下列內容：[適用于 Azure DevTest Labs 的 Azure PowerShell 檔](/powershell/module/az.devtestlabs/)
