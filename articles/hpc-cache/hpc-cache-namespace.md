---
title: 建立 Azure HPC Cache
description: 如何建立 Azure HPC Cache 執行個體
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: aaa939051a1aeafdb0650119772fc7214506aa8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582187"
---
# <a name="plan-the-aggregated-namespace"></a>規劃彙總的命名空間

Azure HPC 緩存允許用戶端通過隱藏後端存儲系統詳細資訊的虛擬命名空間訪問各種存儲系統。

添加存儲目標時，將設置面向用戶端的檔路徑。 用戶端電腦裝載此檔路徑，可以向緩存發出檔讀取請求，而不是直接安裝存儲系統。

由於 Azure HPC 緩存管理此虛擬檔案系統，因此無需更改面向用戶端的路徑即可更改存儲目標。 例如，您可以將硬體存儲系統替換為雲存儲，而無需重寫面向用戶端的過程。

## <a name="aggregated-namespace-example"></a>聚合命名空間示例

規劃聚合命名空間，以便用戶端電腦能夠方便地到達所需的資訊，以便管理員和工作流工程師可以輕鬆地區分路徑。

例如，考慮使用 Azure HPC 緩存實例來處理存儲在 Azure Blob 中的資料的系統。 分析需要存儲在本地資料中心中的範本檔。

範本資料存儲在資料中心中，此作業所需的資訊存儲在以下子目錄中：

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

資料中心存儲系統公開這些匯出：

    /
    /goldline
    /goldline/templates

要分析的資料已通過使用[CLFSLoad 實用程式](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)複製到名為"源集合"的 Azure Blob 存儲容器。

為了便於通過緩存訪問，請考慮使用這些虛擬命名空間路徑創建存儲目標：

| 後端存儲系統 <br/> （NFS 檔路徑或 Blob 容器） | 虛擬命名空間路徑 |
|-----------------------------------------|------------------------|
| /黃金線/範本/acme2017/sku798     | /範本/sku798      |
| /黃金線/範本/acme2017/sku980     | /範本/sku980      |
| 源集合                        | /來源/               |

NFS 存儲目標可以有多個虛擬命名空間路徑，只要每個路徑引用唯一匯出路徑。

由於 NFS 源路徑是同一匯出的子目錄，因此需要從同一存儲目標定義多個命名空間路徑。

| 存儲目標主機名稱  | NFS 匯出路徑      | 子目錄路徑 | 命名空間路徑    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP 位址或主機名稱* | /黃金線/範本  | acme2017/sku798   | /範本/sku798 |
| *IP 位址或主機名稱* | /黃金線/範本  | acme2017/sku980   | /範本/sku980 |

用戶端應用程式可以裝載緩存並輕鬆訪問聚合的命名空間檔路徑``/source````/templates/sku798``和 。 ``/templates/sku980``

## <a name="next-steps"></a>後續步驟

在決定如何設置虛擬檔案系統後，[創建存儲目標](hpc-cache-add-storage.md)以將後端存儲映射到面向用戶端的虛擬檔案路徑。
