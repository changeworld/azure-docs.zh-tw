---
title: 教學課程 - 在 Python 中搭配使用 Azure Key Vault 與 Windows 虛擬機器 | Microsoft Docs
description: 在本教學課程中，您會設定 ASP.NET Core 應用程式以從金鑰保存庫讀取祕密。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/11/2020
ms.author: sudbalas
ms.custom: mvc
ms.openlocfilehash: 00e2f573a85d6926c8941a3ed05eeab1163b9ea1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121094"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>教學課程：在 Python 中搭配使用 Azure Key Vault 與 Windows 虛擬機器

Azure Key Vault 可協助您保護秘密，例如，API 金鑰、存取應用程式時所需的資料庫連接字串、服務和 IT 資源。

在本教學課程中，您將了解如何讓主控台應用程式從 Azure Key Vault 讀取資訊。 若要這樣做，請使用 Azure 資源的受控識別。 

本教學課程說明如何：

> [!div class="checklist"]
> * 建立金鑰保存庫。
> * 將秘密新增至金鑰保存庫。
> * 從金鑰保存庫擷取祕密。
> * 建立 Azure 虛擬機器。
> * 啟用受控識別。
> * 對 VM 身分識別指派權限。

在開始之前，請先閱讀 [Key Vault 基本概念](basic-concepts.md)。 

如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

若為 Windows、Mac 和 Linux：
  * [Git](https://git-scm.com/downloads)
  * 此教學課程需要您在本機執行 Azure CLI。 您必須安裝 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級 CLI，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。

## <a name="about-managed-service-identity"></a>關於受控服務識別

Azure Key Vault 可安全地儲存認證，因此認證不會在程式碼內顯示出來。 不過，您需要向 Azure Key Vault 驗證才能擷取您的金鑰。 若要向 Key Vault 進行驗證，您必須要有認證。 這是典型的啟動程序難題。 受控服務識別 (MSI) 可藉由提供可簡化此程序的「啟動程序身分識別」  來解決此問題。

當您針對 Azure 服務 (例如，Azure 虛擬機器、Azure App Service 或 Azure Functions) 啟用 MSI 時，Azure 會建立[服務主體](basic-concepts.md)。 MSI 會在 Azure Active Directory (Azure AD) 中為服務執行個體執行此作業，並將服務主體的認證插入該執行個體中。 

![MSI](../media/MSI.png)

接下來，為了取得存取權杖，您的程式碼會呼叫 Azure 資源上可用的本機中繼資料服務。 為了向 Azure Key Vault 服務進行驗證，您的程式碼會使用從本機 MSI 端點取得的存取權杖。 

## <a name="log-in-to-azure"></a>登入 Azure

若要使用 Azure CLI 登入 Azure，請輸入：

```azurecli
az login
```

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用 [az group create](/cli/azure/group#az-group-create) 命令來建立資源群組。 

選取資源群組名稱，並填入預留位置。 下列範例會在美國西部位置建立一個資源群組：

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

您會在本教學課程中使用新建立的資源群組。

## <a name="create-a-key-vault"></a>建立金鑰保存庫

為了在您於上一個步驟中建立的資源群組內建立金鑰保存庫，請提供下列資訊：

* 金鑰保存庫名稱：由 3 到 24 個字元組成的字串，只能包含數字 (0-9)、字母 (a-z、A-Z) 和連字號 (-)
* 資源群組名稱
* 位置：美國西部 

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
此時，您的 Azure 帳戶是唯一獲得授權在這個新的金鑰保存庫上執行作業的帳戶。

## <a name="add-a-secret-to-the-key-vault"></a>將秘密新增至金鑰保存庫

我們將新增密碼，以協助說明其運作方式。 此祕密可以是 SQL 連接字串，也可以是任何您必須安全保存並可供應用程式使用的其他資訊。

為了在金鑰保存庫中建立名為 **AppSecret** 的秘密，請輸入下列命令：

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

此秘密會儲存值 **MySecret**。

## <a name="create-a-virtual-machine"></a>建立虛擬機器
您可以使用下列其中一個方法來建立虛擬機器：

* [Azure CLI](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Azure 入口網站](../../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>將身分識別指派給 VM
在此步驟中，請藉由在 Azure CLI 中執行下列命令，為虛擬機器建立系統指派的身分識別：

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

請注意下列程式碼中所顯示的系統指派身分識別。 上述命令的輸出會是： 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>對 VM 身分識別指派權限
現在，您可以執行下列命令來對金鑰保存庫指派先前所建立的身分識別權限：

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>登入虛擬機器

若要登入虛擬機器，請遵循[連接和登入執行 Windows 的 Azure 虛擬機器](../../virtual-machines/windows/connect-logon.md)中的指示。

## <a name="create-and-run-a-sample-python-app"></a>建立和執行 Python 應用程式範例

下一個區段是名為 Sample.py  的範例檔案。 此檔案會使用[要求](https://2.python-requests.org/en/master/)程式庫來發出 HTTP GET 呼叫。

## <a name="edit-samplepy"></a>編輯 Sample.py

在建立 Sample.py  後，請開啟檔案，然後複製此區段中的程式碼。 

此程式碼會顯示雙步驟程序：
1. 從 VM 上的本機 MSI 端點擷取權杖。  
  這樣做也會從 Azure AD 擷取權杖。
1. 將權杖傳遞給金鑰保存庫，然後擷取祕密。 

```python
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.
    # Resources with an MSI configured recieve an AAD access token by using the Azure Instance Metadata Service (IMDS)
    # IMDS provides an endpoint accessible to all IaaS VMs using a non-routable well-known IP Address
    # To learn more about IMDS and MSI Authentication see the following link: https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://{YOUR KEY VAULT NAME}.vault.azure.net/secrets/{YOUR SECRET NAME}?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

您可以執行下列程式碼來顯示祕密值： 

```console
python Sample.py
```

上述程式碼會示範如何在 Windows 虛擬機器中使用 Azure Key Vault 執行作業。 

## <a name="clean-up-resources"></a>清除資源

不再需要時，請刪除虛擬機器和金鑰保存庫。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
