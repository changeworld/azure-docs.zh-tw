---
title: Azure Data Lake Storage Gen2 階層式命名空間
description: 描述階層式命名空間的概念，以用於 Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 6ce94590a1d0de6941c27d972bdd1c4194080e95
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77153072"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Azure Data Lake Storage Gen2 階層式命名空間

若要允許 Azure Data Lake Storage Gen2 以物件儲存體的規模和價格提供檔案系統效能，其關鍵機制在於新增**階層式命名空間**。 這會收集帳戶中的物件/檔案集合，並整理成階層式目錄和巢狀子目錄，此方式與整理電腦上的檔案系統方式相同。 啟用階層命名空間之後，儲存體帳戶就能夠提供物件儲存體的擴充性和成本效益，以及分析引擎和架構所熟悉的檔案系統語義。

## <a name="the-benefits-of-a-hierarchical-namespace"></a>階層式命名空間的優點

以下優勢是關於在 blob 資料上執行階層式命名空間的檔案系統：

- **不可部分完成的目錄操作：** 物件存放區會採用在物件名稱中內嵌斜線 (/) 以表示路徑線段的慣例，來模仿目錄階層的架構。 雖然此慣例適用於整理物件，但此慣例不提供移動、重新命名或刪除目錄等協助動作。 如果沒有真正的目錄，應用程式就必須處理可能數百萬個單獨的 blob 才能達到目錄層級的工作。 相反地，階層式命名空間會藉由更新單一專案（父目錄）來處理這些工作。

    這種大幅最佳化對於許多巨量資料分析架構來說尤為重要。 Hive 和 Spark 等工具經常將輸出寫入暫時位置，然後在作業結束時重新命名該位置。 如果沒有階層式命名空間，此重新命名通常會比分析程式本身更長的時間。 較低的作業延遲等於較低的分析工作負載擁有權總成本 (TCO)。

- **熟悉的介面樣式：** 開發人員和使用者都很熟悉檔案系統。 當您前往雲端時不需要學習新的儲存體範例，因為 Data Lake Storage Gen2 公開的檔案系統介面與大小和電腦使用的範例相同。

以往物件儲存不支援階層式命名空間的原因之一，是因為階層式命名空間限制了規模。 但是，Data Lake Storage Gen2 階層式命名空間能夠線性擴展，不會降低資料容量或效能。

## <a name="deciding-whether-to-enable-a-hierarchical-namespace"></a>決定是否要啟用階層式命名空間

在您的帳戶上啟用階層命名空間之後，您就無法將它還原回一般命名空間。 因此，請考慮根據物件存放區工作負載的本質啟用階層式命名空間是否合理。

某些工作負載可能無法藉由啟用階層式命名空間來獲得任何好處。 範例包括備份、影像儲存及其他應用程式，其中物件組織與物件本身會分開儲存 (例如在單獨的資料庫中)。 

此外，雖然 Blob 儲存體功能的支援和 Azure 服務生態系統持續成長，但仍有一些功能和 Azure 服務在具有階層式命名空間的帳戶中尚不受支援。 查看[已知問題](data-lake-storage-known-issues.md)。 

一般來說，我們建議您針對操作目錄的檔案系統所設計的儲存體工作負載，開啟階層式命名空間。 這包括主要用來分析處理的所有工作負載。 需要高程度組織的資料集也會藉由啟用階層式命名空間而受益。

啟用階層式命名空間的原因取決於 TCO 分析。 一般而言，若要改善因儲存體加速而導致的工作負載延遲，需要計算資源才能縮短時間。 許多工作負載的延遲可能會因為階層式命名空間所啟用的不可部分完成目錄操作而改善。 在許多工作負載中，計算資源佔總成本的 85％ 以上，因此即使適度減少工作負載延遲也相當於大量節省 TCO。 即使在啟用階層式命名空間的情況下，也會增加儲存體成本，但 TCO 仍會因為計算成本降低而降低。

若要分析具有一般階層命名空間與階層命名空間之帳戶之間的資料儲存體價格、交易價格和儲存體容量保留價格的差異，請參閱[Azure Data Lake Storage Gen2 定價](https://azure.microsoft.com/pricing/details/storage/data-lake/)。

## <a name="next-steps"></a>後續步驟

- [建立儲存體帳戶](./data-lake-storage-quickstart-create-account.md)
