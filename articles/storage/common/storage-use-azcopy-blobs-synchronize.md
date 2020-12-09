---
title: 使用 AzCopy v10 與 Azure Blob 儲存體進行同步處理 |Microsoft Docs
description: 本文包含 AzCopy 範例命令的集合，可協助您與 Azure Blob 儲存體進行同步處理。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 6d1e9e8eeddaaa2ce8c891888935faad12d40295
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96907479"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy-v10"></a>使用 AzCopy v10 與 Azure Blob 儲存體進行同步處理

您可以使用 AzCopy v10 命令列公用程式，將本機儲存體與 Azure Blob 儲存體進行同步處理。 

您可以同步處理本機檔案系統與 blob 容器的內容。 您也可以同步處理彼此的容器和虛擬目錄。 同步處理是一種方式。 換句話說，您可以選擇這兩個端點中的哪一個是來源，哪一個是目的地。 同步處理也會使用伺服器對伺服器 Api。 本節中提供的範例也適用于具有階層命名空間的帳戶。 

> [!NOTE]
> 目前的 AzCopy 版本不會在其他來源和目的地之間進行同步處理 (例如：檔案儲存體或 Amazon Web Services (AWS) S3 bucket 值區) 。

若要查看其他工作類型的範例，例如上傳檔案、下載 blob，或複製帳戶之間的 blob，請參閱本文的 [後續步驟](#next-steps) 一節中所提供的連結。

## <a name="get-started"></a>開始使用

請參閱 [開始使用 AzCopy](storage-use-azcopy-v10.md) 文章以下載 AzCopy，並瞭解您可以提供授權認證給儲存體服務的方式。

> [!NOTE] 
> 本文中的範例假設您已使用 Azure Active Directory (Azure AD) 提供授權認證。
>
> 如果您想要使用 SAS 權杖來授與 blob 資料的存取權，您可以將該權杖附加至每個 AzCopy 命令中的資源 URL。 例如： `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'` ken> ' '。

## <a name="guidelines"></a>指導方針

- [Sync](storage-ref-azcopy-sync.md)命令會比較檔案名和上次修改的時間戳記。 將 `--delete-destination` 選擇性旗標設定為的值， `true` `prompt` 如果這些檔案不再存在於來原始目錄中，則刪除目的地目錄中的檔案。

- 如果您將旗標設 `--delete-destination` 為 `true` ，AzCopy 就會刪除檔案，而不會提供提示。 如果您想要在 AzCopy 刪除檔案之前出現提示，請將旗標設定 `--delete-destination` 為 `prompt` 。

- 若要防止意外刪除，請務必先啟用虛 [刪除](../blobs/soft-delete-blob-overview.md) 功能，然後再使用 `--delete-destination=prompt|true` 旗標。

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>使用本機檔案系統的變更來更新容器

在此情況下，容器是目的地，而本機檔案系統則是來源。 

> [!TIP]
> 此範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>將有變更的本機檔案系統上傳至容器

在此情況下，本機檔案系統是目的地，而容器是來源。

> [!TIP]
> 此範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **範例** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

## <a name="update-a-container-with-changes-in-another-container"></a>更新另一個容器中有變更的容器

此命令中出現的第一個容器為來源。 第二個是目的地。

> [!TIP]
> 此範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>使用其他檔案共用中的目錄變更來更新目錄

此命令中出現的第一個目錄是來源。 第二個是目的地。

> [!TIP]
> 此範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **範例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="synchronize-with-optional-flags"></a>使用選擇性旗標進行同步處理

您可以使用選擇性旗標來調整同步處理作業。 以下是一些範例。

|案例|旗標|
|---|---|
|指定在下載時，應如何驗證嚴格的 MD5 雜湊。|**--check-md5** = \[NoCheck \| LogOnly \| FailIfDifferent \| FailIfDifferentOrMissing\]|
|根據模式排除檔案。|**--排除-路徑**|
|指定您想要同步處理相關記錄專案的詳細程度。|**--記錄層級** = \[警告 \| 錯誤 \| 資訊 \| 無\]|

如需完整清單，請參閱 [選項](storage-ref-azcopy-sync.md#options)。

## <a name="next-steps"></a>後續步驟

在這些文章中尋找更多範例：

- [範例：上傳](storage-use-azcopy-blobs-upload.md)
- [範例：下載](storage-use-azcopy-blobs-download.md)
- [範例：帳戶之間的複製](storage-use-azcopy-blobs-copy.md)
- [範例： Amazon S3 bucket](storage-use-azcopy-s3.md)
- [範例： Azure 檔案儲存體](storage-use-azcopy-files.md)
- [教學課程：使用 AzCopy 將內部部署資料移轉至雲端儲存體](storage-use-azcopy-migrate-on-premises-data.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)