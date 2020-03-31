---
title: 使用 AzCopy v10 將資料傳輸到或從 Azure Blob 存儲傳輸資料 |微軟文檔
description: 本文包含一組 AzCopy 示例命令，可説明您在本地檔案系統和容器之間創建容器、複製檔和同步目錄。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: fbdb447905ae43fe92693dfe45c1add710f76355
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933577"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>使用 AzCopy 和 Blob 存儲傳輸資料

AzCopy 是一個命令列實用程式，可用於將資料複製到存儲帳戶、從存儲帳戶中或存儲帳戶之間。 本文包含使用 Blob 存儲的示例命令。

## <a name="get-started"></a>開始使用

請參閱使用[AzCopy 入門](storage-use-azcopy-v10.md)文章下載 AzCopy，並瞭解如何向存儲服務提供授權憑據。

> [!NOTE]
> 本文中的示例假定您已使用 命令`AzCopy login`驗證了身份。 然後，AzCopy 使用 Azure AD 帳戶授權訪問 Blob 存儲中的資料。
>
> 如果您希望使用 SAS 權杖來授權對 Blob 資料的訪問，則可以將該權杖追加到每個 AzCopy 命令中的資源 URL 中。
>
> 例如：`'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`。

## <a name="create-a-container"></a>建立容器

> [!TIP]
> 本節中的示例將路徑參數與單引號 （''） 括起來。 除 Windows 命令外殼 （cmd.exe） 外，在所有命令 shell 中使用單引號。 如果使用 Windows 命令命令命令命令 （cmd.exe），則用雙引號（"）而不是單個引號（''）括起來路徑參數。

可以使用[azcopy make](storage-ref-azcopy-make.md)命令創建容器。 本節中的示例創建名為 的`mycontainer`容器。

|    |     |
|--------|-----------|
| **語法** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **範例** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **示例**（階層命名空間） | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

有關詳細的參考文檔，請參閱[azcopy 製作](storage-ref-azcopy-make.md)。

## <a name="upload-files"></a>上傳檔案

您可以使用[azcopy 複製](storage-ref-azcopy-copy.md)命令從本地電腦上載檔和目錄。

本區段包含下列範例：

> [!div class="checklist"]
> * 上傳檔案
> * 上傳目錄
> * 上傳目錄的內容 
> * 上傳特定檔

有關詳細的參考文檔，請參閱[茲抄襲副本](storage-ref-azcopy-copy.md)。

> [!TIP]
> 本節中的示例將路徑參數與單引號 （''） 括起來。 除 Windows 命令外殼 （cmd.exe） 外，在所有命令 shell 中使用單引號。 如果使用 Windows 命令命令命令命令 （cmd.exe），則用雙引號（"）而不是單個引號（''）括起來路徑參數。

### <a name="upload-a-file"></a>上傳檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **範例** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **示例**（階層命名空間） | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

您還可以使用萬用字元符號 （*） 在檔路徑或檔案名中的任何位置上載檔。 例如：`'C:\myDirectory\*.txt'`或`C:\my*\*.txt`。

> [!NOTE]
> 預設情況下，AzCopy 將資料上載為塊 blob。 要將檔上載為"追加 Blob"或"頁面 Blob"，請使用 標誌`--blob-type=[BlockBlob|PageBlob|AppendBlob]`。
> 預設情況下，AzCopy 會上傳您的資料以繼承帳戶訪問層。 要將檔上載到特定的[訪問層](../blobs/storage-blob-storage-tiers.md)，請使用 標誌`--block-blob-tier=[Hot|Cool|Archive]`。

### <a name="upload-a-directory"></a>上傳目錄

本示例將目錄（以及該目錄中的所有檔）複製到 blob 容器。 結果是容器中同名的目錄。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **示例**（階層命名空間） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

要複製到容器中的目錄，只需在命令字串中指定該目錄的名稱。

|    |     |
|--------|-----------|
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **示例**（階層命名空間） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

如果指定容器中不存在的目錄的名稱，AzCopy 會通過該名稱創建新目錄。

### <a name="upload-the-contents-of-a-directory"></a>上傳目錄的內容

您可以使用萬用字元符號 （*） 上載目錄的內容，而無需複製包含目錄本身。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **範例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **示例**（階層命名空間） | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> 追加`--recursive`標誌以上載所有子目錄中的檔。

### <a name="upload-specific-files"></a>上傳特定檔

您可以指定完整的檔案名，或使用帶有萬用字元 （*） 的部分名稱。

#### <a name="specify-multiple-complete-file-names"></a>指定多個完整的檔案名

將["茲複製"](storage-ref-azcopy-copy.md)命令與`--include-path`選項一起使用。 使用分號分隔單個檔案名 （`;`。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **示例**（階層命名空間） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

在此示例中，AzCopy 傳輸`C:\myDirectory\photos`目錄和`C:\myDirectory\documents\myFile.txt`檔。 您需要包括傳輸`C:\myDirectory\photos`目錄中所有`--recursive`檔的選項。

您還可以使用 選項`--exclude-path`排除檔。 要瞭解更多資訊，請參閱[茲複製參考](storage-ref-azcopy-copy.md)文檔。

#### <a name="use-wildcard-characters"></a>使用萬用字元

將["茲複製"](storage-ref-azcopy-copy.md)命令與`--include-pattern`選項一起使用。 指定包含萬用字元的部分名稱。 使用半科林 （）`;`分隔名稱。 

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **示例**（階層命名空間） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

您還可以使用 選項`--exclude-pattern`排除檔。 要瞭解更多資訊，請參閱[茲複製參考](storage-ref-azcopy-copy.md)文檔。

`--include-pattern`和`--exclude-pattern`選項僅適用于檔案名，不適用於路徑。  如果要複製目錄樹中存在的所有文字檔，請使用`–recursive`選項獲取整個目錄樹，然後使用`–include-pattern`和 指定`*.txt`獲取所有文字檔。

## <a name="download-files"></a>下載檔案

可以使用[azcopy 複製](storage-ref-azcopy-copy.md)命令將 blob、目錄和容器下載到本地電腦。

本區段包含下列範例：

> [!div class="checklist"]
> * 下載檔案
> * 下載目錄
> * 下載目錄的內容
> * 下載特定檔

> [!NOTE]
> 如果`Content-md5`blob 的屬性值包含雜湊，AzCopy 會計算下載資料的 MD5 雜湊值，並驗證存儲在 blob`Content-md5`屬性中的 MD5 雜湊是否與計算的雜湊值匹配。 如果這些值不匹配，則下載將失敗，除非您通過追加`--check-md5=NoCheck`或`--check-md5=LogOnly`複製命令覆蓋此行為。

有關詳細的參考文檔，請參閱[茲抄襲副本](storage-ref-azcopy-copy.md)。

> [!TIP]
> 本節中的示例將路徑參數與單引號 （''） 括起來。 除 Windows 命令外殼 （cmd.exe） 外，在所有命令 shell 中使用單引號。 如果使用 Windows 命令命令命令命令 （cmd.exe），則用雙引號（"）而不是單個引號（''）括起來路徑參數。

### <a name="download-a-file"></a>下載檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **示例**（階層命名空間） | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>下載目錄

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **示例**（階層命名空間） | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

此示例將生成一個名為`C:\myDirectory\myBlobDirectory`目錄的目錄，其中包含所有下載的檔。

### <a name="download-the-contents-of-a-directory"></a>下載目錄的內容

您可以使用萬用字元符號 （*） 下載目錄的內容，而無需複製包含目錄本身。

> [!NOTE]
> 目前，此方案僅支援沒有階層命名空間的帳戶。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> 追加`--recursive`標誌以下載所有子目錄中的檔。

### <a name="download-specific-files"></a>下載特定檔

您可以指定完整的檔案名，或使用帶有萬用字元 （*） 的部分名稱。

#### <a name="specify-multiple-complete-file-names"></a>指定多個完整的檔案名

將["茲複製"](storage-ref-azcopy-copy.md)命令與`--include-path`選項一起使用。 使用半科林 （）`;`分隔單個檔案名。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **示例**（階層命名空間） | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

在此示例中，AzCopy 傳輸`https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos`目錄和`https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt`檔。 您需要包括傳輸`https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos`目錄中所有`--recursive`檔的選項。

您還可以使用 選項`--exclude-path`排除檔。 要瞭解更多資訊，請參閱[茲複製參考](storage-ref-azcopy-copy.md)文檔。

#### <a name="use-wildcard-characters"></a>使用萬用字元

將["茲複製"](storage-ref-azcopy-copy.md)命令與`--include-pattern`選項一起使用。 指定包含萬用字元的部分名稱。 使用半科林 （）`;`分隔名稱。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **示例**（階層命名空間） | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

您還可以使用 選項`--exclude-pattern`排除檔。 要瞭解更多資訊，請參閱[茲複製參考](storage-ref-azcopy-copy.md)文檔。

`--include-pattern`和`--exclude-pattern`選項僅適用于檔案名，不適用於路徑。  如果要複製目錄樹中存在的所有文字檔，請使用`–recursive`選項獲取整個目錄樹，然後使用`–include-pattern`和 指定`*.txt`獲取所有文字檔。

## <a name="copy-blobs-between-storage-accounts"></a>在儲存體帳戶間複製 Blob

您可以使用 AzCopy 將 Blob 複製到其他存儲帳戶。 複製操作是同步的，因此當命令返回時，指示已複製所有檔。 

AzCopy 使用[伺服器到伺服器](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [API，](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)因此資料直接在存儲伺服器之間複製。 這些複製操作不使用電腦的網路頻寬。 您可以通過設置環境變數的值來提高這些操作的`AZCOPY_CONCURRENCY_VALUE`輸送量。 要瞭解更多資訊，請參閱[優化輸送量](storage-use-azcopy-configure.md#optimize-throughput)。

> [!NOTE]
> 此方案在當前版本中具有以下限制。
>
> - 您必須在每個源 URL 中附加 SAS 權杖。 如果使用 Azure 活動目錄 （AD） 提供授權憑據，則可以僅從目標 URL 省略 SAS 權杖。
>-  高級塊 Blob 存儲帳戶不支援訪問層。 通過將`s2s-preserve-access-tier`的 設置為`false`（例如： `--s2s-preserve-access-tier=false`） 從複製操作中省略 Blob 的訪問層。

本區段包含下列範例：

> [!div class="checklist"]
> * 將 Blob 複製到其他存儲帳戶
> * 將目錄複寫到其他存儲帳戶
> * 將容器複製到其他存儲帳戶
> * 將所有容器、目錄和檔案複製到其他存儲帳戶

有關詳細的參考文檔，請參閱[茲抄襲副本](storage-ref-azcopy-copy.md)。

> [!TIP]
> 本節中的示例將路徑參數與單引號 （''） 括起來。 除 Windows 命令外殼 （cmd.exe） 外，在所有命令 shell 中使用單引號。 如果使用 Windows 命令命令命令命令 （cmd.exe），則用雙引號（"）而不是單個引號（''）括起來路徑參數。

 這些示例還處理具有階層命名空間的帳戶。 [通過資料存儲湖存儲的多協定訪問](../blobs/data-lake-storage-multi-protocol-access.md)，您可以在這些帳戶上使用相同的 URL`blob.core.windows.net`語法 （ ） 。 

### <a name="copy-a-blob-to-another-storage-account"></a>將 Blob 複製到其他存儲帳戶

對於具有階層命名空間的`blob.core.windows.net`帳戶，使用相同的 URL 語法 （ ） 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **示例**（階層命名空間） | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>將目錄複寫到其他存儲帳戶

對於具有階層命名空間的`blob.core.windows.net`帳戶，使用相同的 URL 語法 （ ） 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **示例**（階層命名空間）| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>將容器複製到其他存儲帳戶

對於具有階層命名空間的`blob.core.windows.net`帳戶，使用相同的 URL 語法 （ ） 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **示例**（階層命名空間）| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>將所有容器、目錄和 Blob 複製到其他存儲帳戶

對於具有階層命名空間的`blob.core.windows.net`帳戶，使用相同的 URL 語法 （ ） 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **示例**（階層命名空間）| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>同步檔

您可以將本地檔案系統的內容與 Blob 容器同步。 您還可以將容器和虛擬目錄彼此同步。 同步是單向的。 換句話說，您選擇這兩個終結點中的哪一個是源，哪個是目標。 同步還使用伺服器到伺服器 API。 本節仲介紹的示例還處理具有階層命名空間的帳戶。 

> [!NOTE]
> 當前版本的 AzCopy 不會在其他源和目標之間同步（例如：檔存儲或 Amazon Web 服務 （AWS） S3 存儲桶）。

[同步](storage-ref-azcopy-sync.md)命令比較檔案名和上次修改的時間戳記。 如果源`--delete-destination`目錄中不再存在這些檔，則`true`將`prompt`可選標誌設置為 或 刪除目標目錄中的檔。

如果將`--delete-destination`標誌設置為`true`AzCopy，則刪除檔而不提供提示。 如果希望在 AzCopy 刪除檔之前顯示提示，請將`--delete-destination`標誌設置為`prompt`。

> [!NOTE]
> 為了防止意外刪除，請確保在使用`--delete-destination=prompt|true`標誌之前啟用[虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)功能。

有關詳細的參考文檔，請參閱[茲貝比同步](storage-ref-azcopy-sync.md)。

> [!TIP]
> 本節中的示例將路徑參數與單引號 （''） 括起來。 除 Windows 命令外殼 （cmd.exe） 外，在所有命令 shell 中使用單引號。 如果使用 Windows 命令命令命令命令 （cmd.exe），則用雙引號（"）而不是單個引號（''）括起來路徑參數。

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>使用對本地檔案系統的更改更新容器

在這種情況下，容器是目標，本地檔案系統是源。 

|    |     |
|--------|-----------|
| **語法** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>使用容器的更改更新本地檔案系統

在這種情況下，本地檔案系統是目標，容器是源。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **範例** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>使用另一個容器中的更改更新容器

此命令中出現的第一個容器是源。 第二個是目的地。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>使用更改到另一個檔共用中的目錄更新目錄

此命令中出現的第一個目錄是源。 第二個是目的地。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **範例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>後續步驟

在以下任何文章中查找更多示例：

- [開始使用 AzCopy](storage-use-azcopy-v10.md)

- [教學課程：使用 AzCopy 將內部部署資料移轉至雲端儲存體](storage-use-azcopy-migrate-on-premises-data.md)

- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)

- [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)
