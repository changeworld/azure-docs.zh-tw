---
title: Azure 儲存體總管 blob 版本設定指南 |Microsoft Docs
description: Azure 儲存體總管的 Blob 版本設定指引
services: storage
author: chuye
ms.service: storage
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: chuye
ms.openlocfilehash: e20733cb4b93fcfac7606895746645727f12d6c8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783822"
---
# <a name="azure-storage-explorer-blob-versioning-guide"></a>Azure 儲存體總管 blob 版本設定指南

Microsoft Azure 儲存體總管提供簡單的 blob 版本存取和管理。 本指南將協助您瞭解 blob 版本設定在儲存體總管中的運作方式。 繼續之前，建議您先閱讀 [blob 版本](../blobs/versioning-overview.md)設定的詳細資訊。

## <a name="terminology"></a>詞彙

本節提供一些定義，以協助您瞭解其在本文中的使用方式。

- 虛刪除：替代的自動資料保護功能。 您可以在 [這裡](../blobs/soft-delete-blob-overview.md)深入瞭解虛刪除。
- 作用中的 blob： blob 或 blob 版本是以主動狀態建立。 您只能在處於作用中狀態的 blob 或 blob 版本上運作。
- 虛刪除的 blob：標示為虛刪除的 blob 或 blob 版本。 虛刪除的 blob 只會保留保留期限。
- Blob 版本：已啟用 blob 版本設定所建立的 blob。 每個 blob 版本都與版本識別碼相關聯。
- 目前的版本：標示為目前版本的 blob 版本。
- 先前的版本：不是目前版本的 blob 版本。
- 非版本 blob：已停用 blob 版本設定所建立的 blob。 非版本 blob 沒有版本識別碼。

## <a name="view-blob-versions"></a>查看 blob 版本

儲存體總管支援四種不同的視圖來查看 blob。

| 檢視 | 作用中的非版本 blob | 虛刪除的非版本 blob | Blob 版本 |
| ---- | :----------: | :-----------: | :------------------: |
| 作用中的 blob | 是 | 否 | 僅限目前的版本 |
| 作用中的 blob 和虛刪除的 blob | 是 | 是 | 僅限目前的版本 |
| 作用中的 blob 和沒有目前版本的 blob | 是 | 否 | 目前版本或最新的有效版本 |
| 沒有最新版本的所有 blob 和 blob | 是 | 是 | 目前版本或最新版本 |

### <a name="active-blobs"></a>作用中的 blob

在此視圖中，儲存體總管會顯示：

- 作用中的非版本 blob
- 目前的版本

### <a name="active-blobs-and-soft-deleted-blobs"></a>作用中的 blob 和虛刪除的 blob

在此視圖中，儲存體總管會顯示：

- 作用中的非版本 blob
- 虛刪除的非版本 blob
- 目前的版本。

### <a name="active-blobs-and-blobs-without-current-version"></a>作用中的 blob 和沒有目前版本的 blob

在此視圖中，儲存體總管會顯示：

- 作用中的非版本 blob
- 目前的版本
- 最新的有效舊版。 

如果 blob 沒有最新版本，但有舊版使用中的 blob，儲存體總管會顯示其最新的作用中舊版作為該 blob 的表示。

### <a name="all-blobs-and-blobs-without-current-version"></a>沒有最新版本的所有 blob 和 blob

在此視圖中，儲存體總管會顯示：

- 作用中的非版本 blob
- 虛刪除的非版本 blob
- 目前的版本
- 先前的最新版本。 

如果 blob 沒有最新版本，儲存體總管會顯示其最新版本，以作為該 blob 的表示。

> [!Note]
> 由於服務限制，儲存體總管需要一些額外的處理，才能在列出 blob 版本時取得虛擬目錄的階層式觀點。 您需要花費較長的時間來列出下列視圖中的 blob：
> 
> - 作用中的 blob 和沒有目前版本的 blob
> - 沒有最新版本的所有 blob 和 blob

## <a name="manage-blob-versions"></a>管理 blob 版本

### <a name="view-versions-of-a-blob"></a>查看 blob 的版本

儲存體總管提供 [ **管理版本** ] 命令來查看 blob 的所有版本。 若要查看 blob 的版本，請選取您想要查看其版本的 blob，並從工具列或內容功能表中選取 [ **管理歷程記錄 &rarr; 管理版本** ]。

### <a name="download-blob-versions"></a>下載 blob 版本

若要下載一或多個 blob 版本，請選取您要下載的 blob 版本，然後從工具列或內容功能表中選取 [ **下載** ]。

如果您要下載 blob 的多個版本，下載的檔案將會在其檔案名的開頭有其版本識別碼。

### <a name="delete-blob-versions"></a>刪除 blob 版本

若要刪除一或多個 blob 版本，請選取您要刪除的 blob 版本，然後從工具列或內容功能表中選取 [ **刪除** ]。

Blob 版本受限於您的虛刪除原則。 如果已啟用虛刪除，則會將 blob 版本虛刪除。 其中一個特殊案例是刪除目前的版本。 刪除目前的版本會自動使其成為使用中的舊版本。

### <a name="promote-blob-version"></a>升級 blob 版本

您可以藉由升級舊版來還原 blob 的內容，使其成為目前的版本。 選取您要升級的 blob 版本，然後從工具列或內容功能表中選取 [ **升級版本** ]。

升級的 blob 版本將會覆寫非版本的 blob。 確認作業之前，請確定您不再需要該資料，或自行備份資料。 目前的版本會自動成為先前的版本，因此儲存體總管不會提示確認。

### <a name="undelete-blob-version"></a>取消刪除 blob 版本

無法個別刪除 Blob 版本。 它們必須一次全部刪除。 若要取消刪除 blob 的所有 blob 版本，請選取其中一個 blob 版本，然後從工具列或內容功能表選取 [取消 **刪除** ]。

### <a name="change-access-tier-of-blob-versions"></a>變更 blob 版本的存取層

每個 blob 版本都有自己的存取層。 若要變更 blob 版本的存取層，請選取您想要變更存取層的 blob 版本，然後從內容功能表選取 [ **變更存取層** ]。

## <a name="see-also"></a>另請參閱

* [Blob 版本設定](../blobs/versioning-overview.md)
* [Blob 的虛刪除](../blobs/soft-delete-blob-overview.md)