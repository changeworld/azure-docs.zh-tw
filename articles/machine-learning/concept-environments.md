---
title: 關於 Azure Machine Learning 環境
titleSuffix: Azure Machine Learning
description: 在本文中，您將瞭解機器學習環境的優點，其可讓您在各種計算目標上，提供可重現、可審核及可移植的機器學習相依性定義。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 07/08/2020
ms.openlocfilehash: 437db3e1ad367a75d6c8d3a77b138bce79b5f61e
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147126"
---
# <a name="what-are-azure-machine-learning-environments"></a>Azure Machine Learning 環境是什麼？
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning 環境會針對您的訓練和評分指令碼指定 Python 套件、環境變數和軟體設定。 它們也會指定執行時間 (Python、Spark 或 Docker) 。 這些環境是在您的 Machine Learning 工作區內管理和設定版本的實體，可讓您在各種計算目標上進行可重現、可審核和便攜的機器學習工作流程。

您可以使用 `Environment` 本機計算上的物件來執行下列動作：
* 開發您的訓練指令碼。
* 在 Azure Machine Learning Compute 中重複使用相同的環境，以進行大規模的模型訓練。
* 使用同一個環境部署您的模型。

下圖說明如何在您的回合設定 `Environment` 中，針對 web 服務部署使用單一物件，以進行定型和推斷和部署設定。

![機器學習服務工作流程中環境的圖表](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>環境的類型

環境大致上可以分成三個類別：*策劃*、*使用者管理*和*系統管理*。

策劃環境是由 Azure Machine Learning 提供，而且預設會在您的工作區中提供。 其中包含 Python 套件和設定的集合，可協助您開始使用各種機器學習架構。 

在使用者管理的環境中，您必須負責設定環境，並在計算目標上安裝定型腳本所需的每個套件。 Conda 不會檢查您的環境，也不會為您安裝任何專案。 如果您要定義自己的環境，您必須以 pip 相依性的形式列出 `azureml-defaults` `>= 1.0.45` 。 此套件包含將模型裝載為 web 服務所需的功能。

當您想要讓[Conda](https://conda.io/docs/)為您管理 Python 環境和腳本相依性時，可以使用系統管理的環境。 服務預設會假設這種類型的環境，因為它在不是手動設定的遠端計算目標上有其效用。

## <a name="create-and-manage-environments"></a>建立和管理環境

您可以透過下列方式建立環境：

* 藉 `Environment` 由使用策劃環境或定義您自己的相依性，來定義新的物件。
* `Environment`從您的工作區使用現有的物件。 這種方法可讓您使用相依性進行一致性和重現性。
* 從現有的 Anaconda 環境定義匯入。
* 使用 Azure Machine Learning CLI
* [使用 VS Code 延伸模組](how-to-manage-resources-vscode.md#create-environment)

如需特定程式碼範例，請參閱[重複使用環境以進行定型和部署](how-to-use-environments.md#create-an-environment)的「建立環境」一節。 環境也可以透過您的工作區輕鬆管理。 其中包括下列功能：

* 當您提交實驗時，環境會自動註冊到您的工作區。 也可以手動註冊。
* 您可以從工作區提取環境，以用於定型或部署，或對環境定義進行編輯。
* 有了版本設定，您就可以查看您的環境在一段時間內的變更，以確保重現性。
* 您可以從您的環境自動建立 Docker 映射。

如需程式碼範例，請參閱[重複使用環境以進行定型和部署](how-to-use-environments.md#manage-environments)的「管理環境」一節。

## <a name="environment-building-caching-and-reuse"></a>環境建立、快取和重複使用

Azure Machine Learning 服務會將環境定義建立到 Docker 映射和 conda 環境中。 它也會快取環境，以便在後續的訓練回合和服務端點部署中重複使用它們。

### <a name="building-environments-as-docker-images"></a>建立環境作為 Docker 映射

一般而言，當您第一次使用環境提交執行時，Azure Machine Learning 服務會在與工作區相關聯的 Azure Container Registry (ACR) 上叫用[Acr 組建](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)工作。 然後，會在工作區 ACR 上快取建立的 Docker 映射。 開始執行時，計算目標會抓取映射。

映射組建是由兩個步驟所組成：

 1. 下載基底映射，並執行任何 Docker 步驟
 2. 根據環境定義中所指定的 conda 相依性來建立 conda 環境。

如果您指定[使用者管理](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py)的相依性，則會省略第二個步驟。 在此情況下，您必須負責安裝任何 Python 套件，方法是將它們包含在基底映射中，或在第一個步驟中指定自訂的 Docker 步驟。 您也要負責指定 Python 可執行檔的正確位置。

### <a name="image-caching-and-reuse"></a>映射快取和重複使用

如果您針對另一個回合使用相同的環境定義，則 Azure Machine Learning 服務會從工作空間 ACR 重複使用快取的映射。 

若要查看快取影像的詳細資料，請使用[環境. get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-)方法。

為了判斷是否要重複使用快取的影像或建立新的映射，服務會從環境定義計算[出雜湊值](https://en.wikipedia.org/wiki/Hash_table)，並將其與現有環境的雜湊相比較。 雜湊依據：
 
 * 基底映射屬性值
 * 自訂 docker 步驟屬性值
 * Conda 定義中的 Python 套件清單
 * Spark 定義中的套件清單 

雜湊不會相依于環境名稱或版本。 環境定義變更（例如新增或移除 Python 套件或變更封裝版本）會導致雜湊值變更並觸發映射重建。 不過，如果您只是重新命名您的環境，或使用現有的屬性和封裝來建立新的環境，則雜湊值會維持不變，而且會使用快取的影像。

請參閱下圖，其中顯示三個環境定義。 其中兩個有不同的名稱和版本，但基底映射和 Python 套件相同。 它們具有相同的雜湊，因此會對應到相同的快取映射。 第三個環境有不同的 Python 套件和版本，因此對應至不同的快取映射。

![做為 Docker 映射的環境快取圖表](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> 例如，如果您建立的環境具有取消固定的封裝相依性，則 ```numpy``` 該環境會繼續使用在_環境建立時_所安裝的套件版本。 此外，任何未來具有相符定義的環境都會繼續使用舊版本。 

若要更新封裝，請指定用來強制映射重建的版本號碼，例如 ```numpy==1.18.1``` 。 請注意，將會安裝新的相依性，包括內嵌的相依性，這可能會破壞先前運作的案例。

> [!WARNING]
>  在[環境中，build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-)方法會重建快取的映射，而且可能會產生更新已取消釘選封裝的副作用，並中斷與該快取影像對應之所有環境定義的重現性。

## <a name="next-steps"></a>後續步驟

* 瞭解如何在 Azure Machine Learning 中[建立和使用環境](how-to-use-environments.md)。
* 請參閱[環境類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)的 Python SDK 參考檔。
* 請參閱[環境](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)的 R SDK 參考檔。
