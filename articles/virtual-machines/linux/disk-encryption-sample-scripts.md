---
title: Azure 磁碟加密範例指令碼
description: 本文是適用于 Linux Vm 的 Microsoft Azure 磁片加密的附錄。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: dcfae72d5f15399dc4c759ab859ad8059134f11d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91279785"
---
# <a name="azure-disk-encryption-sample-scripts-for-linux-vms"></a>適用于 Linux Vm 的 Azure 磁碟加密範例腳本

本文提供準備預先加密的 Vhd 和其他工作的範例腳本。

 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Azure 磁碟加密的範例 PowerShell 指令碼 

- **列出訂用帳戶中的所有已加密 VM**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **列出所有用來加密金鑰保存庫中 VM 的磁碟加密祕密** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a> 使用 Azure 磁碟加密先決條件 PowerShell 指令碼
如果您已經熟悉 Azure 磁碟加密的必要條件，您可以使用 [Azure 磁碟加密必要條件 PowerShell 指令碼](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 )。 如需使用此 PowerShell 指令碼的範例，請參閱[加密 VM 快速入門](disk-encryption-powershell-quickstart.md)。 您可以從指令碼區段 (起自 211 行) 中移除註解，以對現有資源群組中現有 VM 的所有磁碟加密。 

下表顯示可在 PowerShell 指令碼中使用的參數： 


|參數|描述|是否為強制？|
|------|------|------|
|$resourceGroupName| 金鑰保存庫所屬資源群組的名稱。  如果不存在此名稱的應用程式，將會以此名稱建立新的資源群組。| True|
|$keyVaultName|要用來放置加密金鑰的金鑰保存庫名稱。 如果不存在此名稱的應用程式，將會以此名稱建立新的保存庫。| True|
|$location|金鑰保存庫的位置。 請確定金鑰保存庫和要加密的 VM 位於相同位置。 使用 `Get-AzLocation` 取得位置清單。|True|
|$subscriptionId|要使用的 Azure 訂用帳戶識別碼。  您可以使用 `Get-AzSubscription` 取得您的訂用帳戶識別碼。|True|
|$aadAppName|會用來將祕密寫入到金鑰保存庫的 Azure AD 應用程式名稱。 如果不存在此名稱的應用程式，將會以此名稱建立新的應用程式。 如果此應用程式已經存在，請將 aadClientSecret 參數傳遞至指令碼。|False|
|$aadClientSecret|稍早建立的 Azure AD 應用程式用戶端密碼。|False|
|$keyEncryptionKeyName|金鑰保存庫中選用金鑰加密金鑰的名稱。 如果不存在此名稱的應用程式，將會以此名稱建立新的金鑰。|False|


### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>加密或解密沒有 Azure AD 應用程式的 VM

- [在現有或執行中的 Linux VM 上啟用磁片加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [在執行中 Linux VM 上停用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - 只能在 Linux VM 的資料磁碟區上停用加密。  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>加密或解密具有 Azure AD 應用程式 (舊版) 的 VM 
 
- [在現有或執行中的 Linux VM 上啟用磁片加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [在執行中 Linux VM 上停用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - 只能在 Linux VM 的資料磁碟區上停用加密。 


- [從預先加密的 VHD/儲存體 Blob 建立新加密的受控磁碟](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - 建立已加密的新受控磁碟，新的受控磁碟具備預先加密的 VHD 以及對應的加密設定





## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>在執行中的 Linux VM 上加密 OS 磁碟機

### <a name="prerequisites-for-os-disk-encryption"></a>OS 磁碟加密的必要條件

* VM 必須使用與 OS 磁片加密相容的散發套件，如[Azure 磁碟加密支援的作業系統](disk-encryption-overview.md#supported-vms)所列 
* 必須從 Azure Resource Manager 的Marketplace 映像建立 VM。
* 具有至少 4 GB RAM 的 Azure VM (建議大小為 7 GB)。
* (適用於 RHEL 和 CentOS) 停用 SELinux。 若要停用 SELinux，請參閱「4.4.2. 停用 SELinux」，其位於 VM 上的 [SELinux 使用者和系統管理員指南](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux)。
* 停用 SELinux 之後，至少重新啟動 VM 一次。

### <a name="steps"></a>步驟
1. 使用先前指定的其中一個散發套件來建立 VM。

   若為 CentOS 7.2，支援透過特殊映像加密 OS 磁碟。 若要使用此映像，請在建立 VM 時指定 "7.2n" 做為 SKU︰

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. 根據您的需求設定 VM。 如果您要加密所有 (OS + 資料) 磁碟機，必須可從 /etc/fstab 指定和掛接資料磁碟機。

   > [!NOTE]
   > 使用 UUID=... 來指定 /etc/fstab 中的資料磁碟機，而不是指定區塊裝置名稱 (例如 /dev/sdb1)。 加密期間，VM 上的磁碟機順序會變更。 如果 VM 依賴特定的區塊裝置順序，則無法在加密後掛接裝置。

3. 登出 SSH 工作階段。

4. 若要加密 OS，請在啟用加密時，將 volumeType 指定為 **All** 或 **OS** 。

   > [!NOTE]
   > 未以 `systemd` 服務的形式執行的所有使用者空間程序皆應使用 `SIGKILL` 來終止。 重新啟動 VM。 當您在執行中的 VM 上啟用 OS 磁碟加密時，請規劃 VM 停機時間。

5. 使用[下一節](#monitoring-os-encryption-progress)的指示定期監視加密進度。

6. Get-AzVmDiskEncryptionStatus 顯示「>vmrestartpending」之後，請使用入口網站、PowerShell 或 CLI 登入，以重新開機 VM。
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   重新開機之前，我們建議您儲存 VM 的[開機診斷](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)。

## <a name="monitoring-os-encryption-progress"></a>監視 OS 加密進度
有三種方式可監視 OS 加密進度：

* 使用 `Get-AzVmDiskEncryptionStatus` Cmdlet，並查看 ProgressMessage 欄位︰
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  在 VM 達到 "OS disk encryption started" 之後，需要在進階儲存體所支援的 VM 上耗費大約 40 至 50 分鐘。

  由於 WALinuxAgent 發生 [388 號問題](https://github.com/Azure/WALinuxAgent/issues/388)，某些散發套件的 `OsVolumeEncrypted` 和 `DataVolumesEncrypted` 會顯示為 `Unknown`。 若使用 WALinuxAgent 2.1.5 和更新版本，就會自動修正此問題。 如果您在輸出中看到 `Unknown`，可以使用 Azure 資源 Explorer 確認磁碟加密狀態。

  移至 [Azure 資源 Explorer](https://resources.azure.com/)，然後在左側選取面板中展開這個階層︰

  ```
  |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
  ```                

  在 InstanceView 中，向下捲動以查看磁碟機的加密狀態。

  ![VM 執行個體檢視](./media/disk-encryption/vm-instanceview.png)

* 查看[開機診斷](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)。 來自 ADE 擴充功能的訊息應該會在前面加上 `[AzureDiskEncryption]`。

* 透過 SSH 登入 VM，並從下列位置取得擴充功能記錄檔：

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  不建議在 OS 加密進行時登入 VM。 其他兩個方法都失敗時，只複製記錄。

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>準備已預先加密的 Linux VHD
準備預先加密的 VHD 會根據散發套件而有所不同。 有關於準備 Ubuntu 16、openSUSE 13.2 和 CentOS 7 的範例可用。 

### <a name="ubuntu-16"></a>Ubuntu 16
在發佈安裝期間執行下列步驟以設定加密︰

1. 在分割磁碟時選取 [設定加密的磁碟區]****。

   ![Ubuntu 16.04 設定 - 設定加密的磁碟區](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. 另外建立一個不得加密的開機磁碟機。 加密根磁碟機。

   ![Ubuntu 16.04 設定 - 選取要加密的裝置](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. 提供複雜密碼。 這是您上傳至金鑰保存庫的複雜密碼。

   ![Ubuntu 16.04 設定 - 提供複雜密碼](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. 完成分割。

   ![Ubuntu 16.04 設定 - 完成資料分割](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. 啟動 VM 時會要求輸入複雜密碼，請使用步驟 3 中提供的複雜密碼。

   ![Ubuntu 16.04 設定 - 提供開機複雜密碼](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. 使用[這些指示](./create-upload-ubuntu.md?toc=/azure/virtual-machines/linux/toc.json)準備要上傳到 Azure 的 VM。 還不要執行最後一個步驟 (解除佈建 VM)。

設定加密來與 Azure 搭配運作，請執行下列步驟：

1. 在 /usr/local/sbin/azure_crypt_key.sh 下，使用以下指令碼的內容建立檔案。 請注意 KeyFileName，因為它是 Azure 使用的複雜密碼檔案名稱。

    ```bash
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
   ```

2. 在 */etc/crypttab* 中變更 crypt 設定。 其看起來應該如下：
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. 在指令碼中新增可執行檔權限︰
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. 以加上程式碼行的方式編輯 /etc/initramfs-tools/modules**：
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. 執行 `update-initramfs -u -k all` 來更新 initramfs，以讓 `keyscript` 生效。

7. 現在您可以取消佈建 VM。

   ![Ubuntu 16.04 設定 - update-initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. 繼續進行下一個步驟，並將 VHD 上傳到 Azure。

### <a name="opensuse-132"></a>openSUSE 13.2
若要在發佈安裝期間設定加密，請執行下列步驟︰
1. 分割磁碟時，請選取 [加密磁碟區群組]****，然後輸入密碼。 這是您要上傳到金鑰保存庫的密碼。

   ![openSUSE 13.2 設定 - 加密磁碟區群組](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. 使用您的密碼啟動 VM。

   ![openSUSE 13.2 設定 - 提供開機複雜密碼](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. 請依照[準備 Azure 的 SLES 或 openSUSE 虛擬機器](./suse-create-upload-vhd.md?toc=/azure/virtual-machines/linux/toc.json#prepare-opensuse-131)中的指示準備 VM 以上傳至 Azure。 還不要執行最後一個步驟 (解除佈建 VM)。

若要設定加密來與 Azure 搭配運作，請執行下列步驟：
1. 編輯 /etc/dracut.conf 並新增下面這一行︰
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. 註解化 /usr/lib/dracut/modules.d/90crypt/module-setup.sh 檔案結尾的這幾行程式碼：
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. 在 /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh 檔案開頭附加下面這一行：
   ```bash
    DRACUT_SYSTEMD=0
   ```
   變更所有出現的：
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   變更為：
   ```bash
    if [ 1 ]; then
   ```
4. 編輯/usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh，並將它附加至「# Open LUKS device」：

    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. 執行 `/usr/sbin/dracut -f -v` 以更新 initrd。

6. 現在您可以取消佈建 VM，並將 VHD 上傳到 Azure。

### <a name="centos-7-and-rhel-7"></a>CentOS 7 和 RHEL 7

若要在發佈安裝期間設定加密，請執行下列步驟︰
1. 在分割磁碟時選取 [加密資料]****。

   ![CentOS 7 設定 - 安裝目的地](./media/disk-encryption/centos-encrypt-fig1.png)

2. 確定已為根分割選取 [加密]****。

   ![CentOS 7 設定 - 為根分割選取加密](./media/disk-encryption/centos-encrypt-fig2.png)

3. 提供複雜密碼。 這是您要上傳到金鑰保存庫的複雜密碼。

   ![CentOS 7 設定 - 提供複雜密碼](./media/disk-encryption/centos-encrypt-fig3.png)

4. 啟動 VM 時會要求輸入複雜密碼，請使用步驟 3 中提供的複雜密碼。

   ![CentOS 7 設定 - 在開機時輸入複雜密碼](./media/disk-encryption/centos-encrypt-fig4.png)

5. 請依照[準備 Azure 的 CentOS 式虛擬機器](./create-upload-centos.md?toc=/azure/virtual-machines/linux/toc.json#centos-70)中的「CentOS 7.0+」指示準備 VM 以上傳至 Azure。 還不要執行最後一個步驟 (解除佈建 VM)。

6. 現在您可以取消佈建 VM，並將 VHD 上傳到 Azure。

若要設定加密來與 Azure 搭配運作，請執行下列步驟：

1. 編輯 /etc/dracut.conf 並新增下面這一行︰
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. 註解化 /usr/lib/dracut/modules.d/90crypt/module-setup.sh 檔案結尾的這幾行程式碼：
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. 在 /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh 檔案開頭附加下面這一行：
   ```bash
    DRACUT_SYSTEMD=0
   ```
   變更所有出現的：
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   to
   ```bash
    if [ 1 ]; then
   ```
4. 編輯/usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh，並在 "# Open LUKS device" 之後附加下列內容：
    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. 執行 "/usr/sbin/dracut-f-v" 來更新 initrd。

    ![CentOS 7 設定 - 執行 /usr/sbin/dracut -f -v](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>上傳加密的 VHD 至 Azure 儲存體帳戶
啟用 DM-Crypt 加密之後，需要將本機加密的 VHD 上傳至您的儲存體帳戶。
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>將已預先加密 VM 的祕密上傳至金鑰保存庫
使用 Azure AD 應用程式 (舊版) 加密時，先前取得的磁碟加密密碼必須上傳，作為金鑰保存庫中的密碼。 金鑰保存庫必須為 Azure AD 用戶端啟用磁碟加密以及權限。

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>未使用 KEK 加密的磁碟加密密碼
若要在金鑰保存庫中設定秘密，請使用 [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret)。 複雜密碼會以 base64 字串編碼，然後上傳至金鑰保存庫。 此外，請確定在金鑰保存庫中建立密碼時會設定下列標籤。

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


在下一個步驟中使用 `$secretUrl`，以便[在不使用 KEK 的狀況下連接 OS 磁碟](#without-using-a-kek)。

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>使用 KEK 加密的磁碟加密密碼
將密碼上傳至金鑰保存庫之前，您可以選擇性地使用金鑰加密金鑰來加密密碼。 使用包裝 [API](/rest/api/keyvault/wrapkey) 先加密使用金鑰加密金鑰的密碼。 這項包裝作業的輸出是 base64 URL 編碼的字串，您接著可以使用指令程式，以秘密的形式上傳 [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) 。

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

在下一個步驟中使用 `$KeyEncryptionKey` 和 `$secretUrl`，以便[使用 KEK 連接 OS 磁碟](#using-a-kek)。

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>連接 OS 磁碟時，指定密碼的 URL

###  <a name="without-using-a-kek"></a>不使用 KEK
當您在連接 OS 磁碟時，需要傳遞 `$secretUrl`。 URL 已在「未使用 KEK 加密的磁碟加密密碼」一節中產生。
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>使用 KEK
連接 OS 磁碟時，傳遞 `$KeyEncryptionKey` 和 `$secretUrl`。 URL 已在「使用 KEK 加密的磁碟加密祕密」一節中產生。
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
