---
title: 安全存儲訪問憑據
titleSuffix: Azure Data Science Virtual Machine
description: 了解如何在「資料科學虛擬機器」上安全地儲存存取認證。 您將學習如何使用託管服務標識和 Azure 金鑰保存庫來存儲訪問憑據。
keywords: 深度學習, 資料科學工具, 資料科學虛擬機器, 地理空間分析, 小組資料科學流程
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 1cb0c5094d49eac5a1c8f63406a28d2927d8fa94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477318"
---
# <a name="store-access-credentials-securely-on-an-azure-data-science-virtual-machine"></a>在 Azure 資料科學虛擬機器上安全地存儲訪問憑據

雲應用程式中的代碼通常包含用於對雲服務進行身份驗證的憑據。 如何管理和保護這些憑據是構建雲應用程式的一個眾所周知的挑戰。 理想情況下，憑據不應出現在開發人員工作站上或簽入原始程式碼管理。

[Azure 資源功能的託管標識](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)通過在 Azure 活動目錄 （Azure AD） 中為 Azure 服務提供自動託管標識，使解決此問題變得更加簡單。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。

保護憑據的一種方法是使用 Windows 安裝程式 （MSI） 與[Azure 金鑰保存庫](https://docs.microsoft.com/azure/key-vault/)（一個託管的 Azure 服務）結合使用，以安全地存儲機密和加密金鑰。 您可以使用託管標識訪問金鑰保存庫，然後從金鑰保存庫檢索授權機密和加密金鑰。

有關 Azure 資源和金鑰保存庫的託管標識的文檔包含一個全面的資源，用於提供有關這些服務的深入資訊。 本文的其餘部分逐步介紹資料科學虛擬機器 (DSVM) 上 MSI 和 Key Vault 的基本使用，以便存取 Azure 資源。 

## <a name="create-a-managed-identity-on-the-dsvm"></a>在 DSVM 上建立受控身分識別

```azurecli-interactive
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```

## <a name="assign-key-vault-access-permissions-to-a-vm-principal"></a>將金鑰保存庫存取權限分配給 VM 主體

```azurecli-interactive
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI.

# Assign only get and set permissions but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>從 DSVM 存取金鑰保存庫中的祕密

```bash
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token.
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>從 DSVM 存取儲存體金鑰

```bash
# Prerequisite: You have granted your VMs MSI access to use storage account access keys based on instructions at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```

## <a name="access-the-key-vault-from-python"></a>從 Python 存取金鑰保存庫

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials.
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a Key Vault client.
key_vault_client = KeyVaultClient(
    credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
    key_vault_uri,  # Your key vault URL.
    # The name of your secret that already exists in the key vault.
    "SQLPasswd",
    ""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>從 Azure CLI 存取金鑰保存庫

```azurecli-interactive
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. The following commands enable access to the key vault from Azure CLI without requiring login to an Azure account.
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permissions, like accessing storage account keys, reading specific secrets, and writing new secrets, are provided to the MSI.

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
