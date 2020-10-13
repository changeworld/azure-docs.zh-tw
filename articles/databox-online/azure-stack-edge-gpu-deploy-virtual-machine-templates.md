---
title: 透過範本在您的 Azure Stack Edge Pro 裝置上部署 Vm
description: 說明如何使用範本，在 Azure Stack Edge Pro 裝置上建立和管理 (Vm) 的虛擬機器。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: eeefbcdc080620c60f7cd49b8f749375e23ddd02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899705"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-templates"></a>透過範本在您的 Azure Stack Edge Pro GPU 裝置上部署 Vm

本教學課程說明如何使用範本在 Azure Stack Edge Pro 裝置上建立和管理 VM。 這些範本 JavaScript 物件標記法 (的 JSON) 檔案，這些檔案會定義您 VM 的基礎結構和設定。 在這些範本中，您會指定要部署的資源，以及這些資源的屬性。

範本在不同的環境中有彈性，因為它們可以在執行時間從檔案取得參數做為輸入。 標準命名結構適用 `TemplateName.json` 于範本和參數檔案 `TemplateName.parameters.json` 。 如需 ARM 範本的詳細資訊，請移至 [什麼是 Azure Resource Manager 範本？](../azure-resource-manager/templates/overview.md)。

在本教學課程中，我們將使用預先撰寫的範例範本來建立資源。 您不需要編輯範本檔案，也可以只修改檔案 `.parameters.json` 來自訂您電腦的部署。 

## <a name="vm-deployment-workflow"></a>VM 部署工作流程

若要在多部裝置上部署 Azure Stack Edge Pro Vm，您可以使用單一執行過 sysprep VHD 來取得完整的車隊、部署相同的範本，以及對每個部署位置的參數進行次要變更， (這些變更可能會在這裡或以程式設計方式手動完成。 )  

使用範本的部署工作流程高層級摘要如下所示：

1. **設定必要條件** -有3種類型的必要條件;裝置、用戶端和 VM。

    1. **裝置必要條件**

        1. 連接到裝置上的 Azure Resource Manager。
        2. 透過本機 UI 啟用計算。

    2. **用戶端先決條件**

        1. 在用戶端上下載 VM 範本和相關聯的檔案。
        1. 選擇性地在用戶端上設定 TLS 1.2。
        1. [下載並](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) 在您的用戶端上安裝 Microsoft Azure 儲存體總管。

    3. **VM 必要條件**

        1. 在裝置位置中建立將包含所有 VM 資源的資源群組。
        1. 建立儲存體帳戶，以上傳用來建立 VM 映射的 VHD。
        1. 將本機儲存體帳戶 URI 新增至存取您裝置的用戶端上的 DNS 或 hosts 檔案。
        1. 將 blob 儲存體憑證安裝在裝置上，以及在存取您裝置的本機用戶端上。 （選擇性）將 blob 儲存體憑證安裝在儲存體總管上。
        1. 建立 VHD 並將其上傳至稍早建立的儲存體帳戶。

2. **從範本建立 VM**

    1. 使用 `CreateImageAndVnet.parameters.json` 參數檔案和部署範本來建立 VM 映射和 VNet `CreateImageAndVnet.json` 。
    1. 使用 `CreateVM.parameters.json` 參數檔案和部署範本建立先前建立資源的 VM  `CreateVM.json` 。

## <a name="device-prerequisites"></a>裝置必要條件

在您的 Azure Stack Edge Pro 裝置上設定這些必要條件。

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>用戶端先決條件

在您的用戶端上設定將用來存取 Azure Stack Edge Pro 裝置的必要條件。

1. 如果您要使用它來上傳 VHD，請[下載儲存體總管](https://azure.microsoft.com/features/storage-explorer/)。 或者，您也可以下載 AzCopy 來上傳 VHD。 如果執行舊版的 AzCopy，您可能需要在用戶端電腦上設定 TLS 1.2。 
1. 將[VM 範本和參數檔案下載](https://aka.ms/ase-vm-templates)至您的用戶端電腦。 將它解壓縮至您將作為工作目錄的目錄。


## <a name="vm-prerequisites"></a>VM 必要條件

設定這些必要條件，以建立 VM 建立所需的資源。 

    
### <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 建立 Azure 資源群組。 資源群組是一個邏輯容器，可在其中部署及管理 Azure 資源（例如儲存體帳戶、磁片、受控磁片）。

> [!IMPORTANT]
> 所有資源會建立在與裝置相同的位置中，且位置會設定為 **DBELocal**。

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

下方顯示一項範例輸出。

```powershell
PS C:\windows\system32> New-AzureRmResourceGroup -Name myasegpurgvm -Location DBELocal

ResourceGroupName : myasegpurgvm
Location          : dbelocal
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/DDF9FC44-E990-42F8-9A91-5A6A5CC472DB/resourceGroups/myasegpurgvm

PS C:\windows\system32>
```

### <a name="create-a-storage-account"></a>建立儲存體帳戶

使用上一個步驟中建立的資源群組來建立新的儲存體帳戶。 這是 **本機儲存體帳戶** ，將用來上傳 VM 的虛擬磁片映射。

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> 只有本機儲存體帳戶（例如本機存放區的儲存體 (Standard_LRS 或 Premium_LRS) 可透過 Azure Resource Manager 建立。 若要建立階層式儲存體帳戶，請參閱 [新增的步驟，連接到您 Azure Stack Edge Pro 上的儲存體帳戶](azure-stack-edge-j-series-deploy-add-storage-accounts.md)。

下方顯示一項範例輸出。

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

若要取得儲存體帳戶金鑰，請執行 `Get-AzureRmStorageAccountKey` 命令。 此命令的範例輸出如下所示。

```powershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasegpurgvm
Name: myasegpusavm

KeyName    Value                                                  Permissions   
-------     -----                                                   --
key1 GsCm7QriXurqfqx211oKdfQ1C9Hyu5ZutP6Xl0dqlNNhxLxDesDej591M8y7ykSPN4fY9vmVpgc4ftkwAO7KQ== 11 
key2 7vnVMJUwJXlxkXXOyVO4NfqbW5e/5hZ+VOs+C/h/ReeoszeV+qoyuBitgnWjiDPNdH4+lSm1/ZjvoBWsQ1klqQ== ll
```

### <a name="add-blob-uri-to-hosts-file"></a>將 Blob URI 新增至 hosts 檔案

確定您已針對用來連線到 Blob 儲存體的用戶端，在 [主機] 檔案中新增 blob URI。 **以系統管理員身分執行 [記事本** ]，並在中為 blob URI 新增下列專案 `C:\windows\system32\drivers\etc\hosts` ：

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

在一般環境中，您會設定您的 DNS，讓所有儲存體帳戶指向 Azure Stack Edge Pro 裝置，並 `*.blob.devicename.domainname.com` 輸入一個專案。

### <a name="optional-install-certificates"></a> (選用) 安裝憑證

如果您將透過儲存體總管使用 *HTTP*連線，請略過此步驟。 如果您使用 *HTTPs*，則需要在儲存體總管中安裝適當的憑證。 在此情況下，請安裝 blob 端點憑證。 如需詳細資訊，請參閱如何在 [管理憑證](azure-stack-edge-j-series-manage-certificates.md)中建立和上傳憑證。 

### <a name="create-and-upload-a-vhd"></a>建立和上傳 VHD

請確定您有可在稍後的步驟中用來上傳的虛擬磁片映射。 遵循 [建立 VM 映射](azure-stack-edge-j-series-create-virtual-machine-image.md)中的步驟。 

複製您在先前步驟中建立之本機儲存體帳戶中的分頁 blob 所使用的任何磁片映射。 您可以使用 [儲存體總管](https://azure.microsoft.com/features/storage-explorer/) 或 AzCopy 之類的工具，將 [VHD 上傳至](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md#upload-a-vhd) 您在先前步驟中建立的儲存體帳戶。 

### <a name="use-storage-explorer-for-upload"></a>使用儲存體總管進行上傳

1. 開啟儲存體總管。 移至 [ **編輯** ]，並確定應用程式已設為 **目標 Azure Stack api**。

    ![將目標設定為 Azure Stack Api](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. 以 PEM 格式安裝用戶端憑證。 移至 **> 匯入憑證來編輯 > SSL 憑證**。 指向用戶端憑證。

    ![匯入 blob 儲存體端點憑證](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - 如果您使用裝置產生的憑證，請下載 blob 儲存體端點憑證，並將其轉換成 `.cer` `.pem` 格式。 執行下列命令。 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - 如果您要攜帶自己的憑證，請使用格式的簽署鏈根憑證 `.pem` 。

3. 匯入憑證之後，請重新開機儲存體總管，變更才會生效。

    ![重新啟動儲存體總管](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. 在左窗格中，以滑鼠右鍵按一下 [ **儲存體帳戶** ]，然後選取 **[連接到 Azure 儲存體]**。 

    ![連接到 Azure 儲存體1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. 選取 [使用儲存體帳戶名稱和金鑰]****。 選取 [下一步]  。

    ![連接到 Azure 儲存體2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. 在 [ **使用名稱和金鑰連接]** 中，提供 **顯示名稱**、 **儲存體帳戶名稱**Azure 儲存體 **帳戶金鑰**。 選取 **其他** 儲存體網域，然後提供 `<device name>.<DNS domain>` 連接字串。 如果您未在儲存體總管中安裝憑證，請核取 [ **使用 HTTP** ] 選項。 選取 [下一步]  。

    ![使用名稱和金鑰連接](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. 檢查 **連接摘要** ，然後選取 **[連接]**。

8. 儲存體帳戶會出現在左窗格中。 選取並展開儲存體帳戶。 選取 [ **blob 容器**]，以滑鼠右鍵按一下並選取 [ **建立 blob 容器**]。 提供 blob 容器的名稱。

9. 選取您剛才建立的容器，並在右窗格中選取 [ **上傳] > 上傳**檔案。 

    ![上傳 VHD 檔案1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. 流覽並指向您想要在 **選取**的檔案中上傳的 VHD。 選取 **blob 類型** 作為 **分頁 blob** ，然後選取 **[上傳**]。

    ![上傳 VHD 檔案2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. 將 VHD 載入至 blob 容器之後，請選取 VHD、按一下滑鼠右鍵，然後選取 [ **屬性**]。 

    ![上傳 VHD 檔案3](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. 複製並儲存 Uri，因為您將在稍後的步驟中使用此 **Uri** 。

    ![複製 URI](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)

<!--### Use AzCopy for upload

Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you are using with your Azure Stack Edge Pro device.


```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> ![NOTE]
> Set `BlobType` to page for creating a managed disk out of VHD. Set `BlobType` to block when writing to tiered storage accounts using AzCopy.

You can download the disk images from the marketplace. For detailed steps, go to [Get the virtual disk image from Azure marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

A sample output using AzCopy 7.3 is shown below. For more information on this command, go to [Upload VHD file to storage account using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```-->

## <a name="create-image-and-vnet-for-your-vm"></a>為您的 VM 建立映射和 VNet

若要為您的 VM 建立映射和虛擬網路，您必須編輯參數檔案 `CreateImageAndVnet.parameters.json` ，然後部署 `CreateImageAndVnet.json` 使用此參數檔案的範本。


### <a name="edit-parameters-file"></a>編輯參數檔案

檔案 `CreateImageAndVnet.parameters.json` 會使用下列參數： 

```json
"parameters": {
        "osType": {
              "value": "<Operating system corresponding to the VHD you upload can be Windows or Linux>"
        },
        "imageName": {
            "value": "<Name for the VM iamge>"
        },
        "imageUri": {
              "value": "<Path to the VHD that you uploaded in the Storage account>"
        },
        "vnetName": {
            "value": "<Name for the virtual network where you will deploy the VM>"
        },
        "subnetName": {
            "value": "<Name for the subnet for the VNet>"
        },
        "addressPrefix": {
            "value": "<Address prefix for the virtual network>"
        },
        "subnetPrefix": {
            "value": "<Subnet prefix for the subnet for the Vnet>"
        }
    }
```

編輯檔案 `CreateImageAndVnet.parameters.json` ，以針對您的 Azure Stack Edge Pro 裝置包含下列內容：

1. 提供與您將上傳的 VHD 對應的作業系統類型。 OS 類型可以是 Windows 或 Linux。

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. 將映射 URI 變更為您在先前的步驟中上傳的映射 URI：

    ```json
    "imageUri": {
        "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
    ```
    如果您使用 *HTTP* 搭配儲存體總管，請將此變更為 *HTTPs* URI。

3. 變更 `addressPrefix` 和 `subnetPrefix` 。 在裝置的本機 UI 中，移至 [ **網路** ] 頁面。 尋找您為計算啟用的埠。 取得基本網路的 IP 位址，並新增子網路遮罩以建立 CIDR 標記法。 如果您有標準255.255.255.0 子網，請將 IP 位址的最後一個數位取代為0，並在結尾加上/24。 因此，具有255.255.255.0 子網路遮罩的10.126.68.0 會變成 10.126.68.0/24。 
    
    ```json
    "addressPrefix": {
                "value": "10.126.68.0/24"
            },
            "subnetPrefix": {
                "value": "10.126.68.0/24"
            }
    ```  

4. 為參數提供唯一的映射名稱、VNet 名稱和子網名稱。

    以下是本文中使用的範例 json。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Windows"
        },
        "imageName": {
          "value": "image1"
        },
        "imageUri": {
          "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
        "vnetName": {
          "value": "vnet1"
        },
        "subnetName": {
          "value": "subnet1"
        },
        "addressPrefix": {
          "value": "10.126.68.0/24"
        },
        "subnetPrefix": {
          "value": "10.126.68.0/24"
        }
      }
    }
    ```
5. 儲存參數檔案。


### <a name="deploy-template"></a>部署範本 

部署範本 `CreateImageAndVnet.json` 。 此範本會部署將在稍後步驟中用來建立 Vm 的 VNet 和映射資源。

> [!NOTE]
> 當您在收到驗證錯誤時部署範本時，此會話的 Azure 認證可能已過期。 重新執行 `login-AzureRM` 命令，以再次連接到 Azure Stack Edge Pro 裝置上的 Azure Resource Manager。

1. 執行以下命令： 
    
    ```powershell
    $templateFile = "Path to CreateImageAndVnet.json"
    $templateParameterFile = "Path to CreateImageAndVnet.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```

2. 檢查是否已成功布建映射和 VNet 資源。 以下是已成功建立的映射和 VNet 的範例輸出。
    
    ```powershell
    PS C:\07-30-2020> login-AzureRMAccount -EnvironmentName aztest1 -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest1
    
    PS C:\07-30-2020> $templateFile = "C:\07-30-2020\CreateImageAndVnet.json"
    PS C:\07-30-2020> $templateParameterFile = "C:\07-30-2020\CreateImageAndVnet.parameters.json"
    PS C:\07-30-2020> $RGName = "myasegpurgvm"
    PS C:\07-30-2020> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment1"
    
    DeploymentName          : Deployment1
    ResourceGroupName       : myasegpurgvm
    ProvisioningState       : Succeeded
    Timestamp               : 7/30/2020 5:53:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Windows
                              imageName        String                     image1
                              imageUri         String
                              https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd
                              vnetName         String                     vnet1
                              subnetName       String                     subnet1
                              addressPrefix    String                     10.126.68.0/24
                              subnetPrefix     String                     10.126.68.0/24
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\07-30-2020>
    ```
    
## <a name="create-vm"></a>建立 VM

### <a name="edit-parameters-file-to-create-vm"></a>編輯參數檔案以建立 VM
 
若要建立 VM，請使用 `CreateVM.parameters.json` 參數檔案。 它會採用下列參數。
    
```json
"vmName": {
            "value": "<Name for your VM>"
        },
        "adminUsername": {
            "value": "<Username to log into the VM>"
        },
        "Password": {
            "value": "<Password to log into the VM>"
        },
        "imageName": {
            "value": "<Name for your image>"
        },
        "vmSize": {
            "value": "<A supported size for your VM>"
        },
        "vnetName": {
            "value": "<Name for the virtual network you created earlier>"
        },
        "subnetName": {
            "value": "<Name for the subnet you created earlier>"
        },
        "nicName": {
            "value": "<Name for the network interface>"
        },
        "privateIPAddress": {
            "value": "<Private IP address, enter a static IP in the subnet created earlier or leave empty to assign DHCP>"
        },
        "IPConfigName": {
            "value": "<Name for the ipconfig associated with the network interface>"
        }
```    

在中 `CreateVM.parameters.json` 為您的 Azure Stack Edge Pro 裝置指派適當的參數。

1. 提供唯一的名稱、網路介面名稱和 ipconfig 名稱。 
1. 輸入使用者名稱、密碼和支援的 VM 大小。
1. 為 **VnetName**、 **subnetName**和 **ImageName** 提供相同的名稱，如同的參數中所指定 `CreateImageAndVnet.parameters.json` 。 例如，如果您將 VnetName、subnetName 和 ImageName 指定為 **vnet1**、 **subnet1**和 **image1**，也請將這些值與此範本中的參數保持相同。
1. 現在您將需要一個靜態 IP 位址，以指派給上面定義之子網網路中的 VM。 將 **PrivateIPAddress** 取代為參數檔案中的這個位址。 若要讓 VM 從您的本機 DCHP 伺服器取得 IP 位址，請將此 `privateIPAddress` 值保留空白。  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. 儲存參數檔案。

    以下是本文中使用的範例 json。
    
    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "vmName": {
                "value": "mywindowsvm"
            },
            "adminUsername": {
                "value": "Administrator"
            },
            "Password": {
                "value": "Password1"
            },
            "imageName": {
                "value": "image1"
            },
            "vmSize": {
                "value": "Standard_D1_v2"
            },
            "vnetName": {
                "value": "vnet1"
            },
            "subnetName": {
                "value": "subnet1"
            },
            "nicName": {
                "value": "nic1"
            },
            "privateIPAddress": {
                "value": "10.126.68.186"
            },
            "IPConfigName": {
                "value": "ipconfig1"
            }
        }
    }
    ```      

### <a name="deploy-template-to-create-vm"></a>部署範本以建立 VM

部署 VM 建立範本 `CreateVM.json` 。 此範本會從現有的 VNet 建立網路介面，並從已部署的映射建立 VM。

1. 執行以下命令： 
    
    ```powershell
    Command:
        
        $templateFile = "<Path to CreateVM.json>"
        $templateParameterFile = "<Path to CreateVM.parameters.json>"
        $RGName = "<Resource group name>"
             
        New-AzureRmResourceGroupDeployment `
            -ResourceGroupName $RGName `
            -TemplateFile $templateFile `
            -TemplateParameterFile $templateParameterFile `
            -Name "<DeploymentName>"
    ```   

    建立 VM 將需要15-20 分鐘的時間。 以下是已成功建立 VM 的範例輸出。
    
    ```powershell
    PS C:\07-30-2020> $templateFile = "C:\07-30-2020\CreateWindowsVM.json"
        PS C:\07-30-2020> $templateParameterFile = "C:\07-30-2020\CreateWindowsVM.parameters.json"
        PS C:\07-30-2020> $RGName = "myasegpurgvm"
        PS C:\07-30-2020> New-AzureRmResourceGroupDeployment `
        >>     -ResourceGroupName $RGName `
        >>     -TemplateFile $templateFile `
        >>     -TemplateParameterFile $templateParameterFile `
        >>     -Name "Deployment2"    
        
        DeploymentName          : Deployment2
        ResourceGroupName       : myasegpurgvm
        ProvisioningState       : Succeeded
        Timestamp               : 7/30/2020 6:21:09 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                  Name             Type                       Value
                                  ===============  =========================  ==========
                                  vmName           String                     MyWindowsVM
                                  adminUsername    String                     Administrator
                                  password         String                     Password1
                                  imageName        String                     image1
                                  vmSize           String                     Standard_D1_v2
                                  vnetName         String                     vnet1
                                  subnetName       String                     subnet1
                                  nicName          String                     Nic1
                                  ipConfigName     String                     ipconfig1
                                  privateIPAddress  String                    10.126.68.186
        
        Outputs                 :
        DeploymentDebugLogLevel :    
        
        PS C:\07-30-2020>
    ```   
您也可以 `New-AzureRmResourceGroupDeployment` 使用參數以非同步方式執行命令 `–AsJob` 。 以下是當 Cmdlet 在背景中執行時的範例輸出。 然後，您可以查詢使用指令程式所建立之作業的狀態 `Get-Job` 。

    ```powershell   
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment2" `
    >>     -AsJob
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    2      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmResourceGro...
     
    PS C:\WINDOWS\system32> Get-Job -Id 2
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    2      Long Running... AzureLongRun... Completed     True            localhost            New-AzureRmResourceGro...
    ```

7. 檢查是否已成功布建 VM。 執行以下命令：

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>連接到 VM

根據您建立的是 Windows 或 Linux VM，連接的步驟可能會不同。

### <a name="connect-to-windows-vm"></a>連接到 Windows VM

遵循下列步驟以連線至 Windows VM。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

### <a name="connect-to-linux-vm"></a>連接至 Linux VM

遵循下列步驟以連線至 Linux VM。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

<!--## Manage VM

The following section describes some of the common operations around the VM that you will create on your Azure Stack Edge Pro device.

[!INCLUDE [azure-stack-edge-gateway-manage-vm](../../includes/azure-stack-edge-gateway-manage-vm.md)]-->


## <a name="supported-vm-sizes"></a>支援的 VM 大小

[!INCLUDE [azure-stack-edge-gateway-supported-vm-sizes](../../includes/azure-stack-edge-gateway-supported-vm-sizes.md)]

## <a name="unsupported-vm-operations-and-cmdlets"></a>不支援的 VM 作業和 Cmdlet

不支援延伸模組、擴展集、可用性設定組、快照集。

<!--## Configure AzCopy

When you install the latest version of AzCopy, you will need to configure AzCopy to ensure that it matches the blob storage REST API version of your Azure Stack Edge Pro device.

On the client used to access your Azure Stack Edge Pro device, set up a global variable to match the blob storage REST API version.

### On Windows client 

`$Env:AZCOPY_DEFAULT_SERVICE_API_VERSION = "2017-11-09"`

### On Linux client

`export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`

To verify if the environment variable for AzCopy was set correctly, take the following steps:

1. Run "azcopy env".
2. Find `AZCOPY_DEFAULT_SERVICE_API_VERSION` parameter. This should have the value you set in the preceding steps.-->


## <a name="next-steps"></a>後續步驟

[Azure Resource Manager Cmdlet](https://docs.microsoft.com/powershell/module/azurerm.resources/?view=azurermps-6.13.0)
