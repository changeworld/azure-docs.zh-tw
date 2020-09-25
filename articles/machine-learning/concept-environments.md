---
title: 關於 Azure Machine Learning 環境
titleSuffix: Azure Machine Learning
description: 在本文中，您將瞭解機器學習環境的優點，以在各種計算目標上啟用可重現、可供審核且可攜的機器學習相依性定義。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: BlackMist
ms.date: 07/08/2020
ms.openlocfilehash: e1b92563acd6983b1680cacc06a8f2d0789dddf1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91302497"
---
# <a name="what-are-azure-machine-learning-environments"></a>什麼是 Azure Machine Learning 環境？


Azure Machine Learning 環境是您機器學習訓練發生所在環境的封裝。 他們會指定您訓練和評分腳本周圍的 Python 套件、環境變數和軟體設定。 它們也會指定執行時間 (Python、Spark 或 Docker) 。 環境是您 Machine Learning 工作區中的受控和版本化實體，可在各種計算目標上啟用可重現、可供審核及可移植的機器學習工作流程。

您可以使用 `Environment` 本機計算上的物件來：
* 開發您的訓練指令碼。
* 在 Azure Machine Learning Compute 中重複使用相同的環境，以進行大規模的模型訓練。
* 使用相同的環境部署您的模型。
* 重新審視現有模型已定型的環境。

下圖說明如何在您的回合設定 `Environment` (中使用單一物件，以進行定型) ，以及) 進行 web 服務部署的推斷和部署設定 (。

![機器學習工作流程中的環境圖表](./media/concept-environments/ml-environment.png)

環境、計算目標和定型腳本一起形成回合設定：定型回合的完整規格。

## <a name="types-of-environments"></a>環境的類型

環境可廣泛分為三種類別： *策劃*、 *使用者管理*和 *系統管理*。

策劃環境是由 Azure Machine Learning 提供，且預設會在您的工作區中提供。 其目的是要依原樣使用，其中包含 Python 套件和設定的集合，可協助您開始使用各種機器學習架構。 這些預先建立的環境也允許更快的部署時間。 如需完整清單，請參閱 [策劃環境文章](resource-curated-environments.md)。

在使用者管理的環境中，您必須負責設定您的環境，並在計算目標上安裝定型腳本所需的每個套件。 Conda 不會檢查您的環境，也不會為您安裝任何程式。 如果您要定義自己的環境，您必須 `azureml-defaults` 以 pip 相依性的 `>= 1.0.45` 形式列出。 此套件包含將模型裝載為 web 服務所需的功能。

當您想要讓 [Conda](https://conda.io/docs/) 管理 Python 環境和腳本相依性時，您可以使用系統管理的環境。 新的 conda 環境建基於 conda 相依性物件。 根據預設，Azure Machine Learning 服務會假設這種類型的環境，因為它對不是以手動方式設定的遠端計算目標是有用的。

## <a name="create-and-manage-environments"></a>建立及管理環境

您可以透過下列方式建立環境：

* 藉 `Environment` 由使用策劃環境或定義您自己的相依性，來定義新的物件。
* 使用 `Environment` 您工作區中的現有物件。 這種方法可讓您以一致性和重現性與您的相依性。
* 從現有的 Anaconda 環境定義匯入。
* 使用 Azure Machine Learning CLI
* [使用 VS Code 擴充功能](how-to-manage-resources-vscode.md#create-environment)

如需特定的程式碼範例，請參閱 [如何使用環境](how-to-use-environments.md#create-an-environment)的「建立環境」一節。 環境也可以透過您的工作區輕鬆管理。 它們包含下列功能：

* 當您提交實驗時，環境會自動註冊到您的工作區。 也可以手動註冊。
* 您可以從工作區提取環境以用於定型或部署，或對環境定義進行編輯。
* 使用版本控制時，您可以看到您的環境在一段時間內的變更，以確保重現性。
* 您可以從環境自動建立 Docker 映射。

如需程式碼範例，請參閱 [如何使用環境](how-to-use-environments.md#manage-environments)的「管理環境」一節。

## <a name="environment-building-caching-and-reuse"></a>環境建立、快取和重複使用

Azure Machine Learning 服務會在 Docker 映射和 conda 環境中建立環境定義。 它也會快取環境，以便在後續的定型回合和服務端點部署中重複使用這些環境。 從遠端執行定型腳本需要建立 Docker 映射，而本機執行可以直接使用 Conda 環境。 

### <a name="submitting-a-run-using-an-environment"></a>使用環境提交執行

當您第一次使用環境提交遠端執行時，Azure Machine Learning 服務會在與工作區相關聯的 Azure Container Registry (ACR) 上叫用 [Acr 組建](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) 工作。 然後，會在工作空間 ACR 上快取內建的 Docker 映射。 策劃環境是由全域 ACR 中快取的 Docker 映射所支援。 開始執行時，會從相關 ACR 的計算目標抓取影像。

針對本機執行，會根據環境定義建立 Docker 或 Conda 環境。 然後，腳本會在目標計算上執行-本機執行時間環境或本機 Docker 引擎。

### <a name="building-environments-as-docker-images"></a>建立環境作為 Docker 映射

如果工作區 ACR 中還沒有環境定義存在，則會建立新的映射。 映射組建是由兩個步驟所組成：

 1. 下載基礎映射，並執行任何 Docker 步驟
 2. 根據環境定義中指定的 conda 相依性來建立 conda 環境。

如果您指定 [使用者管理](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py&preserve-view=true)的相依性，則會省略第二個步驟。 在此情況下，您必須負責安裝任何 Python 套件，方法是將它們包含在您的基底映射中，或在第一個步驟中指定自訂的 Docker 步驟。 您也要負責指定 Python 可執行檔的正確位置。 您也可以使用 [自訂的 Docker 基底映射](how-to-deploy-custom-docker-image.md)。

### <a name="image-caching-and-reuse"></a>映射快取和重複使用

如果您針對另一次執行使用相同的環境定義，Azure Machine Learning 服務會從工作區 ACR 重複使用快取的影像。 

若要查看快取影像的詳細資料，請使用 [Environment.get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-image-details-workspace-) 方法。

為了判斷要重複使用快取的映射或建立新的映射，此服務會從環境定義中計算 [雜湊值](https://en.wikipedia.org/wiki/Hash_table) ，並將其與現有環境的雜湊進行比對。 雜湊的依據：
 
 * 基底映射屬性值
 * 自訂 docker 步驟屬性值
 * Conda 定義中的 Python 套件清單
 * Spark 定義中的套件清單 

雜湊不依賴環境名稱或版本-如果您重新命名您的環境，或使用現有的屬性和套件來建立新環境，則雜湊值會維持不變。 不過，環境定義變更（例如新增或移除 Python 套件或變更套件版本）會導致雜湊值變更。 變更環境中相依性或通道的順序將會產生新的環境，因此需要新的映射組建。 請務必注意，對策劃環境所做的任何變更都會使雜湊失效，並產生新的「非策劃」環境。

計算的雜湊值會與工作區中的雜湊值和全域 ACR (或本機執行) 的計算目標進行比較。 如果有相符的結果，則會提取快取的影像，否則會觸發映射組建。 提取快取影像的持續時間包含下載時間，而提取新建立映射的持續時間會包含組建時間和下載時間。 

下圖顯示三個環境定義。 其中兩個有不同的名稱和版本，但基礎映射和 Python 套件相同。 但它們具有相同的雜湊，因此會對應到相同的快取影像。 第三個環境具有不同的 Python 套件和版本，因此會對應至不同的快取映射。

![環境快取作為 Docker 映射的圖表](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> 例如，如果您建立的環境具有取消固定的套件相依性，則 ```numpy``` 該環境會繼續使用在 _建立環境時_所安裝的套件版本。 此外，任何符合定義的未來環境都將繼續使用舊版本。 

若要更新封裝，請指定版本號碼來強制重建映射（例如） ```numpy==1.18.1``` 。 將會安裝新的相依性，包括嵌套的相依性，這可能會中斷先前運作的案例。 

> [!WARNING]
>  此 [環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#&preserve-view=truebuild-workspace--image-build-compute-none-) 會重建快取的映射，並有可能的副作用會更新取消固定的封裝，並中斷與該快取映射對應之所有環境定義的重現性。

## <a name="next-steps"></a>後續步驟

* 瞭解如何在 Azure Machine Learning 中 [建立和使用環境](how-to-use-environments.md) 。
* 請參閱 [環境類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true)的 Python SDK 參考檔。
* 請參閱 R SDK 參考檔中的 [環境](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)。
