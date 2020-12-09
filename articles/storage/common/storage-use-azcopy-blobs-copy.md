---
title: 使用 AzCopy v10 在 Azure 儲存體帳戶之間複製 blob |Microsoft Docs
description: 本文包含 AzCopy 範例命令的集合，可協助您在儲存體帳戶之間複製 blob。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: f536e163e3d19d91c150506ab44fdd9cbc02c693
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96907476"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy-v10"></a>使用 AzCopy v10 在 Azure 儲存體帳戶之間複製 blob

您可以使用 AzCopy v10 命令列公用程式，在儲存體帳戶之間複製 blob、目錄和容器。 

若要查看其他工作類型的範例，例如上傳檔案、下載 blob，以及與 Blob 儲存體同步處理，請參閱本文的 [後續步驟](#next-steps) 一節中所提供的連結。

AzCopy 會使用 [伺服器對伺服器](/rest/api/storageservices/put-block-from-url) [api](/rest/api/storageservices/put-page-from-url)，因此會直接在儲存體伺服器之間複製資料。 這些複製作業不會使用您電腦的網路頻寬。

若要下載 AzCopy，並瞭解您可以提供授權認證給儲存體服務的方式，請參閱 [開始使用 AzCopy](storage-use-azcopy-v10.md)。 

## <a name="guidelines"></a>指導方針

將下列指導方針套用至您的 AzCopy 命令。 

- 將 SAS 權杖附加至每個來源 URL。 

  如果您使用 Azure Active Directory (Azure AD) 提供授權認證，您只能從目的地 URL 中省略 SAS 權杖。 請確定您已在目的地帳戶中設定適當的角色。 請參閱 [選項1：使用 Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory)。 

  本文中的範例假設您已使用 Azure AD 來驗證您的身分識別，因此範例會從目的地 URL 中省略 SAS 權杖。

-  如果您將複製到高階區塊 blob 儲存體帳戶，請將設定 `s2s-preserve-access-tier` 為 (例如：) ，以省略複製作業的 blob 存取層 `false` `--s2s-preserve-access-tier=false` 。 Premium 區塊 blob 儲存體帳戶不支援存取層。 

- 如果您是從具有階層命名空間的帳戶複製到或，請使用 `blob.core.windows.net` 而不是使用 `dfs.core.windows.net` URL 語法。 [Data Lake Storage 上的多重通訊協定存取](../blobs/data-lake-storage-multi-protocol-access.md) 可讓您使用 `blob.core.windows.net` ，而這是唯一支援帳戶複製案例的語法。 

- 您可以藉由設定環境變數的值，來增加複製作業的輸送量 `AZCOPY_CONCURRENCY_VALUE` 。 若要深入瞭解，請參閱 [優化輸送量](storage-use-azcopy-configure.md#optimize-throughput)。 

- 如果來源 blob 有索引標籤，而您想要保留這些標記，您必須將它們重新套用到目的地 blob。 如需有關如何設定索引標籤的詳細資訊，請參閱本文的「將 [Blob 複製到具有索引標記的另一個儲存體帳戶](#copy-between-accounts-and-add-index-tags) 」一節。

## <a name="copy-a-blob"></a>複製 blob

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令，將 blob 複製到另一個儲存體帳戶。 

> [!TIP]
> 此範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

複製作業是同步的，因此當命令傳回時，表示已複製所有檔案。 

## <a name="copy-a-directory"></a>複製目錄

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令，將目錄複寫到另一個儲存體帳戶。 

> [!TIP]
> 此範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

複製作業是同步的，因此當命令傳回時，表示已複製所有檔案。

## <a name="copy-a-container"></a>複製容器

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令，將容器複製到另一個儲存體帳戶。

> [!TIP]
> 此範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

複製作業是同步的，因此當命令傳回時，表示已複製所有檔案。

## <a name="copy-containers-directories-and-blobs"></a>複製容器、目錄和 blob

使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令，將所有容器、目錄和 blob 複製到另一個儲存體帳戶。

> [!TIP]
> 此範例會以單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

複製作業是同步的，因此當命令傳回時，表示已複製所有檔案。

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>複製 blob 和新增索引標記

將 blob 複製到另一個儲存體帳戶，並將 [blob 索引標記 (預覽) ](../blobs/storage-manage-find-blobs.md) 新增至目標 blob。

如果您使用 Azure AD 的授權，則必須將 [儲存體 Blob 資料擁有](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 者角色指派給您的安全性主體，或必須透過 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 自訂 azure 角色獲得對 [azure 資源提供者](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) 作業的許可權。 如果您使用共用存取簽章 (SAS) 權杖，該權杖必須透過 sas 許可權提供 blob 標記的存取權 `t` 。

若要加入標記，請使用 `--blob-tags` 選項以及 URL 編碼的索引鍵/值組。 

例如，若要加入索引鍵 `my tag` 和值 `my tag value` ，您可以新增 `--blob-tags='my%20tag=my%20tag%20value'` 至目的地參數。 

使用 & 符號 () 來分隔多個索引標記 `&` 。  例如，如果您想要加入索引鍵 `my second tag` 和值 `my second tag value` ，則完整的選項字串會是 `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` 。

下列範例示範如何使用 `--blob-tags` 選項。

> [!TIP]
> 這些範例會用單引號括住路徑引數 ( ' ' ) 。 在所有命令 shell 中使用單引號，除了 Windows 命令 Shell ( # A0) 之外。 如果您使用 Windows 命令 Shell ( # A0) ，請使用雙引號括住路徑引數 ( "" ) 而不是單引號 ( ' ' ) 。

|    |     |
|--------|-----------|
| **Blob** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **目錄** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **容器** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **帳戶** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

複製作業是同步的，因此當命令傳回時，表示已複製所有檔案。

> [!NOTE]
> 如果您指定來源的目錄、容器或帳戶，則複製到目的地的所有 blob 都會有您在命令中指定的相同標記。

## <a name="copy-with-optional-flags"></a>使用選擇性旗標複製

您可以使用選擇性旗標來調整複製操作。 以下是一些範例。

|案例|旗標|
|---|---|
|將 blob 複製為區塊、分頁或附加 Blob。|**--blob 類型** = \[BlockBlob \| PageBlob \| AppendBlob\]|
|複製到特定的存取層 (例如封存層) 。|**--區塊-blob 層** = \[無 \| 熱 \| 冷 \| 封存\]|
|自動解壓縮檔案。|**--解壓縮** = \[gzip \| deflate\]|

如需完整清單，請參閱 [選項](storage-ref-azcopy-copy.md#options)。 

## <a name="next-steps"></a>後續步驟

在這些文章中尋找更多範例：

- [範例：上傳](storage-use-azcopy-blobs-upload.md)
- [範例：下載](storage-use-azcopy-blobs-download.md)
- [範例：同步處理](storage-use-azcopy-blobs-synchronize.md)
- [範例： Amazon S3 bucket](storage-use-azcopy-s3.md)
- [範例： Azure 檔案儲存體](storage-use-azcopy-files.md)
- [教學課程：使用 AzCopy 將內部部署資料移轉至雲端儲存體](storage-use-azcopy-migrate-on-premises-data.md)
- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)