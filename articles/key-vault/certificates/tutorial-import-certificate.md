---
title: 教學課程 - 使用 Azure 入口網站在 Key Vault 中匯入憑證 | Microsoft Docs
description: 示範如何在 Azure Key Vault 中匯入憑證的教學課程
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 626d7f161d23e6105eea0ca160cd2c9be0ed0ea0
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935932"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>教學課程：在 Azure Key Vault 中匯入憑證

Azure Key Vault 是一項雲端服務，可為祕密提供安全的存放區。 您也可以安全地儲存金鑰、密碼、憑證和其他祕密。 您可以透過 Azure 入口網站建立和管理 Azure 金鑰保存庫。 在本教學課程中，您會建立金鑰保存庫，然後將其用來匯入憑證。 如需 Key Vault 的詳細資訊，您可以檢閱[概觀](../general/overview.md)。

本教學課程說明如何：

> [!div class="checklist"]
> * 建立金鑰保存庫。
> * 使用入口網站在 Key Vault 中匯入憑證。
> * 使用 CLI 在 Key Vault 中匯入憑證。
> * 使用 PowerShell 在 Key Vault 中匯入憑證。


在開始之前，請先閱讀 [Key Vault 基本概念](../general/basic-concepts.md)。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-vault"></a>建立保存庫

1. 從 Azure 入口網站功能表或 **首頁**，選取 [建立資源]。
2. 在 [搜尋] 方塊中輸入 **Key Vault**。
3. 從結果清單中，選擇 [Key Vault]。
4. 在 [金鑰保存庫] 區段上選擇 [建立]。
5. 在 [建立金鑰保存庫] 區段上提供下列資訊：
    - **Name**：唯一名稱是必要項。 在本快速入門中，我們會使用 **Example-Vault**。 
    - 訂用帳戶：選擇訂用帳戶。
    - 在 [資源群組] 底下，選擇 [新建]，然後輸入資源群組名稱。
    - 在 [位置] 下拉式功能表中選擇位置。
    - 將其他的選項保留預設值。
6. 提供上述資訊之後，請選取 [建立]。

請記下下列兩個屬性：

* **保存庫名稱**：在此範例中，這是 **Example-Vault**。 您將在其他步驟中使用此名稱。
* **保存庫 URI**：在此範例中是 https://example-vault.vault.azure.net/ 。 透過其 REST API 使用保存庫的應用程式必須使用此 URI。

此時，您的 Azure 帳戶是唯一獲得授權在此新保存庫上執行作業的帳戶。

![Key Vault 建立完成後的輸出](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>將憑證匯入至 Key Vault

若要將憑證匯入到保存庫，您必須在磁碟上備有 PEM 或 PFX 憑證檔案。 在本案例中，我們會匯入檔案名稱為 **ExampleCertificate** 的憑證。

> [!IMPORTANT]
> 在 Azure Key Vault 中，支援的憑證格式有 PFX 和 PEM。 
> - .pem 檔案格式包含一或多個 X509 憑證檔案。
> - .pfx 檔案格式則是一種封存檔案格式，可將數個密碼編譯物件儲存在單一檔案中，亦即伺服器憑證 (針對網域所核發)、相匹配的私密金鑰，而且可選擇性地包含中繼 CA。  

1. 在 Key Vault 屬性頁面上，選取 [憑證]。
2. 按一下 [產生/匯入]。
3. 在 [建立憑證] 畫面上，選擇下列值：
    - **憑證建立方法**：匯入。
    - **憑證名稱**：ExampleCertificate。
    - **上傳憑證檔案**：從磁碟選取憑證檔案
    - **密碼**：如果您要上傳受密碼保護的憑證檔案，請在這裡提供該密碼。 否則請留白。 成功匯入憑證檔案之後，金鑰保存庫就會移除該密碼。
4. 按一下 [建立]。

![憑證屬性](../media/certificates/tutorial-import-cert/cert-import.png)

藉由使用 **匯入** 方法來新增憑證，Azure 金鑰保存庫會自動填入憑證參數 (也就是有效期間、簽發者名稱、啟用日期等)。

一旦收到已成功匯入憑證的訊息，即可按一下清單上的憑證以檢視其屬性。 

![顯示檢視憑證屬性位置所在的螢幕擷取畫面。](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>使用 Azure CLI 來匯入憑證

將憑證匯入到指定的金鑰保存庫。 若要將現有包含私密金鑰的有效憑證匯入到 Azure Key Vault，所要匯入的憑證可以是 PFX 或 PEM 格式。 如果憑證是 PEM 格式，則 PEM 檔案必須包含金鑰以及 x509 憑證。 進行此作業需要憑證/匯入權限。

```azurecli
az keyvault certificate import --file
                               --name
                               --vault-name
                               [--disabled {false, true}]
                               [--only-show-errors]
                               [--password]
                               [--policy]
                               [--subscription]
                               [--tags]
```

深入了解[參數](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import)。

匯入憑證之後，您可以使用[憑證顯示](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show)來檢視憑證


```azurecli
az keyvault certificate show [--id]
                             [--name]
                             [--only-show-errors]
                             [--subscription]
                             [--vault-name]
                             [--version]
```

現在，您已建立金鑰保存庫、匯入憑證，並已檢視憑證的屬性。

## <a name="import-a-certificate-using-azure-powershell"></a>使用 Azure PowerShell 匯入憑證

```
Import-AzureKeyVaultCertificate
      [-VaultName] <String>
      [-Name] <String>
      -FilePath <String>
      [-Password <SecureString>]
      [-Tag <Hashtable>]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```

深入了解[參數](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0)。


## <a name="clean-up-resources"></a>清除資源

其他 Key Vault 快速入門和教學課程會以本快速入門為基礎。 如果您打算繼續進行後續的快速入門和教學課程，您可以讓這些資源留在原處。
如果不再需要，請刪除資源群組，這會刪除 Key Vault 和相關資源。 若要透過入口網站刪除資源群組：

1. 在入口網站頂端的 [搜尋] 方塊中，輸入資源群組的名稱。 當您在搜尋結果中看到本快速入門中使用的資源群組時，請加以選取。
2. 選取 [刪除資源群組]。
3. 在 [輸入資源群組名稱:] 方塊中輸入資源群組的名稱，然後選取 [刪除]。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立 Key Vault 並於其中匯入憑證。 若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

- 深入了解如何[在 Azure Key Vault 中管理憑證建立](./create-certificate-scenarios.md)
- 請參閱[使用 REST API 來匯入憑證](/rest/api/keyvault/importcertificate/importcertificate)的範例
- 請參閱 [Key Vault 安全性概觀](../general/security-overview.md)