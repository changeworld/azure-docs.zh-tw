---
title: 加密待用應用程式來源
description: 在 Azure 儲存體中加密您的應用程式資料，並將它部署為封裝檔案。
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 6dfb56a5ef6b1ab3cfbd0762bf7816e37ce0aa39
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081877"
---
# <a name="encrypt-your-application-data-at-rest-using-customer-managed-keys"></a>使用客戶管理的金鑰將您的應用程式資料加密

將您的函式應用程式待用資料加密需要 Azure 儲存體帳戶和 Azure Key Vault。 當您從部署套件執行應用程式時，會使用這些服務。

  - [Azure 儲存體提供待用加密](../storage/common/storage-service-encryption.md)。 您可以使用系統提供的金鑰，或您自己的客戶管理金鑰。 這是您的應用程式資料在 Azure 中的函式應用程式未執行時儲存的位置。
  - [從部署套件執行]（（run-functions-from-deployment-package.md）是 App Service 的部署功能。 它可讓您使用共用存取簽章（SAS） URL，從 Azure 儲存體帳戶部署您的網站內容。
  - [Key Vault 參考](../app-service/app-service-key-vault-references.md)是 App Service 的安全性功能。 它可讓您在執行時間將秘密匯入為應用程式設定。 使用此來加密 Azure 儲存體帳戶的 SAS URL。

## <a name="set-up-encryption-at-rest"></a>設定待用加密

### <a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶

首先，[建立 Azure 儲存體帳戶](../storage/common/storage-account-create.md)，並[使用客戶管理的金鑰進行加密](../storage/common/encryption-customer-managed-keys.md)。 建立儲存體帳戶之後，請使用[Azure 儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md)來上傳封裝檔案。

接下來，使用儲存體總管來[產生 SAS](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer)。 

> [!NOTE]
> 儲存此 SAS URL，稍後用來在執行時間啟用部署套件的安全存取。

### <a name="configure-running-from-a-package-from-your-storage-account"></a>設定從儲存體帳戶的封裝執行
  
將檔案上傳至 Blob 儲存體並擁有該檔案的 SAS URL 後，請將 `WEBSITE_RUN_FROM_PACKAGE` 應用程式設定設為 SAS url。 下列範例會使用 Azure CLI 來執行此動作：

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

新增此應用程式設定會使您的函式應用程式重新開機。 應用程式重新開機之後，請流覽至該應用程式，並確定已使用部署套件正確啟動應用程式。 如果應用程式未正確啟動，請參閱[從套件執行疑難排解指南](run-functions-from-deployment-package.md#troubleshooting)。

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>使用 Key Vault 參考來加密應用程式設定

現在您可以 `WEBSITE_RUN_FROM_PACKAGE` 使用 SAS 編碼 URL 的 Key Vault 參考來取代應用程式設定的值。 這會在 Key Vault 中將 SAS URL 保持加密，以提供額外的安全性層級。

1. 使用下列 [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) 命令來建立 Key Vault 實例。       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. 請遵循[這些指示，將您的應用程式存取權授](../app-service/app-service-key-vault-references.md#granting-your-app-access-to-key-vault)與您的金鑰保存庫：

1. 使用下列 [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) 命令，將您的外部 URL 新增為金鑰保存庫中的秘密：   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  使用下列 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) 命令來建立 `WEBSITE_RUN_FROM_PACKAGE` 應用程式設定，並將其值做為外部 URL 的 Key Vault 參考：

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>`會在前一個命令的輸出中 `az keyvault secret set` 。

更新此應用程式設定會使您的函數應用程式重新開機。 應用程式重新開機之後，請流覽至它，確定它已使用 Key Vault 參考正確啟動。

## <a name="how-to-rotate-the-access-token"></a>如何旋轉存取權杖

最佳做法是定期輪替儲存體帳戶的 SAS 金鑰。 若要確保函式應用程式不會不慎鬆散存取，您也必須更新 Key Vault 中的 SAS URL。

1. 藉由流覽至您在 Azure 入口網站中的儲存體帳戶來輪替 SAS 金鑰。 在 [**設定**]  >  [**存取金鑰**] 底下，按一下圖示以旋轉 SAS 金鑰。

1. 複製新的 SAS URL，然後使用下列命令，在您的金鑰保存庫中設定更新的 SAS URL：

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. 將應用程式設定中的金鑰保存庫參考更新為新的秘密版本：

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>`會在前一個命令的輸出中 `az keyvault secret set` 。

## <a name="how-to-revoke-the-function-apps-data-access"></a>如何撤銷函數應用程式的資料存取

有兩種方法可以撤銷函數應用程式對儲存體帳戶的存取權。 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>輪替 Azure 儲存體帳戶的 SAS 金鑰

如果儲存體帳戶的 SAS 金鑰已輪替，函數應用程式將不再具有儲存體帳戶的存取權，但它會繼續以上次下載的套件檔案版本執行。 重新開機函數應用程式，以清除上次下載的版本。

### <a name="remove-the-function-apps-access-to-key-vault"></a>移除函數應用程式對 Key Vault 的存取權

您可以藉由停用函式應用程式對 Key Vault 的存取權，撤銷函數應用程式對網站資料的存取權。 若要這麼做，請移除函數應用程式身分識別的存取原則。 這是您稍早在設定金鑰保存庫參考時所建立的相同身分識別。

## <a name="summary"></a>總結

您的應用程式檔現在會在您的儲存體帳戶中進行待用加密。 當您的函式應用程式啟動時，它會從您的金鑰保存庫抓取 SAS URL。 最後，函數應用程式會從儲存體帳戶載入應用程式檔。 

如果您需要撤銷函式應用程式對儲存體帳戶的存取權，您可以撤銷金鑰保存庫的存取權，或輪替儲存體帳戶金鑰，這樣會使 SAS URL 失效。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="is-there-any-additional-charge-for-running-my-function-app-from-the-deployment-package"></a>從部署套件執行我的函式應用程式是否有任何額外費用？

僅限與 Azure 儲存體帳戶相關聯的成本，以及任何適用的出口費用。

### <a name="how-does-running-from-the-deployment-package-affect-my-function-app"></a>從部署套件執行如何影響我的函數應用程式？

- 從部署套件執行應用程式會使其成為 `wwwroot/` 唯讀狀態。 當您的應用程式嘗試寫入此目錄時，會收到錯誤。
- 不支援 TAR 和 GZIP 格式。
- 這項功能與本機快取不相容。

## <a name="next-steps"></a>後續步驟

- [App Service 的 Key Vault 參考](../app-service/app-service-key-vault-references.md)
- [待用資料的 Azure 儲存體加密](../storage/common/storage-service-encryption.md)
