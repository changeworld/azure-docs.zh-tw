---
title: 使用 AzCopy v10 將資料傳入或傳出 Azure 檔案儲存體 |Microsoft Docs
description: 使用 AzCopy 和檔案儲存體傳輸資料。 AzCopy 是一種命令列工具，可用於將 blob 或檔案複製到儲存體帳戶或從中複製檔案。 搭配 Azure 檔案儲存體使用 AzCopy。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 6d0dcecf6a0da1fbd3561dadcbe001c0ef8d4ebc
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92479454"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>使用 AzCopy 和檔案儲存體傳輸資料 (機器翻譯) 

AzCopy 是命令列公用程式，可讓您在儲存體帳戶之間複製 Blob 或檔案。 本文包含可搭配 Azure 檔案儲存體使用的命令範例。

開始之前，請先參閱開始 [使用 AzCopy](storage-use-azcopy-v10.md) 文章，以下載 AzCopy 並熟悉此工具。

> [!TIP]
> 本文中的範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

## <a name="create-file-shares"></a>建立檔案共用

您可以使用 [azcopy make](storage-ref-azcopy-make.md) 命令來建立檔案共用。 本節中的範例會建立名為的檔案共用 `myfileshare` 。

|    |     |
|--------|-----------|
| **語法** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'` |
| **範例** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

如需詳細的參考檔，請參閱 [azcopy](storage-ref-azcopy-make.md)。

## <a name="upload-files"></a>上傳檔案

您可以使用 [azcopy 複製](storage-ref-azcopy-copy.md) 命令，從您的本機電腦上傳檔案和目錄。

本區段包含下列範例：

> [!div class="checklist"]
> * 上傳檔案
> * 上傳目錄
> * 上傳目錄的內容
> * 上傳特定檔案

> [!TIP]
> 您可以使用選擇性旗標來調整上傳作業。 以下是一些範例。
>
> |案例|旗標|
> |---|---|
> |將存取控制清單 (Acl) 和檔案一起複製。|**--preserve-smb-許可權** = \[true \| false\]|
> |複製 SMB 屬性資訊和檔案。|**--preserve-smb-資訊** = \[true \| false\]|
> |以附加 Blob 或分頁 Blob 的形式上傳檔案。|**--blob 類型** = \[BlockBlob \| PageBlob \| AppendBlob\]|
> |上傳至特定存取層 (例如封存層)。|**--區塊-blob 層** = \[無 \| 熱 \| 冷 \| 封存\]|
> 
> 如需完整清單，請參閱 [選項](storage-ref-azcopy-copy.md#options)。

> [!NOTE]
> AzCopy 不會自動計算和儲存檔案的 md5 雜湊碼。 如果您想要讓 AzCopy 這麼做，請將 `--put-md5` 旗標附加至每個複製命令。 如此一來，在下載檔案時，AzCopy 會計算所下載資料的 MD5 雜湊，並確認儲存在檔案屬性中的 MD5 雜湊 `Content-md5` 符合計算的雜湊。

### <a name="upload-a-file"></a>上傳檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **範例** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

您也可以使用萬用字元符號來上傳檔案 ( * ) 在檔案路徑或檔案名中的任何位置。 例如： `'C:\myDirectory\*.txt'` 、或 `C:\my*\*.txt` 。

### <a name="upload-a-directory"></a>上傳目錄

此範例會將目錄 (和該目錄中的所有檔案) 複製到檔案共用。 結果會是檔案共用中相同名稱的目錄。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

若要複製到檔案共用中的目錄，只需在命令字串中指定該目錄的名稱即可。

|    |     |
|--------|-----------|
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

如果您指定不存在於檔案共用中的目錄名稱，AzCopy 會依該名稱建立新的目錄。

### <a name="upload-the-contents-of-a-directory"></a>上傳目錄的內容

您可以使用萬用字元符號 ( * ) ，上傳目錄的內容而不復制包含目錄本身。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **範例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> 附加 `--recursive` 旗標以上傳所有子目錄中的檔案。

### <a name="upload-specific-files"></a>上傳特定檔案

您可以使用完整檔案名、使用萬用字元 ( * ) 的部分名稱，或使用日期和時間，來上傳特定檔案。

#### <a name="specify-multiple-complete-file-names"></a>指定多個完整的檔案名

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令搭配 `--include-path` 選項。 使用分號 () 來分隔個別的檔案名 `;` 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

在此範例中，AzCopy 會傳送 `C:\myDirectory\photos` 目錄和檔案 `C:\myDirectory\documents\myFile.txt` 。 您必須包含在 `--recursive` 目錄中傳送所有檔案的選項 `C:\myDirectory\photos` 。

您也可以使用選項來排除檔案 `--exclude-path` 。 若要深入瞭解，請參閱 [azcopy 複製](storage-ref-azcopy-copy.md) 參考檔。

#### <a name="use-wildcard-characters"></a>使用萬用字元

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令搭配 `--include-pattern` 選項。 指定包含萬用字元的部分名稱。 使用分號 () 來分隔名稱 `;` 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

您也可以使用選項來排除檔案 `--exclude-pattern` 。 若要深入瞭解，請參閱 [azcopy 複製](storage-ref-azcopy-copy.md) 參考檔。

`--include-pattern`和 `--exclude-pattern` 選項只適用于檔案名，而不會套用至路徑。  如果您想要複製存在於目錄樹狀結構中的所有文字檔，請使用 `–recursive` 選項來取得整個目錄樹狀結構，然後使用 `–include-pattern` 和指定 `*.txt` 來取得所有文字檔。

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>上傳在日期和時間之後修改過的檔案 

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令搭配 `--include-after` 選項。 以 ISO 8601 格式指定日期和時間 (例如： `2020-08-19T15:04:00Z`) 。 

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **範例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-after '2020-08-19T15:04:00Z'` |

如需詳細參考，請參閱 [azcopy 複製](storage-ref-azcopy-copy.md) 參考檔。

## <a name="download-files"></a>下載檔案

您可以使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令，將檔案、目錄和檔案共用下載到本機電腦。

本區段包含下列範例：

> [!div class="checklist"]
> * 下載檔案
> * 下載目錄
> * 下載目錄的內容
> * 下載特定檔案

> [!TIP]
> 您可以使用選擇性旗標來調整您的下載作業。 以下是一些範例。
>
> |案例|旗標|
> |---|---|
> |將存取控制清單 (Acl) 和檔案一起複製。|**--preserve-smb-許可權** = \[true \| false\]|
> |複製 SMB 屬性資訊和檔案。|**--preserve-smb-資訊** = \[true \| false\]|
> |自動解壓縮檔案。|**--解壓縮**|
> 
> 如需完整清單，請參閱 [選項](storage-ref-azcopy-copy.md#options)。

> [!NOTE]
> 如果檔案的 `Content-md5` 屬性值包含雜湊，AzCopy 會計算所下載資料的 md5 雜湊，並驗證儲存在檔案屬性中的 md5 雜湊是否 `Content-md5` 符合計算的雜湊。 如果這些值不相符，除非您附加 `--check-md5=NoCheck` 或複製命令來覆寫此行為，否則下載會失敗 `--check-md5=LogOnly` 。

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

此範例會產生名為 `C:\myDirectory\myFileShareDirectory` 的目錄，其中包含所有下載的檔案。

### <a name="download-the-contents-of-a-directory"></a>下載目錄的內容

您可以使用萬用字元符號 ( * ) ，在不復制包含目錄本身的情況下下載目錄的內容。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **範例** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> 附加 `--recursive` 旗標以下載所有子目錄中的檔案。

### <a name="download-specific-files"></a>下載特定檔案

您可以使用完整檔案名、使用萬用字元 ( * ) 的部分名稱，或使用日期和時間來下載特定檔案。

#### <a name="specify-multiple-complete-file-names"></a>指定多個完整的檔案名

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令搭配 `--include-path` 選項。 使用分號 () 來分隔個別的檔案名 `;` 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **範例** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

在此範例中，AzCopy 會傳送 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` 目錄和檔案 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` 。 您必須包含在 `--recursive` 目錄中傳送所有檔案的選項 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` 。

您也可以使用選項來排除檔案 `--exclude-path` 。 若要深入瞭解，請參閱 [azcopy 複製](storage-ref-azcopy-copy.md) 參考檔。

#### <a name="use-wildcard-characters"></a>使用萬用字元

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令搭配 `--include-pattern` 選項。 指定包含萬用字元的部分名稱。 使用分號 () 來分隔名稱 `;` 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

您也可以使用選項來排除檔案 `--exclude-pattern` 。 若要深入瞭解，請參閱 [azcopy 複製](storage-ref-azcopy-copy.md) 參考檔。

`--include-pattern`和 `--exclude-pattern` 選項只適用于檔案名，而不會套用至路徑。  如果您想要複製存在於目錄樹狀結構中的所有文字檔，請使用 `–recursive` 選項來取得整個目錄樹狀結構，然後使用 `–include-pattern` 和指定 `*.txt` 來取得所有文字檔。

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>下載在日期和時間之後修改過的檔案 

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令搭配 `--include-after` 選項。 以 ISO-8601 格式指定日期和時間 (例如： `2020-08-19T15:04:00Z`) 。 

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>/*<SAS-token>' '<local-directory-path>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **範例** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/*?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --include-after '2020-08-19T15:04:00Z'` |


如需詳細參考，請參閱 [azcopy 複製](storage-ref-azcopy-copy.md) 參考檔。

## <a name="copy-files-between-storage-accounts"></a>在儲存體帳戶之間複製檔案

您可以使用 AzCopy 將檔案複製到其他儲存體帳戶。 複製作業是同步的，因此當命令傳回時，表示已複製所有檔案。

AzCopy 會使用 [伺服器對伺服器](/rest/api/storageservices/put-block-from-url) [api](/rest/api/storageservices/put-page-from-url)，因此會直接在儲存體伺服器之間複製資料。 這些複製作業不會使用您電腦的網路頻寬。 您可以藉由設定環境變數的值，來增加這些作業的輸送量 `AZCOPY_CONCURRENCY_VALUE` 。 若要深入瞭解，請參閱 [優化輸送量](storage-use-azcopy-configure.md#optimize-throughput)。

本區段包含下列範例：

> [!div class="checklist"]
> * 將檔案複製到另一個儲存體帳戶
> * 將目錄複寫到另一個儲存體帳戶
> * 將檔案共用複製到另一個儲存體帳戶
> * 將所有檔案共用、目錄和檔案複製到另一個儲存體帳戶

> [!TIP]
> 您可以使用選擇性旗標來調整複製操作。 以下是一些範例。
>
> |案例|旗標|
> |---|---|
> |將存取控制清單 (Acl) 和檔案一起複製。|**--preserve-smb-許可權** = \[true \| false\]|
> |複製 SMB 屬性資訊和檔案。|**--preserve-smb-資訊** = \[true \| false\]|
> |將檔案複製為附加 Blob 或分頁 Blob。|**--blob 類型** = \[BlockBlob \| PageBlob \| AppendBlob\]|
> |複製到特定的存取層 (例如封存層) 。|**--區塊-blob 層** = \[無 \| 熱 \| 冷 \| 封存\]|
> 
> 如需完整清單，請參閱 [選項](storage-ref-azcopy-copy.md#options)。

### <a name="copy-a-file-to-another-storage-account"></a>將檔案複製到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **範例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>將目錄複寫到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>將檔案共用複製到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>將所有檔案共用、目錄和檔案複製到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **範例** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>同步處理檔案

您可以將檔案共用的內容與另一個檔案共用進行同步處理。 您也可以將檔案共用中的目錄內容與位於另一個檔案共用中的目錄內容同步處理。 同步處理是單向的。 換句話說，您可以選擇這兩個端點中的哪一個是來源，哪一個是目的地。 同步處理也會使用伺服器對伺服器 Api。

> [!NOTE]
> 目前，只有沒有階層命名空間的帳戶支援此案例。 目前的 AzCopy 版本不會在 Azure 檔案儲存體和 Blob 儲存體之間進行同步處理。

[Sync](storage-ref-azcopy-sync.md)命令會比較檔案名和上次修改的時間戳記。 將 `--delete-destination` 選擇性旗標設定為的值， `true` `prompt` 如果這些檔案不再存在於來原始目錄中，則刪除目的地目錄中的檔案。

如果您將旗標設定 `--delete-destination` 為 `true` AzCopy 刪除檔案，而不提供提示。 如果您想要在 AzCopy 刪除檔案之前出現提示，請將旗標設定 `--delete-destination` 為 `prompt` 。

> [!TIP]
> 您可以使用選擇性旗標來調整同步處理作業。 以下是一些範例。
>
> |案例|旗標|
> |---|---|
> |將存取控制清單 (Acl) 和檔案一起複製。|**--preserve-smb-許可權** = \[true \| false\]|
> |複製 SMB 屬性資訊和檔案。|**--preserve-smb-資訊** = \[true \| false\]|
> |根據模式排除檔案。|**--排除-路徑**|
> |指定您想要同步處理相關記錄專案的詳細程度。|**--記錄層級** = \[警告 \| 錯誤 \| 資訊 \| 無\]|
> 
> 如需完整清單，請參閱 [選項](storage-ref-azcopy-sync.md#options)。

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>使用其他檔案共用的變更來更新檔案共用

此命令中出現的第一個檔案共用為來源。 第二個是目的地。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **範例** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>使用其他檔案共用中的目錄變更來更新目錄

此命令中出現的第一個目錄是來源。 第二個是目的地。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **範例** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>更新檔案共用以符合共用快照集的內容

此命令中出現的第一個檔案共用為來源。 在 URI 的結尾，附加字串， `&sharesnapshot=` 後面接著快照集的 **日期時間** 值。 

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **範例** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

若要深入瞭解共用快照集，請參閱 [Azure 檔案儲存體的共用快照集總覽](/azure/storage/files/storage-snapshots-files)。

## <a name="next-steps"></a>後續步驟

在這些文章中尋找更多範例：

- [開始使用 AzCopy](storage-use-azcopy-v10.md)

- [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)
