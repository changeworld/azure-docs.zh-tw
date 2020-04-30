---
title: 將應用程式和資料複製到集區節點
description: 瞭解如何將應用程式和資料複製到集區節點。
ms.topic: article
ms.date: 02/17/2020
ms.openlocfilehash: 700e9b80f8420266c0300b47bdd30bc271f8421c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115579"
---
# <a name="copying-applications-and-data-to-pool-nodes"></a>將應用程式和資料複製到集區節點

Azure Batch 支援數種將資料和應用程式放入計算節點的方式，讓資料和應用程式可供工作使用。 資料和應用程式可能需要執行整個作業，因此必須安裝在每個節點上。 某些可能只需要特定的工作，或必須針對作業安裝，但不需要在每個節點上。 Batch 有適用于每個案例的工具。

- **集區啟動工作資源檔**：適用于需要安裝在集區中每個節點上的應用程式或資料。 使用此方法搭配應用程式封裝或啟動工作的資源檔集合，以便執行安裝命令。  

範例： 
- 使用啟動工作命令列移動或安裝應用程式

- 指定 Azure 儲存體帳戶中的特定檔案或容器清單。 如需詳細資訊，請參閱[在 REST 中新增 # resourcefile 檔](https://docs.microsoft.com/rest/api/batchservice/pool/add#resourcefile)

- 在集區上執行的每個工作都會執行 MyApplication，必須先與 MyApplication 一併安裝。 如果您使用這項機制，則需要將啟動工作的 [**等待成功**] 屬性設定為 [ **true**]。 如需詳細資訊，請參閱在[REST 中新增 # starttask 檔](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)。

- 集區上的**應用程式套件參考**：適用于需要安裝在集區中每個節點上的應用程式或資料。 沒有與應用程式封裝相關聯的安裝命令，但是您可以使用啟動工作來執行任何安裝命令。 如果您的應用程式不需要安裝，或包含大量的檔案，您可以使用這個方法。 應用程式封裝非常適合大量檔案，因為它們會將大量的檔案參考合併成一個小型承載。 如果您嘗試將超過100個不同的資源檔包含在一個工作中，則 Batch 服務可能會針對單一工作產生內部系統限制。 此外，如果您有嚴格的版本控制需求，而您可能會有許多不同版本的相同應用程式，而且需要在兩者之間進行選擇，請使用應用程式套件。 如需詳細資訊，請參閱[使用 Batch 應用程式套件將應用程式部署至計算節點](https://docs.microsoft.com/azure/batch/batch-application-packages)。

- **作業準備工作資源檔**：適用于必須安裝才能執行作業，但不需要安裝在整個集區上的應用程式或資料。 例如：如果您的集區有許多不同類型的作業，而且只有一種作業類型需要 MyApplication 才能執行，則將安裝步驟放入作業準備工作是合理的做法。 如需作業準備工作的詳細資訊[，請參閱在 Batch 計算節點上執行作業準備和作業釋放](https://azure.microsoft.com/documentation/articles/batch-job-prep-release/)工作。

- 工作**資源檔**：適用于應用程式或資料僅與個別工作相關的。 例如：您有五個工作，每個都會處理不同的檔案，然後將輸出寫入至 blob 儲存體。  在此情況下，您應該在**tasks 資源檔**集合上指定輸入檔，因為每個工作都有自己的輸入檔。

## <a name="determine-the-scope-required-of-a-file"></a>判斷檔案所需的範圍

您需要判斷檔案的範圍-是集區、作業或工作所需的檔案。 範圍設定為集區的檔案應該使用集區應用程式套件或啟動工作。 以工作為範圍的檔案應該使用作業準備工作。 以集區或作業層級為範圍之檔案的良好範例為應用程式。 以工作為範圍的檔案應該使用任務資源檔。

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>在 Batch 計算節點上取得資料的其他方式

還有其他方法可以將資料放入未正式整合至 Batch REST API 的批次計算節點。 由於您可以控制 Azure Batch 的節點，而且可以執行自訂可執行檔，只要 Batch-節點與目標連線，而且您擁有該來源的認證至 [Azure Batch] 節點，就能夠從任何數目的自訂來源提取資料。 一些常見的範例包括：

- 從 SQL 下載資料
- 從其他 web 服務/自訂位置下載資料
- 對應網路共用

### <a name="azure-storage"></a>Azure 儲存體

Blob 儲存體具有下載擴充性目標。 Azure 儲存體檔案共用的擴充性目標與單一 blob 相同。 大小會影響您所需的節點和集區數目。

