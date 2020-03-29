---
title: 在訓練運行中使用機密
titleSuffix: Azure Machine Learning
description: 使用工作區金鑰保存庫以安全方式將機密傳遞給安全運行
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: d877794abf12b8b412cd1ecf4efd72fd1179d768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942254"
---
# <a name="use-secrets-in-training-runs"></a>在訓練運行中使用機密
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將學習如何在訓練運行中使用機密。 身份驗證資訊（如使用者名和密碼）是機密。 例如，如果連接到外部資料庫以查詢訓練資料，則需要將使用者名和密碼傳遞給遠端運行上下文。 在明文中將此類值編碼到訓練腳本中是不安全的，因為它會暴露機密。 

相反，Azure 機器學習工作區具有稱為[Azure 金鑰保存庫](https://docs.microsoft.com/azure/key-vault/key-vault-overview)的關聯資源。 使用此金鑰保存庫通過 Azure 機器學習 Python SDK 中的一組 API 安全地將機密傳遞到遠端運行。

使用機密的基本流程是：
 1. 在本地電腦上，登錄到 Azure 並連接到工作區。
 2. 在本地電腦上，在工作區金鑰保存庫中設置機密。
 3. 提交遠端運行。
 4. 在遠端運行中，從金鑰保存庫獲取金鑰並使用它。

## <a name="set-secrets"></a>設置機密

在 Azure 機器學習中[，Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py)類包含用於設置機密的方法。 在本地 Python 會話中，首先獲取對工作區金鑰保存庫的引用，然後使用[`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-)方法按名稱和值設置機密。 如果名稱已存在 __，set_secret__方法將更新機密值。

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

不要將機密值放在 Python 代碼中，因為將其存儲為明文不安全。 相反，從環境變數（例如 Azure DevOps 生成金鑰）或互動式使用者輸入獲取機密值。

您可以使用 方法列出機密名稱，[`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--)並且還有一個批次處理版本[，set_secrets（））](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-)允許您一次設置多個機密。

## <a name="get-secrets"></a>取得密碼

在本地代碼中，可以使用 方法[`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-)按名稱獲取機密值。

對於提交的運行，[`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-)將[`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-)方法與[`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)類一起使用。 由於提交的運行知道其工作區，此方法將快捷方式工作區具現化並直接返回機密值。

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

小心不要通過寫或列印來暴露機密值。

還有一個批次處理版本[，get_secrets（））用於](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-)一次訪問多個機密。

## <a name="next-steps"></a>後續步驟

 * [查看示例筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [通過 Azure 機器學習瞭解企業安全性](concept-enterprise-security.md)
