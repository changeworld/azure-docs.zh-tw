---
title: 搭配 Linux 使用 Azure 檔案 | Microsoft Docs
description: 了解如何透過 Linux 上的 SMB 掛接 Azure 檔案共用。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 72264755d5f0379f0ffb07852f48885126a36898
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411598"
---
# <a name="use-azure-files-with-linux"></a>搭配 Linux 使用 Azure 檔案
[Azure 檔案服務](storage-files-introduction.md)是 Microsoft 易於使用的雲端檔案系統。 可以使用 [SMB 核心用戶端](https://wiki.samba.org/index.php/LinuxCIFS)將 Azure 檔案共用裝載在 Linux 發行版本中。 本文將說明掛接 Azure 檔案共用的兩種方式：使用 `mount` 命令的隨選掛接，以及建立項目 `/etc/fstab` 的開機掛接。

在 Linux 上裝載 Azure 檔共享的推薦方法是使用 SMB 3.0。 默認情況下,Azure 檔需要在傳輸過程中進行加密,而 SMB 3.0 僅支援加密。 Azure 檔案還支援 SMB 2.1,它不支援傳輸中的加密,但出於安全原因,您不得將 Azure 檔案共享與來自其他 Azure 區域或本地的 SMB 2.1 載入。 除非應用程式特別需要 SMB 2.1,否則沒有理由使用它,因為最流行的、最近發佈的 Linux 發行版支援 SMB 3.0:  

| | SMB 2.1 <br>(掛接在相同 Azure 區域內的 VM 上) | SMB 3.0 <br>(從內部部署環境和跨區域掛接) |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| Red Hat Enterprise Linux (RHEL) | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | 10+ |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP3+ |

如果您使用的是上表中未列出的 Linux 發行版,則可以通過檢查 Linux 內核版本來檢查 Linux 發行版是否支援 SMB 3.0 加密。 SMB 3.0 加密已添加到 Linux 內核版本 4.11 中。 這個`uname`指令會傳回的 Linux 核心的版本:

```bash
uname -r
```

## <a name="prerequisites"></a>Prerequisites
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**確保安裝了 cifs-utils 套件。**  
    可使用套件管理員將 cifs-utils 套件安裝在所選擇的 Linux 發行版本上。 

    在 **Ubuntu** 和 **Debian 型**發行版本上，請使用 `apt` 封裝管理員：

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    在**Fedora**上,**紅帽企業 Linux 8+** 和**CentOS 8 +** 使用`dnf`套件管理員:

    ```bash
    sudo dnf install cifs-utils
    ```

    在舊版本的**紅帽企業 Linux**和**CentOS**`yum`上,使用包管理器:

    ```bash
    sudo yum install cifs-utils 
    ```

    在 **openSUSE** 上，請使用 `zypper` 封裝管理員：

    ```bash
    sudo zypper install cifs-utils
    ```

    在其他發行版本上，請使用適當的封裝管理員或[從來源編譯](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **Azure 命令列介面 (CLI) 的最新版本。** 有關如何安裝 Azure CLI 的詳細資訊,請參閱安裝[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)並選擇作業系統。 如果您更喜歡在 PowerShell 6+ 中使用 Azure PowerShell 模組,則可以為 Azure CLI 提供以下說明。

* **請確定已開啟連接埠 445**：SMB 透過 TCP 通訊埠 445 進行通訊 - 請檢查您的防火牆不會將 TCP 通訊埠 445 從用戶端電腦封鎖。  替代**資源群組><,****並<存儲帳戶>**
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    如果連線成功,您應該會看到類似於以下輸出的內容:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    如果您無法在公司網路上打開埠 445,或者被 ISP 阻止這樣做,則可以使用 VPN 連接或 ExpressRoute 來處理埠 445。 有關詳細資訊,請參閱直接[Azure 檔共享訪問](storage-files-networking-overview.md)的網路注意事項。

## <a name="mounting-azure-file-share"></a>安裝 Azure 檔案分享
要將 Azure 檔案共享與 Linux 發行版一起使用,必須創建一個目錄作為 Azure 檔共享的裝載點。 在 Linux 系統上的任意位置都可以創建裝載點,但在 /mnt 下創建此點是常見的約定。 裝載點之後,可以使用`mount`命令 訪問 Azure 檔共用。

如果需要,可以將同一 Azure 檔共享裝載到多個裝載點。

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>使用 `mount` 隨需掛接 Azure 檔案共用
1. **為載入點建立資料夾**:`<your-resource-group>`取代`<your-storage-account>``<your-file-share>`, 並使用適合環境的資訊:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **使用載入指令載入 Azure 檔案共享**。 在下面的範例中,本地 Linux 檔和資料夾許可權預設為 0755,這意味著為擁有者(基於檔/目錄 Linux 擁有者)讀取、寫入和執行,為擁有者組中的使用者讀取和執行,並為系統上的其他人進行讀取和執行。 可以使用和`uid``gid`載入選項來設置裝載的使用者 ID 和組 ID。 您還可以使用`dir_mode``file_mode`並 根據需要設置自定義許可權。 有關如何設置權限的詳細資訊,請參閱維琪百科上的[UNIX 數位表示法](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation)。 

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > 上面裝載的命令與 SMB 3.0 一起安裝。 如果您的 Linux 發行版不支援 SMB 3.0 加密,或者如果它僅支援 SMB 2.1,則只能從與儲存帳戶相同的區域中的 Azure VM 裝載。 要將 Azure 檔案分享載入到不支援 SMB 3.0 加密的 Linux 發行版上,您需要[關閉儲存帳戶在傳輸中的加密](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

使用 Azure 檔案共用後，即可使用 `sudo umount $mntPath` 取消掛接共用。

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>使用 `/etc/fstab` 建立 Azure 檔案共用的持續掛接點
1. **為裝載點創建資料夾**:可以在檔案系統上的任意位置創建裝載點的資料夾,但在 /mnt 下創建此資料夾是常見的約定。 例如,以下指令建立一個新目錄,取代`<your-resource-group>`,`<your-storage-account>``<your-file-share>`並使用適合環境的資訊:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **建立認證檔來儲存檔案共用的使用者名稱 (儲存體帳戶名稱) 和密碼 (儲存體帳戶金鑰)。** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **變更認證檔的權限，因此只有 root 才能讀取或修改密碼檔。** 由於儲存體帳戶金鑰基本上是儲存體帳戶的超級管理員密碼，因此請務必設定只有 root 才能存取的檔案權限，讓更低權限的使用者無法擷取儲存體帳戶金鑰。   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **使用以下命令將以下行追加到`/etc/fstab`**:在下面的示例中,本地 Linux 檔和資料夾許可權預設為 0755,這意味著為擁有者(基於檔/目錄 Linux 擁有者)讀取、寫入和執行,讀取和執行擁有者組中的使用者,併為系統上的其他人讀取和執行。 可以使用和`uid``gid`載入選項來設置裝載的使用者 ID 和組 ID。 您還可以使用`dir_mode``file_mode`並 根據需要設置自定義許可權。 有關如何設置權限的詳細資訊,請參閱維琪百科上的[UNIX 數位表示法](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation)。

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > 上面裝載的命令與 SMB 3.0 一起安裝。 如果您的 Linux 發行版不支援 SMB 3.0 加密,或者如果它僅支援 SMB 2.1,則只能從與儲存帳戶相同的區域中的 Azure VM 裝載。 要將 Azure 檔案分享載入到不支援 SMB 3.0 加密的 Linux 發行版上,您需要[關閉儲存帳戶在傳輸中的加密](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="using-autofs-to-automatically-mount-the-azure-file-shares"></a>使用 autofs 自動載入 Azure 檔案分享

1. **確保安裝了 autofs 套件。**  

    autofs 套件可以使用您選擇的 Linux 發行版上的包管理器進行安裝。 

    在 **Ubuntu** 和 **Debian 型**發行版本上，請使用 `apt` 封裝管理員：
    ```bash
    sudo apt update
    sudo apt install autofs
    ```
    在**Fedora**上,**紅帽企業 Linux 8+** 和**CentOS 8 +** 使用`dnf`套件管理員:
    ```bash
    sudo dnf install autofs
    ```
    在舊版本的**紅帽企業 Linux**和**CentOS**`yum`上,使用包管理器:
    ```bash
    sudo yum install autofs 
    ```
    在 **openSUSE** 上，請使用 `zypper` 封裝管理員：
    ```bash
    sudo zypper install autofs
    ```
2. **為共享建立載入點:**
   ```bash
    sudo mkdir /fileshares
    ```
3. **克里特一個新的自訂 Autofs 設定檔**
    ```bash
    sudo vi /etc/auto.fileshares
    ```
4. **將以下項目加入 /etc/auto.file 分享**
   ```bash
   echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath"" > /etc/auto.fileshares
   ```
5. **將以下項目加入 /etc/auto.master**
   ```bash
   /fileshares /etc/auto.fileshares --timeout=60
   ```
6. **重新啟動 autofs**
    ```bash
    sudo systemctl restart autofs
    ```
7.  **存取為分享的資料夾**
    ```bash
    cd /fileshares/$filesharename
    ```
## <a name="securing-linux"></a>保護 Linux
為了在 Linux 上安裝 Azure 檔共享,必須造訪埠 445。 許多組織會封鎖連接埠 445，因為 SMB 1 固有的安全性風險。 SMB 1,也稱為 CIFS(通用 Internet 檔案系統),是一個包含許多 Linux 發行版的遺留檔案系統協定。 SMB 1 已過期、沒有效率，而且最重要的是不安全的通訊協定。 好消息是 Azure 檔不支援 SMB 1,並且從 Linux 內核版本 4.18 開始,Linux 可以禁用 SMB 1。 在生產中使用 SMB 檔共用之前,我們始終[強烈建議](https://aka.ms/stopusingsmb1)在 Linux 用戶端上禁用 SMB 1。

從 Linux 內核 4.18`cifs`開始,SMB 內核模組由於遺留原因調用,公開了一個新的模組參數(通常`disable_legacy_dialects`稱為*parm,* 稱為 各種外部文檔)。 儘管在 Linux 內核 4.18 中引入了,但一些供應商已將此更改移植到他們支援的舊內核。 為方便起見,下表詳細介紹了此模組參數在常見 Linux 發行版上的可用性。

| 散發 | 可以關閉 SMB 1 |
|--------------|-------------------|
| 烏本圖 14.04-16.04 | 否 |
| Ubuntu 18.04 | 是 |
| 烏本圖 19.04° | 是 |
| Debian 8-9 | 否 |
| Debian 10° | 是 |
| 費多拉 29° | 是 |
| CentOS 7 | 否 | 
| CentOS 8+ | 是 |
| 紅帽企業 Linux 6.x-7.x | 否 |
| 紅帽企業 Linux 8+ | 是 |
| 開斯斯飛躍 15.0 | 否 |
| 開斯西飛躍 15.1° | 是 |
| 開SUSE翻滾雜草 | 是 |
| SUSE Linux 企業版 11.x-12.x | 否 |
| SUSE Linux 企業 15 | 否 |
| SUSE Linux 企業 15.1 | 否 |

您可以通過以下命令檢查 Linux 發行`disable_legacy_dialects`版是否支援 模組參數。

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

此指令應輸出以下訊息:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

在禁用 SMB 1 之前,必須檢查以確保 SMB 模組當前未載入到您的系統上(如果您已裝載 SMB 共用,則會自動這樣做)。 可以使用以下指令執行此操作,如果未載入 SMB,該命令將不輸出:

```bash
lsmod | grep cifs
```

要卸載模組,首先卸載所有 SMB 共用(`umount`使用上述 命令)。 您可以使用以下指令識別系統上所有載入的 SMB 分享:

```bash
mount | grep cifs
```

卸載所有 SMB 檔共享後,可以安全地卸載模組。 您若要這麼做，可透過 `modprobe` 命令：

```bash
sudo modprobe -r cifs
```

您可以使用以下`modprobe`指令手動載入卸下 SMB 1 的模組:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

最後,您可以通過查看`/sys/module/cifs/parameters`中的載入參數來檢查 SMB 模組已載入了參數:

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

要持續禁用基於 Ubuntu 和 Debian 的分發上的 SMB 1,必須建立一個新檔(如果尚未為其他模組提供`/etc/modprobe.d/local.conf`自定義選項),請使用該設置調用該檔。 可以使用以下指令執行此操作:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

您可以透過載入 SMB 模組來驗證它是否有效:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>意見反應
Linux 使用者，歡迎您提供相關資訊！

適用於 Linux 的 Azure 檔案使用者群組提供了一個論壇，讓您在 Linux 上評估並採用檔案儲存體之際，得以分享意見反應。 請寄電子郵件至 [Azure 檔案 Linux 使用者](mailto:azurefiles@microsoft.com)，以加入使用者的群組。

## <a name="next-steps"></a>後續步驟
請參閱這些連結，以取得 Azure 檔案服務的詳細資訊：

* [規劃 Azure 檔案服務部署](storage-files-planning.md)
* [常見問題集](../storage-files-faq.md)
* [疑難排解](storage-troubleshoot-linux-file-connection-problems.md)
