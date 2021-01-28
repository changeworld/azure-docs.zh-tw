---
title: 在 HDInsight 上的 Apache Spark 上執行 Azure Machine Learning 工作負載
description: 了解如何以自動化機器學習 (AutoML) 在 Azure HDInsight 中的 Apache Spark 上執行 Azure Machine Learning 工作負載。
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/13/2019
ms.openlocfilehash: 4087341a9a96ae56c00972f886ce3cc8891750a5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929736"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>使用 HDInsight 中 Apache Spark 的自動化機器學習來執行 Azure Machine Learning 工作負載

Azure Machine Learning 可簡化並加速機器學習模型的建立、定型和部署。 在自動化機器學習 (AutoML) 中，您會從具有已定義目標功能的定型資料開始，然後逐一查看演算法和特徵選取的組合，以根據定型分數自動為您的資料選取最佳模型。 HDInsight 可讓客戶布建具有數百個節點的叢集。 在 HDInsight 叢集中的 Spark 上執行的 AutoML，可讓使用者在這些節點之間使用計算容量，以向外延展方式執行定型作業，以及平行執行多個定型作業。 這可讓使用者執行 AutoML 實驗，同時將計算與其他的大型資料工作負載共用。

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>在 HDInsight 叢集上安裝 Azure Machine Learning

如需自動化機器學習的一般教學課程，請參閱 [教學課程：使用自動化機器學習建立您的回歸模型](../../machine-learning/tutorial-auto-train-models.md)。
所有新的 HDInsight-Spark 叢集都會隨 AzureML-AutoML SDK 預先安裝。

> [!Note]
> Azure Machine Learning 套件會安裝到 Python3 Conda 環境。 已安裝的 Jupyter Notebook 應使用 PySpark3 核心來執行。

您也可以使用 Zeppelin 筆記本來使用 AutoML。

> [!Note]
> Zeppelin 有一個 [已知問題](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) ，因為 PySpark3 不會挑選正確版本的 Python。 請使用記載的解決辦法。

## <a name="authentication-for-workspace"></a>工作區的驗證

建立工作區和提交實驗都需要驗證權杖。 您可以使用 [Azure AD 應用程式](../../active-directory/develop/app-objects-and-service-principals.md)來產生此權杖。 如果帳戶未啟用多重要素驗證，則 [Azure AD 使用者](/azure/python/python-sdk-azure-authenticate)也可用來產生必要的驗證權杖。  

下列程式碼片段會使用 **Azure AD 應用程式** 來建立驗證權杖。

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```

下列程式碼片段會使用 **Azure AD 使用者** 來建立驗證權杖。

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>載入資料集

Spark 上的自動化機器學習會使用 **資料流程**，這是延遲評估且不可變動的資料作業。  資料流程可以透過公用讀取從 Blob 載入資料集，或透過 SAS 權杖從 Blob URL 載入資料。

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

您也可以使用單次註冊來註冊具有工作區的資料存放區。

## <a name="experiment-submission"></a>實驗提交

在 [自動化機器學習](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)設定中， `spark_context` 應該設定屬性，讓封裝在分散式模式上執行。 `concurrent_iterations` 屬性會決定可平行執行的反覆項目數量上限，其值應設為小於 Spark 應用程式的執行程式核心數目。

## <a name="next-steps"></a>後續步驟

* 如需自動化機器學習背後動機的詳細資訊，請參閱 [使用 Microsoft 自動化機器學習的步調發行模型！](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* 如需有關使用 Azure ML 自動化 ML 功能的詳細資訊，請參閱 [Azure Machine Learning 中的新自動化機器學習功能](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Microsoft Research 中的 AutoML 專案](https://www.microsoft.com/research/project/automl/)
