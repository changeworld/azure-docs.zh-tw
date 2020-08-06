---
title: 如何在 Linux 上將 Azure Blob 儲存體掛接為檔案系統 | Microsoft Docs
description: 瞭解如何使用 blobfuse （Linux 上的虛擬檔案系統驅動程式）掛接 Azure Blob 儲存體容器。
author: rishabpoh
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.date: 2/1/2019
ms.author: ripohane
ms.reviewer: dineshm
ms.openlocfilehash: 8dc7c16b83816d2b408cef7ade06767bfe2a4582
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87826543"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>如何使用 Blobfuse 將 Blob 儲存體掛接為檔案系統

## <a name="overview"></a>概觀
[Blobfuse](https://github.com/Azure/azure-storage-fuse) 是 Azure Blob 儲存體的虛擬檔案系統驅動程式。 Blobfuse 可讓您透過 Linux 檔案系統存取您儲存體帳戶中現有的區塊 Blob 資料。 Blobfuse 會使用以正斜線 '/' 做為分隔符號的虛擬目錄配置。  

本指南示範如何使用 Blobfuse，以及如何在 Linux 上掛接 Blob 儲存體容器並存取資料。 若要深入了解 Blobfuse，請閱讀 [Blobfuse 存放庫](https://github.com/Azure/azure-storage-fuse)中的詳細資料。

> [!WARNING]
> Blobfuse 不保證 100% 合乎 POSIX 的規範，因為它只是將要求轉譯成 [Blob REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) \(英文\)。 例如，重新命名作業在 POSIX 中不可部分完成，不過在 Blobfuse 中並非如此。
> 如需原生檔案系統與 Blobfuse 之間差異的完整清單，請造訪 [Blobfuse 原始程式碼存放庫](https://github.com/azure/azure-storage-fuse)。
> 

## <a name="install-blobfuse-on-linux"></a>在 Linux 上安裝 Blobfuse
在 [Microsoft 的 Linux 軟體存放庫](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software)可取得適用於 Ubuntu 和 RHEL 散發套件的 Blobfuse 二進位檔案。 若要在這些發行版本上安裝 Blobfuse，請設定清單中的任一個存放庫。 如果您的發行版本沒有可用的二進位檔，您也可以遵循 [Azure 儲存體安裝步驟](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) \(英文\)，從原始程式碼建置二進位檔。

Blobfuse 支援在 Ubuntu 14.04、16.04 和18.04 上安裝。 執行此命令以確定您已部署其中一個版本：
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>設定 Microsoft 封裝存放庫
設定 [Microsoft 產品的 Linux 封裝存放庫](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software)。

以 Enterprise Linux 6 發行版為例：
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

同樣地，將 URL 變更為 `.../rhel/7/...`，以指向 Enterprise Linux 7 發行版本。

另一個範例是 Ubuntu 14.04 發行版本：
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

同樣地，將 URL 變更為 `.../ubuntu/16.04/...` 或， `.../ubuntu/18.04/...` 以參考另一個 Ubuntu 版本。

### <a name="install-blobfuse"></a>安裝 Blobfuse

在 Ubuntu/Debian 發佈上：
```bash
sudo apt-get install blobfuse
```

Enterprise Linux 發行版：
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>準備進行掛接
藉由要求檔案系統中的暫存路徑，Blobfuse 提供近似原生的效能，可緩衝及快取任何開啟的檔案。 對於暫存路徑，請選擇效能最高的磁碟，或使用 Ramdisk 來獲得最佳效能。 

> [!NOTE]
> Blobfuse 會將所有開啟的檔案內容存放在暫存路徑。 請確認磁碟空間足夠容納所有開啟的檔案。 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(選擇性) 將 Ramdisk 當做暫存路徑
下列範例會建立 16 GB 的 Ramdisk 和 Blobfuse 的目錄。 請根據需求選擇大小。 Ramdisk 允許 Blobfuse 開啟大小達 16 GB 的檔案。 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>使用 SSD 當做暫存路徑
在 Azure 中，您可以使用虛擬機器上的暫時磁碟 (SSD) 來當做低延遲的 Blobfuse 緩衝區。 在 Ubuntu 發行版本中，此暫時磁碟會掛接於 '/mnt'。 在 Redhat 和 CentOS 發行版本中，該磁碟會掛接於 '/mnt/resource/'。

請確認使用者擁有權存取暫存路徑：
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>設定儲存體帳戶認證
Blobfuse 要求您使用以下格式將認證儲存在文字檔案中： 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
`accountName`是您儲存體帳戶的前置詞，而不是完整的 URL。

使用下列內容建立此檔案：

```
touch ~/fuse_connection.cfg
```

建立並編輯此檔案之後，請務必限制存取，讓其他使用者都無法讀取它。
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> 如果您已在 Windows 上建立組態檔，請務必執行 `dos2unix` 來淨化並將檔案轉換成 Unix 格式。 
>

### <a name="create-an-empty-directory-for-mounting"></a>建立空白的掛接目錄
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>掛接

> [!NOTE]
> 如需完整的掛接選項清單，請參閱 [Blobfuse 存放庫](https://github.com/Azure/azure-storage-fuse#mount-options)。  
> 

若要掛接 Blobfuse，請讓使用者執行以下命令。 該命令會將 '/path/to/fuse_connection.cfg' 指定的容器掛接到 '/mycontainer' 位置。

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

您現在應該可以透過一般的檔案系統 API 存取區塊 Blob 了。 根據預設，掛接該目錄的使用者是唯一能存取它的人，這樣能保護對目錄的存取。 如果您想要允許所有使用者存取，請透過 ```-o allow_other``` 選項掛接。 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>後續步驟

* [Blobfuse 首頁](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [回報 Blobfuse 問題](https://github.com/Azure/azure-storage-fuse/issues) 

