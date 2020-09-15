---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 5e912b76c2ef68aa01dae57d1b42abc386a8e67b
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482114"
---
1. 請使用 `az group create` 命令以建立資源群組：

    ```azurecli
    az group create --name KeyVault-PythonQS-rg --location eastus
    ```

    您也可以將 "eastus" 變更為更接近您的位置。

1. 使用 `az keyvault create` 建立金鑰保存庫：

    ```azurecli
    az keyvault create --name <your-unique-keyvault-name> --resource-group KeyVault-PythonQS-rg
    ```

    以 Azure 中的唯一名稱取代 `<your-unique-keyvault-name>`。 您通常會使用個人或公司名稱，以及其他數字和識別碼。 

1. 建立環境變數，以將 Key Vault 的名稱提供給程式碼：

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    ---
