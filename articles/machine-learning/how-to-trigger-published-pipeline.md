---
title: 從邏輯應用觸發 ML 管道的運行
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure 邏輯應用觸發 ML 管道的運行。
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 6bb976b8b310fb3eb4d0247a8d745599f688d7b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122853"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>從邏輯應用觸發機器學習管道的運行

顯示新資料時觸發 Azure 機器學習管道的運行。 例如，您可能希望在 Blob 存儲帳戶中出現新資料時觸發管道以訓練新模型。 使用[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)設置觸發器。

## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 有關詳細資訊，請參閱創建[Azure 機器學習工作區](how-to-manage-workspace.md)。

* 已發佈的機器學習管道的 REST 終結點。 [創建和發佈管道](how-to-create-your-first-pipeline.md)。 然後，使用管道 ID 查找已發佈管道的 REST 終結點：
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [用於](../storage/blobs/storage-blobs-overview.md)存儲資料的 Azure Blob 存儲。
* 工作區中的[資料存儲](how-to-access-data.md)，其中包含 Blob 存儲帳戶的詳細資訊。

## <a name="create-a-logic-app"></a>建立邏輯應用程式

現在創建[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)實例。 如果需要，[請使用整合服務環境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)並[設置客戶管理的金鑰](../logic-apps/customer-managed-keys-integration-service-environment.md)供邏輯應用使用。

預配邏輯應用後，請使用以下步驟為管道配置觸發器：

1. [創建系統分配的託管標識](../logic-apps/create-managed-service-identity.md)，使應用可以訪問 Azure 機器學習工作區。

1. 導航到邏輯應用設計器視圖並選擇空白邏輯應用範本。 
    > [!div class="mx-imgBorder"]
    > ![空白範本](media/how-to-trigger-published-pipeline/blank-template.png)

1. 在設計器中，搜索**blob**。 選擇"**何時添加或修改 Blob（僅限屬性）"觸發器，** 並將此觸發器添加到邏輯應用。
    > [!div class="mx-imgBorder"]
    > ![新增觸發程序](media/how-to-trigger-published-pipeline/add-trigger.png)

1. 填寫您希望監視的 Blob 存儲帳戶的連接資訊，以便進行 Blob 添加或修改。 選擇要監視的容器。 
 
    選擇 **"間隔**"和 **"頻率**"輪詢適合您使用的更新。  

    > [!NOTE]
    > 此觸發器將監視選定的容器，但不會監視子資料夾。

1. 添加在檢測到新 blob 或修改的 Blob 時將運行的 HTTP 操作。 選擇 **= "新建步驟**"，然後搜索並選擇 HTTP 操作。

  > [!div class="mx-imgBorder"]
  > ![搜索 HTTP 操作](media/how-to-trigger-published-pipeline/search-http.png)

  使用以下設置配置操作：

  | 設定 | 值 | 
  |---|---|
  | HTTP 動作 | POST |
  | URI |作為[先決條件](#prerequisites)找到的已發佈管道的終結點 |
  | 驗證模式 | 受控識別 |

1. 設置計畫以設置您可能具有的任何[資料路徑管道參數](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb)的值：

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    使用`DataStoreName`添加到工作區作為[先決條件](#prerequisites)。
     
    > [!div class="mx-imgBorder"]
    > ![HTTP 設定](media/how-to-trigger-published-pipeline/http-settings.png)

1. 選擇 **"保存**"，您的排程現已準備就緒。