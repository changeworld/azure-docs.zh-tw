---
title: 訓練中的驗證秘密
titleSuffix: Azure Machine Learning
description: 使用工作區 Key Vault 以安全的方式將秘密傳遞至定型執行
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 03/09/2020
ms.openlocfilehash: 8fefeb162dd6f036c21485715dd680972823fbaa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84677470"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>在 Azure Machine Learning 訓練執行中使用驗證認證秘密
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何安全地在定型執行中使用秘密。 您的使用者名稱和密碼等驗證資訊都是秘密。 例如，如果您連接到外部資料庫以查詢定型資料，則必須將您的使用者名稱和密碼傳遞至遠端執行內容。 以純文字將這類值編碼成定型腳本並不安全，因為它會公開秘密。 

相反地，您的 Azure Machine Learning 工作區會有一個稱為[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)的相關聯資源。 使用此 Key Vault 透過 Azure Machine Learning Python SDK 中的一組 Api，安全地將秘密傳遞至遠端執行。

使用秘密的基本流程如下：
 1. 在 [本機電腦] 上，登入 Azure 並聯機到您的工作區。
 2. 在 [本機電腦] 上，于 [工作區 Key Vault 中設定密碼。
 3. 提交遠端執行。
 4. 在遠端執行中，從 Key Vault 取得秘密並加以使用。

## <a name="set-secrets"></a>設定秘密

在 Azure Machine Learning 中， [Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py)類別包含用來設定密碼的方法。 在您的本機 Python 會話中，先取得 Key Vault 的工作區參考，然後使用 [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) 方法，依名稱和值來設定密碼。 如果名稱已存在，則__set_secret__方法會更新密碼值。

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

請勿將秘密值放在 Python 程式碼中，因為以純文字形式將它儲存在檔案中並不安全。 相反地，從環境變數取得秘密值，例如 Azure DevOps 組建密碼，或從互動式使用者輸入。

您可以使用方法列出秘密名稱 [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) ，另外還有一個批次版本[set_secrets （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) ，可讓您一次設定多個密碼。

## <a name="get-secrets"></a>取得密碼

在您的本機程式碼中，您可以使用 [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) 方法，依名稱取得密碼值。

若為已提交的回合 [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-) ，請搭配 [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) 類別使用方法 [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) 。 因為已提交的執行會感知其工作區，所以此方法會將工作區具現化的快捷方式，並直接傳回秘密值。

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

請小心，不要藉由寫出或列印秘密值來公開。

另外還有一個批次版本， [get_secrets （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) ，可一次存取多個密碼。

## <a name="next-steps"></a>後續步驟

 * [View 範例筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [瞭解 Azure Machine Learning 的企業安全性](concept-enterprise-security.md)
