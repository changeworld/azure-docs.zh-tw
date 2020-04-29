---
title: 儲存 & 寫入實驗檔案的位置
titleSuffix: Azure Machine Learning
description: 瞭解儲存實驗輸入檔案的位置，以及要在何處寫入輸出檔，以防止儲存體限制錯誤和實驗延遲。
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 12a38b08fd429280f34b4eb02d4b72187b622261
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79078416"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>儲存和寫入 Azure Machine Learning 實驗檔案的位置
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您會瞭解儲存輸入檔的位置，以及從您的實驗寫入輸出檔案以避免儲存限制錯誤和實驗延遲的位置。

在[計算目標](how-to-set-up-training-targets.md)上啟動定型執行時，它們會與外部環境隔離。 這項設計的目的是要確保實驗的重現性和可攜性。 如果您執行相同的腳本兩次，在相同或另一個計算目標上，您會收到相同的結果。 透過這項設計，您可以將計算目標視為無狀態計算資源，每個都不會與完成後執行的作業有相似性。

## <a name="where-to-save-input-files"></a>儲存輸入檔的位置

您必須先確定該計算目標可以使用必要的檔案，例如您的程式碼需要執行的相依性檔案和資料檔案，才能在計算目標或本機電腦上起始實驗。

Azure Machine Learning 會藉由將整個腳本資料夾複製到目標計算內容來執行定型腳本，然後再建立快照集。 而實驗快照集的儲存空間限制為 300 MB 及 (或) 2000 個檔案。

基於這個理由，我們建議：

* **將您的檔案儲存在 Azure Machine Learning[資料](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)存放區中。** 這可避免實驗延遲問題，並具有從遠端計算目標存取資料的優點，這表示驗證和掛接是由 Azure Machine Learning 管理。 若要深入瞭解如何指定資料存放區作為來原始目錄，以及如何將檔案上傳到您的資料存放區，請參閱[從資料存放區存取資料](how-to-access-data.md)一文。

* **如果您只需要幾個資料檔案和相依性腳本，而且無法使用**資料存放區，請將檔案放在與定型腳本相同的資料夾目錄中。 在您的定型腳本`source_directory`中，或在呼叫定型腳本的程式碼中，直接指定此資料夾。

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>實驗快照集的儲存體限制

針對實驗，Azure Machine Learning 會根據您在設定執行時建議的目錄，自動建立程式碼的實驗快照集。 這是總限制為 300 MB 和/或2000個檔案。 如果超過此限制，您會看到下列錯誤：

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

若要解決此錯誤，請將實驗檔案儲存在資料存放區上。 如果您無法使用資料存放區，下表提供可能的替代方案。

實驗&nbsp;描述|儲存體限制解決方案
---|---
不到2000的檔案 & 無法使用資料存放區| 覆寫快照集大小限制 <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> 這可能需要幾分鐘的時間，視檔案的數量和大小而定。
必須使用特定的腳本目錄| 建立一個`.amlignore`檔案，以從不屬於原始程式碼的實驗快照集排除檔案。 將檔案名新增至`.amlignore`檔案，並將它放在與訓練腳本相同的目錄中。 `.amlignore`檔案會使用與檔案相同的[語法和模式](https://git-scm.com/docs/gitignore) `.gitignore` 。
管線|針對每個步驟使用不同的子目錄
Jupyter Notebook| `.amlignore`建立檔案或將您的筆記本移至新的空白子目錄，然後再次執行您的程式碼。

## <a name="where-to-write-files"></a>寫入檔案的位置

由於定型實驗的隔離，對執行期間所發生的檔案所做的變更不一定會在您的環境之外保存。 如果您的腳本修改了本機檔案，則不會保存變更以供下一次實驗執行，而且它們不會自動傳播回用戶端電腦。 因此，在第一次實驗執行期間所做的變更不會影響到第二個。

寫入變更時，建議您將檔案寫入 Azure Machine Learning 的資料存放區。 請參閱[從您的資料存放區存取資料](how-to-access-data.md)。

如果您不需要資料存放區，請將檔案`./outputs`寫入和/ `./logs`或資料夾。

>[!Important]
> 兩個資料夾、*輸出*和*記錄*，會 Azure Machine Learning 接收特殊處理。 在定型期間，當您將檔案`./outputs`寫入`./logs`和資料夾時，檔案會自動上傳到您的執行歷程記錄，讓您在執行完成之後可以存取這些檔案。

* 若**為輸出（例如狀態訊息或計分結果），請**將`./outputs`檔案寫入資料夾，以便在執行歷程記錄中保存為構件。 請留意寫入此資料夾的檔案數目和大小，因為當內容上傳到執行歷程記錄時，可能會發生延遲。 如果需要考慮延遲，建議您將檔案寫入資料存放區。

* **若要將寫入的檔案儲存為執行歷程記錄中的記錄，請**將檔案寫入`./logs`資料夾。 記錄會即時上傳，因此這個方法適用于從遠端執行串流處理即時更新。

## <a name="next-steps"></a>後續步驟

* 深入瞭解如何[從您的資料存放區存取資料](how-to-access-data.md)。

* 深入瞭解[如何設定定型目標](how-to-set-up-training-targets.md)。
