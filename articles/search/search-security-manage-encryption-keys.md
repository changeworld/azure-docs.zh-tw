---
title: 使用客戶管理的金鑰進行待用加密
titleSuffix: Azure Cognitive Search
description: 使用您在 Azure Key Vault 中建立和管理的金鑰，在 Azure 認知搜尋中補充索引和同義字對應的伺服器端加密。
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: cb17fe24339ad618229b3456ece15c206f79bdb7
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899942"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>在 Azure Key Vault 中使用客戶管理的金鑰，在 Azure 認知搜尋中進行內容的待用加密

根據預設，Azure 認知搜尋會使用[服務管理的金鑰](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models)來加密靜止的索引內容。 您可以使用您在 Azure Key Vault 中建立和管理的金鑰，以額外的加密層補充預設加密。 本文會逐步引導您完成這些步驟。

透過與[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)的整合來支援伺服器端加密。 您可以建立自己的加密金鑰，然後將其儲存在金鑰保存庫中，或是使用 Azure Key Vault 的 API 來產生加密金鑰。 您也可以使用 Azure Key Vault 來審核金鑰使用方式。 

使用客戶管理的金鑰進行加密時，會在建立這些物件時，于索引或同義字對應層級設定，而不是搜尋服務層級。 您無法加密已經存在的內容。 

金鑰不一定要位於相同的 Key Vault。 單一搜尋服務可以裝載多個加密的索引或同義字對應，每個都是以自己的客戶管理的加密金鑰進行加密，並儲存在不同的金鑰保存庫中。  您也可以在使用客戶管理的金鑰未加密的相同服務中擁有索引和同義字對應。 

> [!IMPORTANT] 
> 這項功能適用于[REST API 版本 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/)和[.net SDK 版本 8.0-preview](search-dotnet-sdk-migration-version-9.md)。 目前不支援在 Azure 入口網站中設定客戶管理的加密金鑰。 搜尋服務必須在2019年1月之後建立，而且不能是免費（共用）服務。

## <a name="prerequisites"></a>必要條件

此範例會使用下列服務。 

+ [建立 Azure 認知搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 

+ [建立 Azure Key Vault 資源](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault)，或在您的訂用帳戶下尋找現有的保存庫。

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)或[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)用於設定工作。

+ 您可以使用[Postman](search-get-started-postman.md)、 [Azure PowerShell](search-create-index-rest-api.md)和[Azure 認知搜尋 SDK](https://aka.ms/search-sdk-preview)來呼叫 REST API。 目前不支援客戶管理的加密的入口網站。

>[!Note]
> 由於加密與客戶管理的金鑰功能的本質，如果您的 Azure 金鑰保存庫金鑰已刪除，Azure 認知搜尋將無法取得您的資料。 若要防止 Key Vault 意外刪除金鑰所造成的資料遺失，您**必須**先啟用 Key Vault 中的虛刪除和清除保護，才能使用它。 如需詳細資訊，請參閱[Azure Key Vault 虛刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)。   

## <a name="1---enable-key-recovery"></a>1-啟用金鑰修復

建立 Azure Key Vault 資源之後，請執行下列 PowerShell 或 Azure CLI 命令，以啟用所選金鑰保存庫中的虛**刪除**和**清除保護**：   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2-建立新的金鑰

如果您使用現有的金鑰來加密 Azure 認知搜尋內容，請略過此步驟。

1. 登[入以 Azure 入口網站](https://portal.azure.com)，然後流覽至金鑰保存庫儀表板。

1. 從左側流覽窗格中選取 [**金鑰**] 設定，然後按一下 [ **+ 產生/匯入**]。

1. 在 [建立金鑰] 窗格中，從 [選項] 清單中選擇您要用來建立金鑰的方法。 您可以 [產生] 新的金鑰、[上傳] 現有金鑰，或使用 [還原備份] 來選取金鑰的備份。

1. 輸入金鑰的**名稱**，並選擇性地選取其他金鑰屬性。

1. 按一下 [**建立**] 按鈕以開始部署。

記下金鑰識別碼–這是由**金鑰值 Uri**、**金鑰名稱**和**金鑰版本**所組成。 您將需要這些來定義 Azure 認知搜尋中的加密索引。
 
![建立新的金鑰保存庫金鑰](./media/search-manage-encryption-keys/create-new-key-vault-key.png "建立新的金鑰保存庫金鑰")

## <a name="3---create-a-service-identity"></a>3-建立服務身分識別

將身分識別指派給您的搜尋服務，可讓您將 Key Vault 存取權限授與您的搜尋服務。 您的搜尋服務會使用其身分識別來向 Azure Key vault 進行驗證。

Azure 認知搜尋支援兩種指派身分識別的方式：受控識別或外部管理的 Azure Active Directory 應用程式。 

可能的話，請使用受控識別。 這是將身分識別指派給您的搜尋服務最簡單的方式，而且應該在大部分的情況下使用。 如果您針對索引和同義字對應使用多個金鑰，或如果您的解決方案位於 disqualifies 以身分識別為基礎之驗證的分散式架構中，請使用本文結尾所述的「先進的[外部管理 Azure Active Directory 方法](#aad-app)。

 一般而言，受控識別可讓您的搜尋服務向 Azure Key Vault 進行驗證，而不需要在程式碼中儲存認證。 這種受控識別類型的生命週期會系結至搜尋服務的生命週期，其只能有一個受控識別。 [深入瞭解受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別。

1. 若要建立受控識別，請登[入 toAzure 入口網站](https://portal.azure.com)，並開啟您的搜尋服務儀表板。 

1. 按一下左側流覽窗格中的 [身分**識別**]，將其狀態變更為 [**開啟**]，然後按一下 [**儲存**]。

![啟用受控身分識別](./media/search-enable-msi/enable-identity-portal.png "啟用受控身分識別")

## <a name="4---grant-key-access-permissions"></a>4-授與金鑰存取權限

若要讓您的搜尋服務使用您的 Key Vault 金鑰，您必須將特定存取權限授與您的搜尋服務。

在任何指定的時間都可以撤銷存取權限。 一旦撤銷，任何使用該金鑰保存庫的搜尋服務索引或同義字對應都會變成無法使用。 稍後還原金鑰保存庫存取權限時，將會還原 index\synonym 對應存取。 如需詳細資訊，請參閱[保護金鑰保存庫的存取權](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)。

1. 登[入以 Azure 入口網站](https://portal.azure.com)並開啟您的金鑰保存庫總覽頁面。 

1. 從左側流覽窗格中選取 [**存取原則**] 設定，然後按一下 [ **+ 新增**]。

   ![新增金鑰保存庫存取原則](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "新增金鑰保存庫存取原則")

1. 按一下 [**選取主體**]，然後選取您的 Azure 認知搜尋服務。 您可以依名稱或啟用受控識別後顯示的物件識別碼來搜尋它。

   ![選取 key vault 存取原則主體](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "選取 key vault 存取原則主體")

1. 按一下 [**金鑰許可權**]，然後選取 [*取得*、解除包裝*金鑰*和*換行金鑰*]。 您可以使用*Azure Data Lake Storage 或 Azure 儲存體*範本來快速選取所需的許可權。

   Azure 認知搜尋必須以下列[存取權限](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)授與：

   * *Get* -允許您的搜尋服務在 Key Vault 中取出金鑰的公開部分
   * *包裝金鑰*-允許您的搜尋服務使用您的金鑰來保護內部加密金鑰
   * 解除包裝*金鑰*-允許您的搜尋服務使用您的金鑰來解除包裝內部加密金鑰

   ![選取金鑰保存庫存取原則金鑰許可權](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "選取金鑰保存庫存取原則金鑰許可權")

1. 按一下 **[確定]** ，然後**儲存**存取原則變更。

> [!Important]
> Azure 認知搜尋中的加密內容設定為使用特定 Azure Key Vault 金鑰搭配特定**版本**。 如果您變更了索引鍵或版本, 則必須先將索引或同義字對應更新為使用新的 key\version,**才能**刪除先前的 key\version。 如果無法這樣做，就會導致索引或同義字地圖無法使用，而當金鑰存取遺失後，您將無法解密內容。   

## <a name="5---encrypt-content"></a>5-加密內容

尚無法使用 Azure 入口網站來建立以客戶管理的金鑰加密的索引或同義字對應。 使用 Azure 認知搜尋 REST API 來建立這類索引或同義字地圖。

索引和同義字對應都支援用來指定金鑰的新最上層**encryptionKey**屬性。 

使用金鑰保存庫**Uri**、**金鑰名稱**和金鑰保存庫金鑰的**金鑰版本**，我們可以建立**encryptionKey**定義：

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
> 這些金鑰保存庫的詳細資料都不會被視為秘密，而且可以藉由流覽至 Azure 入口網站中的相關 Azure Key Vault 金鑰頁面來輕鬆地抓取。

如果您使用 AAD 應用程式進行 Key Vault 驗證，而不是使用受控識別，請將 AAD 應用程式**存取**認證新增至您的加密金鑰： 
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
如需透過 REST API 建立新索引的詳細資訊，請參閱[建立索引（Azure 認知搜尋 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-index)，這裡唯一的差異在於將加密金鑰詳細資料指定為索引定義的一部分： 

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

## <a name="example-synonym-map-encryption"></a>範例：同義字對應加密

透過 REST API 建立新同義字地圖的詳細資料可在[建立同義字地圖（Azure 認知搜尋 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)中找到，其中唯一的差異在於將加密金鑰詳細資料指定為同義字地圖定義的一部分： 

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
您現在可以傳送同義字對應建立要求，然後開始正常使用。

>[!Important] 
> 雖然無法將**encryptionKey**新增至現有的 Azure 認知搜尋索引或同義字對應，但您也可以針對三個金鑰保存庫詳細資料（例如更新金鑰版本）提供不同的值來更新它。 變更為新的 Key Vault 金鑰或新的金鑰版本時，任何使用該金鑰的 Azure 認知搜尋索引或同義字對應，都必須先更新為使用新的 key\version，**然後再**刪除先前的 key\version。 若未這麼做，將會導致索引或同義字地圖無法使用，因為一旦失去金鑰存取權，就無法解密內容。   
> 稍後還原金鑰保存庫存取權限時，將會還原內容存取。

## <a name="aad-app"></a>Advanced：使用外部受控 Azure Active Directory 應用程式

當受控識別不可行時，您可以使用 Azure 認知搜尋服務的安全性主體來建立 Azure Active Directory 應用程式。 具體而言，在這些情況下，受控識別並不可行：

* 您無法直接將您的搜尋服務存取權限授與金鑰保存庫（例如，如果搜尋服務位於與 Azure Key Vault 不同的 Active Directory 租使用者中）。

* 需要單一搜尋服務來裝載多個已加密的 indexes\synonym 對應，每個都使用不同金鑰保存庫中的不同金鑰，其中每個 key vault 都必須使用不同的身分**識別**來進行驗證。 如果不需要使用不同的身分識別來管理不同的金鑰保存庫，請考慮使用上述的受控識別選項。  

為了配合這類拓撲，Azure 認知搜尋支援使用 Azure Active Directory （AAD）應用程式，在您的搜尋服務與 Key Vault 之間進行驗證。    
若要在入口網站中建立 AAD 應用程式：

1. [建立 Azure Active Directory 應用程式](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)。

1. [取得應用程式識別碼和驗證金鑰](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)，因為建立加密索引時需要用到。 您需要提供的值包括**應用程式識別碼**和**驗證金鑰**。

>[!Important]
> 決定使用 AAD 應用程式進行驗證而非受控身分識別時，請考慮 Azure 認知搜尋未授權代表您管理 AAD 應用程式，並由您負責管理您的 AAD 應用程式，例如定期應用程式驗證金鑰的輪替。
> 變更 AAD 應用程式或其驗證金鑰時，任何使用該應用程式的 Azure 認知搜尋索引或同義字對應，都必須先更新為使用新的應用**程式 ID\key，然後再刪除**先前的應用程式或其授權金鑰，然後再撤銷您的 Key Vault 存取權。
> 若未這麼做，將會導致索引或同義字地圖無法使用，因為一旦失去金鑰存取權，就無法解密內容。   

## <a name="next-steps"></a>後續步驟

如果您不熟悉 Azure 安全性架構，請參閱[Azure 安全性檔案](https://docs.microsoft.com/azure/security/)，特別是這篇文章：

> [!div class="nextstepaction"]
> [資料靜態加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
