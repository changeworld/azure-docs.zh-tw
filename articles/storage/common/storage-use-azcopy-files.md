---
title: 使用 AzCopy v10，將資料傳入或傳出 Azure 檔案儲存體Microsoft Docs
description: 使用 AzCopy 和檔案儲存體來傳輸資料。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8aa0e5304825b3f016694a40b3fc1e176518237a
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526683"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>使用 AzCopy 和檔案儲存體傳輸資料 

AzCopy 是命令列公用程式，可讓您在儲存體帳戶之間複製 blob 或檔案。 本文包含可搭配 Azure 檔案儲存體使用的範例命令。

開始之前，請參閱開始[使用 AzCopy](storage-use-azcopy-v10.md)一文，下載 AzCopy 並熟悉此工具。

## <a name="create-file-shares"></a>建立檔案共用

您可以使用[azcopy make](storage-ref-azcopy-make.md)命令來建立檔案共用。 本節中的範例會建立名為 `myfileshare`的檔案共用。

> [!TIP]
> 本節中的範例會以單引號（' '）括住路徑引數。 在所有命令 shell 中使用單引號，但 Windows 命令介面（cmd.exe）除外。 如果您使用 Windows 命令 Shell （cmd.exe），請將路徑引數括在雙引號（""），而不是單引號（' '）。

|    |     |
|--------|-----------|
| **語法** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
| **範例** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

如需詳細的參考檔，請參閱[azcopy make](storage-ref-azcopy-make.md)。

## <a name="upload-files"></a>上傳檔案

您可以使用[azcopy copy](storage-ref-azcopy-copy.md)命令，從本機電腦上傳檔案和目錄。

本區段包含下列範例：

> [!div class="checklist"]
> * 上傳檔案
> * 上傳目錄
> * 上傳目錄的內容
> * 上傳特定檔案

> [!NOTE]
> AzCopy 不會自動計算並儲存檔案的 md5 雜湊碼。 如果您想要讓 AzCopy 執行此動作，請將 `--put-md5` 旗標附加至每個複製命令。 如此一來，當下載檔案時，AzCopy 會計算已下載資料的 MD5 雜湊，並驗證儲存在檔案的 `Content-md5` 屬性中的 MD5 雜湊是否符合計算的雜湊。

如需詳細的參考檔，請參閱[azcopy copy](storage-ref-azcopy-copy.md)。

> [!TIP]
> 本節中的範例會以單引號（' '）括住路徑引數。 在所有命令 shell 中使用單引號，但 Windows 命令介面（cmd.exe）除外。 如果您使用 Windows 命令 Shell （cmd.exe），請將路徑引數括在雙引號（""），而不是單引號（' '）。

### <a name="upload-a-file"></a>上傳檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **範例** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

您也可以在檔案路徑或檔案名中的任何位置使用萬用字元符號（*）上傳檔案。 例如： `'C:\myDirectory\*.txt'`，或 `C:\my*\*.txt`。

### <a name="upload-a-directory"></a>上傳目錄

這個範例會將目錄（以及該目錄中的所有檔案）複製到檔案共用。 結果是檔案共用中具有相同名稱的目錄。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

若要複製到檔案共用中的目錄，只要在命令字串中指定該目錄的名稱即可。

|    |     |
|--------|-----------|
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

如果您指定的目錄名稱不存在於檔案共用中，AzCopy 會依該名稱建立新的目錄。

### <a name="upload-the-contents-of-a-directory"></a>上傳目錄的內容

您可以上傳目錄的內容，而不需使用萬用字元符號（*）來複製包含目錄本身。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **範例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> 附加 `--recursive` 旗標，以上傳所有子目錄中的檔案。

### <a name="upload-specific-files"></a>上傳特定檔案

您可以指定完整的檔案名，或使用包含萬用字元（*）的部分名稱。

#### <a name="specify-multiple-complete-file-names"></a>指定多個完整的檔案名

使用[azcopy copy](storage-ref-azcopy-copy.md)命令搭配 `--include-path` 選項。 使用分號（`;`）來分隔個別的檔案名。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

在此範例中，AzCopy 會傳輸 `C:\myDirectory\photos` 目錄和 `C:\myDirectory\documents\myFile.txt` 檔案。 您必須包含 `--recursive` 選項，才能傳送 `C:\myDirectory\photos` 目錄中的所有檔案。

您也可以使用 [`--exclude-path`] 選項來排除檔案。 若要深入瞭解，請參閱[azcopy 複製](storage-ref-azcopy-copy.md)參考檔。

#### <a name="use-wildcard-characters"></a>使用萬用字元

使用[azcopy copy](storage-ref-azcopy-copy.md)命令搭配 `--include-pattern` 選項。 指定包含萬用字元的部分名稱。 使用 semicolin （`;`）來分隔名稱。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

您也可以使用 [`--exclude-pattern`] 選項來排除檔案。 若要深入瞭解，請參閱[azcopy 複製](storage-ref-azcopy-copy.md)參考檔。

[`--include-pattern`] 和 [`--exclude-pattern`] 選項僅適用于檔案名，而不會套用至路徑。  如果您想要複製存在於目錄樹狀結構中的所有文字檔，請使用 `–recursive` 選項來取得整個目錄樹狀結構，然後使用 `–include-pattern` 並指定 `*.txt` 來取得所有文字檔。

## <a name="download-files"></a>下載檔案

您可以使用[azcopy copy](storage-ref-azcopy-copy.md)命令，將檔案、目錄和檔案共用下載到本機電腦。

本區段包含下列範例：

> [!div class="checklist"]
> * 下載檔案
> * 下載目錄
> * 下載目錄的內容
> * 下載特定檔案

> [!NOTE]
> 如果檔案的 `Content-md5` 屬性值包含雜湊，則 AzCopy 會計算已下載資料的 MD5 雜湊，並驗證儲存在檔案 `Content-md5` 屬性中的 MD5 雜湊是否符合計算的雜湊。 如果這些值不相符，除非您將 `--check-md5=NoCheck` 或 `--check-md5=LogOnly` 附加至 copy 命令來覆寫此行為，否則下載將會失敗。

如需詳細的參考檔，請參閱[azcopy copy](storage-ref-azcopy-copy.md)。

> [!TIP]
> 本節中的範例會以單引號（' '）括住路徑引數。 在所有命令 shell 中使用單引號，但 Windows 命令介面（cmd.exe）除外。 如果您使用 Windows 命令 Shell （cmd.exe），請將路徑引數括在雙引號（""），而不是單引號（' '）。

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

這個範例會產生名為 `C:\myDirectory\myFileShareDirectory` 的目錄，其中包含所有下載的檔案。

### <a name="download-the-contents-of-a-directory"></a>下載目錄的內容

您可以下載目錄的內容，而不需使用萬用字元符號（*）複製包含的目錄本身。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **範例** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> 附加 `--recursive` 旗標，以下載所有子目錄中的檔案。

### <a name="download-specific-files"></a>下載特定檔案

您可以指定完整的檔案名，或使用包含萬用字元（*）的部分名稱。

#### <a name="specify-multiple-complete-file-names"></a>指定多個完整的檔案名

使用[azcopy copy](storage-ref-azcopy-copy.md)命令搭配 `--include-path` 選項。 使用 semicolin （`;`）來分隔個別的檔案名。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **範例** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

在此範例中，AzCopy 會傳輸 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` 目錄和 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` 檔案。 您必須包含 `--recursive` 選項，才能傳送 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` 目錄中的所有檔案。

您也可以使用 [`--exclude-path`] 選項來排除檔案。 若要深入瞭解，請參閱[azcopy 複製](storage-ref-azcopy-copy.md)參考檔。

#### <a name="use-wildcard-characters"></a>使用萬用字元

使用[azcopy copy](storage-ref-azcopy-copy.md)命令搭配 `--include-pattern` 選項。 指定包含萬用字元的部分名稱。 使用 semicolin （`;`）來分隔名稱。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

您也可以使用 [`--exclude-pattern`] 選項來排除檔案。 若要深入瞭解，請參閱[azcopy 複製](storage-ref-azcopy-copy.md)參考檔。

[`--include-pattern`] 和 [`--exclude-pattern`] 選項僅適用于檔案名，而不會套用至路徑。  如果您想要複製存在於目錄樹狀結構中的所有文字檔，請使用 `–recursive` 選項來取得整個目錄樹狀結構，然後使用 `–include-pattern` 並指定 `*.txt` 來取得所有文字檔。

## <a name="copy-files-between-storage-accounts"></a>在儲存體帳戶之間複製檔案

您可以使用 AzCopy 將檔案複製到其他儲存體帳戶。 複製作業是同步的，因此當命令傳回時，表示已複製所有檔案。

AzCopy 會使用[伺服器對伺服器](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [api](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)，因此資料會直接複製到存放伺服器之間。 這些複製作業不會使用您電腦的網路頻寬。 您可以藉由設定 `AZCOPY_CONCURRENCY_VALUE` 環境變數的值來增加這些作業的輸送量。 若要深入瞭解，請參閱將[輸送量優化](storage-use-azcopy-configure.md#optimize-throughput)。

本區段包含下列範例：

> [!div class="checklist"]
> * 將檔案複製到另一個儲存體帳戶
> * 將目錄複寫到另一個儲存體帳戶
> * 將檔案共用複製到另一個儲存體帳戶
> * 將所有檔案共用、目錄和檔案複製到另一個儲存體帳戶

如需詳細的參考檔，請參閱[azcopy copy](storage-ref-azcopy-copy.md)。

> [!TIP]
> 本節中的範例會以單引號（' '）括住路徑引數。 在所有命令 shell 中使用單引號，但 Windows 命令介面（cmd.exe）除外。 如果您使用 Windows 命令 Shell （cmd.exe），請將路徑引數括在雙引號（""），而不是單引號（' '）。

### <a name="copy-a-file-to-another-storage-account"></a>將檔案複製到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **範例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>將目錄複寫到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>將檔案共用複製到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>將所有檔案共用、目錄和檔案複製到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **範例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>同步處理檔案

您可以將檔案共用的內容與另一個檔案共用同步處理。 您也可以將檔案共用中的目錄內容與位於另一個檔案共用中的目錄內容進行同步處理。 同步處理是單向的。 換句話說，您可以選擇這兩個端點中的哪一個是來源，以及哪一個是目的地。 同步處理也會使用伺服器到伺服器 Api。

> [!NOTE]
> 目前，只有沒有階層式命名空間的帳戶才支援此案例。 目前的 AzCopy 版本不會在 Azure 檔案儲存體和 Blob 儲存體之間同步處理。

[同步](storage-ref-azcopy-sync.md)命令會比較檔案名和上次修改的時間戳記。 將 [`--delete-destination` 選擇性] 旗標設定為 `true` 或 `prompt` 的值，以便在來原始目錄中不存在這些檔案時，刪除目的地目錄中的檔案。

如果您將 `--delete-destination` 旗標設定為 `true` AzCopy 會刪除檔案，而不會提供提示。 如果您想要在 AzCopy 刪除檔案之前出現提示，請將 [`--delete-destination`] 旗標設定為 [`prompt`]。

如需詳細的參考檔，請參閱[azcopy sync](storage-ref-azcopy-sync.md)。

> [!TIP]
> 本節中的範例會以單引號（' '）括住路徑引數。 在所有命令 shell 中使用單引號，但 Windows 命令介面（cmd.exe）除外。 如果您使用 Windows 命令 Shell （cmd.exe），請將路徑引數括在雙引號（""），而不是單引號（' '）。

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>使用另一個檔案共用的變更來更新檔案共用

出現在此命令中的第一個檔案共用是來源。 第二個是目的地。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **範例** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>更新具有另一個檔案共用中目錄變更的目錄

出現在此命令中的第一個目錄是來源。 第二個是目的地。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **範例** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="next-steps"></a>後續步驟

在這些文章中尋找更多範例：

- [開始使用 AzCopy](storage-use-azcopy-v10.md)

- [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)
