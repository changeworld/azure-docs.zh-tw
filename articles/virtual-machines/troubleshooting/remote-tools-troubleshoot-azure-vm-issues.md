---
title: 使用遠端工具對 Azure VM 問題進行疑難排解 | Microsoft Docs
description: 瞭解 PsExec、PowerShell 文稿和其他遠端工具,您可以使用這些工具解決遠端 Azure VM 問題,而無需使用 RDP。
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
ms.openlocfilehash: d29b2b7c2b9194f20afe4c74d117847f0e343b12
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422612"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>使用遠端工具對 Azure VM 問題進行疑難排解

在 Azure 虛擬機器 (VM) 上排除問題時,可以使用本文中討論的遠端工具而不是使用遠端桌面協定 (RDP) 連接到 VM。

## <a name="serial-console"></a>序列主控台

使用[Azure 虛擬機器的串列主控台](serial-console-windows.md)在遠端 Azure VM 上執行命令。

## <a name="remote-cmd"></a>遠端 CMD

下載 [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec)。 執行下列命令來連線至 VM：

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* 該命令必須在同一虛擬網路中的計算機上運行。
>* DIP 或主機名可用於\<替換 電腦>。
>* -s 參數可確保使用系統帳戶 (系統管理員權限) 來叫用命令。
>* PsExec 會使用 TCP 連接埠 135 和 445。 因此,兩個埠必須在防火牆上打開。

## <a name="run-command"></a>執行命令

有關如何使用 run 命令功能在 VM 執行文稿的詳細資訊,請參閱[使用執行命令 在 Windows VM 中執行 PowerShell 文稿](../windows/run-command.md)。

## <a name="custom-script-extension"></a>自訂指令碼延伸模組

您可以使用自訂指令碼擴充功能，在目標 VM 上執行自訂指令碼。 若要使用此功能，必須符合下列條件：

* VM 具有連線能力。
* Azure 虛擬機器代理已安裝完畢,並且按預期在 VM 上工作。
* 以前未在 VM 上安裝該擴展。
 
  擴展僅在第一次使用腳本時注入腳本。 如果以後使用此功能,擴展將識別該功能已使用,並且不會上傳新腳本。

將腳本上傳到儲存帳戶,並生成自己的容器。 然後，在能與 VM 連線的電腦上，從 Azure PowerShell 中執行下列指令碼。

### <a name="for-classic-deployment-model-vms"></a>對於傳統部署模型 VM

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

### <a name="for-azure-resource-manager-vms"></a>對於 Azure 資源管理員 VM

 

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
>對於 Azure 資源管理器 VM,必須在網路安全組 (NSG) 上打開埠 5986。 如需詳細資訊，請參閱安全性群組。 
>
>針對 RDFE VM，您必須具備有私用連接埠 (5986) 和公用連接埠的端點。 然後,您還必須在 NSG 上打開面向公眾的埠。

### <a name="set-up-the-client-computer"></a>設定用戶端電腦

若要使用 PowerShell 從遠端連線至 VM，首先必須設定用戶端電腦來允許連線。 若要這樣做，請執行下列命令，將 VM 新增至 PowerShell 信任的主機清單 (若適用)。

要將 VM 新增到受信任的主機清單中,:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

要將多個 VM 加入信任的主機清單:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

若要將所有電腦新增至信任的主機清單：

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>啟用 VM 上的 RemotePS

對使用經典部署模型建立的 VM,請使用自訂文稿延伸執行以下文稿:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

對於 Azure 資源管理員 VM,請使用門戶中的執行指令執行啟用RemotePS文本:

![執行命令](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>連接至 VM

依客戶端電腦位置執行以下指令:

* 虛擬網路或部署外部

  * 對於使用經典部署模型建立的 VM,執行以下指令:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * 對於 Azure 資源管理器 VM,首先向公共 IP 位址添加 DNS 名稱。 如需詳細步驟，請參閱[在 Azure 入口網站中為 Windows VM 建立完整網域名稱](../windows/portal-create-fqdn.md)。 然後，執行下列命令：

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* 在虛擬網路或部署中,執行以下命令:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>若設定 SkipCaCheck 旗標，您就可以在啟動工作階段時，略過將憑證匯入 VM 的要求。

您還可以使用調用命令 cmdlet 在 VM 上遠端執行文稿。

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>遠端登錄

>[!NOTE]
>TCP 連接埠 135 或 445 必須開啟，才能使用此選項。
>
>對於 Azure 資源管理器 VM,您必須在 NSG 上打開埠 5986。 如需詳細資訊，請參閱安全性群組。 
>
>針對 RDFE VM，您必須具備有私用連接埠 5986 和公用連接埠的端點。 您還必須在 NSG 上打開該面向公眾的埠。

1. 從同一虛擬網路上的另一個 VM 打開註冊表編輯器 (regedit.exe)。

2. 選擇**檔案** > **連線網路註冊表**。

   ![登錄表編輯器](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. 通過在 **「輸入物件名稱以選擇**」框中輸入目標 VM,按**主機名**或**動態 IP(** 首選)搜尋目標 VM。

   ![輸入要選擇的物件名稱框](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. 輸入目標 VM 的認證。

5. 進行任何必要的登錄變更。

## <a name="remote-services-console"></a>遠端服務主控台

>[!NOTE]
>TCP 連接埠 135 或 445 必須開啟，才能使用此選項。
>
>對於 Azure 資源管理器 VM,您必須在 NSG 上打開埠 5986。 如需詳細資訊，請參閱安全性群組。 
>
>針對 RDFE VM，您必須具備有私用連接埠 5986 和公用連接埠的端點。 您還必須在 NSG 上打開該面向公眾的埠。

1. 從同一虛擬網路上的另一個 VM 打開**Services.msc**的實例。

2. 以滑鼠右鍵按一下 [服務 (本機)\]****。

3. 選取 [連線到另一部電腦]****。

   ![遠端服務](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. 輸入目標 VM 的動態 IP。

   ![輸入動態 IP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. 對服務執行任何必要的變更。

## <a name="next-steps"></a>後續步驟

- 有關 Enter-PSSession cmdlet 的詳細資訊,請參閱[輸入 PSSession](https://technet.microsoft.com/library/hh849707.aspx)。
- 有關使用經典部署模型的 Windows 自訂文稿延伸的詳細資訊,請參閱[Windows 的自訂文稿擴展](../extensions/custom-script-classic.md)。
- PsExec 屬於 [PSTools 套件](https://download.sysinternals.com/files/PSTools.zip)。
- 有關 PSTools 套件的詳細資訊,請參閱[PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools)。


