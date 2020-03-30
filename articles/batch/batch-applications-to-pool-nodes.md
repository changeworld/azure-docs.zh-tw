---
title: 將應用程式和資料複製到池節點
description: 瞭解如何將應用程式和資料複製到池節點。
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.openlocfilehash: 226a0d69ac387142ecf580537e35f8754ac848a6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385580"
---
# <a name="copying-applications-and-data-to-pool-nodes"></a>將應用程式和資料複製到池節點

Azure Batch 支援將資料和應用程式獲取到計算節點的多種方法，以便資料和應用程式可供任務使用。 運行整個作業可能需要資料和應用程式，因此需要在每個節點上安裝。 有些可能僅針對特定任務是必需的，或者需要為作業安裝，但不需要在每個節點上。 Batch 具有針對每種方案的工具。

- **池啟動任務資源檔**：對於需要在池中的每個節點上安裝的應用程式或資料。 將此方法與應用程式包或啟動任務的資源檔集合一起使用，以便執行安裝命令。  

範例： 
- 使用啟動任務命令列移動或安裝應用程式

- 在 Azure 存儲帳戶中指定特定檔或容器的清單。 有關詳細資訊，請參閱[在 REST 文檔中添加資源檔](https://docs.microsoft.com/rest/api/batchservice/pool/add#resourcefile)

- 在池上運行的每個作業都運行 MyApplication.exe，必須首先安裝 MyApplication.msi。 如果使用此機制，則需要將開始任務的**等待成功**屬性設置為**true**。 有關詳細資訊，請參閱 REST[文檔中的 add_starttask。](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)

- **池上的應用程式包引用**：對於需要在池中的每個節點上安裝的應用程式或資料。 沒有與應用程式包關聯的安裝命令，但您可以使用啟動任務運行任何安裝命令。 如果應用程式不需要安裝，或者由大量檔組成，則可以使用此方法。 應用程式包非常適合大量檔，因為它們將大量檔引用合併到小負載中。 如果嘗試將 100 多個單獨的資源檔包含在一個任務中，Batch 服務可能會針對單個任務的內部系統限制。 此外，如果您有嚴格的版本控制要求，其中可能有許多不同版本的同一應用程式，並且需要在它們之間進行選擇，請使用應用程式包。 有關詳細資訊，請閱讀[將應用程式部署到具有 Batch 應用程式包的計算節點](https://docs.microsoft.com/azure/batch/batch-application-packages)。

- **作業準備任務資源檔**：對於必須安裝的應用程式或資料，作業才能運行，但不需要安裝在整個池上。 例如：如果池具有許多不同類型的作業，並且只有一個作業類型需要 MyApplication.msi 運行，則將安裝步驟放入作業準備任務中是有意義的。 有關作業準備任務的詳細資訊，請參閱[在批次處理計算節點上運行作業準備和作業發佈任務](https://azure.microsoft.com/documentation/articles/batch-job-prep-release/)。

- **任務資源檔**：用於應用程式或資料僅與單個任務相關時。 例如：您有五個任務，每個任務處理不同的檔，然後將輸出寫入 Blob 存儲。  在這種情況下，應在**任務資源檔**集合上指定輸入檔，因為每個任務都有自己的輸入檔。

## <a name="determine-the-scope-required-of-a-file"></a>確定檔所需的範圍

您需要確定檔的範圍 - 是池、作業或任務所需的檔。 限定到池的檔應使用池應用程式包或啟動任務。 限定到作業的檔應使用作業準備任務。 在池或作業級別範圍的檔的一個好示例是應用程式。 限定到任務的檔應使用任務資源檔。

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>將資料獲取到批次處理計算節點的其他方法

還有其他方法將資料獲取到未正式集成到批次處理 REST API 中的批次處理計算節點。 由於您可以控制 Azure Batch-節點，並且可以運行自訂可執行檔，因此只要 Batch-節點與目標具有連接，並且具有該源的憑據到 Azure Batch-節點，就可以從任意數量的自訂來源中提取資料。 一些常見示例包括：

- 從 SQL 下載資料
- 從其他 Web 服務/自訂位置下載資料
- 映射網路共用

### <a name="azure-storage"></a>Azure 儲存體

Blob 存儲具有下載可伸縮性目標。 Azure 存儲檔共用可伸縮性目標與單個 Blob 相同。 大小將影響所需的節點和池數。

