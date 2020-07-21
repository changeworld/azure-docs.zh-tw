---
title: 如何使用 Packer 建立 Windows VM 映像
description: 了解如何在 Azure 中使用 Packer 建立 Windows 虛擬機器的映像
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: article
ms.workload: infrastructure
ms.date: 02/22/2019
ms.author: cynthn
ms.openlocfilehash: 14b2e3df6d7ea3f72c1968cfed222a1b9b0d636d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525852"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>如何在 Azure 中使用 Packer 來建立 Windows 虛擬機器映像
Azure 中的每個虛擬機器 (VM) 都是透過映像所建立，而映像則會定義 Windows 散發套件和作業系統版本。 映像中可包含預先安裝的應用程式與組態。 Azure Marketplace 提供了許多第一方和第三方映像，這些映像適用於最常見的作業系統和應用程式環境，而您也可以建立自己自訂的映像，以符合您的需求。 本文詳述如何使用開放原始碼工具 [Packer](https://www.packer.io/) \(英文\)，在 Azure 中定義和建置自訂映像。

本文最後一次測試是在 2019 年 2 月 21 日，使用 [Az PowerShell 模組](/powershell/azure/install-az-ps) 1.3.0 版和 [Packer](https://www.packer.io/docs/install) 1.3.4 版進行的。

> [!NOTE]
> Azure 現在有一個服務，也就是 Azure Image Builder (預覽)，用來定義和建立您自己的自訂映像。 Azure Image Builder 建置在 Packer 上，因此您甚至可以搭配使用現有的 Packer 殼層佈建指令碼。 若要開始使用 Azure Image Builder，請參閱[使用 Azure Image Builder 建立 Windows VM](image-builder.md)。

## <a name="create-azure-resource-group"></a>建立 Azure 資源群組
建置程序進行期間，Packer 會在建置來源 VM 時建立暫存的 Azure 資源。 若要擷取該來源 VM 以作為映像，您必須定義資源群組。 Packer 建置程序所產生的輸出會儲存在此資源群組中。

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 來建立資源群組。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```azurepowershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>建立 Azure 認證
Packer 會使用服務主體來向 Azure 驗證。 Azure 服務主體是安全性識別，可供您與應用程式、服務及諸如 Packer 等自動化工具搭配使用。 您可以控制和定義對於服務主體可以在 Azure 中執行哪些作業的權限。

使用 [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) 建立服務主體，並為服務主體指派權限以便使用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 來建立和管理資源。 `-DisplayName` 的值必須是唯一的；視需求將其取代為您自己的值。  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerServicePrincipal"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

然後輸出密碼和應用程式識別碼。

```powershell
$plainPassword
$sp.ApplicationId
```


若要向 Azure 驗證，您還需要使用 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) 取得 Azure 租用戶與訂用帳戶識別碼：

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>定義 Packer 範本
若要建置映像，您可以將範本建立為 JSON 檔案。 在此範本中，您必須定義產生器和佈建程式，由它們執行實際的建置程序。 Packer 具有[適用於 Azure 的產生器](https://www.packer.io/docs/builders/azure.html)，以供您定義 Azure 資源，例如上述步驟所建立的服務主體認證。

建立名為 windows.json 的檔案，並貼入下列內容。 針對下列參數輸入您自己的值︰

| 參數                           | 取得位置 |
|-------------------------------------|----------------------------------------------------|
| client_id                         | 檢視具有 `$sp.applicationId` 的服務主體識別碼 |
| client_secret                     | 使用 `$plainPassword` 檢視自動產生的密碼 |
| tenant_id                         | `$sub.TenantId` 命令所產生的輸出 |
| *subscription_id*                   | `$sub.SubscriptionId` 命令所產生的輸出 |
| managed_image_resource_group_name | 您在第一個步驟中建立的資源群組名稱 |
| managed_image_name                | 所建立之受控磁碟映像的名稱 |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "5m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_D2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
      "while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }",
      "while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

此範本會建置 Windows Server 2016 VM、安裝 IIS，然後使用 Sysprep 將 VM 一般化。 IIS 安裝會示範如何使用 PowerShell 佈建程式來執行其他命令。 最終的 Packer 映像則會包含必要的軟體安裝和設定。

Windows 來賓代理程式會參與 Sysprep 進程。 必須完整安裝代理程式，然後才能以 sysprep 準備 VM。 若要確保這是 true，在執行 sysprep.exe 之前，所有 agent 服務都必須執行。 前面的 JSON 程式碼片段會顯示在 PowerShell 布建程式中執行這項操作的一種方式。 只有在 VM 設定為安裝代理程式（預設值）時，才需要此程式碼片段。


## <a name="build-packer-image"></a>建置 Packer 映像
如果您尚未在本機電腦上安裝 Packer，請[遵循 Packer 安裝指示](https://learn.hashicorp.com/packer/getting-started/install)。

透過開啟命令提示字元並指定 Packer 範本檔案以建置映像，如下所示：

```
./packer build windows.json
```

上述命令的輸出範例如下所示：

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Packer 需要幾分鐘的時間來建置 VM、執行佈建程式並清除部署。


## <a name="create-a-vm-from-the-packer-image"></a>從 Packer 映像建立 VM
您現在可以使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 從您的映像建立 VM。 如果支援網路資源尚未存在，則會加以建立。 出現提示時，輸入要在 VM 上建立的系統管理使用者名稱和密碼。 下列範例會從 *myPackerImage* 建立名為 *myVM* 的 VM：

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

如果想要在不同於您 Packer 映像的資源群組或區域中建立 VM，請指定映像識別碼，而非映像名稱。 您可以使用 [Get-AzImage](/powershell/module/az.compute/get-azimage) 取得映像識別碼。

從 Packer 映像建立虛擬機器需要幾分鐘的時間。


## <a name="test-vm-and-webserver"></a>測試 VM 和網頁伺服器
使用 [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) 取得 VM 的公用 IP 位址。 下列範例會取得稍早建立的 myPublicIP IP 位址︰

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

若要查看包含來自 Packer 佈建程式之 IIS 安裝的 VM，請在網頁瀏覽器中輸入公用 IP 位址。

![IIS 預設網站](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>後續步驟
您也可以使用現有的 Packer 佈建指令碼與 [Azure Image Builder](image-builder.md) 搭配使用。
