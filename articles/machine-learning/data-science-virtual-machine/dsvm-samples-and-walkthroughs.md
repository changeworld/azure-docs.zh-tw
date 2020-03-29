---
title: '& ML 演練的樣本程式'
titleSuffix: Azure Data Science Virtual Machine
description: 通過這些示例和演練，瞭解如何使用資料科學虛擬機器處理常見任務和方案。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d064e940997d13e51be4fd38e9f678b778ce31f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900056"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Azure 資料科學虛擬機器上的示例

Azure 資料科學虛擬機器 （DSVM） 包括一組全面的示例代碼。 這些示例包括聚居器筆記本和 Python 和 R 等語言的腳本。
> [!NOTE]
> 有關如何在資料科學虛擬機器上運行 Jupyter 筆記本的詳細資訊，請參閱[訪問朱皮特](#access-jupyter)部分。

## <a name="prerequisites"></a>Prerequisites

為了運行這些示例，您必須預配資料科學虛擬機器。 請參閱[Windows](./provision-vm.md)和[Ubuntu](./dsvm-ubuntu-intro.md)的快速入門。

## <a name="available-samples"></a>可用的範例
| 範例類別 | 描述 | 位置 |
| ------------- | ------------- | ------------- |
| R 語言  | 示例說明了如何與基於 Azure 的雲資料存儲連接以及如何比較開源 R 和 Microsoft 機器學習伺服器等方案。 他們還解釋了如何在 Microsoft 機器學習伺服器和 SQL Server 上操作模型。 <br/> [R 語言](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python 語言  | 示例說明了如何與基於 Azure 的雲資料存儲連接以及如何使用 Azure 機器學習等方案。  <br/> [Python 語言](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia 語言  | 提供朱麗亞策劃和深度學習的詳細說明。 還解釋了如何從朱麗亞調用 C 和 Python。 <br/> [朱麗亞語](#julia-language) |<br/> Windows：<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux：<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | 演示如何使用機器學習構建機器學習和深度學習模型。 在任何位置部署模型。 使用自動化機器學習服務和智慧型超參數調整。 此外，也使用模型管理和分散式定型。 <br/> [機器學習](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch Notebook  | 使用基於 PyTorch 的神經網路的深入研究示例。 Notebook 範圍涵蓋初學者到進階案例。  <br/> [PyTorch 筆記本](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  使用 TensorFlow 框架實現的各種神經網路示例和技術。 <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft 辨識工具組 <br/>   | 由 Microsoft 的認知工具組團隊發佈的深度學習示例。  <br/> [認知工具組](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux：<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | 使用基於 Caffe2 的神經網路的深入研究示例。 幾個筆記本讓使用者熟悉了 Caffe2 以及如何有效地使用它。 示例包括圖像預處理和資料集創建。 它們還包括回歸以及如何使用預先訓練的模型。 <br/> [卡夫2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | 基於 Python 的樣本，用於實際問題場景使用 H2O。 <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML 語言  | 通過 pySpark 和 MMLSpark 使用 Apache Spark MLLib 工具組功能的示例：在 Apache Spark 2.x 上為 Apache Spark 進行微軟機器學習。  <br/> [SparkML 語言](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | XGBoost 中針對分類和回歸等方案的標準機器學習示例。 <br/> [XGBoost](#xgboost) | <br/>Windows：<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>存取 Jupyter 

要訪問朱皮特，請選擇桌面或應用程式功能表上的**Jupyter**圖示。 您還可以在 DSVM 的 Linux 版本上訪問 Jupyter。 要從 Web 瀏覽器遠端存取，請訪問`https://<Full Domain Name or IP Address of the DSVM>:8000`Ubuntu。

要添加異常並在瀏覽器上提供 Jupyter 訪問，請使用以下指南：


![啟用 Jupyter 例外](./media/ubuntu-jupyter-exception.png)


使用用於登錄到資料科學虛擬機器的相同密碼登錄。
<br/>

**朱皮特家**
<br/>![Jupyter 首頁](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R 語言 
<br/>![R 範例](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python 語言
<br/>![Python 範例](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia 語言 
<br/>![Julia 範例](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Azure 機器學習示例](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![PyTorch 範例](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow 範例](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![CNTK 範例](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![caffe2 範例](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O 範例](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML 範例](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost 範例](./media/xgboost-samples.png)<br/>

