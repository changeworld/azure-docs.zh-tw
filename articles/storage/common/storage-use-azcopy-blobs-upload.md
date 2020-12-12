---
title: 使用 AzCopy v10 將檔案上傳至 Azure Blob 儲存體 |Microsoft Docs
description: 本文包含 AzCopy 範例命令的集合，可協助您將檔案上傳至 Azure Blob 儲存體。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: ec88a3c740ceda7ccf352f8f32f94e2cd52d0988
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358753"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy-v10"></a>使用 AzCopy v10 將檔案上傳至 Azure Blob 儲存體

您可以使用 AzCopy v10 命令列公用程式，將檔案和目錄上傳至 Blob 儲存體。 

若要查看其他工作類型的範例，例如下載 blob、與 Blob 儲存體同步處理，或在帳戶之間複製 blob，請參閱本文的 [後續步驟](#next-steps) 一節中所提供的連結。

## <a name="get-started"></a>開始使用

請參閱 [開始使用 AzCopy](storage-use-azcopy-v10.md) 文章以下載 AzCopy，並瞭解您可以提供授權認證給儲存體服務的方式。

> [!NOTE] 
> 本文中的範例假設您已使用 Azure Active Directory (Azure AD) 提供授權認證。
>
> 如果您想要使用 SAS 權杖來授與 blob 資料的存取權，您可以將該權杖附加至每個 AzCopy 命令中的資源 URL。 例如：`'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`。

## <a name="create-a-container"></a>建立容器

您可以使用 [azcopy make](storage-ref-azcopy-make.md) 命令來建立容器。

> [!TIP]
> 此範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

|    |     |
|--------|-----------|
| **語法** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **範例** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| 階層命名空間 (**範例**)  | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

如需詳細的參考檔，請參閱 [azcopy](storage-ref-azcopy-make.md)。

## <a name="upload-a-file"></a>上傳檔案

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令上傳檔案。

> [!TIP]
> 此範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **範例** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| 階層命名空間 (**範例**)  | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

您也可以使用萬用字元符號來上傳檔案 ( * ) 在檔案路徑或檔案名中的任何位置。 例如： `'C:\myDirectory\*.txt'` 、或 `C:\my*\*.txt` 。

## <a name="upload-a-directory"></a>上傳目錄

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令上傳目錄。 

此範例會將目錄 (和該目錄中的所有檔案) 複製到 blob 容器。 結果會是容器中相同名稱的目錄。

> [!TIP]
> 此範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| 階層命名空間 (**範例**)  | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

若要複製到容器內的目錄，只需在命令字串中指定該目錄的名稱即可。

|    |     |
|--------|-----------|
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| 階層命名空間 (**範例**)  | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

如果您指定容器中不存在的目錄名稱，AzCopy 會依該名稱建立新的目錄。

## <a name="upload-directory-contents"></a>上傳目錄內容

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令上傳目錄的內容。 使用萬用字元符號 ( * ) 上傳內容，而不復制包含的目錄本身。

> [!TIP]
> 此範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **範例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| 階層命名空間 (**範例**)  | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |


附加 `--recursive` 旗標以上傳所有子目錄中的檔案。

## <a name="upload-specific-files"></a>上傳特定檔案

您可以使用完整檔案名、使用萬用字元 ( * ) 的部分名稱，或使用日期和時間，來上傳特定檔案。

> [!TIP]
> 這些範例會用單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

### <a name="specify-multiple-complete-file-names"></a>指定多個完整的檔案名

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令搭配 `--include-path` 選項。 使用分號 () 來分隔個別的檔案名 `;` 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| 階層命名空間 (**範例**)  | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

在此範例中，AzCopy 會傳送 `C:\myDirectory\photos` 目錄和檔案 `C:\myDirectory\documents\myFile.txt` 。 包含在 `--recursive` 目錄中傳送所有檔案的選項 `C:\myDirectory\photos` 。

您也可以使用選項來排除檔案 `--exclude-path` 。 若要深入瞭解，請參閱 [azcopy 複製](storage-ref-azcopy-copy.md) 參考檔。

### <a name="use-wildcard-characters"></a>使用萬用字元

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令搭配 `--include-pattern` 選項。 指定包含萬用字元的部分名稱。 使用 semicolin () 來分隔名稱 `;` 。 

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| 階層命名空間 (**範例**)  | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

您也可以使用選項來排除檔案 `--exclude-pattern` 。 若要深入瞭解，請參閱 [azcopy 複製](storage-ref-azcopy-copy.md) 參考檔。

`--include-pattern`和 `--exclude-pattern` 選項只適用于檔案名，而不會套用至路徑。  如果您想要複製存在於目錄樹狀結構中的所有文字檔，請使用 `–recursive` 選項來取得整個目錄樹狀結構，然後使用 `–include-pattern` 和指定 `*.txt` 來取得所有文字檔。

### <a name="upload-files-that-were-modified-before-or-after-a-date-and-time"></a>上傳在日期和時間之前或之後修改過的檔案 

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令搭配 `--include-before` 或 `--include-after` 選項。 以 ISO-8601 格式指定日期和時間 (例如： `2020-08-19T15:04:00Z`) 。 

下列範例會上傳在指定日期或之後修改的檔案。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **範例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| 階層命名空間 (**範例**)  | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

如需詳細參考，請參閱 [azcopy 複製](storage-ref-azcopy-copy.md) 參考檔。

## <a name="upload-with-index-tags"></a>上傳索引標記

您可以上傳檔案，並將 [blob 索引標記 (預覽) ](../blobs/storage-manage-find-blobs.md) 新增至目標 blob。  

如果您使用 Azure AD 的授權，則必須將 [儲存體 Blob 資料擁有](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 者角色指派給您的安全性主體，或必須透過 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 自訂 azure 角色獲得對 [azure 資源提供者](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) 作業的許可權。 如果您使用共用存取簽章 (SAS) 權杖，該權杖必須透過 sas 許可權提供 blob 標記的存取權 `t` 。

若要加入標記，請使用 `--blob-tags` 選項以及 URL 編碼的索引鍵/值組。 例如，若要加入索引鍵 `my tag` 和值 `my tag value` ，您可以新增 `--blob-tags='my%20tag=my%20tag%20value'` 至目的地參數。 

使用 & 符號 () 來分隔多個索引標記 `&` 。  例如，如果您想要加入索引鍵 `my second tag` 和值 `my second tag value` ，則完整的選項字串會是 `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` 。

下列範例示範如何使用 `--blob-tags` 選項。

> [!TIP]
> 此範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

|    |     |
|--------|-----------|
| **上傳檔案** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **上傳目錄** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`|
| **上傳目錄內容** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

> [!NOTE]
> 如果您為來源指定目錄，則複製到目的地的所有 blob 都會有您在命令中指定的相同標記。

## <a name="upload-with-optional-flags"></a>使用選擇性旗標上傳

您可以使用選擇性旗標來調整上傳作業。 以下是一些範例。

|案例|旗標|
|---|---|
|以附加 Blob 或分頁 Blob 的形式上傳檔案。|**--blob 類型** = \[BlockBlob \| PageBlob \| AppendBlob\]|
|上傳至特定存取層 (例如封存層)。|**--區塊-blob 層** = \[無 \| 熱 \| 冷 \| 封存\]|

如需完整清單，請參閱 [選項](storage-ref-azcopy-copy.md#options)。

## <a name="next-steps"></a>後續步驟

在這些文章中尋找更多範例：

- [範例：下載](storage-use-azcopy-blobs-download.md)
- [範例：在帳戶之間複製](storage-use-azcopy-blobs-copy.md)
- [範例：同步處理](storage-use-azcopy-blobs-synchronize.md)
- [範例：Amazon S3 Bucket](storage-use-azcopy-s3.md)
- [範例： Azure 檔案儲存體](storage-use-azcopy-files.md)
- [教學課程：使用 AzCopy 將內部部署資料移轉至雲端儲存體](storage-use-azcopy-migrate-on-premises-data.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)