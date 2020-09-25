---
title: 匯出或刪除工作區資料
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning studio、CLI、SDK 和已驗證的 REST Api 來匯出或刪除您的工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: e847f62c2ae3d1d68c39685a38b67e1d0ada8c2f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91251009"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>匯出或刪除您的 Machine Learning services 工作區資料



在 Azure Machine Learning 中，您可以使用入口網站的圖形化介面或 Python SDK 來匯出或刪除工作區資料。 本文說明這兩個選項。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>控制您的工作區資料

Azure Machine Learning 所儲存的產品內資料可供匯出及刪除。 您可以使用 Azure Machine Learning studio、CLI 和 SDK 來匯出和刪除。 透過 Azure 隱私權入口網站可以存取遙測資料。 

在 Azure Machine Learning 中，個人資料會包含在執行歷程記錄檔中的使用者資訊。 

## <a name="delete-high-level-resources-using-the-portal"></a>使用入口網站刪除高階資源

當您建立工作區時，Azure 會在資源群組內建立一些資源：

- 工作區本身
- 儲存體帳戶
- 容器登錄
- Application Insights 實例
- 金鑰保存庫

您可以從清單中選取這些資源，然後選擇 [**刪除**]，以刪除這些資源 

:::image type="content" source="media/how-to-export-delete-data/delete-resource-group-resources.png" alt-text="入口網站的螢幕擷取畫面，其中已醒目提示刪除圖示":::

執行歷程記錄檔（可能包含個人使用者資訊）儲存在 blob 儲存體的儲存體帳戶中，位於的子資料夾中 `/azureml` 。 您可以從入口網站下載及刪除資料。

:::image type="content" source="media/how-to-export-delete-data/storage-account-folders.png" alt-text="入口網站中儲存體帳戶內 azureml 目錄的螢幕擷取畫面":::

## <a name="export-and-delete-machine-learning-resources-using-azure-machine-learning-studio"></a>使用 Azure Machine Learning studio 來匯出和刪除機器學習資源

Azure Machine Learning studio 提供您機器學習資源的統一觀點，例如筆記本、資料集、模型和實驗。 Azure Machine Learning studio 強調保留資料和實驗的記錄。 您可以使用瀏覽器來刪除計算資源（例如管線和計算資源）。 針對這些資源，請流覽至有問題的資源，然後選擇 [ **刪除**]。 

您可以取消註冊資料集，並封存實驗，但是這些作業不會刪除資料。 若要完全移除資料，必須在儲存層級刪除資料集和執行資料。 如先前所述，您可以使用入口網站來刪除儲存層級的。

您可以使用 Studio 從實驗回合下載訓練成品。 選擇您感興趣的 **實驗** 和 **執行** 。 選擇 [ **輸出 + 記錄** ]，並流覽至您想要下載的特定構件。 選擇 **[...]** 並 **下載**。

您可以流覽至所需的 **模型** ，然後選擇 [ **下載**]，以下載已註冊的模型。 

:::image type="contents" source="media/how-to-export-delete-data/model-download.png" alt-text="醒目提示下載選項的 studio 模型頁面螢幕擷取畫面":::

## <a name="export-and-delete-resources-using-the-python-sdk"></a>使用 Python SDK 來匯出和刪除資源

您可以使用下列程式下載特定執行的輸出： 

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['my-experiment']
run = next(run for run in ex.get_runs() if run.id == run_id)
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

您可以使用 Python SDK 來刪除下列機器學習資源： 

| 類型 | 函式呼叫  | 注意 | 
| --- | --- | --- |
| `Workspace` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete-delete-dependent-resources-false--no-wait-false-) | 用 `delete-dependent-resources` 來串聯刪除 |
| `Model` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--) | | 
| `ComputeTarget` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--) | |
| `WebService` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true) | | 

