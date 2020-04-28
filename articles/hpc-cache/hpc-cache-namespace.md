---
title: 建立 Azure HPC Cache
description: 如何建立 Azure HPC Cache 執行個體
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: aaa939051a1aeafdb0650119772fc7214506aa8d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73582187"
---
# <a name="plan-the-aggregated-namespace"></a>規劃彙總的命名空間

Azure HPC 快取可讓用戶端透過會隱藏後端儲存系統詳細資料的虛擬命名空間，來存取各種不同的儲存系統。

當您新增儲存體目標時，會設定用戶端面向的檔案路徑。 用戶端電腦會掛接此檔案路徑，而且可以對快取提出檔案讀取要求，而不是直接裝載儲存系統。

由於 Azure HPC 快取會管理此虛擬檔案系統，因此您可以變更儲存體目標，而不需要變更用戶端對應的路徑。 例如，您可以更換硬體儲存系統與雲端存放裝置，而不需要重寫用戶端面向的程式。

## <a name="aggregated-namespace-example"></a>匯總命名空間範例

規劃您的匯總命名空間，讓用戶端電腦可以方便地觸達所需的資訊，讓系統管理員和工作流程工程師可以輕鬆區分這些路徑。

例如，假設有一個系統，其中 Azure HPC 快取實例用來處理儲存在 Azure Blob 中的資料。 分析需要儲存在內部部署資料中心內的範本檔案。

範本資料會儲存在資料中心，而此作業所需的資訊會儲存在這些子目錄中：

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

資料中心儲存體系統會公開這些匯出：

    /
    /goldline
    /goldline/templates

使用[CLFSLoad 公用程式](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)，將要分析的資料複製到名為 "sourcecollection" 的 Azure Blob 儲存體容器。

若要允許透過快取輕鬆存取，請考慮使用這些虛擬命名空間路徑來建立儲存體目標：

| 後端儲存系統 <br/> （NFS 檔案路徑或 Blob 容器） | 虛擬命名空間路徑 |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection                        | /source               |

NFS 儲存體目標可以有多個虛擬命名空間路徑，前提是每一個都參考唯一的匯出路徑。

因為 NFS 來源路徑是相同匯出的子目錄，所以您必須從相同的儲存體目標定義多個命名空間路徑。

| 儲存體目標主機名稱  | NFS 匯出路徑      | 子目錄路徑 | 命名空間路徑    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP 位址或主機名稱* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *IP 位址或主機名稱* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

用戶端應用程式可以掛接快取，並輕鬆地存取匯總的``/source``命名``/templates/sku798``空間檔案``/templates/sku980``路徑、和。

## <a name="next-steps"></a>後續步驟

在決定如何設定虛擬檔案系統之後，請[建立儲存體目標](hpc-cache-add-storage.md)，以將後端儲存體對應至用戶端對向虛擬檔案路徑。
