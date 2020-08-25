---
title: 深度學習 & AI 架構
titleSuffix: Azure Data Science Virtual Machine
description: Azure 資料科學虛擬機器上提供的深度學習架構和工具。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 0c189efa4fc9c981a486ebd174ad80b348c6bc9a
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815533"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>適用于 Azure 資料科學 VM 的深度學習和 AI 架構
DSVM 上的深度學習架構如下所示。

> [!NOTE]
> 下列 VM 映射已被取代：
>
> * Windows Server 2016 DSVM 映射
> * Ubuntu 16.04 DSVM 映射 
>
> 這些 VM 映射僅適用于使用 ARM 部署的現有客戶。 這些映射將繼續收到重大的安全性修補程式，直到其淘汰日期為止，但未來將不會以最新的資料科學工具更新。
> 
> 請參閱 [ [DSVM 影像棄用功能] 頁面](./reference-deprecation.md)。

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

| 類別 | 值 |
| ------------- | ------------- |
| ) 支援的版本 ( | |
| 支援的 DSVM 版本      | Ubuntu 16.04 (已淘汰)     |
| 它是如何在 DSVM 上設定/安裝的？  | Caffe 是安裝在 `/opt/caffe` 中。   範例位於中 `/opt/caffe/examples` 。|
| 如何執行      | 使用 X2Go 來登入您的 VM，然後啟動新的終端機並輸入下列內容：<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>具有範例筆記本的新瀏覽器視窗將會開啟。 二進位檔安裝在 /opt/caffe/build/install/bin。<br/><br/>已安裝的 Caffe 版本需要 Python 2.7，且不會使用預設會啟用的 Python 3.5。 若要切換至 Python 2.7，請執行 `source activate root` 以切換至 Anaconda 環境。|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

| 類別 | 值 |
| ------------- | ------------- |
| ) 支援的版本 ( | |
| 支援的 DSVM 版本      | Ubuntu 16.04 (已淘汰)      |
| 它是如何在 DSVM 上設定/安裝的？  | Caffe2 安裝在 [Python 2.7 (根) conda 環境中。 |
| 如何執行      | 終端機：啟動 Python，並匯入 Caffe2。 <br/> * JupyterHub：連線 [至 JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然後移至 Caffe2 目錄以尋找範例筆記本。 某些筆記本會要求以 Python 程式碼設定 Caffe2 根，請輸入 /opt/caffe2。 |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

| 類別 | 值 |
| ------------- | ------------- |
| ) 支援的版本 ( | 5.2 |
| 支援的 DSVM 版本      | Ubuntu 16.04 (已淘汰)     |
| 它是如何在 DSVM 上設定/安裝的？  | Chainer 安裝在 Python 3.5 中。 |
| 如何執行      | 終端機：啟用 Python 3.5 環境、執行 `python` ，然後執行 `import chainer` 。 <br/> * JupyterHub：連線 [至 JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然後移至 Chainer 目錄以尋找範例筆記本。| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA、cuDNN、NVIDIA 驅動程式](https://developer.nvidia.com/cuda-toolkit)

| 類別 | 值 |
| ------------- | ------------- |
| ) 支援的版本 ( | 10.0.130|
| 支援的 DSVM 版本      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 (已淘汰) <br> Ubuntu 16.04 (已淘汰)   |
| 它是如何在 DSVM 上設定/安裝的？  |_nvidia-smi_ 可於系統路徑上取得。  |
| 如何執行      | 開啟命令提示字元 (在 Windows) 或 Linux) 上的終端機 (，然後執行 _nvidia smi-s_。 |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

| 類別 | 值 |
| ------------- | ------------- |
| ) 支援的版本 ( | 0.16.1|
| 支援的 DSVM 版本      | Ubuntu 18.04<br> Ubuntu 16.04 (已淘汰)    |
| 它是如何在 DSVM 上設定/安裝的？  | Horovod 安裝在 Python 3。5 |
| 如何執行      | 在終端機上啟用正確的環境，然後執行 Python。 |

## <a name="keras"></a>[Keras](https://keras.io/)

| 類別 | 值 |
| ------------- | ------------- |
| ) 支援的版本 ( | 2.2.4 |
| 支援的 DSVM 版本      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 (已淘汰) <br> Ubuntu 16.04 (已淘汰)    |
| 它是如何在 DSVM 上設定/安裝的？  | Keras 安裝在 Windows 上的 Python 3.6 和 Linux 中的 Python 3。5 |
| 如何執行      | 在終端機上啟用正確的環境，然後執行 Python。 |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

| 類別 | 值 |
| ------------- | ------------- |
| ) 支援的版本 ( | 2.5.1 |
| 支援的 DSVM 版本      | Windows 2016 (已淘汰) <br> Ubuntu 16.04 (已淘汰)    |
| 它是如何在 DSVM 上設定/安裝的？  | CNTK 安裝在 [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) 上的 python 3.6 和 [Linux](./dsvm-tools-languages.md#python-linux-edition) 上的 python 3.5 中)  |
| 如何執行      | 終端機：啟用正確的環境，然後執行 Python。 <br/>Jupyter：連接到 [Jupyter](provision-vm.md) 或 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然後開啟 CNTK 目錄以取得範例。 |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
| 類別 | 值 |
| ------------- | ------------- |
| ) 支援的版本 ( | 1.3.0 |
| 支援的 DSVM 版本      | Windows 2016 (已淘汰) <br> Ubuntu 16.04 (已淘汰)    |
| 它是如何在 DSVM 上設定/安裝的？  | MXNet 安裝在 `C:\dsvm\tools\mxnet` Windows 和 `/dsvm/tools/mxnet` Ubuntu 上。 Python 系結會安裝在 [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) 上的 python 3.6 和 [Linux](./dsvm-tools-languages.md#python-linux-edition) 上的 Python 3.5 中) R 系結也包含在 Ubuntu DSVM 中。 |
| 如何執行      | 終端機：啟用正確的 conda 環境，然後執行 `import mxnet` 。 <br/>Jupyter：連接到 [Jupyter](provision-vm.md#access-the-dsvm) 或 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然後開啟 `mxnet` 目錄以取得範例。 |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

| 類別 | 值 |
| ------------- | ------------- |
| ) 支援的版本 ( | 1.0.1 |
| 支援的 DSVM 版本      | Windows 2016 (已淘汰) <br> Ubuntu 16.04 (已淘汰)    |
| 它是如何在 DSVM 上設定/安裝的？  | MXNet 模型伺服器安裝在 [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) 上的 python 3.6 和 [Linux](./dsvm-tools-languages.md#python-linux-edition) 上的 python 3.5 中)  |
| 如何執行      | Terminal：執行 `sudo systemctl stop jupyterhub` 以停止 JupyterHub 服務，因為兩者都在相同的埠上接聽。 然後啟動正確的 conda 環境並執行 `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia 系統管理介面 (nvidia-smi-s) ](https://developer.nvidia.com/nvidia-system-management-interface)

| 類別 | 值 |
| ------------- | ------------- |
| ) 支援的版本 ( |  |
| 支援的 DSVM 版本      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 (已淘汰) <br> Ubuntu 16.04 (已淘汰)    |
| 其用途為何？ | 用來查詢 GPU 活動的 NVIDIA 工具 |
| 它是如何在 DSVM 上設定/安裝的？  | `nvidia-smi` 位於系統路徑上。 |
| 如何執行      | 在 **具有 GPU 的**虛擬機器上，開啟命令提示字元 (在 Windows) 或 Linux) 上的終端機 (，然後執行 `nvidia-smi` 。 |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

| 類別 | 值 |
| ------------- | ------------- |
| ) 支援的版本 ( | 1.2.0 (Ubuntu 16.04) 、1.4.0 (Ubuntu 18.04、Windows 2019)  |
| 支援的 DSVM 版本      | Windows Server 2019<br>Ubuntu 18.04<br> Ubuntu 16.04 (已淘汰)  |
| 它是如何在 DSVM 上設定/安裝的？  | 安裝在 [Python 3.5](dsvm-tools-languages.md#python-linux-edition)中。 包含範例 Jupyter 筆記本，範例位於/dsvm/samples/pytorch。 |
| 如何執行      | 終端機：啟用正確的環境，然後執行 Python。<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)： Connect，然後開啟 PyTorch 目錄以取得範例。  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

| 類別 | 值 |
| ------------- | ------------- |
| ) 支援的版本 ( | 1.13 |
| 支援的 DSVM 版本      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 (已淘汰) <br> Ubuntu 16.04 (已淘汰)  |
| 它是如何在 DSVM 上設定/安裝的？  | 安裝在 [Linux](dsvm-tools-languages.md#python-linux-edition) 上的 python 3.5 和 Windows 上的 python 3.6 [2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| 如何執行      | 終端機：啟用正確的環境，然後執行 Python。 <br/> * Jupyter：連接到 [Jupyter](provision-vm.md) 或 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然後開啟 TensorFlow 目錄以取得範例。   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

| 類別 | 值 |
| ------------- | ------------- |
| ) 支援的版本 ( | 1.12 |
| 支援的 DSVM 版本      | Ubuntu 16.04 (已淘汰)  |
| 它是如何在 DSVM 上設定/安裝的？  | 可在終端機上使用 tensorflow_model_server。 |
| 如何執行      |  可從[線上](https://www.tensorflow.org/serving/)取得範例。   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

| 類別 | 值 |
| ------------- | ------------- |
| ) 支援的版本 ( | 1.0.3 |
| 支援的 DSVM 版本      | Ubuntu 16.04 (已淘汰)  |
| 它是如何在 DSVM 上設定/安裝的？  |Theano 安裝在 Python 2.7 (_根_) ，以及 python 3.5 (_py35_) 環境中。 |
| 如何執行      |  終端機：啟用您想要 (root 或 py35) 的 Python 版本，執行 Python，然後匯入 Theano。<br/>* Jupyter：選取 Python 2.7 或3.5 核心，然後匯入 Theano。  <br/>若要解決最近的數學核心程式庫 (MKL) bug，您必須先設定 MKL 執行緒層，如下所示：<br/><br/>`export MKL_THREADING_LAYER=GNU`  |
