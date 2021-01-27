---
title: 使用 AzCopy v10 從 Azure Blob 儲存體下載 blob |Microsoft Docs
description: 本文包含 AzCopy 範例命令的集合，可協助您從 Azure Blob 儲存體下載 blob。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 20da05399eed4cb9c5a4b69a82b0b1e799997751
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880113"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy-v10"></a>使用 AzCopy v10 從 Azure Blob 儲存體下載 blob

您可以使用 AzCopy v10 命令列公用程式，從 Blob 儲存體下載 blob 和目錄。 

若要查看其他工作類型的範例，例如上傳檔案、與 Blob 儲存體同步處理，或複製帳戶之間的 blob，請參閱本文的 [後續步驟](#next-steps) 一節中所提供的連結。

## <a name="get-started"></a>開始使用

請參閱 [開始使用 AzCopy](storage-use-azcopy-v10.md) 文章以下載 AzCopy，並瞭解您可以提供授權認證給儲存體服務的方式。

> [!NOTE] 
> 本文中的範例假設您已使用 Azure Active Directory (Azure AD) 提供授權認證。
>
> 如果您想要使用 SAS 權杖來授與 blob 資料的存取權，您可以將該權杖附加至每個 AzCopy 命令中的資源 URL。 例如：`'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`。

## <a name="download-a-blob"></a>下載 Blob

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令下載 blob。

> [!TIP]
> 此範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| 階層命名空間 (**範例**)  | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> 如果 `Content-md5` blob 的屬性值包含雜湊，AzCopy 會計算所下載資料的 md5 雜湊，並驗證儲存在 blob 屬性中的 md5 雜湊是否 `Content-md5` 符合計算的雜湊。 如果這些值不相符，除非您附加 `--check-md5=NoCheck` 或複製命令來覆寫此行為，否則下載會失敗 `--check-md5=LogOnly` 。

## <a name="download-a-directory"></a>下載目錄

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令下載目錄。

> [!TIP]
> 此範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| 階層命名空間 (**範例**)  | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

此範例會產生名為 `C:\myDirectory\myBlobDirectory` 的目錄，其中包含所有已下載的 blob。

## <a name="download-directory-contents"></a>下載目錄內容

您可以下載目錄的內容，而不需使用萬用字元符號 (*) 複製包含的目錄本身。

> [!TIP]
> 此範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

> [!NOTE]
> 目前，只有沒有階層命名空間的帳戶支援此案例。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

附加 `--recursive` 旗標以下載所有子目錄中的檔案。

## <a name="download-specific-blobs"></a>下載特定 blob

您可以使用完整檔案名、使用萬用字元 ( * ) 的部分名稱，或使用日期和時間來下載特定 blob。 

> [!TIP]
> 這些範例會用單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

#### <a name="specify-multiple-complete-blob-names"></a>指定多個完整的 blob 名稱

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令搭配 `--include-path` 選項。 使用 semicolin () 來分隔個別的 blob 名稱 `;` 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| 階層命名空間 (**範例**)  | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

在此範例中，AzCopy 會傳送 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` 目錄和檔案 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` 。 包含在 `--recursive` 目錄中傳送所有 blob 的選項 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` 。

您也可以使用選項來排除 blob `--exclude-path` 。 若要深入瞭解，請參閱 [azcopy 複製](storage-ref-azcopy-copy.md) 參考檔。

#### <a name="use-wildcard-characters"></a>使用萬用字元

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令搭配 `--include-pattern` 選項。 指定包含萬用字元的部分名稱。 使用 semicolin () 來分隔名稱 `;` 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| 階層命名空間 (**範例**)  | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

您也可以使用選項來排除 blob `--exclude-pattern` 。 若要深入瞭解，請參閱 [azcopy 複製](storage-ref-azcopy-copy.md) 參考檔。

`--include-pattern`和 `--exclude-pattern` 選項只適用于 blob 名稱，而不會套用至路徑。  如果您想要複製存在於目錄樹狀結構中 (blob) 的所有文字檔，請使用 `–recursive` 選項取得整個目錄樹狀結構，然後使用 `–include-pattern` 和指定 `*.txt` 來取得所有文字檔。

#### <a name="download-blobs-that-were-modified-before-or-after-a-date-and-time"></a>下載在日期和時間之前或之後修改的 blob 

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令搭配 `--include-before` 或 `--include-after` 選項。 以 ISO-8601 格式指定日期和時間 (例如： `2020-08-19T15:04:00Z`) 。 

下列範例會下載在指定日期或之後修改過的檔案。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| 階層命名空間 (**範例**)  | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

如需詳細參考，請參閱 [azcopy 複製](storage-ref-azcopy-copy.md) 參考檔。

#### <a name="download-previous-versions-of-a-blob"></a>下載先前版本的 blob

如果您已啟用 [blob 版本](../blobs/versioning-enable.md)設定，則可以下載一或多個舊版的 blob。 

首先，建立包含 [版本識別碼](../blobs/versioning-overview.md)清單的文字檔。 每個版本識別碼都必須出現在不同的行上。 例如： 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

然後，將 [azcopy copy](storage-ref-azcopy-copy.md) 命令與選項搭配使用 `--list-of-versions` 。 指定包含版本清單之文字檔的位置 (例如： `D:\\list-of-versions.txt`) 。  

#### <a name="download-a-blob-snapshot"></a>下載 Blob 快照集

您可以藉由參考 blob 快照集的 **日期時間** 值來下載 [blob 快照](../blobs/snapshots-overview.md)集。 

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>?sharesnapshot=<DateTime-of-snapshot>' '<local-file-path>'` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |
| 階層命名空間 (**範例**)  | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> 如果您使用 SAS 權杖來授與 blob 資料的存取權，請在 SAS 權杖之後附加快照 **日期時間** 。 例如：`'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z'`。

## <a name="download-with-optional-flags"></a>使用選擇性旗標下載

您可以使用選擇性旗標來調整您的下載作業。 以下是一些範例。

|狀況|旗標|
|---|---|
|自動解壓縮檔案。|**--解壓縮**|
|指定您希望複製相關記錄專案的詳細程度。|**--記錄層級** = \[警告 \| 錯誤 \| 資訊 \| 無\]|
|指定是否及如何覆寫目的地端的衝突檔案和 blob。|**--覆寫** = \[true \| false \| ifSourceNewer \| 提示字元\]|

如需完整清單，請參閱 [選項](storage-ref-azcopy-copy.md#options)。

## <a name="next-steps"></a>後續步驟

在這些文章中尋找更多範例：

- [範例：上傳](storage-use-azcopy-blobs-upload.md)
- [範例：帳戶之間的複製](storage-use-azcopy-blobs-copy.md)
- [範例：同步處理](storage-use-azcopy-blobs-synchronize.md)
- [範例：Amazon S3 Bucket](storage-use-azcopy-s3.md)
- [範例： Azure 檔案儲存體](storage-use-azcopy-files.md)
- [教學課程：使用 AzCopy 將內部部署資料移轉至雲端儲存體](storage-use-azcopy-migrate-on-premises-data.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)