---
title: 在何處保存&編寫實驗檔
titleSuffix: Azure Machine Learning
description: 瞭解在何處保存實驗輸入檔，以及編寫輸出檔案的位置，以防止存儲限制錯誤和實驗延遲。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79078416"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>在何處保存和寫入用於 Azure 機器學習實驗的檔
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解在何處保存輸入檔，以及從實驗中寫入輸出檔案的位置，以防止存儲限制錯誤和實驗延遲。

在[計算目標](how-to-set-up-training-targets.md)上啟動訓練時，它們與外部環境隔離。 此設計的目的是確保實驗的可重複性和可攜性。 如果在同一或另一個計算目標上運行同一腳本兩次，則收到相同的結果。 使用此設計，您可以將計算目標視為無狀態計算資源，每個計算目標與完成後運行的作業沒有相關性。

## <a name="where-to-save-input-files"></a>保存輸入檔的位置

在計算目標或本地電腦上啟動實驗之前，必須確保該計算目標可以使用必要的檔，例如代碼需要運行的依賴項檔和資料檔案。

Azure 機器學習通過將整個腳本資料夾複製到目標計算上下文來運行訓練腳本，然後拍攝快照。 而實驗快照集的儲存空間限制為 300 MB 及 (或) 2000 個檔案。

因此，我們建議：

* **將檔存儲在 Azure 機器學習[資料存儲](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)中。** 這可以防止實驗延遲問題，並且具有從遠端計算目標訪問資料的優勢，這意味著身份驗證和安裝由 Azure 機器學習管理。 詳細瞭解如何將資料存儲指定為原始目錄，以及將資料存儲中的["訪問資料"](how-to-access-data.md)文章中的檔上載到資料存儲。

* **如果只需要幾個資料檔案和依賴項腳本，並且無法使用資料存儲，請**將檔與訓練腳本放在同一資料夾目錄中。 直接在訓練腳本或`source_directory`調用訓練腳本的代碼中指定此資料夾作為您的資料夾。

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>實驗快照的存儲限制

對於實驗，Azure 機器學習會根據配置運行時建議的目錄自動創建代碼的實驗快照。 這總限制為 300 MB 和/或 2000 個檔。 如果超過此限制，您將看到以下錯誤：

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

要解決此錯誤，請將實驗檔存儲在資料存儲中。 如果您無法使用資料存儲，下表提供了可能的備用解決方案。

實驗&nbsp;描述|存儲限制解決方案
---|---
&無法使用資料存儲的檔少於 2000 個| 使用 <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> 這可能需要幾分鐘時間，具體取決於檔的數量和大小。
必須使用特定的腳本目錄| 製作一`.amlignore`個檔，從實驗快照中排除不屬於原始程式碼的檔。 將檔案名添加到檔中，`.amlignore`並將其放在與訓練腳本相同的目錄中。 該檔`.amlignore`使用與`.gitignore`檔相同的[語法和模式](https://git-scm.com/docs/gitignore)。
管線|為每個步驟使用不同的子目錄
Jupyter Notebook| 創建`.amlignore`檔或將筆記本移動到新的空子目錄中，然後再次運行代碼。

## <a name="where-to-write-files"></a>在哪裡寫入檔

由於訓練實驗的隔離，運行期間發生的檔的更改不一定會保留在環境之外。 如果腳本修改要計算的檔本地，則更改不會保留用於下一次實驗運行，並且不會自動傳播回用戶端電腦。 因此，在第一次實驗運行期間所做的更改不會也不應影響第二個實驗中的更改。

編寫更改時，我們建議將檔寫入 Azure 機器學習資料存儲。 請參閱[從資料存儲訪問資料](how-to-access-data.md)。

如果不需要資料存儲，請將檔寫入`./outputs`和/或`./logs`資料夾。

>[!Important]
> 兩個資料夾（*輸出*和*日誌*）接受 Azure 機器學習的特殊處理。 在培訓期間，當您將檔和`./outputs``./logs`資料夾寫入時，這些檔將自動上載到執行歷程記錄，以便在運行完成後可以訪問這些檔。

* **對於狀態訊息或評分結果等輸出，** 將檔寫入`./outputs`資料夾，以便它們作為專案保留在執行歷程記錄中。 請注意寫入此資料夾的檔的數量和大小，因為當內容上載到執行歷程記錄時，可能會出現延遲。 如果延遲是一個問題，建議將檔寫入資料存儲。

* **要將書面檔另存為執行歷程記錄中的日誌，請**將檔寫入`./logs`資料夾。 日誌是即時上載的，因此此方法適用于從遠端運行資料流即時更新。

## <a name="next-steps"></a>後續步驟

* 詳細瞭解[如何從資料存儲訪問資料](how-to-access-data.md)。

* 詳細瞭解[如何設置培訓目標](how-to-set-up-training-targets.md)。
