---
title: Azure Batch 集區的 Azure 檔案共用
description: 如何從 Linux 中的計算節點或 Azure Batch 中的 Windows 集區，裝載 Azure Files 共用。
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: e5682e7ba853973592c3a650a06ce72615cec7b6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735489"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>搭配 Batch 集區使用 Azure 檔案共用

[Azure Files](../storage/files/storage-files-introduction.md) 提供雲端中完全受控的檔案共用，可透過伺服器訊息區 (SMB) 通訊協定存取。 本文章提供在集區計算節點上裝載和使用 Azure 檔案共用的資訊和程式碼範例。

## <a name="considerations-for-use-with-batch"></a>搭配 Batch 的使用考量

* 如果使用非 premium Azure 檔案儲存體，當您的集區執行相對較低數目的平行工作時，請考慮使用 Azure 檔案共用。 根據您預期的集區大小和資產檔案數量，檢閱[效能和擴充目標](../storage/files/storage-files-scale-targets.md)，以判斷是否要使用 (使用 Azure 儲存體帳戶的) Azure Files。 

* Azure 檔案共用不但[符合成本效益](https://azure.microsoft.com/pricing/details/storage/files/)，還能設定將資料複寫到另一個區域，因此達成全域備援的目標。 

* 您可同時從內部部署電腦裝載 Azure 檔案共用。 不過，請確定您瞭解在使用 REST Api 時的 [並行影響](../storage/blobs/concurrency-manage.md) 。

* 另請參閱 Azure 檔案共用的一般[規劃考量](../storage/files/storage-files-planning.md)。


## <a name="create-a-file-share"></a>建立檔案共用

在儲存體帳戶[建立檔案共用](../storage/files/storage-how-to-create-file-share.md)，並連結至您的 Batch 帳戶，或者在個別的儲存體帳戶建立。

## <a name="mount-an-azure-file-share-on-a-batch-pool"></a>在 Batch 集區上掛接 Azure 檔案共用

請參閱檔，以瞭解如何在 [Batch 集區上掛接虛擬檔案系統](virtual-file-mount.md)。

## <a name="next-steps"></a>後續步驟

* 關於在 Batch 中讀取和寫入資料的其他選項，請參閱[持續作業及工作輸出](batch-task-output.md)。
* 另請參閱 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 工具組，其中包括 [Shipyard 訣竅](https://github.com/Azure/batch-shipyard/tree/master/recipes)，以部署 Batch 容器工作負載的檔案系統。