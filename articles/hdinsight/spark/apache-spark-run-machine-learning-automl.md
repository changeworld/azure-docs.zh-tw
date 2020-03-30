---
title: 在 HDInsight 中的 Apache Spark 上運行 Azure 機器學習工作負荷
description: 了解如何以自動化機器學習 (AutoML) 在 Azure HDInsight 中的 Apache Spark 上執行 Azure Machine Learning 工作負載。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: 6fc0d4cfe29e0fb189c44b307576bd08d2da8a31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638877"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>在 HDInsight 中的 Apache Spark 上通過自動機器學習運行 Azure 機器學習工作負荷

Azure 機器學習可簡化並加快機器學習模型的構建、培訓和部署。 在自動機器學習 （AutoML） 中，您從具有定義目標功能的訓練資料開始，然後反覆運算演算法和要素選擇的組合，以便根據訓練分數自動選擇資料的最佳模型。 HDInsight 允許客戶預配具有數百個節點的群集。 在 HDInsight 群集中 Spark 上運行的 AutoML 允許使用者跨這些節點使用計算容量以橫向擴展方式運行訓練作業，並並行運行多個培訓作業。 這允許使用者在與其他大資料工作負載共用計算的同時運行 AutoML 實驗。

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>在 HDInsight 叢集上安裝 Azure Machine Learning

有關自動機器學習的一般教程，請參閱[教程：使用自動機器學習構建回歸模型](../../machine-learning/tutorial-auto-train-models.md)。
所有新的 HDInsight-Spark 群集都預先安裝了 AzureML-自動ML SDK。

> [!Note]
> Azure Machine Learning 套件會安裝到 Python3 Conda 環境。 已安裝的 Jupyter Notebook 應使用 PySpark3 核心來執行。

您也可以使用 Zepelin 筆記本使用自動ML。

> [!Note]
> Zepelin 有一[個已知問題](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html)，即 PySpark3 不會選擇正確的 Python 版本。 請使用記錄的解決方法。

## <a name="authentication-for-workspace"></a>工作區的驗證

建立工作區和提交實驗都需要驗證權杖。 您可以使用 [Azure AD 應用程式](../../active-directory/develop/app-objects-and-service-principals.md)來產生此權杖。 如果帳戶未啟用多重要素驗證，則 [Azure AD 使用者](/azure/python/python-sdk-azure-authenticate)也可用來產生必要的驗證權杖。  

下列程式碼片段會使用 **Azure AD 應用程式**來建立驗證權杖。

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```

下列程式碼片段會使用 **Azure AD 使用者**來建立驗證權杖。

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>載入資料集

Spark 上的自動化機器學習會使用**資料流程**，這是延遲評估且不可變動的資料作業。  資料流程可以透過公用讀取從 Blob 載入資料集，或透過 SAS 權杖從 Blob URL 載入資料。

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

您也可以使用單次註冊來註冊具有工作區的資料存放區。

## <a name="experiment-submission"></a>實驗提交

在[自動機器學習配置](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)中，應設置該屬性`spark_context`，使包在分散式模式下運行。 `concurrent_iterations` 屬性會決定可平行執行的反覆項目數量上限，其值應設為小於 Spark 應用程式的執行程式核心數目。

## <a name="next-steps"></a>後續步驟

* 有關自動機器學習背後的動機的詳細資訊，請參閱[使用 Microsoft 自動化機器學習的速度發佈模型！](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* 有關使用 Azure ML 自動 ML 功能的詳細資訊，請參閱[Azure 機器學習中的新自動機器學習功能](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Microsoft Research 中的 AutoML 專案](https://www.microsoft.com/research/project/automl/)
