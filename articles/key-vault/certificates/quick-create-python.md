---
title: 快速入門 – Azure Key Vault Python 用戶端程式庫 – 管理憑證
description: 了解如何使用 Python 用戶端程式庫，從 Azure Key Vault 建立、擷取和刪除憑證
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 05b1ea8e6daa9fae0588535534c4a8be45ab91d5
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285255"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-python"></a>快速入門：適用於 Python 的 Azure Key Vault 憑證用戶端程式庫

開始使用適用於 Python 的 Azure Key Vault 憑證用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。 藉由使用 Key Vault 來儲存憑證，您可以避免將憑證儲存在您的程式碼中，如此會增加應用程式的安全性。

[API 參考文件](/python/api/overview/azure/keyvault-certificates-readme) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates) | [套件 (Python Package Index)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python 2.7+ 或 3.5.3+](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

本快速入門假設您是在 Linux 終端機視窗中執行 [Azure CLI](/cli/azure/install-azure-cli)。

## <a name="set-up-your-local-environment"></a>設定您的本機環境

本快速入門會使用 Azure 身分識別程式庫搭配 Azure CLI，向 Azure 服務驗證使用者。 開發人員也可以使用 Visual Studio 或 Visual Studio Code 來驗證其呼叫。如需詳細資訊，請參閱[使用 Azure 身分識別用戶端程式庫驗證用戶端](/java/api/overview/azure/identity-readme)

### <a name="sign-in-to-azure"></a>登入 Azure

1. 執行 `login` 命令。

    ```azurecli-interactive
    az login
    ```

    如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入 Azure 登入頁面。

    否則，請在 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 中開啟瀏覽器頁面，並輸入顯示在終端機中的授權碼。

2. 請在瀏覽器中使用您的帳戶認證登入。

### <a name="install-the-packages"></a>安裝套件

1. 在終端機或命令提示字元中，建立適當的專案資料夾，然後建立並啟動 Python 虛擬環境，如[使用 Python 虛擬環境](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)中所述

1. 安裝 Azure Active Directory 身分識別程式庫：

    ```terminal
    pip install azure.identity
    ```


1. 安裝 Key Vault 憑證用戶端程式庫：

    ```terminal
    pip install azure-keyvault-certificates
    ```

### <a name="create-a-resource-group-and-key-vault"></a>建立資源群組和金鑰保存庫

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>授與對金鑰保存庫的存取權

建立金鑰保存庫的存取原則，將祕密權限授與服務主體

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>設定環境變數

此應用程式使用金鑰保存庫名稱作為稱為 `KEY_VAULT_NAME` 的環境變數。

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS 或 Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>建立範例程式碼

適用於 Python 的 Azure Key Vault 憑證用戶端程式庫可讓您管理憑證。 下列程式碼範例示範如何建立用戶端、設定憑證、擷取憑證及刪除憑證。

建立名為 kv_certificates.py 的檔案，其中包含此程式碼。

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>執行程式碼

請確定上一節中的程式碼位於名為 kv_certificates.py 的檔案中。 使用下列命令來執行程式碼：

```terminal
python kv_certificates.py
```

- 如果您遇到權限錯誤，請確定您已執行 [`az keyvault set-policy` 命令](#grant-access-to-your-key-vault)。
- 以相同的金鑰名稱重新執行程式碼可能會產生錯誤「(衝突) 憑證 <name> 目前處於已刪除但可復原的狀態。」 使用不同的金鑰名稱。

## <a name="code-details"></a>程式碼詳細資料

### <a name="authenticate-and-create-a-client"></a>驗證並建立用戶端

在本快速入門中，已登入的使用者是用來向金鑰保存庫進行驗證，這是本機開發的慣用方法。 針對部署至 Azure 的應用程式，應將受控識別指派給 App Service 或虛擬機器。如需詳細資訊，請參閱[受控識別概觀](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

在下列範例中，金鑰保存庫的名稱會以 "https://\<your-key-vault-name\>.vault.azure.net" 格式，擴充至金鑰保存庫 URI。 這個範例會使用 ['DefaultAzureCredential()'](/python/api/azure-identity/azure.identity.defaultazurecredential) 類別，允許在各種不同的環境中使用相同的程式碼，搭配不同的選項來提供身分識別。 如需詳細資訊，請參閱[預設 Azure 認證驗證](https://docs.microsoft.com/python/api/overview/azure/identity-readme)。 

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>儲存憑證

取得金鑰保存庫的用戶端物件之後，您可以使用 [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-create-certificate-certificate-name--policy----kwargs-) 方法來建立憑證： 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

在這裡，憑證需要使用 [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?#get-default--) 方法取得原則。

呼叫 `begin_create_certificate` 方法會針對金鑰保存庫產生 Azure REST API 的非同步呼叫。 非同步呼叫會傳回輪詢者物件。 若要等候作業的結果，請呼叫輪詢者的 `result` 方法。

處理要求時，Azure 會使用您提供給用戶端的認證物件，來驗證呼叫者的身分識別 (服務主體)，


### <a name="retrieve-a-certificate"></a>擷取憑證

若要從 Key Vault 讀取憑證，請使用 [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#get-certificate-certificate-name----kwargs-) 方法：

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

您也可以使用 Azure CLI 命令 [az keyvault certificate show](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show) 來確認憑證是否已完成設定。

### <a name="delete-a-certificate"></a>刪除憑證

若要刪除憑證，請使用 [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-delete-certificate-certificate-name----kwargs-) 方法：

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

`begin_delete_certificate` 方法是非同步，並會傳回輪詢者物件。 呼叫輪詢器的 `result` 方法會等待其完成。

您可以使用 Azure CLI 命令 [az keyvault certificate show](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show) 來確認是否已刪除憑證。

一旦刪除，憑證會保留為已刪除但可復原的狀態一段時間。 如果再次執行程式碼，請使用不同的憑證名稱。

## <a name="clean-up-resources"></a>清除資源

如果您想要實驗[祕密](../secrets/quick-create-python.md)和[金鑰](../keys/quick-create-python.md)，可以重複使用在本文中建立的 Key Vault。

否則，當您完成本文中建立的資源時，請使用下列命令來刪除資源群組及其包含的所有資源：

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>後續步驟

- [Azure 金鑰保存庫概觀](../general/overview.md)
- [針對金鑰保存庫的存取進行保護](../general/secure-your-key-vault.md)
- [Azure Key Vault 開發人員指南](../general/developers-guide.md)
- [Azure Key Vault 最佳做法](../general/best-practices.md)
- [使用 Key Vault 進行驗證](../general/authentication.md)