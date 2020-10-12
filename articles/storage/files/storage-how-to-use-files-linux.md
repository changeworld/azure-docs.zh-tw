---
title: 搭配 Linux 使用 Azure 檔案 | Microsoft Docs
description: 了解如何透過 Linux 上的 SMB 掛接 Azure 檔案共用。 請參閱必要條件清單。 請參閱 Linux 用戶端上的 SMB 安全性考慮。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 957e827e621d07ed9b5533a1607f955f05985d9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90004777"
---
# <a name="use-azure-files-with-linux"></a>搭配 Linux 使用 Azure 檔案
[Azure 檔案服務](storage-files-introduction.md)是 Microsoft 易於使用的雲端檔案系統。 可以使用 [SMB 核心用戶端](https://wiki.samba.org/index.php/LinuxCIFS)將 Azure 檔案共用裝載在 Linux 發行版本中。 本文將說明掛接 Azure 檔案共用的兩種方式：使用 `mount` 命令的隨選掛接，以及建立項目 `/etc/fstab` 的開機掛接。

在 Linux 上掛接 Azure 檔案共用的建議方式是使用 SMB 3.0。 根據預設，Azure 檔案儲存體需要在傳輸中加密，只有 SMB 3.0 支援。 Azure 檔案儲存體也支援 SMB 2.1 （不支援傳輸中加密），但您可能不會基於安全考慮，從另一個 Azure 區域或內部部署的 SMB 2.1 掛接 Azure 檔案共用。 除非您的應用程式特別需要 SMB 2.1，否則不需要使用它，因為最受歡迎、最近發行的 Linux 發行版本支援 SMB 3.0：  

| Linux 發行版本 | SMB 2.1 <br>(掛接在相同 Azure 區域內的 VM 上) | SMB 3.0 <br>(從內部部署環境和跨區域掛接) |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| Red Hat Enterprise Linux (RHEL) | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | 10+ |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP2+ |

如果您使用的 Linux 散發套件未列于上表中，您可以檢查 Linux 核心版本，以查看您的 Linux 散發套件是否支援具有加密的 SMB 3.0。 使用加密的 SMB 3.0 已新增至 Linux 核心版本4.11。 此 `uname` 命令會傳回使用中的 Linux 核心版本：

```bash
uname -r
```

## <a name="prerequisites"></a>必要條件
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**確定已安裝 cifs 公用程式套件。**  
    可使用套件管理員將 cifs-utils 套件安裝在所選擇的 Linux 發行版本上。 

    在 **Ubuntu** 和 **Debian 型**發行版本上，請使用 `apt` 封裝管理員：

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    在 **Fedora**、 **Red Hat Enterprise Linux 8 +** 及 **CentOS 8 +** 上，使用 `dnf` 套件管理員：

    ```bash
    sudo dnf install cifs-utils
    ```

    在較舊版本的 **Red Hat Enterprise Linux** 和 **CentOS**上，使用 `yum` 套件管理員：

    ```bash
    sudo yum install cifs-utils 
    ```

    在 **openSUSE** 上，請使用 `zypper` 封裝管理員：

    ```bash
    sudo zypper install cifs-utils
    ```

    在其他發行版本上，請使用適當的封裝管理員或[從來源編譯](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **最新版本的 Azure 命令列介面 (CLI) 。** 如需有關如何安裝 Azure CLI 的詳細資訊，請參閱 [安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 並選取您的作業系統。 如果您想要在 PowerShell 6 + 中使用 Azure PowerShell 模組，您可能會看到下列的 Azure CLI 的指示。

* **請確定已開啟連接埠 445**：SMB 透過 TCP 通訊埠 445 進行通訊 - 請檢查您的防火牆不會將 TCP 通訊埠 445 從用戶端電腦封鎖。  取代 `<your-resource-group>` ， `<your-storage-account>` 然後執行下列腳本：
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

    如果連接成功，您應該會看到類似下列輸出的內容：

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    如果您無法在公司網路上開啟埠445，或是因為 ISP 封鎖了此問題，您可以使用 VPN 連線或 ExpressRoute 來解決埠445。 如需詳細資訊，請參閱 [直接 Azure 檔案共用存取的網路考慮](storage-files-networking-overview.md)。

## <a name="mounting-azure-file-share"></a>裝載 Azure 檔案共用
若要搭配使用 Azure 檔案共用與您的 Linux 散發套件，您必須建立一個作為 Azure 檔案共用之掛接點的目錄。 您可以在 Linux 系統上的任何位置建立掛接點，但在/mnt。下建立它是常見的慣例 在掛接點之後，您可以使用 `mount` 命令來存取 Azure 檔案共用。

如有需要，您可以將相同的 Azure 檔案共用掛接至多個掛接點。

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>使用 `mount` 隨需掛接 Azure 檔案共用
1. **建立掛接點的資料夾**：將 `<your-resource-group>` 、和取代為 `<your-storage-account>` `<your-file-share>` 您環境的適當資訊：

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **使用 mount 命令掛接 Azure 檔案共用**。 在下列範例中，本機 Linux 檔案和資料夾許可權預設為0755，這表示會根據檔案/目錄 Linux 擁有者) 、讀取和執行擁有者群組中的使用者，以及讀取和執行系統上的其他使用者，來讀取、寫入和執行擁有者 (。 您可以使用 `uid` 和 `gid` 掛接選項來設定掛接的使用者識別碼和群組識別碼。 您也可以使用 `dir_mode` 和 `file_mode` 來設定所需的自訂許可權。 如需有關如何設定許可權的詳細資訊，請參閱維琪百科上的 [UNIX 數值標記法](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) 。 

    ```bash
    # This command assumes you have logged in with az login
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
    > 上述掛接命令掛接了 SMB 3.0。 如果您的 Linux 散發套件不支援具有加密的 SMB 3.0，或它僅支援 SMB 2.1，您只能從與儲存體帳戶位於相同區域內的 Azure VM 進行掛接。 若要將 Azure 檔案共用掛接在不支援 SMB 3.0 （含加密）的 Linux 散發套件上，您必須 [停用儲存體帳戶的傳輸中加密](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

使用 Azure 檔案共用後，即可使用 `sudo umount $mntPath` 取消掛接共用。

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>使用 `/etc/fstab` 建立 Azure 檔案共用的持續掛接點
1. **建立掛接點的資料夾**：掛接點的資料夾可以在檔案系統上的任何位置建立，但在/mnt。下建立它是常見的慣例 例如，下列命令會建立新的目錄、將 `<your-resource-group>` 、和取代為 `<your-storage-account>` `<your-file-share>` 您環境的適當資訊：

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

1. **使用下列命令，將 `/etc/fstab` 下行附加至**：在下列範例中，本機 Linux 檔案和資料夾許可權預設為0755，這表示會根據檔案/目錄 Linux 擁有者) 、讀取和執行擁有者群組中使用者的讀取和執行，以及讀取和執行系統上的其他使用者，來讀取、寫入和執行擁有者的 (。 您可以使用 `uid` 和 `gid` 掛接選項來設定掛接的使用者識別碼和群組識別碼。 您也可以使用 `dir_mode` 和 `file_mode` 來設定所需的自訂許可權。 如需有關如何設定許可權的詳細資訊，請參閱維琪百科上的 [UNIX 數值標記法](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) 。

    ```bash
    # This command assumes you have logged in with az login
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
    > 上述掛接命令掛接了 SMB 3.0。 如果您的 Linux 散發套件不支援具有加密的 SMB 3.0，或它僅支援 SMB 2.1，您只能從與儲存體帳戶位於相同區域內的 Azure VM 進行掛接。 若要將 Azure 檔案共用掛接在不支援 SMB 3.0 （含加密）的 Linux 散發套件上，您必須 [停用儲存體帳戶的傳輸中加密](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="using-autofs-to-automatically-mount-the-azure-file-shares"></a>使用 autofs 自動掛接 Azure 檔案共用 (s) 

1. **確定已安裝 autofs 套件。**  

    您可以在您選擇的 Linux 散發套件上，使用套件管理員來安裝 autofs 套件。 

    在 **Ubuntu** 和 **Debian 型**發行版本上，請使用 `apt` 封裝管理員：
    ```bash
    sudo apt update
    sudo apt install autofs
    ```
    在 **Fedora**、 **Red Hat Enterprise Linux 8 +** 及 **CentOS 8 +** 上，使用 `dnf` 套件管理員：
    ```bash
    sudo dnf install autofs
    ```
    在較舊版本的 **Red Hat Enterprise Linux** 和 **CentOS**上，使用 `yum` 套件管理員：
    ```bash
    sudo yum install autofs 
    ```
    在 **openSUSE** 上，請使用 `zypper` 封裝管理員：
    ```bash
    sudo zypper install autofs
    ```
2. **為共用 () 建立掛接點 **：
   ```bash
    sudo mkdir /fileshares
    ```
3. **建立新的自訂 autofs 設定檔**
    ```bash
    sudo vi /etc/auto.fileshares
    ```
4. **將下列專案新增至/etc/auto.fileshares**
   ```bash
   echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath"" > /etc/auto.fileshares
   ```
5. **將下列專案新增至/etc/auto.master**
   ```bash
   /fileshares /etc/auto.fileshares --timeout=60
   ```
6. **重新開機 autofs**
    ```bash
    sudo systemctl restart autofs
    ```
7.  **存取為共用指定的資料夾**
    ```bash
    cd /fileshares/$filesharename
    ```
## <a name="securing-linux"></a>保護 Linux
為了在 Linux 上掛接 Azure 檔案共用，必須能夠存取埠445。 許多組織會封鎖連接埠 445，因為 SMB 1 固有的安全性風險。 SMB 1 也稱為 CIFS (Common Internet File System) ，是許多 Linux 發行版本隨附的舊版檔案系統通訊協定。 SMB 1 已過期、沒有效率，而且最重要的是不安全的通訊協定。 好消息是，Azure 檔案儲存體不支援 SMB 1，而且從 Linux 核心版本4.18 開始，Linux 讓它可以停用 SMB 1。 在生產環境中使用 SMB 檔案共用之前，我們一律 [強烈建議](https://aka.ms/stopusingsmb1) 停用 Linux 用戶端上的 smb 1。

從 Linux 核心4.18 開始，由於舊版原因，SMB 核心模組會 `cifs` 公開新的模組參數， (通常稱為不同的外部檔) 的 *>parm* `disable_legacy_dialects` 。 雖然在 Linux 核心4.18 中引進，但有些廠商已將這項變更 backport 至它們所支援的較舊核心。 為了方便起見，下表詳細說明此模組參數在一般 Linux 發行版本上的可用性。

| 散發 | 可以停用 SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | 否 |
| Ubuntu 18.04 | 是 |
| Ubuntu 19.04 + | 是 |
| Debian 8-9 | 否 |
| Debian 10 + | 是 |
| Fedora 29 + | 是 |
| CentOS 7 | 否 | 
| CentOS 8 + | 是 |
| Red Hat Enterprise Linux 6.x-7。x | 否 |
| Red Hat Enterprise Linux 8 + | 是 |
| openSUSE 閏15。0 | 否 |
| openSUSE Leap 15.1 + | 是 |
| openSUSE Tumbleweed | 是 |
| SUSE Linux Enterprise 11. x-12. x | 否 |
| SUSE Linux Enterprise 15 | 否 |
| SUSE Linux Enterprise 15。1 | 否 |

您可以透過下列命令查看您的 Linux 散發套件是否支援 `disable_legacy_dialects` module 參數。

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

此命令應該會輸出下列訊息：

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

停用 SMB 1 之前，您必須先檢查以確定您的系統上目前未載入 SMB 模組 (如果您已將 SMB 共用) 掛接，就會自動進行此作業。 您可以使用下列命令來執行這項操作，如果未載入 SMB，則應該不會輸出任何內容：

```bash
lsmod | grep cifs
```

若要卸載模組，請先將所有 SMB 共用 (`umount` 如上所述，使用命令來卸載) 。 您可以使用下列命令，在系統上識別所有掛接的 SMB 共用：

```bash
mount | grep cifs
```

當您將所有 SMB 檔案共用取消掛接之後，就可以安全地卸載模組。 您若要這麼做，可透過 `modprobe` 命令：

```bash
sudo modprobe -r cifs
```

您可以使用下列命令，以手動方式載入已卸載 SMB 1 的模組 `modprobe` ：

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

最後，您可以藉由查看中已載入的參數，來檢查已載入的 SMB 模組是否已載入 `/sys/module/cifs/parameters` ：

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

若要在 Ubuntu 和 Debian 為基礎的發行版本上持續停用 SMB 1，如果您還沒有其他模組的自訂選項) 使用設定來呼叫，則必須建立新的檔案 (`/etc/modprobe.d/local.conf` 。 您可以使用下列命令來執行這項操作：

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

您可以藉由載入 SMB 模組來確認這項工作是否正常運作：

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>後續步驟
請參閱這些連結，以取得 Azure 檔案服務的詳細資訊：

* [規劃 Azure 檔案部署](storage-files-planning.md)
* [常見問題集](../storage-files-faq.md)
* [疑難排解](storage-troubleshoot-linux-file-connection-problems.md)
