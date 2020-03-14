---
title: Web 服務參數-Azure Machine Learning Studio （傳統） |Microsoft Docs
description: 如何使用 Azure Machine Learning Web 服務參數來修改模型在 Web 服務受到存取時的行為。
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2017
ms.openlocfilehash: d6ddd9603f22bd3820d18be020b9c620cf06aa42
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204404"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>使用 Azure Machine Learning Studio （傳統） web 服務參數

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

藉由發行包含可設定參數模組的試驗，來建立 Azure Machine Learning Web 服務。 在某些情況下，您可能想要在執行 Web 服務時之際，變更模組的行為。 「Web 服務參數」可讓您執行這項工作。 

常見的範例是設定匯[入資料][reader]模組，讓已發佈之 web 服務的使用者可以在存取 web 服務時，指定不同的資料來源。 或設定[匯出資料][writer]模組，以便能夠指定不同的目的地。 一些其他範例包括變更[特徵雜湊][feature-hashing]模組的位數，或針對以[篩選為基礎的特徵選取][filter-based-feature-selection]模組所需的功能數目。 

您可以設定 Web 服務參數，並使其與實驗中的一個或多個模組參數產生關聯，而且您可以指定它們是必要還是選用參數。 然後 Web 服務的使用者就可以在呼叫 Web 服務時，提供這些參數的值。 



## <a name="how-to-set-and-use-web-service-parameters"></a>如何設定及使用 Web 服務參數
您可以按一下模組參數旁邊的圖示，然後選取 [設為 Web 服務參數] 來定義 Web 服務參數。 這會建立新的 Web 服務參數並將它連線至該模組參數。 然後，在 Web 服務受到存取時，使用者可以指定 Web 服務參數的值，該值就會套用至模組參數。

在您定義 Web 服務參數之後，該參數就可以供試驗中任何其他的模組參數使用。 如果您定義和某個模組參數相關聯的 Web 服務參數，那麼只要參數預期具有相同類型的值，您就可以將該 Web 服務參數用於任何其他模組。 例如，如果 Web 服務參數是數值，那麼該參數就只能用於預期值為數值的模組參數。 當使用者設定 Web 服務參數的值時，該值將會套用至所有相關聯的模組參數。

您可以決定是否要為 Web 服務參數提供預設值。 如果您提供預設值，Web 服務的使用者就可以選擇是否使用該參數。 如果您沒有提供預設值，使用者就需要在存取 Web 服務時輸入值。

Web 服務的 API 文件會包含 Web 服務使用者在存取 Web 服務時，如何以程式設計方式指定 Web 服務參數的資訊。

> [!NOTE]
> 傳統 web 服務的 API 檔是透過 Machine Learning Studio （傳統）中 web 服務**儀表板**中的 [ **api 說明頁面**] 連結來提供。 新的 Web 服務的 API 文件則是透過 [Azure Machine Learning Web Services](https://services.azureml.net/Quickstart) 入口網站中 Web 服務的 [取用] 和 [Swagger API] 頁面提供。
> 
> 

## <a name="example"></a>範例
例如，假設我們有一個[匯出資料][writer]模組的實驗，可將資訊傳送至 Azure blob 儲存體。 我們將會定義名為 Blob path 的 Web 服務參數，以在服務被存取時允許 Web 服務使用者將路徑變更至 Blob 儲存體。

1. 在 [Machine Learning Studio （傳統）] 中，按一下 [[匯出資料][writer]] 模組來選取它。 其屬性會顯示在試驗畫布右邊的 [屬性] 窗格中。
2. 指定儲存體類型：
   
   * 在 **[請指定資料目的地]** 底下，選取 [Azure Blob 儲存體]。
   * 在 **[請指定驗證類型]** 底下，選取 [帳戶]。
   * 輸入 Azure Blob 儲存體的帳戶資訊。 

3. 按一下 **[以容器參數為開頭的 Blob 路徑]** 右邊的圖示。 它看起來像這樣：
   
   ![Web 服務參數圖示](./media/web-service-parameters/icon.png)
   
   選取 [設為 Web 服務參數]。
   
   就會在 **[屬性] 窗格底部的 [Web 服務參數]** 底下新增一個名為 [以容器為開頭的 Blob 路徑] 項目。 這是現在與此[匯出資料][writer]模組參數相關聯的 Web 服務參數。
4. 若要重新命名 Web 服務參數，請按一下名稱，輸入 Blob path，然後按 **Enter** 鍵。 
5. 若要為 Web 服務參數提供預設值，請按一下名稱右邊的圖示，選取 [提供預設值]，輸入值 (例如 container1/output1.csv)，然後按 **Enter** 鍵。
   
   ![Web 服務參數](./media/web-service-parameters/parameter.png)
6. 按一下 **[執行]** 。 
7. 按一下 [部署 Web 服務]，然後選取 [部署 Web 服務 [傳統]] 或 [部署 Web 服務 [新]] 可部署 Web 服務。

> [!NOTE] 
> 若要部署新的 Web 服務，您必須在要部署 Web 服務的訂用帳戶中具備足夠的權限。 如需詳細資訊，請參閱[使用 Azure Machine Learning Web 服務入口網站管理 Web 服務](manage-new-webservice.md)。 

Web 服務的使用者現在可以在存取 web 服務時，為[匯出資料][writer]模組指定新的目的地。

## <a name="more-information"></a>詳細資訊
如需更詳細的範例，請參閱 [Machine Learning Blog](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) 中的 [Web 服務參數](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx)項目。

有關存取 Machine Learning Web 服務的詳細資訊，請參閱[如何使用 Azure Machine Learning Web 服務](consume-web-services.md)。

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

