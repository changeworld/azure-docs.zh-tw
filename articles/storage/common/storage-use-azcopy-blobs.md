---
title: 使用 AzCopy v10 將資料傳輸到或從 Azure Blob 儲存傳輸資料 |微軟文件
description: 本文包含一組 AzCopy 範例命令,可説明您在本地檔案系統和容器之間創建容器、複製檔和同步目錄。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 73685f124f93bb541f33b3b70727d90ce22b3cdd
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263432"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>使用 AzCopy 與 Blob 儲存傳輸資料

AzCopy 是一個命令行實用程式,可用於將數據複製到存儲帳戶、從存儲帳戶中或存儲帳戶之間。 本文包含使用 Blob 儲存的範例命令。

> [!TIP]
> 本文中的示例將路徑參數與單引號 ('') 括起來。 除 Windows 命令外殼 (cmd.exe) 外,在所有命令 shell 中使用單引號。 如果使用 Windows 命令命令命令命令 (cmd.exe),則用雙引號(")而不是單個引號('')括起來路徑參數。

## <a name="get-started"></a>開始使用

請參閱使用[AzCopy 入門](storage-use-azcopy-v10.md)文章下載 AzCopy,並瞭解如何向儲存服務提供授權認證。

> [!NOTE]
> 本文中的範例假定您已使用`AzCopy login`命令 驗證了身份。 然後,AzCopy 使用 Azure AD 帳戶授權存取 Blob 儲存中的數據。
>
> 如果您希望使用 SAS 令牌來授權對 Blob 資料的訪問,則可以將該權杖追加到每個 AzCopy 命令中的資源 URL 中。
>
> 例如： `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'` 。

## <a name="create-a-container"></a>建立容器

可以使用[azcopy make](storage-ref-azcopy-make.md)命令建立容器。 本節中的示例創建名為`mycontainer`的 容器。

|    |     |
|--------|-----------|
| **語法** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **範例** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **範例**(分割區層命名空間) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

有關詳細的參考文件,請參閱[azcopy 製作](storage-ref-azcopy-make.md)。

## <a name="upload-files"></a>上傳檔案

您可以使用[azcopy 複製](storage-ref-azcopy-copy.md)命令從本地電腦上載檔和目錄。

本區段包含下列範例：

> [!div class="checklist"]
> * 上傳檔案
> * 上傳目錄
> * 上傳目錄的內容 
> * 上傳檔案

> [!TIP]
> 您可以使用可選標誌調整上載操作。 下面是一些示例。
>
> |狀況|旗標|
> |---|---|
> |將檔案上載為追加 Blob 或頁面 Blob。|**--Blob**=\[類型\|塊\|Blob 頁面 Blob 追加 Blob\]|
> |上載到特定訪問層(如存檔層)。|**--塊 blob**=\[\|\|層\|無 冷 清存檔\]|
> |自動解壓縮檔。|**--解壓縮**=\[gzip\|放氣\]|
> 
> 有關完整清單,請參閱[選項](storage-ref-azcopy-copy.md#options)。

### <a name="upload-a-file"></a>上傳檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **範例** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **範例**(分割區層命名空間) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

您還可以使用通配符符號 (*) 在檔路徑或檔名中的任何位置上載檔。 例如:`'C:\myDirectory\*.txt'``C:\my*\*.txt`或 。

### <a name="upload-a-directory"></a>上傳目錄

本示例將目錄(以及該目錄中的所有檔案)複製到 blob 容器。 結果是容器中同名的目錄。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **範例**(分割區層命名空間) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

要複製到容器中的目錄,只需在命令字串中指定該目錄的名稱。

|    |     |
|--------|-----------|
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **範例**(分割區層命名空間) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

如果指定容器中不存在的目錄的名稱,AzCopy 會通過該名稱創建新目錄。

### <a name="upload-the-contents-of-a-directory"></a>上傳目錄的內容

您可以使用通配符符號 (*) 上傳目錄的內容,而無需複製包含目錄本身。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **範例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **範例**(分割區層命名空間) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> 追加`--recursive`標誌以上載所有子目錄中的檔。

### <a name="upload-specific-files"></a>上傳檔案

您可以指定完整的檔名,或使用帶有通配符 (*) 的部分名稱。

#### <a name="specify-multiple-complete-file-names"></a>指定多個完整的檔案名稱

將[「茲複製」](storage-ref-azcopy-copy.md)`--include-path`命令與 選項一起使用。 使用分號分隔單個檔案名稱`;`( 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **範例**(分割區層命名空間) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

在此範例中,AzCopy`C:\myDirectory\photos`傳輸目錄`C:\myDirectory\documents\myFile.txt`和檔案。 您需要包括傳輸`C:\myDirectory\photos`目錄中`--recursive`所有 檔案的選項。

您還可以使用選項`--exclude-path`排除檔。 要瞭解更多資訊,請參閱[茲複製參考](storage-ref-azcopy-copy.md)文檔。

#### <a name="use-wildcard-characters"></a>使用通配子

將[「茲複製」](storage-ref-azcopy-copy.md)`--include-pattern`命令與 選項一起使用。 指定包含通配符的部分名稱。 使用半科林 ()`;`分隔名稱。 

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **範例**(分割區層命名空間) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

您還可以使用選項`--exclude-pattern`排除檔。 要瞭解更多資訊,請參閱[茲複製參考](storage-ref-azcopy-copy.md)文檔。

`--include-pattern`和`--exclude-pattern`選項僅適用於檔名,不適用於路徑。  如果要複製目錄樹中存在的所有文本檔,請使用`–recursive`選項獲取整個目錄樹,然後`–include-pattern`使用和`*.txt`指定 獲取所有文本檔。

## <a name="download-files"></a>下載檔案

可以使用[azcopy 複製](storage-ref-azcopy-copy.md)命令將 blob、目錄和容器下載到本地電腦。

本區段包含下列範例：

> [!div class="checklist"]
> * 下載檔案
> * 下載目錄
> * 下載目錄的內容
> * 下載檔案

> [!TIP]
> 您可以使用可選標誌調整下載操作。 下面是一些示例。
>
> |狀況|旗標|
> |---|---|
> |自動解壓縮檔。|**--解壓縮**=\[gzip\|放氣\]|
> |指定您希望與複製相關的日誌條目的詳細程度。|**--日誌級**=\[\|警告\|\|錯誤 資訊 無\]|
> |指定是否以及如何覆蓋目標上的衝突檔和 blob。|**--覆蓋**=\[\|真\|假\|如果來源更新 提示\]|
> 
> 有關完整清單,請參閱[選項](storage-ref-azcopy-copy.md#options)。

> [!NOTE]
> 如果`Content-md5`blob 的屬性值包含哈希,AzCopy 會計算下載數據的 MD5 哈希值,並`Content-md5`驗證存儲在 blob 屬性中的 MD5 哈希是否與計算的哈希值匹配。 如果這些值不匹配,則下載將失敗,除非您通過追加`--check-md5=NoCheck``--check-md5=LogOnly`或複製命令覆蓋此行為。

### <a name="download-a-file"></a>下載檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **範例**(分割區層命名空間) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>下載目錄

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **範例**(分割區層命名空間) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

此示例將生成一個名為`C:\myDirectory\myBlobDirectory`目錄的目錄,其中包含所有下載的檔。

### <a name="download-the-contents-of-a-directory"></a>下載目錄的內容

您可以使用通配符符號 (*) 下載目錄的內容,而無需複製包含目錄本身。

> [!NOTE]
> 目前,此方案僅支持沒有分層命名空間的帳戶。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> 附加`--recursive`標誌以下載所有子目錄中的檔案。

### <a name="download-specific-files"></a>下載檔案

您可以指定完整的檔名,或使用帶有通配符 (*) 的部分名稱。

#### <a name="specify-multiple-complete-file-names"></a>指定多個完整的檔案名稱

將[「茲複製」](storage-ref-azcopy-copy.md)`--include-path`命令與 選項一起使用。 使用半科林 ()`;`分隔單個檔名。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **範例**(分割區層命名空間) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

在此範例中,AzCopy`https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos`傳輸目錄`https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt`和檔案。 您需要包括傳輸`https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos`目錄中`--recursive`所有 檔案的選項。

您還可以使用選項`--exclude-path`排除檔。 要瞭解更多資訊,請參閱[茲複製參考](storage-ref-azcopy-copy.md)文檔。

#### <a name="use-wildcard-characters"></a>使用通配子

將[「茲複製」](storage-ref-azcopy-copy.md)`--include-pattern`命令與 選項一起使用。 指定包含通配符的部分名稱。 使用半科林 ()`;`分隔名稱。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **範例**(分割區層命名空間) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

您還可以使用選項`--exclude-pattern`排除檔。 要瞭解更多資訊,請參閱[茲複製參考](storage-ref-azcopy-copy.md)文檔。

`--include-pattern`和`--exclude-pattern`選項僅適用於檔名,不適用於路徑。  如果要複製目錄樹中存在的所有文本檔,請使用`–recursive`選項獲取整個目錄樹,然後`–include-pattern`使用和`*.txt`指定 獲取所有文本檔。

## <a name="copy-blobs-between-storage-accounts"></a>在儲存體帳戶間複製 Blob

您可以使用 AzCopy 將 Blob 複製到其他儲存帳戶。 複製操作是同步的,因此當命令返回時,指示已複製所有檔。 

AzCopy 使用[伺服器到伺服器](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [API,](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)因此資料直接在儲存伺服器之間複製。 這些複製操作不使用計算機的網路頻寬。 您可以通過設置環境變數的值來提高這些操作的`AZCOPY_CONCURRENCY_VALUE`輸送量。 要瞭解更多資訊,請參閱[優化輸送量](storage-use-azcopy-configure.md#optimize-throughput)。

> [!NOTE]
> 此方案在當前版本中具有以下限制。
>
> - 您必須在每個源 URL 中附加 SAS 權杖。 如果使用 Azure 活動目錄 (AD) 提供授權認證,則可以僅從目標 URL 省略 SAS 權杖。
>-  高級塊 Blob 儲存帳戶不支援訪問層。 通過將`s2s-preserve-access-tier`的設置`false`為 (`--s2s-preserve-access-tier=false`例如: ) 從複製操作中省略 Blob 的訪問層。

本區段包含下列範例：

> [!div class="checklist"]
> * 將 Blob 複製到其他儲存帳戶
> * 將目錄複製到其他儲存帳戶
> * 將容器複製到其他儲存帳戶
> * 將所有容器、目錄和檔案複製到其他儲存帳戶

這些示例還處理具有分層命名空間的帳戶。 [使用資料儲存的儲存的儲存的儲存的儲存的儲存記憶體 ,](../blobs/data-lake-storage-multi-protocol-access.md)您可以在這些帳戶`blob.core.windows.net`上使用相同的網址語法 ( ) 。

> [!TIP]
> 您可以使用可選標誌調整複製操作。 下面是一些示例。
>
> |狀況|旗標|
> |---|---|
> |將檔案複製為追加 Blob 或頁面 Blob。|**--Blob**=\[類型\|塊\|Blob 頁面 Blob 追加 Blob\]|
> |複製到特定訪問層(如存檔層)。|**--塊 blob**=\[\|\|層\|無 冷 清存檔\]|
> |自動解壓縮檔。|**--解壓縮**=\[gzip\|放氣\]|
> 
> 有關完整清單,請參閱[選項](storage-ref-azcopy-copy.md#options)。

### <a name="copy-a-blob-to-another-storage-account"></a>將 Blob 複製到其他儲存帳戶

此檔案是此選項`blob.core.windows.net`, 您會使用相同的網址語法 ( ) 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **範例**(分割區層命名空間) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>將目錄複製到其他儲存帳戶

此檔案是此選項`blob.core.windows.net`, 您會使用相同的網址語法 ( ) 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **範例**(分割區層命名空間)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>將容器複製到其他儲存帳戶

此檔案是此選項`blob.core.windows.net`, 您會使用相同的網址語法 ( ) 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **範例**(分割區層命名空間)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>將所有容器、目錄與 Blob 複製到其他儲存帳戶

此檔案是此選項`blob.core.windows.net`, 您會使用相同的網址語法 ( ) 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **範例**(分割區層命名空間)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>同步檔案

您可以將本地檔案系統的內容與 Blob 容器同步。 您還可以將容器和虛擬目錄彼此同步。 同步是單向的。 換句話說,您選擇這兩個終結點中的哪一個是源,哪個是目標。 同步還使用伺服器到伺服器 API。 本節中介紹的示例還處理具有分層命名空間的帳戶。 

> [!NOTE]
> 當前版本的 AzCopy 不會在其他源和目標之間同步(例如:檔案存儲或 Amazon Web 服務 (AWS) S3 儲存桶)。

[同步](storage-ref-azcopy-sync.md)命令比較檔名和上次修改的時間戳。 如果來源`--delete-destination`檔中不再存在這些檔案,則`true`將`prompt`可選標誌設定為 或刪除目標目錄中的檔案。

如果將`--delete-destination`標誌設置`true`為 AzCopy,則刪除檔而不提供提示。 如果希望在 AzCopy 刪除檔案之前顯示提示,`--delete-destination`請將旗`prompt`標設定為 。

> [!NOTE]
> 為了防止意外刪除,請確保在使用`--delete-destination=prompt|true`標誌之前啟用[軟刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)功能。

> [!TIP]
> 您可以使用可選標誌調整同步操作。 下面是一些示例。
>
> |狀況|旗標|
> |---|---|
> |指定下載時應嚴格驗證 MD5 哈希。|**--檢查-md5**=\[\|無 檢查\|日誌\|僅失敗如果 不同故障,如果失敗或缺失\]|
> |根據模式排除檔。|**--排除路徑**|
> |指定希望同步相關日誌條目的詳細程度。|**--日誌級**=\[\|警告\|\|錯誤 資訊 無\]|
> 
> 有關完整清單,請參閱[選項](storage-ref-azcopy-sync.md#options)。

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>使用對本地端檔案系統的變更更新容器

在這種情況下,容器是目標,本地檔案系統是源。 

|    |     |
|--------|-----------|
| **語法** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>使用容器的變更更新本地端檔案系統

在這種情況下,本地檔案系統是目標,容器是源。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **範例** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>使用另一個容器中的變更更新容器

此命令中出現的第一個容器是源。 第二個是目的地。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>使用變更到另一個檔案分享的目錄更新目錄

此命令中出現的第一個目錄是源。 第二個是目的地。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **範例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>後續步驟

在以下任何文章中尋找更多範例:

- [開始使用 AzCopy](storage-use-azcopy-v10.md)

- [教學課程：使用 AzCopy 將內部部署資料移轉至雲端儲存體](storage-use-azcopy-migrate-on-premises-data.md)

- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)

- [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)
