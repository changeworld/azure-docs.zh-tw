---
title: 觸發新資料的 ML 管線
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Logic Apps 觸發 ML 管線的執行。
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: how-to
ms.date: 02/07/2020
ms.custom: contperfq4
ms.openlocfilehash: b56b07bce558a65df79a23eaf1948aac8e4fabad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84433761"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>從邏輯應用程式觸發 Machine Learning 管線的執行

當出現新資料時，觸發 Azure Machine Learning 管線的執行。 例如，您可能會想要觸發管線，以便在 blob 儲存體帳戶中出現新資料時，將新的模型定型。 使用[Azure Logic Apps](../logic-apps/logic-apps-overview.md)設定觸發程式。

## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 如需詳細資訊，請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

* 已發佈 Machine Learning 管線的 REST 端點。 [建立併發布您的管線](how-to-create-your-first-pipeline.md)。 然後使用管線識別碼尋找 PublishedPipeline 的 REST 端點：
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* 用來儲存資料的[Azure blob 儲存體](../storage/blobs/storage-blobs-overview.md)。
* 您工作區中的[資料](how-to-access-data.md)存放區，其中包含 blob 儲存體帳戶的詳細資料。

## <a name="create-a-logic-app"></a>建立邏輯應用程式

現在，建立[Azure 邏輯應用程式](../logic-apps/logic-apps-overview.md)實例。 如有需要，請[使用整合服務環境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)並[設定客戶管理的金鑰](../logic-apps/customer-managed-keys-integration-service-environment.md)，以供邏輯應用程式使用。

一旦布建您的邏輯應用程式，請使用下列步驟來設定管線的觸發程式：

1. [建立系統指派的受控識別](../logic-apps/create-managed-service-identity.md)，以將應用程式存取權授與您的 Azure Machine Learning 工作區。

1. 流覽至邏輯應用程式設計工具的視圖，然後選取 [空白邏輯應用程式] 範本。 
    > [!div class="mx-imgBorder"]
    > ![空白範本](media/how-to-trigger-published-pipeline/blank-template.png)

1. 在設計工具中，搜尋 [ **blob**]。 選取 [**新增或修改 Blob 時（僅限屬性）** ] 觸發程式，並將此觸發程式新增至您的邏輯應用程式。
    > [!div class="mx-imgBorder"]
    > ![新增觸發程序](media/how-to-trigger-published-pipeline/add-trigger.png)

1. 針對您想要監視以進行 blob 新增或修改的 Blob 儲存體帳戶，填入其連線資訊。 選取要監視的容器。 
 
    選擇要為您工作的更新輪詢**間隔**和**頻率**。  

    > [!NOTE]
    > 此觸發程式會監視選取的容器，但不會監視子資料夾。

1. 新增當偵測到新的或修改過的 blob 時，將會執行的 HTTP 動作。 選取 [ **+ 新增步驟**]，然後搜尋並選取 [HTTP] 動作。

  > [!div class="mx-imgBorder"]
  > ![搜尋 HTTP 動作](media/how-to-trigger-published-pipeline/search-http.png)

  使用下列設定來設定您的動作：

  | 設定 | 值 | 
  |---|---|
  | HTTP 動作 | POST |
  | URI |您找到作為必要條件的已發佈管線的端點[Prerequisite](#prerequisites) |
  | 驗證模式 | 受控識別 |

1. 設定排程以設定您可能擁有的任何[資料路徑 pipelineparameters.json](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb)值：

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

    使用 `DataStoreName` 您新增至工作區的作為必要條件[Prerequisite](#prerequisites)。
     
    > [!div class="mx-imgBorder"]
    > ![HTTP 設定](media/how-to-trigger-published-pipeline/http-settings.png)

1. 選取 [**儲存**]，您的排程現在已就緒。
