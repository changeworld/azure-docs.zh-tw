---
title: 使用 AzCopy v10 將資料複製或移動到 Azure 儲存體 |Microsoft Docs
description: AzCopy 是一種命令列公用程式，可讓您用來在儲存體帳戶之間複製資料。 本文可協助您下載 AzCopy、連線到您的儲存體帳戶，然後傳輸檔案。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: bec221f762213fa8576ccef30e9b6a27aa708f01
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944141"
---
# <a name="get-started-with-azcopy"></a>開始使用 AzCopy

AzCopy 是命令列公用程式，可讓您在儲存體帳戶之間複製 Blob 或檔案。 本文可協助您下載 AzCopy、連線到您的儲存體帳戶，然後傳輸檔案。

> [!NOTE]
> AzCopy **V10** 是目前支援的 AzCopy 版本。
>
> 如果您需要使用舊版的 AzCopy，請參閱本文的「 [使用舊版的 AzCopy](#previous-version) 」一節。

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>下載 AzCopy

首先，將 AzCopy V10 可執行檔下載至您電腦上的任何目錄。 AzCopy V10 只是一個可執行檔，因此不需要安裝任何專案。

- [Windows 64](https://aka.ms/downloadazcopy-v10-windows) 位 (zip) 
- [Windows 32](https://aka.ms/downloadazcopy-v10-windows-32bit) 位 (zip) 
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar) 
- [macOS](https://aka.ms/downloadazcopy-v10-mac) (zip) 

這些檔案會壓縮成 zip 檔案 (Windows 和 Mac) 或 (Linux) 的 tar 檔案。 若要下載並解壓縮 Linux 上的 tar 檔案，請參閱您 Linux 發行版本的檔。

> [!NOTE]
> 如果您想要將資料複製到您的 [Azure 表格儲存體](../tables/table-storage-overview.md) 服務或從中複製資料，請安裝 [AzCopy 7.3 版](https://aka.ms/downloadazcopynet)。


## <a name="run-azcopy"></a>執行 AzCopy

為了方便起見，請考慮將 AzCopy 可執行檔的目錄位置新增至您的系統路徑，以方便使用。 如此一來，您就可以 `azcopy` 從系統上的任何目錄進行輸入。

如果您選擇不要將 AzCopy 目錄新增至您的路徑，您必須將目錄變更為 AzCopy 可執行檔的位置，並 `azcopy` `.\azcopy` 在 Windows PowerShell 命令提示字元中輸入或。

若要查看命令的清單，請輸入， `azcopy -h` 然後按下 enter 鍵。

若要瞭解特定命令，請只包含命令的名稱 (例如： `azcopy list -h`) 。

> [!div class="mx-imgBorder"]
> ![內嵌說明](media/storage-use-azcopy-v10/azcopy-inline-help.png)


若要尋找每個命令和命令參數的詳細參考檔，請參閱 [azcopy](storage-ref-azcopy.md)

> [!NOTE] 
> 作為 Azure 儲存體帳戶的擁有者，您不會自動獲指派存取資料的許可權。 您必須先決定要如何為儲存體服務提供授權認證，才能使用 AzCopy 來進行任何有意義的動作。 

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>授權 AzCopy

您可以使用 Azure Active Directory (AD) 或使用共用存取簽章 (SAS) 權杖來提供授權認證。

使用此資料表作為指南：

| 儲存體類型 | 目前支援的授權方法 |
|--|--|
|**Blob 儲存體** | Azure AD 和 SAS |
|**Blob 儲存體 (階層命名空間)** | Azure AD 和 SAS |
|**檔案儲存體** | 僅限 SAS |

#### <a name="option-1-use-azure-active-directory"></a>選項1：使用 Azure Active Directory

此選項僅適用于 blob 儲存體。 藉由使用 Azure Active Directory，您可以提供一次認證，而不需要將 SAS 權杖附加至每個命令。  

> [!NOTE]
> 在目前的版本中，如果您打算在儲存體帳戶之間複製 blob，則必須將 SAS 權杖附加至每個來源 URL。 您只能從目的地 URL 中省略 SAS 權杖。 如需範例，請參閱 [在儲存體帳戶之間複製 blob](#transfer-data)。

若要使用 Azure AD 來授與存取權，請參閱使用 [AzCopy 和 Azure Active Directory (Azure AD) 來授權存取 blob ](storage-use-azcopy-authorize-azure-active-directory.md)。

#### <a name="option-2-use-a-sas-token"></a>選項2：使用 SAS 權杖

您可以將 SAS 權杖附加至每個在 AzCopy 命令中使用的來源或目的地 URL。

此範例命令會以遞迴方式將資料從本機目錄複寫到 blob 容器。 虛構的 SAS 權杖會附加至容器 URL 的結尾。

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

若要深入瞭解 SAS 權杖，以及如何取得 SAS 權杖，請參閱 [使用共用存取簽章 (SAS) ](./storage-sas-overview.md)。

<a id="transfer-data"></a>

## <a name="transfer-data"></a>傳送資料

授權您的身分識別或取得 SAS 權杖之後，您就可以開始傳輸資料。

若要尋找範例命令，請參閱這些文章。

| 服務 | 發行項 |
|--------|-----------|
|Azure Blob 儲存體 |[將檔案上傳至 Azure Blob 儲存體](storage-use-azcopy-blobs-upload.md)<br><br>[從 Azure Blob 儲存體下載 blob](storage-use-azcopy-blobs-download.md)<br><br>[在 Azure 儲存體帳戶之間複製 blob](storage-use-azcopy-blobs-copy.md)<br><br>[與 Azure Blob 儲存體同步處理](storage-use-azcopy-blobs-synchronize.md)|
|Azure 檔案 |[使用 AzCopy 和檔案儲存體傳輸資料](storage-use-azcopy-files.md) (機器翻譯)|
|Amazon S3|[使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)|
|Azure Stack 儲存體|[使用 AzCopy 和 Azure Stack 儲存體傳輸資料](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="use-in-a-script"></a>在腳本中使用

#### <a name="obtain-a-static-download-link"></a>取得靜態下載連結

經過一段時間後，AzCopy [下載連結](#download-and-install-azcopy) 會指向 AzCopy 的新版本。 如果您的腳本下載 AzCopy，如果較新版本的 AzCopy 修改您的腳本所依賴的功能，腳本可能會停止運作。

若要避免這些問題，請) 連結至 AzCopy 的目前版本，取得靜態 (不變的連結。 如此一來，您的腳本就會在每次執行時下載相同的 AzCopy 完全相同版本。

若要取得連結，請執行下列命令：

| 作業系統  | 命令 |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> 若是 Linux， `--strip-components=1` 命令上的 `tar` 命令會移除包含版本名稱的最上層資料夾，並改為將二進位檔直接解壓縮到目前的資料夾中。 這可讓您只更新 URL 來更新腳本的新版本 `azcopy` `wget` 。

URL 會出現在此命令的輸出中。 然後，您的腳本就可以使用該 URL 來下載 AzCopy。

| 作業系統  | 命令 |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>在 SAS 權杖中 Escape 特殊字元

在副檔名為的批次檔中 `.cmd` ，您必須將 `%` 出現在 SAS 權杖中的字元進行換用。 若要這麼做，您可以 `%` `%` 在 SAS 權杖字串中的現有字元旁新增額外的字元。

#### <a name="run-scripts-by-using-jenkins"></a>使用 Jenkins 執行腳本

如果您打算使用 [Jenkins](https://jenkins.io/) 來執行腳本，請務必在腳本的開頭放置下列命令。

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>在 Azure 儲存體總管中使用

[儲存體總管](https://azure.microsoft.com/features/storage-explorer/) 使用 AzCopy 來執行它的所有資料傳輸作業。 如果您想要利用 AzCopy 的效能優勢，但想要使用圖形化使用者介面而非命令列來與檔案互動，您可以使用 [儲存體總管](https://azure.microsoft.com/features/storage-explorer/) 。

儲存體總管會使用您的帳戶金鑰來執行操作，因此在您登入儲存體總管之後，就不需要提供額外的授權認證。

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>設定、優化和修正

請參閱 [設定、優化和疑難排解 AzCopy](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>使用先前的版本

如果您需要使用舊版的 AzCopy，請參閱下列其中一個連結：

- [AzCopy on Windows (v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy on Linux (v7) ](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>後續步驟

如果您有任何疑問、問題或一般意見反應，請 [在 GitHub 頁面上](https://github.com/Azure/azure-storage-azcopy) 提交。
