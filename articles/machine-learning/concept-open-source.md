---
title: 開放原始碼機器學習整合
titleSuffix: Azure Machine Learning
description: 瞭解如何使用開放原始碼 Python 機器學習架構，在 Azure Machine Learning 中訓練、部署及管理端對端機器學習解決方案。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 12/16/2020
ms.openlocfilehash: b3fa9767310bfc300087c9dc7384f33993aca70d
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97675838"
---
# <a name="open-source-integration-with-azure-machine-learning-projects"></a>與 Azure Machine Learning 專案的開放原始碼整合

您可以使用開放原始碼 Python 機器學習程式庫和平臺，在 Azure Machine Learning 中訓練、部署及管理端對端機器學習程式。  使用開發工具（例如 Jupyter 筆記本和 Visual Studio Code）來利用 Azure Machine Learning 中現有的模型和腳本。  

在本文中，深入瞭解這些開放原始碼程式庫和平臺。

## <a name="train-open-source-machine-learning-models"></a>訓練開放原始碼機器學習模型

機器學習定型程式牽涉到將演算法應用到您的資料，以達成工作或解決問題。 視問題而定，您可以選擇最適合工作和資料的不同演算法。 如需機器學習服務之不同分支的詳細資訊，請參閱 [深度學習與機器學習服務文章](./concept-deep-learning-vs-machine-learning.md) ，以及 [機器學習服務演算法](algorithm-cheat-sheet.md)的功能。

### <a name="preserve-data-privacy-using-differential-privacy"></a>使用差異隱私權保留資料隱私權

若要定型機器學習模型，您需要資料。 有時候資料是敏感的，請務必確定資料是安全且私用的。 差異隱私權是在資料集中保留資訊機密性的技巧。 若要深入瞭解，請參閱 [保留資料隱私權](concept-differential-privacy.md)的文章。 

開放原始碼差異隱私權工具組（例如 [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core-python) ）可協助您保存 Azure Machine Learning 解決方案中的 [資料隱私權](how-to-differential-privacy.md) 。

### <a name="classical-machine-learning-scikit-learn"></a>傳統機器學習： scikit-learn-學習

針對牽涉到傳統機器學習演算法工作（例如分類、叢集和回歸）的訓練工作，您可能會使用類似 Scikit-learn 的內容。 若要瞭解如何訓練花卉分類模型，請參閱 [如何使用 scikit-learn 學習訓練文章](how-to-train-scikit-learn.md)。

### <a name="neural-networks-pytorch-tensorflow-keras"></a>類神經網路： PyTorch、TensorFlow、Keras

開放原始碼機器學習演算法稱為類神經網路（機器學習的子集），適用于在 Azure Machine Learning 中定型深度學習模型。

開放原始碼深度學習架構和操作指南包括：

 *  [PyTorch](https://github.com/pytorch/pytorch)： [在 PyTorch 中使用傳輸學習來定型深度學習影像分類模型](how-to-train-pytorch.md) 
 *  [TensorFlow](https://github.com/tensorflow/tensorflow)： [使用 TensorFlow 辨識手寫數位](how-to-train-tensorflow.md)
 *  [Keras](https://github.com/keras-team/keras)： [使用 Keras 建立類神經網路以分析影像](how-to-train-keras.md)

從頭開始定型深度學習模型，通常需要大量的時間、資料及計算資源。 您可以使用「轉移學習」來使用定型程式的快捷方式。 轉移學習是一種技術，可從解決一個問題到不同但相關的問題，來運用所獲得的知識。 這表示您可以採用現有的模型來重新調整它的用途。 若要深入瞭解轉移學習，請參閱 [深度學習文章](concept-deep-learning-vs-machine-learning.md#transfer-learning) 。

### <a name="reinforcement-learning-ray-rllib"></a>增強式學習：光線 RLLib

增強式學習是一種人工智慧技術，使用動作、狀態和獎勵來定型模型：增強式 learning 代理商學習採用一組預先定義的動作，根據環境的目前狀態將指定的獎勵最大化。 

[光線 RLLib](https://github.com/ray-project/ray)專案具有設定的功能，可讓您在整個訓練過程中獲得高擴充性。 反復進行的程式會耗費時間和資源，因為增強式 learning 代理程式會嘗試學習最佳的工作方式。  光線 RLLib 本身也支援深度學習架構，例如 TensorFlow 和 PyTorch。  

若要瞭解如何使用光線 RLLib 搭配 Azure Machine Learning，請參閱 [如何定型增強式學習模型](how-to-use-reinforcement-learning.md)。

### <a name="monitor-model-performance-tensorboard"></a>監視模型效能： TensorBoard

將單一或多個模型定型需要視覺效果和檢查所需的度量，以確保模型如預期般執行。 您可以 [在 Azure Machine Learning 中使用 TensorBoard 來追蹤和視覺化實驗計量](./how-to-monitor-tensorboard.md)

### <a name="frameworks-for-interpretable-and-fair-models"></a>可解譯和公平模型的架構

機器學習系統適用于社會的不同區域，例如銀行、教育和醫療保健。 因此，這些系統必須負責進行預測和建議，以防止非預期的結果。

[InterpretML](https://github.com/interpretml/interpret/)和 Fairlearn 等開放原始碼架構 (https://github.com/fairlearn/fairlearn) 與 Azure Machine Learning 合作，以建立更透明和合理的機器學習模型。

如需如何建立公平和可解譯模型的詳細資訊，請參閱下列文章：

- [Azure Machine Learning 中的模型可解釋性](how-to-machine-learning-interpretability.md)
- [解讀和說明機器學習模型](how-to-machine-learning-interpretability-aml.md)
- [說明 AutoML 模型](how-to-machine-learning-interpretability-automl.md)
- [減輕機器學習模型的公平](concept-fairness-ml.md)
- [使用 Azure Machine Learning 資產模型公平](how-to-machine-learning-fairness-aml.md)

## <a name="model-deployment"></a>模型部署

一旦模型已定型且準備好用於生產環境，您就必須選擇部署模型的方式。 Azure Machine Learning 提供各種部署目標。 如需詳細資訊，請參閱 [部署文章的位置和方式](./how-to-deploy-and-where.md)。

### <a name="standardize-model-formats-with-onnx"></a>使用 ONNX 標準化模型格式

定型之後，模型的內容（例如學習的參數）會序列化並儲存至檔案。 每個架構都有自己的序列化格式。 使用不同的架構和工具時，表示您必須根據架構的需求部署模型。 若要標準化此程式，您可以使用 Open Neural Network Exchange (ONNX) 格式。 ONNX 是人工智慧模型的開放原始碼格式。 ONNX 支援架構間的互通性。 這表示您可以在許多熱門的機器學習架構（例如 PyTorch）中訓練模型、將它轉換成 ONNX 格式，然後在不同的架構（例如 ML.NET）中使用 ONNX 模型。

如需有關 ONNX 以及如何使用 ONNX 模型的詳細資訊，請參閱下列文章：

- [使用 ONNX 建立及加速 ML 模型](concept-onnx.md)
- [在 .NET 應用程式中使用 ONNX 模型](how-to-use-automl-onnx-model-dotnet.md)

### <a name="package-and-deploy-models-as-containers"></a>封裝模型並將其部署為容器

Docker 之類的容器技術是將模型部署為 web 服務的一種方式。 容器提供平臺和資源中立的方式來建立和協調可重現的軟體環境。 有了這些核心技術，您就可以使用預先設定的環境、預先設定的[容器映射](./how-to-deploy-custom-docker-image.md)或自訂的[環境](./how-to-use-environments.md)，將機器學習模型部署到[Kubernetes](./how-to-deploy-azure-kubernetes-service.md?tabs=python)叢集。 針對需要 GPU 的工作流程，您可以使用 NVIDIA Triton 推斷伺服器之類的工具，以 [使用 gpu 進行預測](how-to-deploy-with-triton.md?tabs=python)。

### <a name="secure-deployments-with-homomorphic-encryption"></a>使用同態加密保護部署

在部署過程中，保護部署的安全是很重要的一部分。 若要 [部署加密的推斷服務](how-to-homomorphic-encryption-seal.md)，請使用 `encrypted-inference` 開放原始碼 Python 程式庫。 `encrypted inferencing`套件會根據[Microsoft 密封](https://github.com/Microsoft/SEAL)（同態加密程式庫）提供系結。

## <a name="machine-learning-operations-mlops"></a>Machine Learning 作業 (MLOps) 

Machine Learning 作業 (MLOps) ，通常視為機器學習的 DevOps，可讓您建立更透明、更具彈性且可重現的機器學習工作流程。 若要深入瞭解 MLOps，請參閱 [MLOps 文章](./concept-model-management-and-deployment.md) 。 

您可以使用 DevOps 做法（例如持續整合 (CI) 和持續部署 (CD) ）將端對端機器學習生命週期自動化，並在其周圍捕捉治理資料。 您可以 [在 GitHub 動作中定義機器學習 CI/CD 管線](./how-to-github-actions-machine-learning.md) ，以執行 Azure Machine Learning 訓練和部署工作。 

捕捉軟體相依性、計量、中繼資料、資料和模型版本控制是 MLOps 流程中很重要的一部分，以建立透明、可重現且可供審核的管線。 針對這項工作，您可以 [在 Azure Machine Learning 中使用 MLFlow](how-to-use-mlflow.md) ，也可以在 [Azure Databricks 中訓練機器學習模型](./how-to-use-mlflow-azure-databricks.md)時使用。
