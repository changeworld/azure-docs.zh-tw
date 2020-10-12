---
title: 策展的環境
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 中提供的策劃環境集合
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 09/03/2020
ms.openlocfilehash: 4ae96976f81aab9a0949594551c82d3a3fec4f0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89662086"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning 策劃環境

本文列出 Azure Machine Learning 中的策劃環境，以及預先安裝的套件和通道。 策劃環境是由 Azure Machine Learning 提供，且預設會在您的工作區中提供。 它們是由快取的 Docker 映射所支援，可減少執行準備成本，並允許更快的部署時間。 使用這些環境來快速開始使用各種機器學習架構。

> [!NOTE]
> 這份清單會在2020年9月更新。 使用 Python SDK 取得最新的清單。 如需詳細資訊，請參閱 [環境文章](./how-to-use-environments.md#use-a-curated-environment)。

## <a name="azureml-automl"></a>AzureML-AutoML

**封裝通道：**

* 蟒蛇
* conda-偽造
* pytorch

**Conda 套件：**

* Python
* numpy
* scikit-learn
* pandas
* .py-xgboost
* fbprophet
* 假期
* setuptools-git
* psutil

**Pip 套件：**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-預設值
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* 推斷-架構
* .py-cpuinfo

## <a name="azureml-automl-dnn"></a>AzureML-AutoML-DNN

**封裝通道：**

* 蟒蛇
* conda-偽造
* pytorch

**Conda 套件：**

* Python
* numpy
* scikit-learn
* pandas
* .py-xgboost
* fbprophet
* 假期
* setuptools-git
* pytorch
* cudatoolkit
* psutil

**Pip 套件：**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-預設值
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* 推斷-架構
* pytorch-轉換器
* spacy
* en_core_web_sm
* .py-cpuinfo

## <a name="azureml-automl-dnn-gpu"></a>AzureML-AutoML-DNN-GPU

**封裝通道：**

* 蟒蛇
* conda-偽造
* pytorch

**Conda 套件：**

* Python
* numpy
* scikit-learn
* pandas
* fbprophet
* 假期
* setuptools-git
* pytorch
* cudatoolkit
* psutil

**Pip 套件：**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-預設值
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* 推斷-架構
* horovod
* pytorch-轉換器
* spacy
* en_core_web_sm
* .py-cpuinfo

## <a name="azureml-automl-dnn-vision-gpu"></a>AzureML-AutoML-DNN-願景-GPU

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-資料集-執行時間
* azureml-contrib-dataset
* azureml-telemetry
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-預設值
* azureml-interpret
* azureml-explain-model
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-contrib-automl-dnn-願景

## <a name="azureml-automl-gpu"></a>AzureML-AutoML-GPU

**封裝通道：**

* 蟒蛇
* conda-偽造
* pytorch

**Conda 套件：**

* Python
* numpy
* scikit-learn
* pandas
* fbprophet
* 假期
* setuptools-git
* psutil

**Pip 套件：**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-預設值
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* 推斷-架構
* .py-cpuinfo

## <a name="azureml-chainer-510-cpu"></a>AzureML-Chainer-5.1.0-CPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* chainer
* mpi4py

## <a name="azureml-chainer-510-gpu"></a>AzureML-Chainer-5.1.0-GPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* chainer
* 才能 cupy-cuda90
* mpi4py

## <a name="azureml-dask-cpu"></a>AzureML-Dask-CPU

**封裝通道：**

* conda-偽造
* pytorch
* 預設值

**Conda 套件：**

* Python

**Pip 套件：**

* adlfs
* azureml-core
* azureml-資料集-執行時間
* dask [完成]
* dask-ml [完成]
* 分散式
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* notebook
* pyarrow

## <a name="azureml-dask-gpu"></a>AzureML-Dask-GPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python
* matplotlib

**Pip 套件：**

* azureml-預設值
* adlfs
* azureml-core
* dask [完成]
* dask-ml [完成]
* 分散式
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* notebook
* pyarrow

## <a name="azureml-hyperdrive-forecastdnn"></a>AzureML-Hyperdrive-ForecastDNN

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-預設值
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-contrib-automl-dnn-預測

## <a name="azureml-minimal"></a>AzureML-Minimal

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值

## <a name="azureml-pyspark-mmlspark-015"></a>AzureML-PySpark-MmlSpark-0.15

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core

## <a name="azureml-pytorch-10-cpu"></a>AzureML-PyTorch-1.0-CPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* 火炬
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-10-gpu"></a>AzureML-PyTorch-1.0-GPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* 火炬
* torchvision
* mkl
* horovod

## <a name="azureml-pytorch-11-cpu"></a>AzureML-PyTorch-1.1-CPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* 火炬
* torchvision
* mkl
* horovod
* tensorboard
* 未來

## <a name="azureml-pytorch-11-gpu"></a>AzureML-PyTorch-1.1-GPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* 火炬
* torchvision
* mkl
* horovod
* tensorboard
* 未來

## <a name="azureml-pytorch-12-cpu"></a>AzureML-PyTorch-1.2-CPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* 火炬
* torchvision
* mkl
* horovod
* tensorboard
* 未來

## <a name="azureml-pytorch-12-gpu"></a>AzureML-PyTorch-1.2-GPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* 火炬
* torchvision
* mkl
* horovod
* tensorboard
* 未來

## <a name="azureml-pytorch-13-cpu"></a>AzureML-PyTorch-1.3-CPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* 火炬
* torchvision
* mkl
* horovod
* tensorboard
* 未來

## <a name="azureml-pytorch-13-gpu"></a>AzureML-PyTorch-1.3-GPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* 火炬
* torchvision
* mkl
* horovod
* tensorboard
* 未來

## <a name="azureml-pytorch-14-cpu"></a>AzureML-PyTorch-1.4-CPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* 火炬
* torchvision
* mkl
* horovod
* tensorboard
* 未來

## <a name="azureml-pytorch-14-gpu"></a>AzureML-PyTorch-1.4-GPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* 火炬
* torchvision
* mkl
* horovod
* tensorboard
* 未來

## <a name="azureml-pytorch-15-cpu"></a>AzureML-PyTorch-1.5-CPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* 火炬
* torchvision
* mkl
* horovod
* tensorboard
* 未來

## <a name="azureml-pytorch-15-gpu"></a>AzureML-PyTorch-1.5-GPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* 火炬
* torchvision
* mkl
* horovod
* tensorboard
* 未來

## <a name="azureml-pytorch-16-cpu"></a>AzureML-PyTorch-1.6-CPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* 火炬
* torchvision
* mkl
* horovod
* tensorboard
* 未來

## <a name="azureml-pytorch-16-gpu"></a>AzureML-PyTorch-1.6-GPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* 火炬
* torchvision
* mkl
* horovod
* tensorboard
* 未來

## <a name="azureml-scikit-learn-0203"></a>AzureML-Scikit-learn-學習-0.20。3

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* scikit-learn
* scipy
* joblib

## <a name="azureml-tensorflow-110-cpu"></a>AzureML-TensorFlow-1.10-CPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-110-gpu"></a>AzureML-TensorFlow-1.10-GPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-112-cpu"></a>AzureML-TensorFlow-1.12-CPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-112-gpu"></a>AzureML-TensorFlow-1.12-GPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-113-cpu"></a>AzureML-TensorFlow-1.13-CPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-113-gpu"></a>AzureML-TensorFlow-1.13-GPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-20-cpu"></a>AzureML-TensorFlow-2.0-CPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-20-gpu"></a>AzureML-TensorFlow-2.0-GPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-21-cpu"></a>AzureML-TensorFlow-2.1-CPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-21-gpu"></a>AzureML-TensorFlow-2.1-GPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tensorflow-22-cpu"></a>AzureML-TensorFlow-2.2-CPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-22-gpu"></a>AzureML-TensorFlow-2.2-GPU

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* tensorflow-gpu
* horovod

## <a name="azureml-tutorial"></a>AzureML-Tutorial

**封裝通道：**

* 蟒蛇
* conda-偽造

**Conda 套件：**

* Python
* pandas
* numpy
* tqdm
* scikit-learn
* matplotlib

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-telemetry
* azureml-定型-restclients-hyperdrive
* azureml-train-core
* azureml-widgets
* azureml-pipeline-core
* azureml-pipeline-steps
* azureml-opendatasets
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-定型
* azureml-sdk
* azureml-interpret
* azureml-tensorboard
* azureml-mlflow
* mlflow
* sklearn-pandas

## <a name="azureml-vowpalwabbit-880"></a>AzureML-VowpalWabbit-8.8。0

**封裝通道：**

* conda-偽造

**Conda 套件：**

* Python

**Pip 套件：**

* azureml-core
* azureml-預設值
* azureml-dataset-runtime [保險絲，pandas]
