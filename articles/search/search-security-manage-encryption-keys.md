---
title: 使用客戶管理的金鑰進行待用加密
titleSuffix: Azure Cognitive Search
description: 使用您在 Azure Key Vault 中建立和管理的索引鍵，在 Azure 認知搜尋中補充索引和同義字對應的伺服器端加密。
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/26/2020
ms.custom: references_regions
ms.openlocfilehash: fdc0ae3fef2fb70b7372ab4fb28497ea6a6400a4
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635427"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>在 Azure 認知搜尋中設定客戶管理的金鑰進行資料加密

Azure 認知搜尋會使用 [服務管理的金鑰](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components)自動加密待用的索引內容。 如果需要更多保護，您可以使用您在 Azure Key Vault 中建立和管理的金鑰，以額外的加密層補充預設加密。 本文將逐步引導您完成設定 CMK 加密的步驟。

CMK 加密相依于 [Azure Key Vault](../key-vault/general/overview.md)。 您可以建立自己的加密金鑰，然後將其儲存在金鑰保存庫中，或是使用 Azure Key Vault 的 API 來產生加密金鑰。 使用 Azure Key Vault，如果您 [啟用記錄功能](../key-vault/general/logging.md)，也可以審核金鑰使用方式。  

使用客戶管理的金鑰進行加密時，會在建立這些物件時套用至個別的索引或同義字地圖，而不會在搜尋服務層級本身指定。 只有新的物件可以加密。 您無法加密已存在的內容。

金鑰不一定要位於相同的金鑰保存庫中。 單一搜尋服務可以裝載多個加密的索引或同義字對應，每個都使用自己的客戶管理加密金鑰進行加密，並儲存在不同的金鑰保存庫中。 您也可以在相同的服務中，使用客戶管理的金鑰來加密索引和同義字對應。

>[!Important]
> 如果您執行客戶管理的金鑰，請務必在例行金鑰保存庫金鑰的例行輪替期間遵循嚴格的程式，並 Active Directory 應用程式秘密和註冊。 在刪除舊的內容之前，請一律更新所有加密的內容，以使用新的秘密和金鑰。 如果您錯過這個步驟，您的內容就無法解密。

## <a name="double-encryption"></a>雙重加密

針對在2020年8月1日之後建立的服務，以及在特定區域中，CMK 加密的範圍包含暫存磁片，目前可在下欄區域中取得 [完整的雙重加密](search-security-overview.md#double-encryption)： 

+ 美國西部 2
+ 美國東部
+ 美國中南部
+ US Gov 維吉尼亞州
+ US Gov 亞利桑那州

如果您使用不同的區域，或在8月1日之前建立的服務，則您的 CMK 加密僅限於資料磁片，但不包括服務使用的暫存磁片。

## <a name="prerequisites"></a>必要條件

此範例會使用下列工具和服務。 

+ [建立認知搜尋服務](search-create-service-portal.md) 或 [尋找現有](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)的辨識項。 

+ [建立 Azure Key Vault 資源](../key-vault/secrets/quick-create-portal.md#create-a-vault) 或尋找現有資源。 Key Vault 和認知搜尋都必須位於相同的訂用帳戶中。 金鑰保存庫必須啟用虛 **刪除** 和 **清除保護** 。

+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 註冊應用程式，並建立應用程式用來驗證的秘密字串。 如果您沒有帳戶，請 [設定一個新的租](../active-directory/develop/quickstart-create-new-tenant.md)使用者。

您應該有可建立加密物件的搜尋應用程式。 在此程式碼中，您將參考金鑰保存庫金鑰，並 Active Directory 註冊資訊。 此程式碼可以是可運作的應用程式，或原型程式碼，例如 [c # 程式碼範例 DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)。

> [!TIP]
> 您可以使用 [Postman](search-get-started-postman.md) 或 [AZURE POWERSHELL](./search-get-started-powershell.md) 來呼叫 REST api，以建立包含加密金鑰參數的索引和同義字地圖。 目前沒有可將索引鍵加入至索引或同義字對應的入口網站支援。

## <a name="1---enable-key-recovery"></a>1-啟用金鑰復原

由於使用客戶管理的金鑰進行加密的本質，如果刪除您的 Azure Key vault 金鑰，就不會有任何人可以取出您的資料。 若要防止意外刪除 Key Vault 金鑰所造成的資料遺失，必須在金鑰保存庫上啟用虛刪除和清除保護。 預設會啟用虛刪除，因此您只會在刻意停用時才會遇到問題。 預設不會啟用清除保護，但 Azure 認知搜尋 CMK 加密需要它。 如需詳細資訊，請參閱虛 [刪除](../key-vault/general/soft-delete-overview.md) 和 [清除保護](../key-vault/general/soft-delete-overview.md#purge-protection) 概述。

您可以使用入口網站、PowerShell 或 Azure CLI 命令來設定這兩個屬性。

### <a name="using-azure-portal"></a>使用 Azure 入口網站

1. 登[入 Azure 入口網站](https://portal.azure.com)，然後開啟您的金鑰保存庫總覽頁面。

1. 在 [ **概要] 頁面的** [ **基本** 資訊] 下，啟用虛 **刪除** 和 **清除保護** 。

### <a name="using-powershell"></a>使用 PowerShell

1. 執行 `Connect-AzAccount` 以設定您的 Azure 認證。

1. 執行下列命令以連線到您的金鑰保存庫， `<vault_name>` 並以有效的名稱取代：

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. 建立的 Azure Key Vault 會啟用虛刪除。 如果您的保存庫已停用，請執行下列命令：

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. 啟用清除保護：

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. 儲存您的更新：

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>使用 Azure CLI

+ 如果您安裝了 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)，您可以執行下列命令來啟用必要的屬性。

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2-在 Key Vault 中建立金鑰

如果您在 Azure Key Vault 中已經有金鑰，請略過此步驟。

1. 登[入 Azure 入口網站](https://portal.azure.com)，然後開啟您的金鑰保存庫總覽頁面。

1. 選取左邊的索引 **鍵** ，然後選取 [ **+ 產生/匯入** ]。

1. 在 [建立金鑰]  窗格中，從 [選項]  清單中選擇您要用來建立金鑰的方法。 您可以 [產生]  新的金鑰、[上傳]  現有金鑰，或使用 [還原備份]  來選取金鑰的備份。

1. 輸入您的金鑰 **名稱** ，並選擇性地選取其他索引鍵屬性。

1. 選取 [建立]  以開始部署。

1. 記下金鑰識別碼，它是由 **金鑰值 Uri** 、 **金鑰名稱** 和 **金鑰版本** 所組成。 在 Azure 認知搜尋中，您將需要用來定義加密索引的識別碼。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="建立新的金鑰保存庫金鑰":::

## <a name="3---register-an-app-in-active-directory"></a>3-在 Active Directory 中註冊應用程式

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找訂用帳戶的 Azure Active Directory 資源。

1. 在左側的 [ **管理** ] 底下，選取 [ **應用程式註冊** ]，然後選取 [ **新增註冊** ]。

1. 為註冊提供名稱，可能是類似于搜尋應用程式名稱的名稱。 選取 [註冊]。

1. 一旦建立應用程式註冊，請複製應用程式識別碼。 您必須將此字串提供給您的應用程式。 

   如果您正在逐步執行 [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)，請將此值貼入檔案中的 **appsettings.js** 。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="建立新的金鑰保存庫金鑰":::

1. 接下來，選取左側 **& 秘密的憑證** 。

1. 選取 [新增用戶端密碼]。 為秘密提供顯示名稱，然後選取 [ **新增** ]。

1. 複製應用程式秘密。 如果您要逐步執行範例，請將此值貼入檔案中的 **appsettings.js** 。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="建立新的金鑰保存庫金鑰":::

## <a name="4---grant-key-access-permissions"></a>4-授與金鑰存取權限

在此步驟中，您將在 Key Vault 中建立存取原則。 此原則會為您註冊的應用程式提供 Active Directory 的許可權，以使用客戶管理的金鑰。

您可以在任何指定時間撤銷存取權限。 一旦撤銷之後，任何使用該金鑰保存庫的搜尋服務索引或同義字對應都會變成無法使用。 稍後還原 Key vault 存取權限將會還原 index\synonym 對應存取。 如需詳細資訊，請參閱 [安全存取金鑰保存庫](../key-vault/general/secure-your-key-vault.md)。

1. 仍在 Azure 入口網站中，開啟您的金鑰保存庫 **總覽** 頁面。 

1. 選取左側的 **存取原則** ，然後選取 [ **+ 新增存取原則** ]。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="建立新的金鑰保存庫金鑰":::

1. 選擇 [ **選取主體** ]，然後選取您向 Active Directory 註冊的應用程式。 您可以依名稱搜尋它。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="建立新的金鑰保存庫金鑰":::

1. 在 [ **金鑰許可權** ] 中，選擇 [ *取得* ]、[解除包裝 *金鑰* 和 *包裝金鑰* ]。

1. 在 [ **秘密許可權** ] 中，選取 [ *取得* ]。

1. 在 [ **憑證許可權** ] 中，選取 [ *取得* ]。

1. 選取 [ **新增** ]，然後按一下 [ **儲存** ]。

> [!Important]
> Azure 認知搜尋中的加密內容設定為使用特定的 Azure Key Vault 金鑰搭配特定 **版本** 。 如果您變更索引鍵或版本，則必須先更新索引或同義字對應，才能使用新的 key\version， **然後再** 刪除先前的 key\version。 如果無法這樣做，將會導致無法使用索引或同義字地圖，當金鑰存取遺失時，您將無法解密內容。

## <a name="5---encrypt-content"></a>5-加密內容

若要在索引或同義字地圖上加入客戶管理的金鑰，請使用 REST API 或 SDK 來建立其定義包含的物件 `encryptionKey` 。

這個範例會使用 REST API，以及 Azure Key Vault 和 Azure Active Directory 的值：

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

> [!Note]
> 這些金鑰保存庫的詳細資料都不會被視為秘密，而且可以藉由流覽至 Azure 入口網站中相關的 Azure Key Vault 金鑰頁面來輕鬆取出。

## <a name="rest-examples"></a>REST 範例

本節顯示加密索引和同義字對應的完整 JSON

### <a name="index-encryption"></a>索引加密

您可以在 [Create index (REST API) ](/rest/api/searchservice/create-index)找到透過 REST API 建立新索引的詳細資料，其中唯一的差別在於將加密金鑰詳細資料指定為索引定義的一部分：

```json
{
 "name": "hotels",  
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

您現在可以傳送索引建立要求，然後開始正常使用索引。

### <a name="synonym-map-encryption"></a>同義字地圖加密

若要透過 REST API 建立新同義字地圖的詳細資料，請參閱 [Create 同義字 map (REST API) ](/rest/api/searchservice/create-synonym-map)，其中唯一的差別在於將加密金鑰詳細資料指定為同義字對應定義的一部分： 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "activeDirectoryAccessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

您現在可以傳送同義字對應建立要求，然後正常地開始使用它。

>[!Important]
> 雖然 `encryptionKey` 無法新增至現有的搜尋索引或同義字對應，但它可能會藉由提供三個金鑰保存庫詳細資料的不同值來更新 (例如，更新金鑰版本) 。 變更為新的 Key Vault 金鑰或新的金鑰版本時，必須先更新使用該金鑰的任何搜尋索引或同義字對應，才能使用新的 key\version， **然後再** 刪除先前的 key\version。 如果無法這樣做，將無法使用索引或同義字對應，因為它在金鑰存取遺失之後將無法解密內容。 雖然稍後還原金鑰保存庫存取權限會還原內容存取。

## <a name="simpler-alternative-trusted-service"></a>更簡單的替代方法：信任的服務

視租使用者設定和驗證需求而定，您可能可以實行更簡單的方法來存取金鑰保存庫金鑰。 您可以為其啟用系統管理的身分識別，而不是建立和使用 Active Directory 應用程式，而是讓搜尋服務成為受信任的服務。 然後，您會使用受信任的搜尋服務做為安全性原則，而不是 AD 註冊的應用程式，以存取金鑰保存庫金鑰。

這種方法可讓您略過應用程式註冊和應用程式秘密的步驟，並將加密金鑰定義簡化為只 (URI、保存庫名稱、金鑰版本) 的金鑰保存庫元件。

一般而言，受控識別可讓您的搜尋服務驗證 Azure Key Vault，而不需要在程式碼中儲存 (ApplicationID 或 ApplicationSecret) 的認證。 這種受控識別的生命週期會系結至搜尋服務的生命週期，而此服務只能有一個受控識別。 如需受控識別如何運作的詳細資訊，請參閱 [什麼是適用于 Azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md)識別。

1. 將您的搜尋服務設為受信任的服務。

   ![開啟系統指派的受控識別](./media/search-managed-identities/turn-on-system-assigned-identity.png "開啟系統指派的受控識別")

1. 在 Azure Key Vault 中設定存取原則時，請選擇受信任的搜尋服務作為原則 (，而不是以 AD 註冊的應用程式) 。 指派相同的許可權 (多個取得、包裝、解除包裝) ，如同授與存取金鑰許可權步驟中的指示。

1. 使用 `encryptionKey` 省略 Active Directory 屬性的簡化結構。

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

防止您採用這種簡化方法的條件包括：

+ 您無法直接將您的搜尋服務存取權限授與金鑰保存庫 (例如，如果搜尋服務與 Azure Key Vault) 位於不同的 Active Directory 租使用者中。

+ 需要單一搜尋服務來裝載多個加密 indexes\synonym 對應，每個都使用不同金鑰保存庫中的不同金鑰，每個金鑰保存庫都必須使用不同的身分 **識別** 來進行驗證。 因為搜尋服務只能有一個受控識別，所以多個身分識別的需求會 disqualifies 您案例的簡化方法。  

## <a name="work-with-encrypted-content"></a>使用加密的內容

使用 CMK 加密時，您會發現索引和查詢的延遲，因為額外的加密/解密工作。 Azure 認知搜尋不會記錄加密活動，但您可以透過 key vault 記錄來監視金鑰存取權。 建議您在金鑰保存庫設定中 [啟用記錄功能](../key-vault/general/logging.md) 。

預期會在一段時間後進行金鑰輪替。 每當您輪替金鑰時，請務必遵循下列順序：

1. [判斷索引或同義字地圖所使用的索引鍵](search-security-get-encryption-keys.md)。
1. [在 key vault 中建立新的金鑰](../key-vault/keys/quick-create-portal.md)，但保留可用的原始金鑰。
1. 更新索引或同義字地圖上[的 encryptionKey 屬性](/rest/api/searchservice/update-index)，以使用新的值。 只有原本使用此屬性建立的物件可以更新為使用不同的值。
1. 在金鑰保存庫中停用或刪除先前的金鑰。 監視金鑰存取權來確認正在使用新的金鑰。

基於效能考慮，搜尋服務會快取金鑰，最多可達數小時。 如果您在未提供新金鑰的情況下停用或刪除該金鑰，則查詢會繼續以暫時的方式運作，直到快取到期為止。 不過，一旦搜尋服務無法解密內容，您將會收到下列訊息：「禁止存取。 使用的查詢金鑰可能已遭撤銷-請重試。」 

## <a name="next-steps"></a>後續步驟

如果您不熟悉 Azure 安全性架構，請參閱「 [Azure 安全性檔案](../security/index.yml)」，特別是本文：

> [!div class="nextstepaction"]
> [資料靜態加密](../security/fundamentals/encryption-atrest.md)