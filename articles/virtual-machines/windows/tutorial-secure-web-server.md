---
title: 教學課程：在 Azure 中使用 TLS/SSL 憑證來保護 Windows 網頁伺服器
description: 在本教學課程中，您將了解如何搭配使用 Azure PowerShell 與 Azure Key Vault 中儲存的 TLS/SSL 憑證，來保護執行 IIS 網頁伺服器的 Windows 虛擬機器。
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3b65d04b383fdc0a409e23ab6b6649604be502c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86525580"
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-tlsssl-certificates-stored-in-key-vault"></a>教學課程：在 Azure 中使用 Key Vault 內儲存的 TLS/SSL 憑證，來保護 Windows 虛擬機器上的網頁伺服器

> [!NOTE]
> 此文件目前僅適用於一般化映像。 如果嘗試使用特製化磁碟進行本教學課程，您將會收到錯誤訊息。 

若要保護 Web 伺服器，您可以使用傳輸層安全性 (TLS) (先前稱為安全通訊端層 (SSL)) 憑證來加密網路流量。 這些 TLS/SSL 憑證可儲存在 Azure Key Vault，並且能夠讓您將憑證安全地部署到 Azure 中的 Windows 虛擬機器 (VM)。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立 Azure Key Vault
> * 產生或上傳憑證至 Key Vault
> * 建立 VM 並安裝 IIS 網頁伺服器
> * 將憑證插入 VM 並使用 TLS 繫結來設定 IIS


## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 您也可以移至 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。


## <a name="overview"></a>總覽
Azure Key Vault 會保護密碼編譯金鑰和祕密，這類憑證或密碼。 Key Vault 有助於簡化憑證管理程序，並可讓您掌控用來存取這些憑證的金鑰。 您可以在 Key Vault 內建立自我簽署憑證，或上傳您目前已經擁有的受信任憑證。

您不必使用包含了內建憑證的自訂 VM 映像，而是要將憑證插入執行中的 VM。 此程序可確保您在部署期間安裝在網頁伺服器上的憑證會是最新的。 如果您更新或取代憑證，您就不必另外再建立新的自訂 VM 映像。 當您建立其他 VM 時，系統會自動插入最新的憑證。 在整個過程中，憑證絕對不會離開 Azure 平台，或在指令碼、命令列記錄或範本中公開。


## <a name="create-an-azure-key-vault"></a>建立 Azure Key Vault
建立 Key Vault 和憑證之前，請先使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 來建立資源群組。 下列範例會在「美國東部」** 位置建立名為 myResourceGroupSecureWeb** 的資源群組：

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

接下來，使用 [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) 建立金鑰保存庫。 每個 Key Vault 需要唯一的名稱，而且應該全部小寫。 使用您自己唯一的 Key Vault 名稱來取代下列範例中的 `mykeyvault`：

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>產生憑證並儲存於 Key Vault
若要在生產環境中使用，您應該使用 [Import-AzKeyVaultCertificate](/powershell/module/az.keyvault/import-azkeyvaultcertificate) 來匯入由受信任的提供者所簽署的有效憑證。 在本教學課程中，下列範例示範如何使用 [Add-AzKeyVaultCertificate](/powershell/module/az.keyvault/add-azkeyvaultcertificate) 來產生自我簽署憑證，而且該憑證會使用透過 [New-AzKeyVaultCertificatePolicy](/powershell/module/az.keyvault/new-azkeyvaultcertificatepolicy) 所得到的預設憑證原則。 

```azurepowershell-interactive
$policy = New-AzKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>建立虛擬機器
使用 [Get-credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1) 來設定 VM 的系統管理員使用者名稱和密碼：

```azurepowershell-interactive
$cred = Get-Credential
```

現在您可以使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 建立 VM。 下列範例會在 *EastUS* 位置中建立名為 *myVM* 的 VM。 如果它們尚不存在，則會建立支援網路資源。 為了允許安全 Web 流量，此 Cmdlet 也會開啟連接埠 *443*。

```azurepowershell-interactive
# Create a VM
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -OpenPorts 443

# Use the Custom Script Extension to install IIS
Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

系統需要花幾分鐘的時間來建立 VM。 最後一個步驟會使用 Azure 自訂指令碼擴充功能，利用 [Set-AzVmExtension](/powershell/module/az.compute/set-azvmextension) 來安裝 IIS 網頁伺服器。


## <a name="add-a-certificate-to-vm-from-key-vault"></a>將憑證從 Key Vault 新增至 VM
若要將憑證從 Key Vault 新增至 VM，請使用 [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) 來取得憑證的識別碼。 使用 [Add-AzVMSecret](/powershell/module/az.compute/add-azvmsecret) 將憑證新增至 VM：

```azurepowershell-interactive
$certURL=(Get-AzKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>設定 IIS 以使用憑證
再次搭配 [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) 來使用自訂指令碼擴充功能，以更新 IIS 組態。 這次的更新會套用從 Key Vault 插入到 IIS 的憑證，並設定 Web 繫結：

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>測試安全的 Web 應用程式
使用 [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) 取得 VM 的公用 IP 位址。 下列範例會取得稍早建立之 `myPublicIP` 的 IP 位址︰

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

現在，您可以開啟 Web 瀏覽器，並在網址列輸入 `https://<myPublicIP>`。 若要在使用自我簽署憑證時接受安全性警告，請依序按一下 [詳細資料] 與 [繼續瀏覽網頁]：

![接受 Web 瀏覽器安全性警告](./media/tutorial-secure-web-server/browser-warning.png)

接著會顯示受保護的 IIS 網站，如下列範例所示：

![檢視執行中安全的 IIS 網站](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>後續步驟
在本教學課程中，您已使用儲存在 Azure Key Vault 中的 TLS/SSL 憑證來保護 IIS 網頁伺服器。 您已了解如何︰

> [!div class="checklist"]
> * 建立 Azure Key Vault
> * 產生或上傳憑證至 Key Vault
> * 建立 VM 並安裝 IIS 網頁伺服器
> * 將憑證插入 VM 並使用 TLS 繫結來設定 IIS

用以下連結查看預先建立的虛擬機器指令碼範例。

> [!div class="nextstepaction"]
> [Windows 虛擬機器指令碼範例](./powershell-samples.md)
