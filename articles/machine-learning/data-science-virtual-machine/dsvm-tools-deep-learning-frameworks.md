---
title: 深度學習& AI 框架
titleSuffix: Azure Data Science Virtual Machine
description: Azure 資料科學虛擬機器上的可用深度學習框架和工具。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d8a5cf428f41b130e6faf68ac87a075c15211099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270065"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Azure 資料科學 VM 的深度學習和 AI 框架
下面列出了 DSVM 上的深度學習框架。

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| 支援版本 | |
| 支援的 DSVM 版本      | Linux (Ubuntu)     |
| 它是如何在 DSVM 上設定/安裝的？  | Caffe 是安裝在 `/opt/caffe` 中。   樣本在`/opt/caffe/examples`中。|
| 如何運行它      | 使用 X2Go 登錄到 VM，然後啟動新終端並輸入以下內容：<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>具有範例筆記本的新瀏覽器視窗將會開啟。 二進位檔安裝在 /opt/caffe/build/install/bin。<br/><br/>已安裝版本的 Caffe 需要 Python 2.7，並且不能與預設情況下啟動的 Python 3.5 配合使用。 要切換到 Python 2.7，`source activate root`請運行以切換到 Anaconda 環境。|    

## <a name="caffe2"></a>[卡夫2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| 支援版本 | |
| 支援的 DSVM 版本      | Linux (Ubuntu)     |
| 它是如何在 DSVM 上設定/安裝的？  | Caffe2 安裝在 #Python 2.7（根）環境中。 |
| 如何運行它      | 終端：啟動 Python 並導入 Caffe2。 <br/> • JupyterHub：[連接到JupyterHub，](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)然後轉到Caffe2目錄查找示例筆記本。 某些筆記本會要求以 Python 程式碼設定 Caffe2 根，請輸入 /opt/caffe2。 |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| 支援版本 | 5.2 |
| 支援的 DSVM 版本      | Linux (Ubuntu)     |
| 它是如何在 DSVM 上設定/安裝的？  | Chainer 安裝在 Python 3.5 中。 |
| 如何運行它      | 終端：啟動 Python 3.5 環境`python`，運行，`import chainer`然後 。 <br/> • JupyterHub：[連接到JupyterHub，](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)然後轉到鏈子目錄以查找示例筆記本。| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA、cuDNN、NVIDIA 驅動程式](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| 支援版本 | 10.0.130|
| 支援的 DSVM 版本      | Windows 與 Linux   |
| 它是如何在 DSVM 上設定/安裝的？  |_nvidia-smi_ 可於系統路徑上取得。  |
| 如何運行它      | 打開命令提示符（在 Windows 上）或終端（在 Linux 上），然後運行_nvidia-smi_。 |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| 支援版本 | 0.16.1|
| 支援的 DSVM 版本      | Linux (Ubuntu)   |
| 它是如何在 DSVM 上設定/安裝的？  | 霍羅沃德安裝在 Python 3.5 中 |
| 如何運行它      | 在終端啟動正確的環境，然後運行 Python。 |

## <a name="keras"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| 支援版本 | 2.2.4 |
| 支援的 DSVM 版本      | Windows 與 Linux   |
| 它是如何在 DSVM 上設定/安裝的？  | Keras 安裝在 Windows 上的 Python 3.6 和 Linux 中的 Python 3.5 中 |
| 如何運行它      | 在終端啟動正確的環境，然後運行 Python。 |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| 支援版本 | 2.5.1 |
| 支援的 DSVM 版本      | Windows 與 Linux   |
| 它是如何在 DSVM 上設定/安裝的？  | CNTK 安裝在[2016 年 Windows](dsvm-tools-languages.md#python-windows-server-2016-edition)上的 Python 3.6 和[Linux](./dsvm-tools-languages.md#python-linux-edition)上的 Python 3.5 中。 |
| 如何運行它      | 終端：啟動正確的環境並運行 Python。 <br/>朱比特：連接到[朱比特](provision-vm.md)或[朱比特Hub，](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)然後打開CNTK目錄的樣本。 |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| 支援版本 | 1.3.0 |
| 支援的 DSVM 版本      | Windows 與 Linux   |
| 它是如何在 DSVM 上設定/安裝的？  | MXNet 安裝在`C:\dsvm\tools\mxnet`Windows 和`/dsvm/tools/mxnet`Ubuntu 上。 Python 綁定安裝在[Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition)上的 Python 3.6 和[Linux](./dsvm-tools-languages.md#python-linux-edition)上的 Python 3.5 中，R 綁定也包含在 Ubuntu DSVM 中。 |
| 如何運行它      | 終端：啟動正確的 conda 環境，然後`import mxnet`運行 。 <br/>朱皮特：連接到[朱比特](provision-vm.md#access-the-dsvm)或[朱比特Hub，](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)然後打開樣本`mxnet`的目錄。 |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| 支援版本 | 1.0.1 |
| 支援的 DSVM 版本      | Windows 與 Linux   |
| 它是如何在 DSVM 上設定/安裝的？  | MXNet 模型伺服器安裝在[Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition)上的 Python 3.6 和[Linux](./dsvm-tools-languages.md#python-linux-edition)上的 Python 3.5 中。 |
| 如何運行它      | 終端：運行`sudo systemctl stop jupyterhub`以首先停止 JupyterHub 服務，因為兩者都在同一端口上偵聽。 然後啟動正確的 conda 環境並運行`mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia 系統管理介面（nvidia-smi）](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| 支援版本 |  |
| 支援的 DSVM 版本      | Windows 與 Linux   |
| 其用途為何？ | 用來查詢 GPU 活動的 NVIDIA 工具 |
| 它是如何在 DSVM 上設定/安裝的？  | `nvidia-smi`在系統路徑上。 |
| 如何運行它      | 在**具有 GPU 的**虛擬機器上，打開命令提示符（在 Windows 上）或終端（在 Linux`nvidia-smi`上），然後運行 。 |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| 支援版本 | 1.2.0 （Ubuntu 16.04， Windows 2016）， 1.4.0 （Ubuntu 18.04， Windows 2019） |
| 支援的 DSVM 版本      | Linux |
| 它是如何在 DSVM 上設定/安裝的？  | 安裝在[Python 3.5](dsvm-tools-languages.md#python-linux-edition)中。 包括樣品朱皮特筆記本，樣品位於 /dsvm/樣品/pytorch 中。 |
| 如何運行它      | 終端：啟動正確的環境，然後運行 Python。<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)：連接，然後打開 PyTorch 目錄以找到樣本。  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| 支援版本 | 1.13 |
| 支援的 DSVM 版本      | Windows、Linux |
| 它是如何在 DSVM 上設定/安裝的？  | 安裝在[Linux](dsvm-tools-languages.md#python-linux-edition)上的 Python 3.5 中，在[Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition)上安裝 Python 3.6 |
| 如何運行它      | 終端：啟動正確的環境，然後運行 Python。 <br/> • 朱比特：連接到[朱比特](provision-vm.md)或[朱比特Hub，](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)然後打開 TensorFlow 目錄進行採樣。   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| 支援版本 | 1.12 |
| 支援的 DSVM 版本      | Linux |
| 它是如何在 DSVM 上設定/安裝的？  | 可在終端機上使用 tensorflow_model_server。 |
| 如何運行它      |  可從[線上](https://www.tensorflow.org/serving/)取得範例。   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| 支援版本 | 1.0.3 |
| 支援的 DSVM 版本      | Linux |
| 它是如何在 DSVM 上設定/安裝的？  |Theano 安裝在 Python 2.7 （_根_）和 Python 3.5 _（py35_） 環境中。 |
| 如何運行它      |  終端：啟動所需的 Python 版本（根或 py35），運行 Python，然後導入 Theano。<br/>• 聚居器：選擇 Python 2.7 或 3.5 內核，然後導入 Theano。  <br/>要解決最近的數學內核庫 （MKL） 錯誤，您需要首先將 MKL 執行緒層設置為如下所示：<br/><br/>`export MKL_THREADING_LAYER=GNU`  |