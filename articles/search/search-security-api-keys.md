---
title: 建立、管理及保護系統管理員和查詢 api 金鑰
titleSuffix: Azure Cognitive Search
description: Api 金鑰可控制對服務端點的存取。 系統管理金鑰授與寫入權限。 可針對唯讀存取權建立查詢金鑰。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7cd6e61b8614e4c8ff5d54232972865c81cbb3ff
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928865"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>建立及管理 Azure 認知搜尋服務的 api 金鑰

搜尋服務的所有要求需要專為您服務而產生的唯讀 API 金鑰。 此 API 金鑰是驗證存取您搜尋服務端點的唯一機制，必須包含在每個要求上。 在[REST 解決方案](search-get-started-postman.md)中，API 金鑰通常會要求標頭中指定。 在 [.NET 解決方案](search-howto-dotnet-sdk.md#core-scenarios)中，金鑰通常會指定為組態設定，然後作為 [管理金鑰](/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) 或 [SearchCredentials](/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (查詢金鑰) 在 [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) 上傳遞。

金鑰會在服務佈建期間與您的搜尋服務一起建立。 您可以在 [Azure 入口網站](https://portal.azure.com)中檢視及取得金鑰值。

![入口網站頁面 > [設定] > [金鑰] 區段](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>什麼是 API 金鑰

API 金鑰是由隨機產生的數字和字母所組成的字串。 透過[角色型權限](search-security-rbac.md)，您可以刪除或讀取金鑰，但是您無法使用使用者定義的密碼取代金鑰，也無法使用 Active Directory 作為存取搜尋作業的主要驗證方法。 

存取搜尋服務的金鑰有兩種類型：管理 (讀寫) 和查詢 (唯讀)。

|Key|描述|限制|  
|---------|-----------------|------------|  
|管理|授與所有作業的完整權限，包括能夠管理服務、建立和刪除索引、索引子及資料來源。<br /><br /> 當服務建立時，在入口網站中會產生兩個系統管理金鑰 (稱為「主要」** 和「次要」** 金鑰)，而且您可以視需要個別重新產生這些金鑰。 擁有兩個金鑰可讓您在變換一個金鑰時，使用第二個金鑰來繼續存取服務。<br /><br /> 指定管理金鑰時，只能在 HTTP 要求標頭中指定。 您無法將管理 API 金鑰放在 URL 中。|每個服務的上限為 2 個|  
|查詢|授與索引和文件的唯讀存取權，且通常會分派給發出搜尋要求的用戶端應用程式。<br /><br /> 查詢金鑰是視需要建立的。 您可以在入口網站中手動建立這些金鑰，或是透過[管理 REST API](/rest/api/searchmanagement/) \(英文\) 以程式設計方式建立這些金鑰。<br /><br /> 您可以在 HTTP 要求標頭中指定查詢金鑰，以進行查詢、建議或查閱作業。 或者，您也可以在 URL 上將查詢金鑰當作參數來傳遞。 視您用戶端應用程式制定要求的方式而定，將金鑰當作查詢參數來傳遞可能會較為簡單：<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|每個服務 50 個|  

 管理金鑰或查詢金鑰在外觀上並無差別。 兩種金鑰都是由 32 個隨機產生的英數字元所組成。 如果您忘記在應用程式中指定的是哪種類型的金鑰，您可以[在入口網站中查看金鑰值](https://portal.azure.com)，或使用 [REST API](/rest/api/searchmanagement/) 來傳回值和金鑰類型。  

> [!NOTE]  
>  在要求 URI 中傳遞敏感性資料 (例如 `api-key`) 被視為不佳的安全性做法。 基於這個理由，Azure 認知搜尋只接受查詢索引鍵做為 `api-key` 查詢字串中的，除非您的索引內容應該公開可用，否則您應該避免這樣做。 一般規則是建議將 `api-key` 當作要求標頭來傳遞。  

## <a name="find-existing-keys"></a>尋找現有的金鑰

您可以在入口網站中或透過[管理 REST API](/rest/api/searchmanagement/) \(英文\) 取得存取金鑰。 如需詳細資訊，請參閱[管理和查詢 API 金鑰](search-security-api-keys.md)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 列出您訂用帳戶的[搜尋服務](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。
3. 選取服務，然後在 [總覽] 頁面上，按一下 [**設定**  > **金鑰**] 以查看管理員和查詢金鑰。

   ![入口網站頁面 > [設定] > [金鑰] 區段](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>建立查詢金鑰

查詢金鑰是用於以檔集合為目標之作業的索引中的檔唯讀存取。 搜尋、篩選和建議查詢都是採用查詢金鑰的所有作業。 任何會傳回系統資料或物件定義的唯讀作業（例如索引定義或索引子狀態）都需要系統管理金鑰。

在用戶端應用程式中限制存取和作業，對於保護服務上的搜尋資產是不可或缺的。 針對源自用戶端應用程式的任何查詢，一律使用查詢金鑰，而非管理金鑰。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 列出您訂用帳戶的[搜尋服務](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。
3. 選取服務，然後按一下 [總覽] 頁面上的 [**設定**  > **金鑰**]。
4. 按一下 [ **管理查詢金鑰**]。
5. 使用已為您的服務產生的查詢金鑰，或建立最多50個新的查詢金鑰。 預設的查詢金鑰未命名，但可以命名其他查詢金鑰以進行管理。

   ![建立或使用查詢金鑰](media/search-security-overview/create-query-key.png) 

> [!Note]
> 在 [c # 中查詢 Azure 認知搜尋索引時](./search-get-started-dotnet.md)，可以找到顯示查詢金鑰使用方式的程式碼範例。

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>重新產生系統管理金鑰

系統會為每個服務建立兩個系統管理金鑰，讓您可以使用次要金鑰來旋轉主要金鑰，以進行商務持續性。

1. 在 [**設定**  > **金鑰**] 頁面中，複製次要金鑰。
2. 針對所有的應用程式，更新 API 金鑰設定以使用次要金鑰。
3. 重新產生主要金鑰。
4. 更新所有應用程式以使用新的主要金鑰。

如果您不小心同時產生兩個金鑰，使用這些金鑰的所有用戶端要求都會失敗，並出現 HTTP 403 禁止。 但是內容不會遭到刪除，而且您也不會永久鎖定。 

您仍然可以透過入口網站或管理層 ([REST API](/rest/api/searchmanagement/)、 [PowerShell](./search-manage-powershell.md)或 Azure Resource Manager) 來存取服務。 管理函式是透過訂用帳戶識別碼來作業系統，而不是服務 api 金鑰，因此即使您的 api 金鑰不是，仍可使用。 

當您透過入口網站或管理層建立新的金鑰之後，會將存取權還原至您的內容 (索引、索引子、資料來源、同義字對應) 有新的金鑰，並在要求上提供這些金鑰。

## <a name="secure-api-keys"></a>保護 API 金鑰
藉由限制透過入口網站或 Resource Manager 介面 (PowerShell 或命令列介面) 的存取來確保金鑰安全性。 如前所述，訂用帳戶系統管理員可以檢視及重新產生所有的 API 金鑰。 為以防萬一，請檢閱角色指派以了解誰具有管理員金鑰存取權。

+ 在服務儀表板中，按一下 [存取控制 (IAM)]****，然後按一下 [角色指派]**** 索引標籤，以檢視您服務的角色指派。

下列角色的成員可以檢視及重新產生金鑰：擁有者、參與者、[搜尋服務參與者](../role-based-access-control/built-in-roles.md#search-service-contributor)

> [!Note]
> 針對搜尋結果的身分識別型存取，您可以建立安全性篩選，依身分識別修剪結果、移除要求者不應具備存取權的文件。 如需詳細資訊，請參閱[安全性篩選](search-security-trimming-for-azure-search.md)和[使用 Active Directory 保護安全](search-security-trimming-for-azure-search-with-aad.md)。

## <a name="see-also"></a>請參閱

+ [Azure 認知搜尋中以角色為基礎的存取控制](search-security-rbac.md)
+ [使用 Powershell 管理](search-manage-powershell.md) 
+ [效能與最佳化發行項](search-performance-optimization.md)