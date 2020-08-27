---
title: 使用客戶管理的金鑰進行待用加密
titleSuffix: Azure Cognitive Search
description: 使用您在 Azure Key Vault 中建立和管理的索引鍵，在 Azure 認知搜尋中補充索引和同義字對應的伺服器端加密。
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: 2dc7458dd905ff84455927c81b4ea93765d4f5cb
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928814"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>在 Azure 認知搜尋中設定客戶管理的金鑰進行資料加密

Azure 認知搜尋會使用 [服務管理的金鑰](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components)自動加密待用的索引內容。 如果需要更多保護，您可以使用您在 Azure Key Vault 中建立和管理的金鑰，以額外的加密層補充預設加密。 本文將逐步引導您完成設定 CMK 加密的步驟。

CMK 加密相依于 [Azure Key Vault](../key-vault/general/overview.md)。 您可以建立自己的加密金鑰，然後將其儲存在金鑰保存庫中，或是使用 Azure Key Vault 的 API 來產生加密金鑰。 使用 Azure Key Vault，如果您 [啟用記錄功能](../key-vault/general/logging.md)，也可以審核金鑰使用方式。  

使用客戶管理的金鑰進行加密時，會在建立這些物件時套用至個別的索引或同義字地圖，而不會在搜尋服務層級本身指定。 只有新的物件可以加密。 您無法加密已存在的內容。

金鑰不一定要位於相同的金鑰保存庫中。 單一搜尋服務可以裝載多個加密的索引或同義字對應，每個都使用自己的客戶管理加密金鑰進行加密，並儲存在不同的金鑰保存庫中。 您也可以在相同的服務中，使用客戶管理的金鑰來加密索引和同義字對應。 

## <a name="double-encryption"></a>雙重加密

針對在2020年8月1日之後建立的服務，以及在特定區域中，CMK 加密的範圍包含暫存磁片，目前可在下欄區域中取得 [完整的雙重加密](search-security-overview.md#double-encryption)： 

+ 美國西部 2
+ 美國東部
+ 美國中南部
+ US Gov 維吉尼亞州
+ US Gov 亞利桑那州

如果您使用不同的區域，或在8月1日之前建立的服務，則您的 CMK 加密僅限於資料磁片，但不包括服務使用的暫存磁片。

## <a name="prerequisites"></a>必要條件

在此範例中，會使用下列服務和服務。 

+ [建立 Azure 認知搜尋服務](search-create-service-portal.md)，或[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 

+ [建立 Azure Key Vault 資源](../key-vault/secrets/quick-create-portal.md#create-a-vault) ，或在與 Azure 認知搜尋相同的訂用帳戶中尋找現有的保存庫。 這項功能具有相同的訂用帳戶需求。

+ [Azure PowerShell](/powershell/azure/) 或 [Azure CLI](/cli/azure/install-azure-cli) 用於設定工作。

+ 您可以使用[Postman](search-get-started-postman.md)、 [AZURE POWERSHELL](./search-get-started-powershell.md)和[.net SDK preview](https://aka.ms/search-sdk-preview)來呼叫 REST API，以建立包含加密金鑰參數的索引和同義字地圖。 目前沒有可將索引鍵加入至索引或同義字對應的入口網站支援。

>[!Note]
> 由於使用客戶管理的金鑰進行加密的本質，如果刪除您的 Azure Key vault 金鑰，Azure 認知搜尋將無法取得您的資料。 若要防止意外刪除 Key Vault 金鑰所造成的資料遺失，必須在金鑰保存庫上啟用虛刪除和清除保護。 預設會啟用虛刪除，因此您只會在刻意停用時才會遇到問題。 預設不會啟用清除保護，但 Azure 認知搜尋 CMK 加密需要它。 如需詳細資訊，請參閱虛 [刪除](../key-vault/general/soft-delete-overview.md) 和 [清除保護](../key-vault/general/soft-delete-overview.md#purge-protection) 概述。

## <a name="1---enable-key-recovery"></a>1-啟用金鑰復原

金鑰保存庫必須啟用虛 **刪除** 和 **清除保護** 。 您可以使用入口網站或下列 PowerShell 或 Azure CLI 命令來設定這些功能。

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

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2-建立新的金鑰

如果您使用現有的金鑰來加密 Azure 認知搜尋內容，請略過此步驟。

1. 登[入 Azure 入口網站](https://portal.azure.com)，然後開啟您的金鑰保存庫總覽頁面。

1. 從左側導覽窗格中選取 [ **金鑰** ] 設定，然後按一下 [ **+ 產生/匯入**]。

1. 在 [建立金鑰]**** 窗格中，從 [選項]**** 清單中選擇您要用來建立金鑰的方法。 您可以 [產生]**** 新的金鑰、[上傳]**** 現有金鑰，或使用 [還原備份]**** 來選取金鑰的備份。

1. 輸入您的金鑰 **名稱** ，並選擇性地選取其他索引鍵屬性。

1. 按一下 [ **建立** ] 按鈕以開始部署。

記下金鑰識別碼–這是由 **金鑰值 Uri**、 **金鑰名稱**和 **金鑰版本**所組成。 您將需要這些來定義 Azure 認知搜尋中的加密索引。
 
![建立新的金鑰保存庫金鑰](./media/search-manage-encryption-keys/create-new-key-vault-key.png "建立新的金鑰保存庫金鑰")

## <a name="3---create-a-service-identity"></a>3-建立服務身分識別

將身分識別指派給您的搜尋服務，可讓您將 Key Vault 存取權限授與您的搜尋服務。 您的搜尋服務將會使用其身分識別來向 Azure Key vault 進行驗證。

Azure 認知搜尋支援兩種指派身分識別的方式：受控識別或外部管理的 Azure Active Directory 應用程式。 

可能的話，請使用受控識別。 這是將身分識別指派給搜尋服務的最簡單方式，而且在大部分的情況下都應該可以使用。 如果您使用多個索引鍵來編制索引和同義字地圖，或如果您的解決方案是在 disqualifies 身分識別型驗證的分散式架構中，請使用本文結尾所述的 advanced [對外 Azure Active Directory 方法](#aad-app) 。

 一般而言，受控識別可讓您的搜尋服務驗證 Azure Key Vault，而不需要在程式碼中儲存認證。 這種受控識別的生命週期會系結至搜尋服務的生命週期，而此服務只能有一個受控識別。 [深入瞭解受控](../active-directory/managed-identities-azure-resources/overview.md)識別。

1. 登[入 Azure 入口網站](https://portal.azure.com)，然後開啟您的搜尋服務 [總覽] 頁面。 

1. 按一下左方流覽窗格中的 [ **識別** ]，將其狀態變更為 [ **開啟**]，然後按一下 [ **儲存**]。

![啟用受控識別](./media/search-enable-msi/enable-identity-portal.png "啟用受控身分識別")

## <a name="4---grant-key-access-permissions"></a>4-授與金鑰存取權限

若要讓您的搜尋服務使用您的 Key Vault 金鑰，您必須將特定存取權限授與您的搜尋服務。

您可以在任何指定時間撤銷存取權限。 一旦撤銷之後，任何使用該金鑰保存庫的搜尋服務索引或同義字對應都會變成無法使用。 稍後還原 Key vault 存取權限將會還原 index\synonym 對應存取。 如需詳細資訊，請參閱 [安全存取金鑰保存庫](../key-vault/general/secure-your-key-vault.md)。

1. 登[入 Azure 入口網站](https://portal.azure.com)，然後開啟您的金鑰保存庫總覽頁面。 

1. 從左側導覽窗格中選取 [ **存取原則** ] 設定，然後按一下 [ **+ 加入新**的]。

   ![新增金鑰保存庫存取原則](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "新增金鑰保存庫存取原則")

1. 按一下 [ **選取主體** ]，然後選取您的 Azure 認知搜尋服務。 您可以依名稱或在啟用受控識別之後所顯示的物件識別碼來搜尋它。

   ![選取 key vault 存取原則主體](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "選取 key vault 存取原則主體")

1. 按一下 [ **金鑰許可權** ]，然後選取 [ *取得*]、[解除包裝 *金鑰* 和 *包裝金鑰*]。 您可以使用 *Azure Data Lake Storage 或 Azure 儲存體* 範本，快速選取所需的許可權。

   您必須使用下列 [存取權限](../key-vault/keys/about-keys.md#key-operations)來授與 Azure 認知搜尋：

   * *Get* -允許您的搜尋服務在 Key Vault 中取出金鑰的公開部分
   * *包裝金鑰* -允許您的搜尋服務使用您的金鑰來保護內部加密金鑰
   * 解除包裝*金鑰*-允許您的搜尋服務使用您的金鑰來解除包裝內部加密金鑰

   ![選取 key vault 存取原則金鑰許可權](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "選取 key vault 存取原則金鑰許可權")

1. 若為 **秘密許可權**，請選取 [ *取得*]。

1. 針對 [ **憑證許可權**]，選取 [ *取得*]。

1. 按一下 **[確定]** 並 **儲存** 存取原則變更。

> [!Important]
> Azure 認知搜尋中的加密內容設定為使用特定的 Azure Key Vault 金鑰搭配特定 **版本**。 如果您變更索引鍵或版本，則必須先更新索引或同義字對應，才能使用新的 key\version， **然後再** 刪除先前的 key\version。 如果無法這樣做，將會導致無法使用索引或同義字地圖，當金鑰存取遺失時，您將無法解密內容。   

## <a name="5---encrypt-content"></a>5-加密內容

若要在索引或同義字地圖上加入客戶管理的金鑰，您必須使用 [搜尋 REST API](/rest/api/searchservice/) 或 SDK。 入口網站不會公開同義字地圖或加密屬性。 當您使用有效的 API 時，索引和同義字對應都支援最上層的 **encryptionKey** 屬性。 

使用金鑰保存 **庫 Uri**、金鑰 **名稱** 和金鑰 **版本** 的金鑰保存庫金鑰，建立 **encryptionKey** 定義，如下所示：

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> 這些金鑰保存庫的詳細資料都不會被視為秘密，而且可以藉由流覽至 Azure 入口網站中相關的 Azure Key Vault 金鑰頁面來輕鬆取出。

如果您使用 AAD 應用程式來 Key Vault 驗證，而不是使用受控識別，請將 AAD 應用程式 **存取** 認證新增至您的加密金鑰： 
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

## <a name="example-index-encryption"></a>範例：索引加密
若要透過 REST API 建立新索引的詳細資料，請參閱 [Create index (Azure 認知搜尋 REST API) ](/rest/api/searchservice/create-index)，其中唯一的差別在於將加密金鑰詳細資料指定為索引定義的一部分： 

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
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
您現在可以傳送索引建立要求，然後開始正常使用索引。

## <a name="example-synonym-map-encryption"></a>範例：同義字地圖加密

若要透過 REST API 建立新同義字地圖的詳細資料，請參閱 [建立同義字地圖 (Azure 認知搜尋 REST API) ](/rest/api/searchservice/create-synonym-map)，其中唯一的差別在於將加密金鑰詳細資料指定為同義字對應定義的一部分： 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
您現在可以傳送同義字對應建立要求，然後正常地開始使用它。

>[!Important] 
> 雖然 **encryptionKey** 無法新增至現有的 Azure 認知搜尋索引或同義字對應，但它可能會藉由提供三個金鑰保存庫詳細資料的不同值來更新 (例如，更新金鑰版本) 。 變更為新的 Key Vault 金鑰或新的金鑰版本時，任何使用該金鑰的 Azure 認知搜尋索引或同義字對應都必須先更新為使用新的 key\version， **才能** 刪除先前的 key\version。 如果無法這樣做，將無法使用索引或同義字對應，因為它在金鑰存取遺失之後將無法解密內容。   
> 稍後還原金鑰保存庫存取權限將會還原內容存取。

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a> Advanced：使用外部受控的 Azure Active Directory 應用程式

如果無法使用受控識別，您可以建立 Azure Active Directory 的應用程式，其中包含 Azure 認知搜尋服務的安全性主體。 具體而言，在這些情況下，受控識別並非可行：

* 您無法直接將您的搜尋服務存取權限授與金鑰保存庫 (例如，如果搜尋服務與 Azure Key Vault) 位於不同的 Active Directory 租使用者中。

* 需要單一搜尋服務來裝載多個加密 indexes\synonym 對應，每個都使用不同金鑰保存庫中的不同金鑰，每個金鑰保存庫都必須使用不同的身分 **識別** 來進行驗證。 如果不需要使用不同的身分識別來管理不同的金鑰保存庫，請考慮使用上述的受控識別選項。  

為了配合這類拓撲，Azure 認知搜尋支援使用 Azure Active Directory (AAD) 應用程式，在您的搜尋服務與 Key Vault 之間進行驗證。    
若要在入口網站中建立 AAD 應用程式：

1. [建立 Azure Active Directory 應用程式](../active-directory/develop/howto-create-service-principal-portal.md)。

1. 取得建立加密索引所需的[應用程式識別碼和驗證金鑰](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)。 您必須提供的值包括 **應用程式識別碼** 和 **驗證金鑰**。

>[!Important]
> 當您決定使用 AAD 應用程式進行驗證，而不是使用受控識別時，請考慮 Azure 認知搜尋不會授權管理您的 AAD 應用程式，而是由您負責管理您的 AAD 應用程式，例如定期輪替應用程式驗證金鑰。
> 變更 AAD 應用程式或其驗證金鑰時，使用該應用程式的任何 Azure 認知搜尋索引或同義字對應都必須先更新為使用新的應用 **程式 ID\key，然後再刪除** 先前的應用程式或其授權金鑰，以及撤銷您的 Key Vault 存取權。
> 如果無法這樣做，將無法使用索引或同義字對應，因為它在金鑰存取遺失之後將無法解密內容。

## <a name="work-with-encrypted-content"></a>使用加密的內容

使用 CMK 加密時，您會發現索引和查詢的延遲，因為額外的加密/解密工作。 Azure 認知搜尋不會記錄加密活動，但您可以透過 key vault 記錄來監視金鑰存取權。 建議您在設定金鑰保存庫的過程中 [啟用記錄功能](../key-vault/general/logging.md) 。

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