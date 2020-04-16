---
title: 教學課程 - 使用 Linux 虛擬機器和 Python 應用程式將秘密儲存在 Azure Key Vault 中 | Microsoft Docs
description: 在本教學課程中，您將了解如何設定 Python 應用程式以從 Azure Key Vault 讀取祕密。
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: df089f0338a177c08f4d9e88d55b501fd12f88f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81426357"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>教學課程：使用 Linux VM 和 Python 應用程式將這些秘密儲存在 Azure Key Vault 中

Azure Key Vault 可協助您保護秘密，例如存取應用程式、服務和 IT 資源所需的 API 金鑰和資料庫連接字串。

在本教學課程中，您會設定 Azure Web 應用程式，以使用 Azure 資源的受控識別從 Azure Key Vault 讀取資訊。 您會了解如何：

> [!div class="checklist"]
> * 建立金鑰保存庫
> * 將秘密儲存在金鑰保存庫中
> * 建立 Linux 虛擬機器
> * 啟用虛擬機器的[受控識別](../../active-directory/managed-identities-azure-resources/overview.md)
> * 授與主控台應用程式從金鑰保存庫讀取資料所需的權限
> * 從金鑰保存庫擷取祕密

繼續操作前，請務必先了解 [Key Vault 的基本概念](../general/basic-concepts.md)。

## <a name="prerequisites"></a>Prerequisites

* [Git](https://git-scm.com/downloads)。
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Azure CLI 2.0.4 版或更新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)或 Azure Cloud Shell。

[!INCLUDE [Azure Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>了解受控服務識別

Azure Key Vault 可安全地儲存認證，因此認證不在您的程式碼中。 若要擷取認證，您必須向 Azure Key Vault 進行驗證。 不過，若要向 Key Vault 進行驗證，您必須要有認證。 這是典型的啟動程序問題。 透過 Azure 和 Azure Active Directory (Azure AD)，受控服務識別 (MSI) 可提供啟動程序身分識別，讓您更輕鬆地開始執行工作。

當您為 Azure 服務 (例如虛擬機器、App Service 或 Functions) 啟用 MSI 時，Azure 會為 Azure AD 中的服務執行個體建立服務主體。 Azure 會將服務主體的認證插入該服務的執行個體中。

![MSI](../media/MSI.png)

接下來，您的程式碼會呼叫 Azure 資源上可用的本機中繼資料服務，以取得存取權杖。 您的程式碼會使用從本機 MSI 端點取得的存取權杖，向 Azure Key Vault 服務進行驗證。

## <a name="sign-in-to-azure"></a>登入 Azure

若要使用 Azure CLI 登入 Azure，請輸入：

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用 `az group create` 命令和下列程式碼，在美國西部位置建立資源群組。 將 `YourResourceGroupName` 取代為您選擇的名稱。

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

您在本教學課程中都將使用此資源群組。

## <a name="create-a-key-vault"></a>建立金鑰保存庫

接下來，您會在上一個步驟中建立的資源群組中建立金鑰保存庫。 請提供下列資訊：

* 金鑰保存庫名稱：名稱必須是 3-24 個字元的字串，且只能包含 0-9、a-z、A-Z 和連字號 (-)。
* 資源群組名稱。
* 位置：**美國西部**。

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

此時，您的 Azure 帳戶是唯一獲得授權在此新保存庫上執行任何作業的帳戶。

## <a name="add-a-secret-to-the-key-vault"></a>將秘密新增至金鑰保存庫

我們將新增密碼，以協助說明其運作方式。 您可以儲存 SQL 連接字串，或任何需要確保安全性且可供應用程式使用的其他資訊。

輸入下列命令，以在名為 *AppSecret* 的金鑰保存庫中建立秘密。 此秘密會儲存值 **MySecret**。

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>建立 Linux 虛擬機器

使用 `az vm create` 命令來建立 VM。

下列範例會建立名為 myVM  的 VM，並新增名為 azureuser  的使用者帳戶。 `--generate-ssh-keys` 參數會自動產生 SSH 金鑰，並將它放在預設金鑰位置 (~/.ssh  )。 若要改為建立一組特定金鑰，請使用 `--ssh-key-value` 選項。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

建立虛擬機器和支援資源需要幾分鐘的時間。 下列範例輸出會顯示 VM 建立作業成功：

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

請從 VM 的輸出中記下您自己的 `publicIpAddress`。 您將在後續步驟中使用此位址來存取 VM。

## <a name="assign-an-identity-to-the-vm"></a>將身分識別指派給 VM

執行下列命令，對虛擬機器建立系統指派的身分識別：

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

命令的輸出如下所示。

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

記下 `systemAssignedIdentity`。 您在下一個步驟將會用到這項資訊。

## <a name="give-the-vm-identity-permission-to-key-vault"></a>為 Key Vault 授與 VM 身分識別權限

現在，您可以為 Key Vault 授與您所建立的身分識別的權限。 執行以下命令：

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>登入 VM

使用終端機登入虛擬機器。

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>在 VM 上安裝 Python 程式庫

下載並安裝[要求](https://pypi.org/project/requests/2.7.0/) Python 程式庫，以發出 HTTP GET 呼叫。

## <a name="create-edit-and-run-the-sample-python-app"></a>建立、編輯和執行範例 Python 應用程式

建立名為 **Sample.py** 的 Python 檔案。

開啟 Sample.py 並加以編輯，使其包含下列程式碼：

```python
# importing the requests library
import requests

# Step 1: Fetch an access token from an MSI-enabled Azure resource      
# Note that the resource here is https://vault.azure.net for the public cloud, and api-version is 2018-02-01
MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"})

# Extracting data in JSON format 
# This request gets an access token from Azure Active Directory by using the local MSI endpoint
data = r.json()

# Step 2: Pass the access token received from the previous HTTP GET call to the key vault
KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})

print(kvSecret.json()["value"])
```

上述程式碼會執行兩步驟的程序：

   1. 從 VM 上的本機 MSI 端點擷取權杖。 然後，端點會從 Azure Active Directory 擷取權杖。
   1. 將權杖傳至金鑰保存庫並擷取祕密。

執行下列命令。 您應該會看到祕密值。

```console
python Sample.py
```

在本教學課程中，您已了解如何搭配使用 Azure Key Vault 與在 Linux 虛擬機器上執行的 Python 應用程式。

## <a name="clean-up-resources"></a>清除資源

請刪除不再需要的資源群組、虛擬機器和所有相關的資源。 若要這樣做，請選取 VM 的資源群組，然後選取 [刪除]  。

使用 `az keyvault delete` 命令刪除金鑰保存庫：

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
