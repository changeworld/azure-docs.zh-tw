---
title: 關於 Azure 機器學習環境
titleSuffix: Azure Machine Learning
description: 在本文中，瞭解機器學習環境的優勢，這些環境支援跨各種計算目標的可重現、可審核和可移植的機器學習依賴定義。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 03/18/2020
ms.openlocfilehash: 50ddbffd00e0cbbd0641089613aaa40d03658c9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064199"
---
# <a name="what-are-azure-machine-learning-environments"></a>什麼是 Azure 機器學習環境？
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 機器學習環境指定有關定型腳本和評分腳本的 Python 包、環境變數和軟體設置。 它們還指定執行時間（Python、Spark 或 Docker）。 這些環境是機器學習工作區中的管理和版本化實體，可跨各種計算目標實現可重現、可審核和可移植的機器學習工作流。

您可以將本地計算上`Environment`的物件用於：
* 開發您的培訓腳本。
* 在 Azure 機器學習計算上重用相同的環境，以便大規模進行模型培訓。
* 使用相同的環境部署模型。

下圖說明瞭如何在回合組態、培訓以及推理和`Environment`部署配置中使用單個物件進行 Web 服務部署。

![機器學習工作流中環境圖](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>環境的類型

環境大致可以分為三類：*策劃*、*使用者管理和**系統管理*。

精選環境由 Azure 機器學習提供，預設情況下在工作區中可用。 它們包含 Python 包和設置的集合，可説明您開始使用各種機器學習框架。 

在使用者管理的環境中，您負責設置環境並安裝訓練腳本在計算目標上所需的每個包。 康達不會檢查您的環境或為您安裝任何內容。 如果要定義自己的環境，則必須將版本`azureml-defaults``>= 1.0.45`列為點依賴項。 此包包含將模型託管為 Web 服務所需的功能。

當您希望[Conda](https://conda.io/docs/)為您管理 Python 環境和腳本依賴項時，可以使用系統管理的環境。 預設情況下，該服務假定這種類型的環境，因為它對無法手動設定的遠端計算目標很有用。

## <a name="create-and-manage-environments"></a>創建和管理環境

您可以通過：

* 通過使用精心策劃的`Environment`環境或定義自己的依賴項來定義新物件。
* 使用工作區`Environment`中的現有物件。 此方法允許與您的依賴項保持一致和重現。
* 從現有的 Anaconda 環境定義導入。
* 使用 Azure 機器學習 CLI

有關特定代碼示例，請參閱[用於培訓和部署 的"重用環境](how-to-use-environments.md#create-an-environment)"部分。 環境也可通過工作區輕鬆管理。 它們包括以下功能：

* 提交實驗時，環境將自動註冊到工作區。 也可以手動註冊它們。
* 可以從工作區獲取環境以用於訓練或部署，或對環境定義進行編輯。
* 通過版本控制，您可以看到環境隨時間的變化，從而確保可重現性。
* 可以從環境中自動生成 Docker 映射。

有關代碼示例，請參閱[用於培訓和部署 的"重用環境](how-to-use-environments.md#manage-environments)"部分。

## <a name="environment-building-caching-and-reuse"></a>環境建設、緩存和重用

Azure 機器學習服務將環境定義構建到 Docker 映射和 conda 環境中。 它還緩存環境，以便在後續訓練運行和服務終結點部署中重用它們。

### <a name="building-environments-as-docker-images"></a>將環境構建為 Docker 映射

通常，當您首次使用環境提交運行時，Azure 機器學習服務將調用與工作區關聯的 Azure 容器註冊表 （ACR） 上的[ACR 生成任務](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)。 然後，生成的 Docker 映射將緩存在工作區 ACR 上。 在運行執行開始時，計算目標檢索圖像。

映射生成由兩個步驟組成：

 1. 下載基本映射，並執行任何 Docker 步驟
 2. 根據環境定義中指定的 conda 依賴關係構建 conda 環境。

如果指定[使用者管理的依賴項，](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py)則省略第二步。 在這種情況下，您負責安裝任何 Python 包，通過在基本映射中包括它們，或在第一步中指定自訂 Docker 步驟。 您還負責為 Python 可執行檔指定正確的位置。

### <a name="image-caching-and-reuse"></a>圖像緩存和重用

如果對另一次運行使用相同的環境定義，Azure 機器學習服務將重用工作區 ACR 中的緩存映射。 

要查看緩存映射的詳細資訊，請使用[環境.get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-)方法。

要確定是重用緩存映射還是生成新映射，服務將從環境定義中計算[雜湊值](https://en.wikipedia.org/wiki/Hash_table)，並將其與現有環境的雜湊值進行比較。 雜湊基於：
 
 * 基本圖像屬性值
 * 自訂 Docker 步驟屬性值
 * 康達定義中的 Python 包清單
 * Spark 定義中的包清單 

雜湊不依賴于環境名稱或版本。 環境定義更改（如添加或刪除 Python 包或更改包版本）會導致雜湊值更改並觸發映射重建。 但是，如果只是重命名環境或創建具有現有環境的確切屬性和包的新環境，則雜湊值保持不變，並使用緩存的圖像。

請參閱下圖，該圖顯示了三個環境定義。 其中兩個的名稱和版本不同，但基本映射和 Python 包相同。 它們具有相同的雜湊，因此對應于相同的緩存圖像。 第三個環境具有不同的 Python 包和版本，因此對應于不同的緩存映射。

![環境緩存圖作為 Docker 映射](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> 例如```numpy```，如果創建具有未固定包依賴項的環境，則該環境將繼續使用_創建環境時安裝的_包版本。 此外，任何具有匹配定義的將來環境都將繼續使用舊版本。 

要更新包，請指定一個版本號以強制映射重建，例如```numpy==1.18.1```。 請注意，將安裝新的依賴項（包括嵌套依賴項），這些依賴項可能會破壞以前的工作方案。

> [!WARNING]
>  [Environment.build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-)方法將重建緩存映射，並可能具有更新未固定包和破壞與該緩存映射對應的所有環境定義的重現性的副作用。

## <a name="next-steps"></a>後續步驟

* 瞭解如何在 Azure 機器學習中[創建和使用環境](how-to-use-environments.md)。
* 請參閱[環境類](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)的 Python SDK 參考文檔。
* 請參閱[環境](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)的 R SDK 參考文檔。
