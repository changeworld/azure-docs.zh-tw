---
title: 在自動化機器學習實驗中設定交叉驗證和資料分割
titleSuffix: Azure Machine Learning
description: 瞭解如何設定自動化機器學習實驗的交叉驗證和資料集分割
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 06/16/2020
ms.openlocfilehash: 3e2711243d7c093d3ab8aa5f0e7ebac0a5ec95f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90886187"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>在自動化機器學習中設定資料分割和交叉驗證

在本文中，您將瞭解針對自動化機器學習、AutoML、實驗設定定型/驗證資料分割和交叉驗證的不同選項。

在 Azure Machine Learning 中，當您使用 AutoML 來建立多個 ML 模型時，每個子系執行都必須藉由計算該模型的品質計量（例如精確度或 AUC 加權）來驗證相關的模型。 這些計量的計算方式是比較每個模型所做的預測與驗證資料中過去觀察的真實標籤。 

AutoML 實驗會自動執行模型驗證。 下列各節說明如何使用 [Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true)來進一步自訂驗證設定。 

如需低程式碼或無程式碼的體驗，請參閱 [在 Azure Machine Learning studio 中建立自動化機器學習實驗](how-to-use-automated-ml-for-ml-models.md)。 

> [!NOTE]
> Studio 目前支援定型/驗證資料分割和交叉驗證選項，但不支援針對您的驗證集指定個別的資料檔案。 

## <a name="prerequisites"></a>必要條件

針對本文，您需要

* Azure Machine Learning 工作區。 若要建立工作區，請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

* 熟悉如何使用 Azure Machine Learning SDK 來設定自動化機器學習實驗。 遵循 [教學](tutorial-auto-train-models.md) 課程或操作 [說明](how-to-configure-auto-train.md) ，查看基本的自動化機器學習實驗設計模式。

* 瞭解交叉驗證和定型/驗證資料分割作為 ML 概念。 如需概要說明，

    * [關於 Machine Learning 中的訓練、驗證和測試集](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [瞭解交叉驗證](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd)

## <a name="default--data-splits-and-cross-validation"></a>預設資料分割和交叉驗證

使用 [AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py&preserve-view=true) 物件來定義您的實驗和定型設定。 在下列程式碼片段中，請注意，只會定義必要的參數，也就是 `n_cross_validation` `validation_ data` **不** 包含或的參數。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

如果您未明確指定 `validation_data` 或 `n_cross_validation` 參數，AutoML 會根據提供的單一資料集中的資料列數目，套用預設的技術 `training_data` ：

|定型 &nbsp; 資料 &nbsp; 大小| 驗證技術 |
|---|-----|
|**大於 &nbsp; 20000 的資料 &nbsp; &nbsp; 列**| 套用定型/驗證資料分割。 預設值是以10% 的初始訓練資料集做為驗證集。 接著，該驗證集會用於計算度量。
|**小於 &nbsp; 20000 的資料 &nbsp; &nbsp; 列**| 套用交叉驗證方法。 預設的折迭數目取決於資料列數。 <br> **如果資料集小於1000個數據列**，則會使用10個折迭。 <br> **如果資料列介於1000到20000之間**，則會使用三個折迭。

## <a name="provide-validation-data"></a>提供驗證資料

在此情況下，您可以從單一資料檔開始，然後將它分割成定型集和驗證集，也可以為驗證集提供個別的資料檔案。 無論使用哪種方式， `validation_data` 物件中的參數都會 `AutoMLConfig` 指派要作為驗證集的資料。 此參數只接受 [Azure Machine Learning 資料集](how-to-create-register-datasets.md) 或 pandas 資料框架格式的資料集。   

下列程式碼範例會明確定義中所提供資料的哪個部分用於 `dataset` 定型和驗證。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>提供驗證集大小

在此情況下，只會為實驗提供單一資料集。 也就是說， `validation_data` **未** 指定參數，並將提供的資料集指派給  `training_data` 參數。  在您的 `AutoMLConfig` 物件中，您可以設定 `validation_size` 參數來保存定型資料的一部分以進行驗證。 這表示驗證集將依 AutoML 從提供的初始中分割 `training_data` 。 此值應介於0.0 和1.0 （非內含）之間 (例如，0.2 表示會保留20% 的資料以供驗證資料) 。

請參閱下列程式碼範例：

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="set-the-number-of-cross-validations"></a>設定交叉驗證的數目

若要執行交叉驗證，請包含 `n_cross_validations` 參數，並將其設定為值。 此參數會根據相同的折迭數目來設定要執行的交叉驗證數目。

在下列程式碼中，會定義交叉驗證的五個折迭。 因此，五種不同的訓練、每次使用4/5 的資料進行定型，以及每次使用1/5 的資料搭配不同的每次驗證都有不同的維持折迭。

因此，系統會以5個驗證度量的平均值計算計量。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>指定自訂交叉驗證資料折迭

您也可以提供自己的交叉驗證 (CV) 資料折迭。 這會被視為更先進的案例，因為您要指定要分割和用於驗證的資料行。  在定型資料中包含自訂的 CV 分割資料行，並藉由填入參數中的資料行名稱來指定資料行 `cv_split_column_names` 。 每個資料行都代表一個交叉驗證分割，並以整數值1或0填滿，其中1表示應該用於定型的資料列，而0表示資料列應用於驗證。

下列程式碼片段包含具有兩個 CV 分割資料行 ' cv1 ' 和 ' cv2 ' 的銀行行銷資料。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> 若要搭配使用 `cv_split_column_names` 與 `training_data` `label_column_name` ，請升級您的 Azure Machine Learning Python SDK 1.6.0 版或更新版本。 針對先前的 SDK 版本，請參閱使用 `cv_splits_indices` ，但請注意，它只會搭配 `X` 和 `y` 資料集輸入使用。 

## <a name="next-steps"></a>後續步驟

* [防止不平衡資料和過度學習](concept-manage-ml-pitfalls.md)。
* [教學課程：使用自動化機器學習來預測出租車車資分割資料區段](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets)。
* 如何 [自動定型時間序列預測模型](how-to-auto-train-forecast.md)。
