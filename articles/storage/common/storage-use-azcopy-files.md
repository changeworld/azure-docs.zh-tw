---
title: 使用 AzCopy v10 將資料傳輸到或從 Azure 檔案傳輸資料 |微軟文檔
description: 使用 AzCopy 和檔存儲傳輸資料。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8aa0e5304825b3f016694a40b3fc1e176518237a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526683"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>使用 AzCopy 和檔案儲存體轉送資料 

AzCopy 是一個命令列實用程式，可用於將 Blob 或檔案複製到存儲帳戶或從存儲帳戶複製。 本文包含使用 Azure 檔的示例命令。

開始之前，請參閱[開始使用 AzCopy](storage-use-azcopy-v10.md)文章以下載 AzCopy 並熟悉該工具。

## <a name="create-file-shares"></a>建立檔案共用

您可以使用[azcopy make](storage-ref-azcopy-make.md)命令創建檔共用。 本節中的示例創建名為`myfileshare`的檔共用。

> [!TIP]
> 本節中的示例將路徑參數與單引號 （''） 括起來。 除 Windows 命令外殼 （cmd.exe） 外，在所有命令 shell 中使用單引號。 如果使用 Windows 命令命令命令命令 （cmd.exe），則用雙引號（"）而不是單個引號（''）括起來路徑參數。

|    |     |
|--------|-----------|
| **語法** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
| **範例** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

有關詳細的參考文檔，請參閱[azcopy 製作](storage-ref-azcopy-make.md)。

## <a name="upload-files"></a>上傳檔案

您可以使用[azcopy 複製](storage-ref-azcopy-copy.md)命令從本地電腦上載檔和目錄。

本區段包含下列範例：

> [!div class="checklist"]
> * 上傳檔案
> * 上傳目錄
> * 上傳目錄的內容
> * 上傳特定檔

> [!NOTE]
> AzCopy 不會自動計算和存儲檔的 md5 雜湊代碼。 如果希望 AzCopy 執行此操作，請將`--put-md5`標誌追加到每個複製命令。 這樣，當下載檔時，AzCopy 會計算下載資料的 MD5 雜湊值，並驗證存儲在檔`Content-md5`屬性中的 MD5 雜湊是否與計算的雜湊值匹配。

有關詳細的參考文檔，請參閱[茲抄襲副本](storage-ref-azcopy-copy.md)。

> [!TIP]
> 本節中的示例將路徑參數與單引號 （''） 括起來。 除 Windows 命令外殼 （cmd.exe） 外，在所有命令 shell 中使用單引號。 如果使用 Windows 命令命令命令命令 （cmd.exe），則用雙引號（"）而不是單個引號（''）括起來路徑參數。

### <a name="upload-a-file"></a>上傳檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **範例** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

您還可以使用萬用字元符號 （*） 在檔路徑或檔案名中的任何位置上載檔。 例如：`'C:\myDirectory\*.txt'`或`C:\my*\*.txt`。

### <a name="upload-a-directory"></a>上傳目錄

本示例將目錄（以及該目錄中的所有檔）複製到檔共用。 結果是檔共用中的同名目錄。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

要複製到檔共用中的目錄，只需在命令字串中指定該目錄的名稱。

|    |     |
|--------|-----------|
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

如果指定檔共用中不存在的目錄的名稱，AzCopy 會通過該名稱創建新目錄。

### <a name="upload-the-contents-of-a-directory"></a>上傳目錄的內容

您可以使用萬用字元符號 （*） 上載目錄的內容，而無需複製包含目錄本身。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **範例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> 追加`--recursive`標誌以上載所有子目錄中的檔。

### <a name="upload-specific-files"></a>上傳特定檔

您可以指定完整的檔案名，或使用帶有萬用字元 （*） 的部分名稱。

#### <a name="specify-multiple-complete-file-names"></a>指定多個完整的檔案名

將["茲複製"](storage-ref-azcopy-copy.md)命令與`--include-path`選項一起使用。 使用分號分隔單個檔案名 （`;`。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

在此示例中，AzCopy 傳輸`C:\myDirectory\photos`目錄和`C:\myDirectory\documents\myFile.txt`檔。 您需要包括傳輸`C:\myDirectory\photos`目錄中所有`--recursive`檔的選項。

您還可以使用 選項`--exclude-path`排除檔。 要瞭解更多資訊，請參閱[茲複製參考](storage-ref-azcopy-copy.md)文檔。

#### <a name="use-wildcard-characters"></a>使用萬用字元

將["茲複製"](storage-ref-azcopy-copy.md)命令與`--include-pattern`選項一起使用。 指定包含萬用字元的部分名稱。 使用半科林 （）`;`分隔名稱。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

您還可以使用 選項`--exclude-pattern`排除檔。 要瞭解更多資訊，請參閱[茲複製參考](storage-ref-azcopy-copy.md)文檔。

`--include-pattern`和`--exclude-pattern`選項僅適用于檔案名，不適用於路徑。  如果要複製目錄樹中存在的所有文字檔，請使用`–recursive`選項獲取整個目錄樹，然後使用`–include-pattern`和 指定`*.txt`獲取所有文字檔。

## <a name="download-files"></a>下載檔案

您可以使用[azcopy 複製](storage-ref-azcopy-copy.md)命令將檔、目錄和檔共用下載到本地電腦。

本區段包含下列範例：

> [!div class="checklist"]
> * 下載檔案
> * 下載目錄
> * 下載目錄的內容
> * 下載特定檔

> [!NOTE]
> 如果檔`Content-md5`的屬性值包含雜湊，AzCopy 會計算下載資料的 MD5 雜湊值，並驗證存儲在檔`Content-md5`屬性中的 MD5 雜湊是否與計算的雜湊值匹配。 如果這些值不匹配，則下載將失敗，除非您通過追加`--check-md5=NoCheck`或`--check-md5=LogOnly`複製命令覆蓋此行為。

有關詳細的參考文檔，請參閱[茲抄襲副本](storage-ref-azcopy-copy.md)。

> [!TIP]
> 本節中的示例將路徑參數與單引號 （''） 括起來。 除 Windows 命令外殼 （cmd.exe） 外，在所有命令 shell 中使用單引號。 如果使用 Windows 命令命令命令命令 （cmd.exe），則用雙引號（"）而不是單個引號（''）括起來路徑參數。

### <a name="download-a-file"></a>下載檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **範例** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>下載目錄

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **範例** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

此示例將生成一個名為`C:\myDirectory\myFileShareDirectory`目錄的目錄，其中包含所有下載的檔。

### <a name="download-the-contents-of-a-directory"></a>下載目錄的內容

您可以使用萬用字元符號 （*） 下載目錄的內容，而無需複製包含目錄本身。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **範例** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> 追加`--recursive`標誌以下載所有子目錄中的檔。

### <a name="download-specific-files"></a>下載特定檔

您可以指定完整的檔案名，或使用帶有萬用字元 （*） 的部分名稱。

#### <a name="specify-multiple-complete-file-names"></a>指定多個完整的檔案名

將["茲複製"](storage-ref-azcopy-copy.md)命令與`--include-path`選項一起使用。 使用半科林 （）`;`分隔單個檔案名。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **範例** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

在此示例中，AzCopy 傳輸`https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos`目錄和`https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt`檔。 您需要包括傳輸`https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos`目錄中所有`--recursive`檔的選項。

您還可以使用 選項`--exclude-path`排除檔。 要瞭解更多資訊，請參閱[茲複製參考](storage-ref-azcopy-copy.md)文檔。

#### <a name="use-wildcard-characters"></a>使用萬用字元

將["茲複製"](storage-ref-azcopy-copy.md)命令與`--include-pattern`選項一起使用。 指定包含萬用字元的部分名稱。 使用半科林 （）`;`分隔名稱。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

您還可以使用 選項`--exclude-pattern`排除檔。 要瞭解更多資訊，請參閱[茲複製參考](storage-ref-azcopy-copy.md)文檔。

`--include-pattern`和`--exclude-pattern`選項僅適用于檔案名，不適用於路徑。  如果要複製目錄樹中存在的所有文字檔，請使用`–recursive`選項獲取整個目錄樹，然後使用`–include-pattern`和 指定`*.txt`獲取所有文字檔。

## <a name="copy-files-between-storage-accounts"></a>在儲存體帳戶之間複製檔案

您可以使用 AzCopy 將檔案複製到其他存儲帳戶。 複製操作是同步的，因此當命令返回時，指示已複製所有檔。

AzCopy 使用[伺服器到伺服器](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [API，](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)因此資料直接在存儲伺服器之間複製。 這些複製操作不使用電腦的網路頻寬。 您可以通過設置環境變數的值來提高這些操作的`AZCOPY_CONCURRENCY_VALUE`輸送量。 要瞭解更多資訊，請參閱[優化輸送量](storage-use-azcopy-configure.md#optimize-throughput)。

本區段包含下列範例：

> [!div class="checklist"]
> * 將檔案複製到其他存儲帳戶
> * 將目錄複寫到其他存儲帳戶
> * 將檔共用複製到其他存儲帳戶
> * 將所有檔共用、目錄和檔案複製到其他存儲帳戶

有關詳細的參考文檔，請參閱[茲抄襲副本](storage-ref-azcopy-copy.md)。

> [!TIP]
> 本節中的示例將路徑參數與單引號 （''） 括起來。 除 Windows 命令外殼 （cmd.exe） 外，在所有命令 shell 中使用單引號。 如果使用 Windows 命令命令命令命令 （cmd.exe），則用雙引號（"）而不是單個引號（''）括起來路徑參數。

### <a name="copy-a-file-to-another-storage-account"></a>將檔案複製到其他存儲帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **範例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>將目錄複寫到其他存儲帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>將檔共用複製到其他存儲帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>將所有檔共用、目錄和檔案複製到其他存儲帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **範例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>同步檔

您可以將檔共用的內容與其他檔共用同步。 還可以將檔共用中的目錄內容與另一個檔共用中的目錄的內容同步。 同步是單向的。 換句話說，您選擇這兩個終結點中的哪一個是源，哪個是目標。 同步還使用伺服器到伺服器 API。

> [!NOTE]
> 目前，此方案僅支援沒有階層命名空間的帳戶。 當前版本的 AzCopy 不會在 Azure 檔和 Blob 存儲之間同步。

[同步](storage-ref-azcopy-sync.md)命令比較檔案名和上次修改的時間戳記。 如果源`--delete-destination`目錄中不再存在這些檔，則`true`將`prompt`可選標誌設置為 或 刪除目標目錄中的檔。

如果將`--delete-destination`標誌設置為`true`AzCopy，則刪除檔而不提供提示。 如果希望在 AzCopy 刪除檔之前顯示提示，請將`--delete-destination`標誌設置為`prompt`。

有關詳細的參考文檔，請參閱[茲貝比同步](storage-ref-azcopy-sync.md)。

> [!TIP]
> 本節中的示例將路徑參數與單引號 （''） 括起來。 除 Windows 命令外殼 （cmd.exe） 外，在所有命令 shell 中使用單引號。 如果使用 Windows 命令命令命令命令 （cmd.exe），則用雙引號（"）而不是單個引號（''）括起來路徑參數。

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>使用更改更新檔共用，並更改其他檔共用

此命令中顯示的第一個檔共用是源。 第二個是目的地。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **範例** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>使用更改到另一個檔共用中的目錄更新目錄

此命令中出現的第一個目錄是源。 第二個是目的地。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **範例** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="next-steps"></a>後續步驟

在以下任何文章中查找更多示例：

- [開始使用 AzCopy](storage-use-azcopy-v10.md)

- [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)
