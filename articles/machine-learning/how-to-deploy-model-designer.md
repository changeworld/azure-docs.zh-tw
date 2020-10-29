---
title: 使用 studio 部署在設計工具中定型的模型
titleSuffix: Azure Machine Learning
description: 使用 Azure Machine Learning studio 來部署在設計工具中定型的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: peterlu
ms.date: 10/29/2020
ms.topic: conceptual
ms.custom: how-to, deploy, studio
ms.openlocfilehash: 0d98d5103e26eb0b4ee0d31b95f1d07cdaa396ae
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927578"
---
# <a name="use-the-studio-to-deploy-models-trained-in-the-designer"></a>使用 studio 部署在設計工具中定型的模型

在本文中，您將瞭解如何在 Azure Machine Learning studio 中，從設計工具將定型的模型部署為即時端點。

Studio 中的部署是由下列步驟所組成：

1. 註冊已定型的模型。
1. 下載模型的 [輸入腳本] 和 [conda 相依性] 檔案。
1.  (選擇性) 設定輸入腳本。
1. 將模型部署至計算目標。

您也可以直接在設計工具中部署模型，以略過模型註冊和檔案下載步驟。 這有助於快速部署。 如需詳細資訊，請參閱 [使用設計工具部署模型](tutorial-designer-automobile-price-deploy.md)。

在設計工具中定型的模型也可以透過 SDK 或命令列介面 (CLI) 來部署。 如需詳細資訊，請參閱 [使用 Azure Machine Learning 部署現有的模型](how-to-deploy-existing-model.md)。

## <a name="prerequisites"></a>先決條件

* [Azure Machine Learning 工作區](how-to-manage-workspace.md)

* 完成的定型管線，其中包含下列其中一個模組：
    - [訓練模組](./algorithm-module-reference/train-model.md)
    - [定型異常偵測模型模組](./algorithm-module-reference/train-anomaly-detection-model.md)
    - [定型群集模型模組](./algorithm-module-reference/train-clustering-model.md)
    - [定型 Pytorch 模型模組](./algorithm-module-reference/train-pytorch-model.md)
    - [訓練 SVD 推薦課程模組](./algorithm-module-reference/train-svd-recommender.md)
    - [定型 Vowpal Wabbit 模型模組](./algorithm-module-reference/train-vowpal-wabbit-model.md)
    - [訓練 Wide & 深度模型模組](./algorithm-module-reference/train-wide-and-deep-recommender.md)

## <a name="register-the-model"></a>註冊模型

定型管線完成之後，請將定型的模型註冊到您的 Azure Machine Learning 工作區，以存取其他專案中的模型。

1. 選取 [ [訓練模型] 模組](./algorithm-module-reference/train-model.md)。
1. 選取右窗格中的 [ **輸出 + 記錄** ] 索引標籤。
1. 選取齒輪圖示的 **註冊模型** 圖示 ![ 螢幕擷取畫面 ](./media/how-to-deploy-model-designer/register-model-icon.png) 。

    ![定型模型模組右窗格的螢幕擷取畫面](./media/how-to-deploy-model-designer/train-model-right-pane.png)

1. 輸入模型的名稱，然後選取 [ **儲存** ]。

註冊您的模型之後，您可以在 studio 的 [ **模型** 資產] 頁面中找到它。
    
![[模型資產] 頁面中已註冊模型的螢幕擷取畫面](./media/how-to-deploy-model-designer/models-asset-page.png)


## <a name="download-the-entry-script-file-and-conda-dependencies-file"></a>下載輸入腳本檔案和 conda 相依性檔案

您需要下列檔案，才能在 Azure Machine Learning studio 中部署模型：

- **進入腳本** 檔-載入定型的模型、處理來自要求的輸入資料、執行即時推斷，然後傳回結果。 `score.py`當「 **定型模型** 」模組完成時，設計工具會自動產生輸入腳本檔案。

- **Conda** 相依性檔案-指定您的 webservice 依存的 pip 和 Conda 套件。 `conda_env.yaml`當「 **定型模型** 」模組完成時，設計工具會自動建立檔案。

您可以在 [ **訓練模型** ] 模組的右窗格中下載這兩個檔案：

1. 選取 **訓練模型** 模組。
1. 在 [ **輸出 + 記錄** 檔] 索引標籤中，選取資料夾 `trained_model_outputs` 。
1. 下載 `conda_env.yaml` 檔和檔案 `score.py` 。

    ![在右窗格中下載部署檔案的螢幕擷取畫面](./media/how-to-deploy-model-designer/download-artifacts-in-right-pane.png)

或者，您可以在註冊模型之後從 [ **模型** 資產] 頁面下載檔案：

1. 流覽至 [ **模型** 資產] 頁面。
1. 選取您要部署的模型。
1. 選取 [ **成品** ] 索引標籤。
1. 選取 `trained_model_outputs` 資料夾。
1. 下載 `conda_env.yaml` 檔和檔案 `score.py` 。  

    ![模型詳細資料頁面中部署的下載檔案螢幕擷取畫面](./media/how-to-deploy-model-designer/download-artifacts-in-models-page.png)

> [!NOTE]
> 檔案所 `score.py` 提供的功能與 **評分模型** 模組幾乎相同。 不過，某些模組（例如 [計分 SVD 推薦](./algorithm-module-reference/score-svd-recommender.md)、 [分數寬和深度推薦](./algorithm-module-reference/score-wide-and-deep-recommender.md)）和 [分數 Vowpal Wabbit 模型](./algorithm-module-reference/score-vowpal-wabbit-model.md) 具有不同評分模式的參數。 您也可以在輸入腳本中變更這些參數。
>
>如需有關在檔案中設定參數的詳細資訊 `score.py` ，請參閱設定 [輸入腳本](#configure-the-entry-script)一節。

## <a name="deploy-the-model"></a>部署模型

下載所需的檔案之後，您就可以開始部署模型。

1. 在 [ **模型** 資產] 頁面中，選取已註冊的模型。
1. 選取 [ **部署** ] 按鈕。
1. 在 [設定] 功能表中，輸入下列資訊：

    - 輸入端點的名稱。
    - 選取即可將模型部署到 [Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) 或 [Azure 容器實例](how-to-deploy-azure-container-instance.md)。
    - 上傳 `score.py` **輸入腳本** 檔案的。
    - 上傳 Conda 相依性檔案的 `conda_env.yml` 。 **Conda dependencies file** 

    >[!TIP]
    > 在 [ **Advanced** ] 設定中，您可以設定 CPU/記憶體容量和其他用於部署的參數。 這些設定對於某些模型很重要，例如 PyTorch 模型，這會耗用相當大量的 memery (大約 4 GB 的) 。

1. 選取 [ **部署** ]，將您的模型部署為即時端點。

    ![模型資產頁面中部署模型的螢幕擷取畫面](./media/how-to-deploy-model-designer/deploy-model.png)

## <a name="consume-the-real-time-endpoint"></a>使用即時端點

部署成功後，您可以在 [ **端點** 資產] 頁面中找到即時端點。 在那裡，您會發現 REST 端點，用戶端可以用它來提交要求至即時端點。 

> [!NOTE]
> 設計工具也會產生用於測試的範例資料 json 檔案，您可以 `_samples.json` 在 **trained_model_outputs** 資料夾中下載。

使用下列程式碼範例來使用即時端點。

```python

import json
from pathlib import Path
from azureml.core.workspace import Workspace, Webservice
 
service_name = 'YOUR_SERVICE_NAME'
ws = Workspace.get(
    name='WORKSPACE_NAME',
    subscription_id='SUBSCRIPTION_ID',
    resource_group='RESOURCEGROUP_NAME'
)
service = Webservice(ws, service_name)
sample_file_path = '_samples.json'
 
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)
score_result = service.run(json.dumps(sample_data))
print(f'Inference result = {score_result}')
```

### <a name="consume-computer-vision-related-real-time-endpoints"></a>使用電腦視覺相關的即時端點

當取用電腦視覺相關的即時端點時，您需要將影像轉換成位元組，因為 web 服務只接受字串做為輸入。 以下是範例程式碼：

```python
import base64
import json
from copy import deepcopy
from pathlib import Path
from azureml.studio.core.io.image_directory import (IMG_EXTS, image_from_file, image_to_bytes)
from azureml.studio.core.io.transformation_directory import ImageTransformationDirectory

# image path
image_path = Path('YOUR_IMAGE_FILE_PATH')

# provide the same parameter setting as in the training pipeline. Just an example here.
image_transform = [
    # format: (op, args). {} means using default parameter values of torchvision.transforms.
    # See https://pytorch.org/docs/stable/torchvision/transforms.html
    ('Resize', 256),
    ('CenterCrop', 224),
    # ('Pad', 0),
    # ('ColorJitter', {}),
    # ('Grayscale', {}),
    # ('RandomResizedCrop', 256),
    # ('RandomCrop', 224),
    # ('RandomHorizontalFlip', {}),
    # ('RandomVerticalFlip', {}),
    # ('RandomRotation', 0),
    # ('RandomAffine', 0),
    # ('RandomGrayscale', {}),
    # ('RandomPerspective', {}),
]
transform = ImageTransformationDirectory.create(transforms=image_transform).torch_transform

# download _samples.json file under Outputs+logs tab in the right pane of Train Pytorch Model module
sample_file_path = '_samples.json'
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)

# use first sample item as the default value
default_data = sample_data[0]
data_list = []
for p in image_path.iterdir():
    if p.suffix.lower() in IMG_EXTS:
        data = deepcopy(default_data)
        # convert image to bytes
        data['image'] = base64.b64encode(image_to_bytes(transform(image_from_file(p)))).decode()
        data_list.append(data)

# use data.json as input of consuming the endpoint
data_file_path = 'data.json'
with open(data_file_path, 'w') as f:
    json.dump(data_list, f)
```

## <a name="configure-the-entry-script"></a>設定輸入腳本

設計工具中的某些模組（例如 [計分 SVD 推薦](./algorithm-module-reference/score-svd-recommender.md)、 [分數廣泛和深度推薦](./algorithm-module-reference/score-wide-and-deep-recommender.md)）和 [分數 Vowpal Wabbit 模型](./algorithm-module-reference/score-vowpal-wabbit-model.md) 具有不同評分模式的參數。 

在本節中，您將瞭解如何在輸入腳本檔中更新這些參數。

下列範例會更新定型的 **寬 & 深層推薦** 模型的預設行為。 根據預設，檔案會 `score.py` 告知 web 服務預測使用者與專案之間的評等。 

您可以修改輸入腳本檔以提出專案建議，並藉由變更參數來傳回建議的專案 `recommender_prediction_kind` 。


```python
import os
import json
from pathlib import Path
from collections import defaultdict
from azureml.studio.core.io.model_directory import ModelDirectory
from azureml.designer.modules.recommendation.dnn.wide_and_deep.score. \
    score_wide_and_deep_recommender import ScoreWideAndDeepRecommenderModule
from azureml.designer.serving.dagengine.utils import decode_nan
from azureml.designer.serving.dagengine.converter import create_dfd_from_dict

model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'trained_model_outputs')
schema_file_path = Path(model_path) / '_schema.json'
with open(schema_file_path) as fp:
    schema_data = json.load(fp)


def init():
    global model
    model = ModelDirectory.load(load_from_dir=model_path)


def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    for row in data:
        for key, val in row.items():
            input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)

    # The parameter names can be inferred from Score Wide and Deep Recommender module parameters:
    # convert the letters to lower cases and replace whitespaces to underscores.
    score_params = dict(
        trained_wide_and_deep_recommendation_model=model,
        dataset_to_score=data_frame_directory,
        training_data=None,
        user_features=None,
        item_features=None,
        ################### Note #################
        # Set 'Recommender prediction kind' parameter to enable item recommendation model
        recommender_prediction_kind='Item Recommendation',
        recommended_item_selection='From All Items',
        maximum_number_of_items_to_recommend_to_a_user=5,
        whether_to_return_the_predicted_ratings_of_the_items_along_with_the_labels='True')
    result_dfd, = ScoreWideAndDeepRecommenderModule().run(**score_params)
    result_df = result_dfd.data
    return json.dumps(result_df.to_dict("list"))
```

針對 **Wide & 深層推薦** 和 **Vowpal Wabbit** 模型，您可以使用下列方法設定評分模式參數：

- 參數名稱是 [計分 Vowpal Wabbit 模型](./algorithm-module-reference/score-vowpal-wabbit-model.md) 和 [分數和深度推薦](./algorithm-module-reference/score-wide-and-deep-recommender.md)之參數名稱的小寫和底線組合;
- 模式類型參數值是對應的選項名稱的字串。 在上述的範例中採用 **推薦預測種類** ，例如，值可以是 `'Rating Prediction'` 或 `'Item Recommendation'` 。 不允許其他值。

針對 **SVD 推薦** 定型的模型，參數名稱和值可能較不明顯，而且您可以查閱下表以決定如何設定參數。

| [計分 SVD 推薦](./algorithm-module-reference/score-svd-recommender.md)中的參數名稱                           | 輸入腳本檔中的參數名稱 |
| ------------------------------------------------------------ | --------------------------------------- |
| 推薦預測種類                                  | prediction_kind                         |
| 選擇建議項目                                   | recommended_item_selection              |
| 單一使用者的建議集區大小下限    | min_recommendation_pool_size            |
| 建議給使用者的項目數上限               | max_recommended_item_count              |
| 是否要傳回專案的預測評等以及標籤 | return_ratings                          |

下列程式碼會示範如何設定 SVD 推薦的參數，其使用所有六個參數來建議已附加預測評等的分級專案。

```python
score_params = dict(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        training_data=None,
        # RecommenderPredictionKind has 2 members, 'RatingPrediction' and 'ItemRecommendation'. You
        # can specify prediction_kind parameter with one of them.
        prediction_kind=RecommenderPredictionKind.ItemRecommendation,
        # RecommendedItemSelection has 3 members, 'FromAllItems', 'FromRatedItems', 'FromUndatedItems'.
        # You can specify recommended_item_selection parameter with one of them.
        recommended_item_selection=RecommendedItemSelection.FromRatedItems,
        min_recommendation_pool_size=1,
        max_recommended_item_count=3,
        return_ratings=True,
    )
```


## <a name="next-steps"></a>後續步驟

* [在設計工具中將模型定型](tutorial-designer-automobile-price-train-score.md)
* [使用 Azure Machine Learning SDK 部署模型](how-to-deploy-and-where.md)
* [針對失敗的部署進行疑難排解](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [建立用戶端應用程式以使用 web 服務](how-to-consume-web-service.md)
* [更新 Web 服務](how-to-deploy-update-web-service.md)
