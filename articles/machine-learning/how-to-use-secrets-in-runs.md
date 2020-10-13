---
title: 訓練中的驗證秘密
titleSuffix: Azure Machine Learning
description: 使用工作區 Key Vault 以安全的方式將秘密傳遞給定型執行
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 89934470dc3bf86bb2843137a2129bff13323ca0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91302072"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>在 Azure Machine Learning 定型執行中使用驗證認證秘密


在本文中，您將瞭解如何安全地在定型執行中使用秘密。 驗證資訊（例如您的使用者名稱和密碼）是秘密。 例如，如果您連接到外部資料庫以查詢定型資料，您就必須將使用者名稱和密碼傳遞給遠端執行內容。 以純文字將這類值編碼為定型腳本並不安全，因為它會公開秘密。 

相反地，您的 Azure Machine Learning 工作區有一個相關聯的資源，稱為 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。 使用此 Key Vault 透過 Azure Machine Learning Python SDK 中的一組 Api，安全地將秘密傳遞給遠端執行。

使用秘密的標準流程為：
 1. 在 [本機電腦] 上，登入 Azure 並聯機至您的工作區。
 2. 在 [本機電腦] 的 [工作區] Key Vault 中設定秘密。
 3. 提交遠端執行。
 4. 在遠端執行中，從 Key Vault 取得秘密，然後使用它。

## <a name="set-secrets"></a>設定秘密

在 Azure Machine Learning 中， [Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true) 類別包含用來設定秘密的方法。 在您的本機 Python 會話中，請先取得您工作區 Key Vault 的參考，然後使用 [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=trueset-secret-name--value-) 方法，依名稱和值設定秘密。 如果名稱已存在， __set_secret__ 方法會更新密碼值。

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

請勿將秘密值放在您的 Python 程式碼中，因為不安全地將它以純文字形式儲存在檔案中。 相反地，請從環境變數取得秘密值，例如 Azure DevOps 組建密碼，或從互動式使用者輸入。

您可以使用方法列出秘密名稱，也可以使用 [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=truelist-secrets--) 批次版本[set_secrets ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=trueset-secrets-secrets-batch-) ，讓您一次設定多個密碼。

## <a name="get-secrets"></a>取得密碼

在您的本機程式碼中，您可以使用 [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-secret-name-) 方法，依名稱取得秘密值。

針對提交的執行 [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true#&preserve-view=truesubmit-config--tags-none----kwargs-)  ，請使用 [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-secret-name-) 方法搭配 [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) 類別。 因為已提交的執行會感知其工作區，所以此方法會將工作區具現化並直接傳回秘密值。

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

請注意不要藉由撰寫或列印秘密值來公開秘密值。

另外還有一個批次版本， [get_secrets ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-secrets-secrets-) 可同時存取多個秘密。

## <a name="next-steps"></a>後續步驟

 * [查看範例筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [瞭解 Azure Machine Learning 的企業安全性](concept-enterprise-security.md)
