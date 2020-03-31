---
title: 使用客戶管理的金鑰進行靜態加密
titleSuffix: Azure Cognitive Search
description: 使用在 Azure 金鑰保存庫中創建和管理的金鑰，在 Azure 認知搜索中補充索引和同義字映射上的伺服器端加密。
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: cb17fe24339ad618229b3456ece15c206f79bdb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76899942"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>使用 Azure 金鑰保存庫中的客戶管理金鑰在 Azure 認知搜索中對其餘內容進行加密

預設情況下，Azure 認知搜索使用[服務託管金鑰](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models)對靜態索引內容進行加密。 您可以使用在 Azure 金鑰保存庫中創建和管理的金鑰，使用其他加密層補充預設加密。 本文將引導您完成這些步驟。

通過與[Azure 金鑰保存庫](https://docs.microsoft.com/azure/key-vault/key-vault-overview)集成，支援伺服器端加密。 您可以建立自己的加密金鑰，然後將其儲存在金鑰保存庫中，或是使用 Azure Key Vault 的 API 來產生加密金鑰。 使用 Azure 金鑰保存庫，還可以審核金鑰使用方式。 

使用客戶管理的金鑰的加密在創建物件時在索引或同義字映射級別配置，而不是在搜索服務等級配置。 不能加密已存在的內容。 

金鑰並不都需要在同一金鑰保存庫中。 單個搜索服務可以承載多個加密索引或同義字映射，每個索引或同義字映射都使用存儲在不同金鑰保存庫中的客戶託管加密金鑰進行加密。  您還可以在同一服務中具有索引和同義字映射，這些索引和同義字映射未使用客戶管理的金鑰進行加密。 

> [!IMPORTANT] 
> 此功能在[REST API 版本 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/)和[.NET SDK 版本 8.0 預覽](search-dotnet-sdk-migration-version-9.md)版上可用。 當前不支援在 Azure 門戶中配置客戶託管加密金鑰。 搜索服務必須在 2019 年 1 月之後創建，不能為免費（共用）服務。

## <a name="prerequisites"></a>Prerequisites

此示例中使用以下服務。 

+ [建立 Azure 認知搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 

+ [創建 Azure 金鑰保存庫資源](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault)或在訂閱下查找現有保存庫。

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)或[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)用於配置任務。

+ [郵遞員](search-get-started-postman.md)[、Azure 電源外殼](search-create-index-rest-api.md)和[Azure 認知搜索 SDK](https://aka.ms/search-sdk-preview)可用於調用 REST API。 目前沒有針對客戶管理的加密的門戶支援。

>[!Note]
> 由於使用客戶管理的金鑰功能進行加密的性質，如果 Azure 金鑰保存庫金鑰被刪除，Azure 認知搜索將無法檢索資料。 為了防止意外刪除金鑰保存庫金鑰而導致的資料丟失，**必須先**在金鑰保存庫中啟用虛刪除和清除保護，然後才能使用它。 有關詳細資訊，請參閱[Azure 金鑰保存庫虛刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)。   

## <a name="1---enable-key-recovery"></a>1 - 啟用金鑰復原

創建 Azure 金鑰保存庫資源後，通過執行以下 PowerShell 或 Azure CLI 命令，在選定的金鑰保存庫中啟用**虛刪除**和**清除保護**：   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 - 創建新金鑰

如果使用現有金鑰加密 Azure 認知搜索內容，請跳過此步驟。

1. [登錄到 Azure 門戶](https://portal.azure.com)並導航到金鑰保存庫儀表板。

1. 從左側功能窗格中選擇 **"鍵**"設置，然後按一下 **"生成/導入**"。

1. 在 [建立金鑰]**** 窗格中，從 [選項]**** 清單中選擇您要用來建立金鑰的方法。 您可以 [產生]**** 新的金鑰、[上傳]**** 現有金鑰，或使用 [還原備份]**** 來選取金鑰的備份。

1. 輸入金鑰**的名稱**，並可以選擇選擇其他鍵屬性。

1. 按一下"**創建**"按鈕以啟動部署。

記下金鑰識別碼 – 這是由**鍵值 Uri、****鍵名稱**和**金鑰版本**組成的。 您需要這些來在 Azure 認知搜索中定義加密索引。
 
![創建新的金鑰保存庫金鑰](./media/search-manage-encryption-keys/create-new-key-vault-key.png "創建新的金鑰保存庫金鑰")

## <a name="3---create-a-service-identity"></a>3 - 創建服務標識

將標識分配給搜索服務使您能夠向搜索服務授予金鑰保存庫存取權限。 搜索服務將使用其標識使用 Azure 金鑰保存庫進行身份驗證。

Azure 認知搜索支援兩種分配標識的方法：託管標識或外部管理的 Azure 活動目錄應用程式。 

如果可能，請使用託管標識。 這是將標識分配給搜索服務的最簡單方法，在大多數情況下應該工作。 如果使用多個鍵進行索引和同義字映射，或者您的解決方案位於取消基於標識的身份驗證的分散式體系結構中，請使用本文末尾介紹的高級[外部管理的 Azure 活動目錄方法](#aad-app)。

 通常，託管標識使搜索服務能夠對 Azure 金鑰保存庫進行身份驗證，而無需在代碼中存儲憑據。 這種類型的託管標識的生命週期與搜索服務的生命週期相關聯，搜索服務只能有一個託管標識。 [瞭解有關託管標識的更多。](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

1. 要創建託管標識，請[登錄到 Azure 門戶](https://portal.azure.com)並打開搜索服務儀表板。 

1. 按一下左側功能窗格中的 **"標識**"，將其狀態更改為 **"打開**"，然後按一下"**保存**"。

![啟用託管標識](./media/search-enable-msi/enable-identity-portal.png "啟用已啟用的標識")

## <a name="4---grant-key-access-permissions"></a>4 - 授予金鑰存取權限

要使搜索服務能夠使用金鑰保存庫金鑰，您需要授予搜索服務某些存取權限。

可以在任何給定時間吊銷存取權限。 一旦被吊銷，使用該金鑰保存庫的任何搜索服務索引或同義字映射都將不可用。 稍後還原金鑰保存庫存取權限將恢復索引_同義字映射訪問。 有關詳細資訊，請參閱[安全訪問金鑰保存庫](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)。

1. [登錄到 Azure 門戶](https://portal.azure.com)並打開金鑰保存庫概覽頁。 

1. 從左側功能窗格中選擇 **"訪問策略**"設置，然後按一下 **"添加新**"。

   ![添加新金鑰保存庫訪問策略](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "添加新金鑰保存庫訪問策略")

1. 按一下 **"選擇主體"** 並選擇 Azure 認知搜索服務。 您可以按名稱或啟用託管標識後顯示的物件識別碼搜索它。

   ![選擇金鑰保存庫訪問策略主體](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "選擇金鑰保存庫訪問策略主體")

1. 按一下 **"金鑰許可權**"並*選擇"獲取*、*取消包裝鍵*"和 *"包裝鍵*"。 可以使用 Azure*資料存儲或 Azure 存儲*範本快速選擇所需的許可權。

   Azure 認知搜索必須授予以下[存取權限](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)：

   * *獲取*- 允許您的搜索服務在金鑰保存庫中檢索金鑰的公共部分
   * *包裝金鑰*- 允許搜索服務使用金鑰來保護內部加密金鑰
   * *取消包裝金鑰*- 允許搜索服務使用金鑰解包內部加密金鑰

   ![選擇金鑰保存庫訪問策略金鑰許可權](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "選擇金鑰保存庫訪問策略金鑰許可權")

1. 按一下 **"確定"** 並**保存**訪問策略更改。

> [!Important]
> Azure 認知搜索中的加密內容配置為使用特定**版本的**Azure 金鑰保存庫金鑰。 如果更改金鑰或版本，則必須更新索引或同義字映射才能使用新金鑰版本 **，然後才能**刪除以前的金鑰版本。 否則將導致索引或同義字映射不可用，一旦金鑰訪問丟失，您將無法解密內容。   

## <a name="5---encrypt-content"></a>5 - 加密內容

使用 Azure 門戶尚未創建使用客戶託管金鑰加密的索引或同義字映射。 使用 Azure 認知搜索 REST API 創建這樣的索引或同義字映射。

索引和同義字映射都支援用於指定金鑰的新頂級**加密金鑰**屬性。 

使用**金鑰保存庫 Uri、****金鑰名稱**和金鑰保存庫金鑰**的金鑰版本**，我們可以創建**加密金鑰**定義：

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
> 這些金鑰保存庫詳細資訊均未被視為機密，並且可以通過流覽 Azure 門戶中的相關 Azure 金鑰保存庫金鑰頁輕鬆檢索。

如果您使用的是 AAD 應用程式進行金鑰保存庫身份驗證，而不是使用託管標識，請將 AAD 應用程式**訪問憑據**添加到加密金鑰： 
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

## <a name="example-index-encryption"></a>示例：索引加密
通過 REST API 創建新索引的詳細資訊可在[創建索引（Azure 認知搜索 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-index)中找到，此處唯一的區別是將加密金鑰詳細資訊指定為索引定義的一部分： 

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
您現在可以發送索引創建請求，然後正常開始使用索引。

## <a name="example-synonym-map-encryption"></a>示例：同義字映射加密

通過 REST API 創建新的同義字映射的詳細資訊可在[創建同義字映射（Azure 認知搜索 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)中找到，此處唯一的區別是將加密金鑰詳細資訊指定為同義字映射定義的一部分： 

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
您現在可以發送同義字映射創建請求，然後開始正常使用。

>[!Important] 
> 雖然**加密金鑰**無法添加到現有的 Azure 認知搜索索引或同義字映射中，但可以通過為三個金鑰保存庫詳細資訊中的任何一個（例如，更新金鑰版本）提供不同的值來更新它。 更改為新的金鑰保存庫金鑰或新金鑰版本時，必須先更新使用該鍵的任何 Azure 認知搜索索引或同義字映射，才能在刪除以前的金鑰版本**之前**使用新金鑰版本。 否則將導致索引或同義字映射不可用，因為一旦金鑰訪問丟失，將無法解密內容。   
> 稍後還原金鑰保存庫存取權限將恢復內容訪問。

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a>高級：使用外部管理的 Azure 活動目錄應用程式

當無法進行託管標識時，可以創建具有 Azure 認知搜索服務安全主體的 Azure 活動目錄應用程式。 具體而言，在以下條件下，託管標識不可行：

* 不能直接向金鑰保存庫授予搜索服務存取權限（例如，如果搜索服務位於與 Azure 金鑰保存庫不同的活動目錄租戶中）。

* 需要一個搜索服務來承載多個加密索引_同義字映射，每個映射使用不同的金鑰來自不同的金鑰保存庫，其中每個金鑰保存庫必須使用不同的**標識**進行身份驗證。 如果使用其他標識管理不同的金鑰保存庫不是要求，請考慮使用上面的託管標識選項。  

為了適應此類拓撲，Azure 認知搜索支援使用 Azure 活動目錄 （AAD） 應用程式在搜索服務和金鑰保存庫之間進行身份驗證。    
要在門戶中創建 AAD 應用程式，請執行以下計畫：

1. [創建 Azure 活動目錄應用程式](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)。

1. [獲取應用程式 ID 和身份驗證金鑰](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)，因為創建加密索引所需的金鑰。 您需要提供的值包括**應用程式 ID**和**身份驗證金鑰**。

>[!Important]
> 在決定使用 AAD 應用程式而不是託管標識時，請考慮 Azure 認知搜索無權代表您管理 AAD 應用程式，由您管理 AAD 應用程式（如定期）應用程式身份驗證金鑰的旋轉。
> 更改 AAD 應用程式或其身份驗證金鑰時，必須先更新使用該應用程式的任何 Azure 認知搜索索引或同義字映射才能在刪除以前的應用程式或其授權金鑰**之前**，以及撤銷對它的金鑰保存庫存取權限之前使用新的應用程式 ID\key。
> 否則將導致索引或同義字映射不可用，因為一旦金鑰訪問丟失，將無法解密內容。   

## <a name="next-steps"></a>後續步驟

如果您不熟悉 Azure 安全體系結構，請查看[Azure 安全文檔](https://docs.microsoft.com/azure/security/)，特別是，本文：

> [!div class="nextstepaction"]
> [資料靜態加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
