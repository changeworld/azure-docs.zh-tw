---
title: '& 寫入實驗檔案的儲存位置'
titleSuffix: Azure Machine Learning
description: 瞭解儲存實驗輸入檔案的位置，以及寫入輸出檔的位置，以防止儲存體限制錯誤和實驗延遲。
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 03/10/2020
ms.openlocfilehash: df254e0766a755754aabcfb8c98a8c140b43cb20
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650843"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>在哪裡儲存和寫入 Azure Machine Learning 實驗的檔案
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解儲存輸入檔案的位置，以及從您的實驗寫入輸出檔案的位置，以防止儲存空間限制錯誤和實驗延遲。

在 [計算目標](concept-compute-target.md)上啟動定型回合時，會與外部環境隔離。 此設計的目的是要確保實驗的重現性和可攜性。 如果您在相同或其他計算目標上執行相同的腳本兩次，則會收到相同的結果。 透過此設計，您可以將計算目標視為無狀態的計算資源，每個資源都與完成後執行的作業沒有相似性。

## <a name="where-to-save-input-files"></a>輸入檔案的儲存位置

在您可以在計算目標或本機電腦上起始實驗之前，您必須確定該計算目標可使用必要的檔案，例如您的程式碼需要執行的相依性檔案和資料檔案。

Azure Machine Learning 藉由複製整個來原始目錄來執行定型腳本。 如果您有不想要上傳的機密資料，請使用 [. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) 檔案，或不要將它包含在來原始目錄中。 相反地， [使用資料存放](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true)區存取您的資料。

而實驗快照集的儲存空間限制為 300 MB 及 (或) 2000 個檔案。

基於這個理由，我們建議您：

* **將您的檔案儲存在 Azure Machine Learning [資料](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true)存放區中。** 這可防止實驗延遲問題，並具有從遠端計算目標存取資料的優點，這表示驗證和裝載是由 Azure Machine Learning 管理。 深入瞭解如何將資料存放區指定為來原始目錄，並將檔案上傳至您的資料存放區，以 [從您的資料存放區文章存取資料](how-to-access-data.md) 。

* **如果您只需要幾個資料檔案和相依性腳本，而且無法使用** 資料存放區，請將檔案放在與定型腳本相同的資料夾目錄中。 您可以 `source_directory` 直接在定型腳本中或在呼叫定型腳本的程式碼中指定這個資料夾。

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>實驗快照集的儲存體限制

針對實驗，Azure Machine Learning 會根據您在設定執行時建議的目錄，自動建立程式碼的實驗快照集。 這總共有 300 MB 和/或2000檔案的限制。 如果超出此限制，您將會看到下列錯誤：

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

若要解決此錯誤，請將實驗檔案儲存在資料存放區上。 如果您無法使用資料存放區，下表提供可能的替代方案。

實驗 &nbsp; 描述|儲存體限制解決方案
---|---
小於2000的檔案 & 無法使用資料存放區| 覆寫快照集大小限制 <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> 這可能需要幾分鐘的時間，視檔案的數目和大小而定。
必須使用特定的腳本目錄| [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
管線|針對每個步驟使用不同的子目錄
Jupyter 筆記本| 建立檔案 `.amlignore` 或將筆記本移至新的空白子目錄，然後重新執行您的程式碼。

## <a name="where-to-write-files"></a>寫入檔案的位置

由於訓練實驗的隔離，執行期間所發生之檔案的變更不一定會保存在您的環境之外。 如果您的腳本修改了本機的檔案來進行計算，則不會為下一個實驗回合保存變更，也不會自動將它們傳播回用戶端電腦。 因此，在第一次實驗執行期間所做的變更不會影響到第二個。

寫入變更時，建議您將檔案寫入 Azure Machine Learning 資料存放區。 請參閱 [從您的資料存放區存取資料](how-to-access-data.md)。

如果您不需要資料存放區，請將檔案寫入 `./outputs` 和/或 `./logs` 資料夾。

>[!Important]
> Azure Machine Learning 會接收兩個資料夾、 *輸出* 和 *記錄*檔的特殊處理方式。 在訓練期間，當您將檔案寫入 `./outputs` 和 `./logs` 資料夾時，檔案會自動上傳至您的執行歷程記錄，讓您可以在執行完成後存取這些檔案。

* **針對狀態訊息或計分結果等輸出，請** 將檔案寫入 `./outputs` 資料夾，以便在執行歷程記錄中保存為成品。 請留意寫入此資料夾的檔案數目和大小，因為當內容上傳到執行歷程記錄時可能會發生延遲。 如果有考慮延遲，建議將檔案寫入資料存放區。

* **若要在執行歷程記錄中將寫入的檔案儲存為記錄檔，請** 將檔案寫入 `./logs` 資料夾。 記錄會即時上傳，因此這個方法適用于從遠端執行串流處理即時更新。

## <a name="next-steps"></a>接下來的步驟

* 深入瞭解如何 [從您的資料存放區存取資料](how-to-access-data.md)。

* 深入瞭解如何使用 [PYTHON SDK](how-to-create-attach-compute-sdk.md) 或 [studio](how-to-create-attach-compute-studio.md)來建立計算目標。
