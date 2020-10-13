---
title: 瞭解 Azure HPC Cache 匯總的命名空間
description: 如何規劃 Azure HPC Cache 的虛擬命名空間
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 1c28f549cf93d77f6aef6bcde6a2225345a79cc9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91612943"
---
# <a name="plan-the-aggregated-namespace"></a>規劃彙總的命名空間

Azure HPC Cache 可讓用戶端透過虛擬命名空間來存取各種不同的儲存體系統，此命名空間會隱藏後端儲存體系統的詳細資料。

新增儲存體目標之後，您可以為儲存體目標設定一或多個面向用戶端的命名空間路徑。 用戶端電腦會掛接此檔案路徑，而且可以對快取提出檔案讀取要求，而不是直接掛接儲存體系統。

由於 Azure HPC Cache 管理此虛擬檔案系統，因此您可以變更儲存體目標，而不需要變更面向用戶端的路徑。 例如，您可以將硬體儲存系統取代為雲端存放裝置，而不需要重寫用戶端程式。

## <a name="aggregated-namespace-example"></a>匯總命名空間範例

規劃您的匯總命名空間，讓用戶端電腦可以輕鬆地到達所需的資訊，讓系統管理員和工作流程工程師可以輕鬆地區分路徑。

例如，假設有一個系統正在使用 Azure HPC Cache 實例來處理 Azure Blob 中儲存的資料。 分析需要儲存在內部部署資料中心的範本檔案。

範本資料會儲存在資料中心，而這項作業所需的資訊會儲存在這些子目錄中：

* */goldline/templates/acme2017/sku798*
* */goldline/templates/acme2017/sku980*

資料中心儲存體系統會公開這些匯出：

* */*
* */goldline*
* */goldline/templates*

使用 [CLFSLoad 公用程式](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)，將要分析的資料複製到名為 "sourcecollection" 的 Azure Blob 儲存體容器。

若要允許透過快取輕鬆存取，請考慮使用這些虛擬命名空間路徑來建立儲存體目標：

| 後端儲存體系統 <br/>  (NFS 檔案路徑或 Blob 容器)  | 虛擬命名空間路徑 |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection                        | /source               |

NFS 儲存體目標可以有多個虛擬命名空間路徑，只要每一個都參考唯一的匯出路徑即可。  (讀取 [nfs 命名空間路徑](add-namespace-paths.md#nfs-namespace-paths) ，以瞭解每個 NFS 儲存體目標的建議命名空間路徑數目上限。 ) 

因為 NFS 來源路徑是相同匯出的子目錄，所以您必須從相同的儲存體目標定義多個命名空間路徑。

| 儲存體目標主機名稱  | NFS 匯出路徑     | 子目錄路徑 | 命名空間路徑    |
|--------------------------|---------------------|-------------------|-------------------|
| *IP 位址或主機名稱* | /goldline/templates | acme2017/sku798   | /templates/sku798 |
| *IP 位址或主機名稱* | /goldline/templates | acme2017/sku980   | /templates/sku980 |

用戶端應用程式可以掛接快取，並輕鬆存取匯總的命名空間檔案路徑 ``/source`` 、 ``/templates/sku798`` 和 ``/templates/sku980`` 。

另一種方法是建立虛擬路徑（例如， `/templates` 連結至上層目錄）， `acme2017` 然後讓用戶端在裝載快取之後流覽至個別 `sku798` 和 `sku980` 目錄。 不過，您無法建立命名空間路徑，它是另一個命名空間路徑的子目錄。 因此，如果您建立目錄的路徑 `acme2017` ，您也無法建立任何命名空間路徑來直接存取其子目錄。

[Azure HPC Cache **命名空間** 設定] 頁面會顯示面向用戶端的檔案系統，並可讓您新增或編輯路徑。 如需詳細資料，請參閱 [設定匯總的命名空間](add-namespace-paths.md) 。

## <a name="next-steps"></a>後續步驟

在決定如何設定虛擬檔案系統之後，請採取下列步驟來建立它：

* [建立儲存體目標](hpc-cache-add-storage.md) ，以將您的後端儲存體系統新增至 Azure HPC Cache
* [新增命名空間路徑](add-namespace-paths.md) 以建立用戶端電腦用來存取檔案的匯總命名空間
