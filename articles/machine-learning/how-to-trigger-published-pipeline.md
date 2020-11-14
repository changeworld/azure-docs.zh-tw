---
title: 觸發新資料的 ML 管線
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Logic Apps 來回應新資料，以觸發 Azure Machine Learning 管線的執行。
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.date: 02/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4
ms.openlocfilehash: 119436cbe03e619b0d09df30ab2c5dc34126b0ce
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629388"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>從邏輯應用程式觸發 Machine Learning 管線的執行

當新資料出現時，觸發 Azure Machine Learning 管線的執行。 例如，當 blob 儲存體帳戶中出現新的資料時，您可能會想要觸發管線以訓練新模型。 使用 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)設定觸發程式。

## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 如需詳細資訊，請參閱 [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

* 已發佈 Machine Learning 管線的 REST 端點。 [建立併發布您的管線](how-to-create-your-first-pipeline.md)。 然後使用管線識別碼來尋找 PublishedPipeline 的 REST 端點：
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* 用來儲存資料的[Azure blob 儲存體](../storage/blobs/storage-blobs-overview.md)。
* 您工作區中的[資料](how-to-access-data.md)存放區，其中包含您 blob 儲存體帳戶的詳細資料。

## <a name="create-a-logic-app"></a>建立邏輯應用程式

現在，建立 [Azure 邏輯應用程式](../logic-apps/logic-apps-overview.md) 實例。 如果您想要的話，請 [使用 (ISE) 的整合服務環境 ](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) ，並 [設定客戶管理的金鑰](../logic-apps/customer-managed-keys-integration-service-environment.md) ，以供邏輯應用程式使用。

布建您的邏輯應用程式之後，請使用下列步驟來設定管線的觸發程式：

1. [建立系統指派的受控識別](../logic-apps/create-managed-service-identity.md) ，以授與應用程式存取您的 Azure Machine Learning 工作區。

1. 流覽至 [邏輯應用程式設計工具] 視圖，然後選取 [空白邏輯應用程式] 範本。 
    > [!div class="mx-imgBorder"]
    > ![空白範本](media/how-to-trigger-published-pipeline/blank-template.png)

1. 在設計工具中，搜尋 **blob** 。 選取 [ **新增或修改 Blob 時] (屬性只)** 觸發程式，並將此觸發程式新增至邏輯應用程式。
    > [!div class="mx-imgBorder"]
    > ![新增觸發程序](media/how-to-trigger-published-pipeline/add-trigger.png)

1. 填入您想要監視的 blob 儲存體帳戶的連線資訊，以進行 blob 新增或修改。 選取要監視的容器。 
 
    選擇要輪詢適用于您的更新的 **間隔** 和 **頻率** 。  

    > [!NOTE]
    > 此觸發程式會監視選取的容器，但不會監視子資料夾。

1. 新增在偵測到新的或已修改的 blob 時，將會執行的 HTTP 動作。 選取 [ **+ 新增步驟** ]，然後搜尋並選取 [HTTP] 動作。

  > [!div class="mx-imgBorder"]
  > ![搜尋 HTTP 動作](media/how-to-trigger-published-pipeline/search-http.png)

  使用下列設定來設定您的動作：

  | 設定 | 值 | 
  |---|---|
  | HTTP 動作 | POST |
  | URI |您[發現為必要條件](#prerequisites)之已發佈管線的端點 |
  | 驗證模式 | 受控識別 |

1. 設定您的排程，以設定您可能擁有的任何 [資料路徑 PipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) 值：

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

1. 選取 [ **儲存** ]，您的排程現在已就緒。

> [!IMPORTANT]
> 如果您使用以角色為基礎的存取控制 (RBAC) 來管理管線的存取權，請 [設定管線案例的許可權 (定型或計分) ](how-to-assign-roles.md#common-scenarios)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱

> [!div class="nextstepaction"]
> [使用 Azure Machine Learning 管線來進行批次評分](tutorial-pipeline-batch-scoring-classification.md)

* 深入瞭解 [管線](concept-ml-pipelines.md)
* 深入瞭解如何 [使用 Jupyter 探索 Azure Machine Learning](samples-notebooks.md)

