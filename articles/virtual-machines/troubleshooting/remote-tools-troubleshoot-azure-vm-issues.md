---
title: 使用遠端工具對 Azure VM 問題進行疑難排解 | Microsoft Docs
description: 瞭解您可以用來對遠端 Azure VM 問題進行疑難排解的 PsExec、PowerShell 腳本和其他遠端工具，而不需要使用 RDP。
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: ac785d43a71039ce52f0c8cd4315149a11e91cfc
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737348"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>使用遠端工具對 Azure VM 問題進行疑難排解

當您針對 Azure 虛擬機器 (VM) 的問題進行疑難排解時，您可以使用本文討論的遠端工具來連線至 VM，而不是使用遠端桌面通訊協定 (RDP) 。

## <a name="serial-console"></a>序列主控台

使用 [適用于 Azure 虛擬機器的序列主控台](serial-console-windows.md) ，在遠端 Azure VM 上執行命令。

## <a name="remote-cmd"></a>遠端 CMD

下載 [PsExec](/sysinternals/downloads/psexec)。 執行下列命令來連線至 VM：

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* 命令必須在相同虛擬網路中的電腦上執行。
>* 可以使用 DIP 或主機名稱取代 \<computer>。
>* -s 參數可確保使用系統帳戶 (系統管理員權限) 來叫用命令。
>* PsExec 會使用 TCP 連接埠 135 和 445。 因此，您必須在防火牆上開啟這兩個埠。

## <a name="run-command"></a>執行命令

如需有關如何使用「執行命令」功能在 VM 上執行腳本的詳細資訊，請參閱 [使用 run 命令在您的 WINDOWS VM 中執行 PowerShell 腳本](../windows/run-command.md)。

## <a name="custom-script-extension"></a>自訂指令碼延伸模組

您可以使用自訂指令碼擴充功能，在目標 VM 上執行自訂指令碼。 若要使用此功能，必須符合下列條件：

* VM 具有連線能力。
* Azure 虛擬機器代理程式已安裝，且在 VM 上如預期般運作。
* 此延伸模組先前未安裝在 VM 上。
 
  擴充功能只會在第一次使用時插入腳本。 如果您稍後使用這項功能，擴充功能會辨識出它已在使用中，而且不會上傳新的腳本。

將您的腳本上傳至儲存體帳戶，並產生它自己的容器。 然後，在能與 VM 連線的電腦上，從 Azure PowerShell 中執行下列指令碼。

### <a name="for-classic-deployment-model-vms"></a>針對傳統部署模型 Vm

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group or cloud service where the VM is hosted. For example, for "demo305.cloudapp.net" the cloud service is going to be demo305.

#Set up the Azure PowerShell module, and ensure the access to the subscription.
Import-Module Azure
Add-AzureAccount  #Ensure login with the account associated with the subscription ID.
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-azure-resource-manager-vms"></a>針對 Azure Resource Manager Vm

 

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Set up the Azure PowerShell module, and ensure the access to the subscription.
Login-AzAccount #Ensure login with the account associated with the subscription ID.
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>遠端 Powershell

>[!NOTE]
>TCP 連接埠 5986 (HTTPS) 必須開啟，您才能使用此選項。
>
>針對 Azure Resource Manager Vm，您必須在網路安全性群組 (NSG) 上開啟埠5986。 如需詳細資訊，請參閱安全性群組。 
>
>針對 RDFE VM，您必須具備有私用連接埠 (5986) 和公用連接埠的端點。 然後，您也必須在 NSG 上開啟該公開端口。

### <a name="set-up-the-client-computer"></a>設定用戶端電腦

若要使用 PowerShell 從遠端連線至 VM，首先必須設定用戶端電腦來允許連線。 若要這樣做，請執行下列命令，將 VM 新增至 PowerShell 信任的主機清單 (若適用)。

若要將一部 VM 新增至信任的主機清單：

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

若要將多個 Vm 新增至信任的主機清單：

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

若要將所有電腦新增至信任的主機清單：

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>啟用 VM 上的 RemotePS

針對使用傳統部署模型建立的 Vm，請使用自訂腳本擴充功能來執行下列腳本：

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

針對 Azure Resource Manager Vm，請使用入口網站中的 [執行命令] 來執行 EnableRemotePS 腳本：

![執行命令](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>連接至 VM

根據用戶端電腦位置執行下列命令：

* 在虛擬網路或部署之外

  * 針對使用傳統部署模型建立的 VM，請執行下列命令：

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * 針對 Azure Resource Manager VM，請先將 DNS 名稱新增至公用 IP 位址。 如需詳細步驟，請參閱[在 Azure 入口網站中為 Windows VM 建立完整網域名稱](../create-fqdn.md)。 然後，執行下列命令：

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* 在虛擬網路或部署內，執行下列命令：
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>若設定 SkipCaCheck 旗標，您就可以在啟動工作階段時，略過將憑證匯入 VM 的要求。

您也可以使用 Invoke-Command Cmdlet 從遠端在 VM 上執行腳本。

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>遠端登錄

>[!NOTE]
>TCP 連接埠 135 或 445 必須開啟，才能使用此選項。
>
>針對 Azure Resource Manager Vm，您必須在 NSG 上開啟埠5986。 如需詳細資訊，請參閱安全性群組。 
>
>針對 RDFE VM，您必須具備有私用連接埠 5986 和公用連接埠的端點。 您也必須在 NSG 上開啟該公眾面向的埠。

1. 從相同虛擬網路上的另一個 VM，開啟登錄編輯程式 ( # A0) 。

2. 選取  >  **[File Connect Network Registry]**。

   ![登錄編輯程式](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. 在 [**輸入要選取的物件名稱**] 方塊中輸入目標 VM （依 **主機名稱** 或 **動態 IP** (慣用的) ）。

   ![輸入要選取的物件名稱方塊](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. 輸入目標 VM 的認證。

5. 進行任何必要的登錄變更。

## <a name="remote-services-console"></a>遠端服務主控台

>[!NOTE]
>TCP 連接埠 135 或 445 必須開啟，才能使用此選項。
>
>針對 Azure Resource Manager Vm，您必須在 NSG 上開啟埠5986。 如需詳細資訊，請參閱安全性群組。 
>
>針對 RDFE VM，您必須具備有私用連接埠 5986 和公用連接埠的端點。 您也必須在 NSG 上開啟該公眾面向的埠。

1. 從相同虛擬網路上的另一個 VM，開啟 **services.msc** 的實例。

2. 以滑鼠右鍵按一下 [服務 (本機)\]。

3. 選取 [連線到另一部電腦]。

   ![遠端服務](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. 輸入目標 VM 的動態 IP。

   ![輸入動態 IP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. 對服務執行任何必要的變更。

## <a name="next-steps"></a>後續步驟

- 如需 Enter-PSSession Cmdlet 的詳細資訊，請參閱 [輸入-PSSession](/powershell/module/microsoft.powershell.core/enter-pssession)。
- 如需使用傳統部署模型的 Windows 自訂腳本擴充功能的詳細資訊，請參閱 [適用于 windows 的自訂腳本擴充](../extensions/custom-script-windows.md)功能。
- PsExec 屬於 [PSTools 套件](https://download.sysinternals.com/files/PSTools.zip)。
- 如需 PSTools Suite 的詳細資訊，請參閱 [PSTools](/sysinternals/downloads/pstools)。
