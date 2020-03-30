---
title: 靜態加密應用程式源
description: 在 Azure 存儲中加密應用程式資料並將其部署為包檔。
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 62179e900ace0d6d7b8b1f07e8f0ab685508f991
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408719"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>使用客戶管理的金鑰進行靜態加密

在靜態時加密函數應用的應用程式資料需要 Azure 存儲帳戶和 Azure 金鑰保存庫。 當您從部署包運行應用時，將使用這些服務。

  - [Azure 存儲提供靜態加密](../storage/common/storage-service-encryption.md)。 您可以使用系統提供的金鑰或您自己的客戶管理的金鑰。 這是應用程式資料在 Azure 中的函數應用中未運行時存儲的位置。
  - [從部署包運行]（run-functions-from-deployment-package.md）是應用服務的一個部署功能。 它允許您使用共用訪問簽名 （SAS） URL 從 Azure 存儲帳戶部署網站內容。
  - [金鑰保存庫引用](../app-service/app-service-key-vault-references.md)是應用服務的安全功能。 它允許您在運行時作為應用程式設定導入機密。 使用此選項可加密 Azure 存儲帳戶的 SAS URL。

## <a name="set-up-encryption-at-rest"></a>設置靜態加密

### <a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶

首先，[創建一個 Azure 存儲帳戶](../storage/common/storage-account-create.md)[，並使用客戶託管金鑰組其進行加密](../storage/common/encryption-customer-managed-keys.md)。 創建存儲帳戶後，使用 Azure[存儲資源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)上載包檔。

接下來，使用存儲資源管理器生成[SAS](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer)。 

> [!NOTE]
> 保存此 SAS URL，稍後用於在運行時啟用部署包的安全訪問。

### <a name="configure-running-from-a-package-from-your-storage-account"></a>配置從存儲帳戶的包運行
  
將檔上載到 Blob 存儲並具有檔的 SAS URL 後，請將`WEBSITE_RUN_FROM_PACKAGE`應用程式設定設置為 SAS URL。 下面的示例通過使用 Azure CLI 進行：

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

添加此應用程式設定會導致函數應用重新開機。 重新開機應用後，流覽到它並確保應用已使用部署包正確啟動。 如果應用程式未正確啟動，請參閱["從包運行疑難排解指南](run-functions-from-deployment-package.md#troubleshooting)"。

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>使用金鑰保存庫引用加密應用程式設定

現在，您可以將`WEBSITE_RUN_FROM_PACKAGE`應用程式設定的值替換為對 SAS 編碼 URL 的金鑰保存庫引用。 這樣可以保持金鑰保存庫中的 SAS URL 加密，從而提供額外的安全層。

1. 使用以下[`az keyvault create`](/cli/azure/keyvault#az-keyvault-create)命令創建金鑰保存庫實例。       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. 按照[以下說明授予應用](../app-service/app-service-key-vault-references.md#granting-your-app-access-to-key-vault)對金鑰保存庫的存取權限：

1. 使用以下[`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set)命令將外部 URL 添加為金鑰保存庫中的機密：   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  使用以下[`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set)命令創建具有該值`WEBSITE_RUN_FROM_PACKAGE`的應用程式設定作為對外部 URL 的金鑰保存庫引用：

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>`將在上一個`az keyvault secret set`命令的輸出中。

更新此應用程式設定會導致函數應用重新開機。 重新開機應用後，流覽到它請確保使用金鑰保存庫引用正確啟動。

## <a name="how-to-rotate-the-access-token"></a>如何旋轉訪問權杖

最佳做法是定期輪換存儲帳戶的 SAS 金鑰。 為了確保函數應用不會無意中失去訪問，還必須更新金鑰保存庫中的 SAS URL。

1. 通過在 Azure 門戶中導航到存儲帳戶來旋轉 SAS 金鑰。 在 **"設置** > **便捷鍵**"下，按一下該圖示以旋轉 SAS 鍵。

1. 複製新的 SAS URL，並使用以下命令在金鑰保存庫中設置更新的 SAS URL：

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. 將應用程式設定中的金鑰保存庫引用更新為新的秘密版本：

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>`將在上一個`az keyvault secret set`命令的輸出中。

## <a name="how-to-revoke-the-function-apps-data-access"></a>如何撤銷函數應用的資料訪問

有兩種方法可以撤銷函數應用對存儲帳戶的存取權限。 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>旋轉 Azure 存儲帳戶的 SAS 金鑰

如果旋轉存儲帳戶的 SAS 金鑰，則函數應用將不再有權訪問存儲帳戶，但它將繼續使用包檔的最後一個下載版本運行。 重新開機函數應用以清除上次下載的版本。

### <a name="remove-the-function-apps-access-to-key-vault"></a>刪除功能應用對金鑰保存庫的存取權限

您可以通過禁用函數應用對金鑰保存庫的訪問來撤銷函數應用對網站資料的訪問。 為此，請刪除函數應用標識的訪問策略。 這與之前在配置金鑰保存庫引用時創建的相同標識相同。

## <a name="summary"></a>總結

您的應用程式檔現在在存儲帳戶中靜態加密。 當函數應用啟動時，它會從金鑰保存庫檢索 SAS URL。 最後，函數應用從存儲帳戶載入應用程式檔。 

如果需要撤銷函數應用對存儲帳戶的存取權限，可以撤銷對金鑰保存庫的存取權限或輪換存儲帳戶金鑰，這會使 SAS URL 失效。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="is-there-any-additional-charge-for-running-my-function-app-from-the-deployment-package"></a>從部署包運行我的函數應用需要額外付費嗎？

僅與 Azure 存儲帳戶關聯的成本和任何適用的出口費用。

### <a name="how-does-running-from-the-deployment-package-affect-my-function-app"></a>從部署包運行如何影響我的函數應用？

- 從部署包運行應用可使`wwwroot/`唯讀。 應用嘗試寫入此目錄時收到錯誤。
- 不支援 TAR 和 GZIP 格式。
- 此功能與本機快取不相容。

## <a name="next-steps"></a>後續步驟

- [應用服務的關鍵保存庫引用](../app-service/app-service-key-vault-references.md)
- [靜態資料的 Azure 存儲加密](../storage/common/storage-service-encryption.md)
