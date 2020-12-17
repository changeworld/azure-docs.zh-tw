---
title: 使用 Active Directory 來修剪結果的安全性篩選
titleSuffix: Azure Cognitive Search
description: 瞭解如何在檔層級為 Azure 認知搜尋搜尋結果，使用安全性篩選和 Azure Active Directory (AD) 身分識別來執行安全性許可權。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5788585b2365b12a90a508e5a972b61f73e48c15
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629505"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>使用 Active Directory 身分識別來修剪 Azure 認知搜尋結果的安全性篩選

本文示範如何使用 Azure Active Directory (AD) 安全性身分識別以及 Azure 認知搜尋中的篩選準則，以根據使用者群組成員資格來修剪搜尋結果。

本文涵蓋下列工作：
> [!div class="checklist"]
> - 建立 Azure AD 群組和使用者
> - 將使用者與您建立的群組相關聯
> - 快取新的群組
> - 使用相關聯的群組索引文件
> - 使用群組識別碼篩選條件發出搜尋要求
> 
> [!NOTE]
> 本文中的範例程式碼片段是以 C# 撰寫。 您可以 [在 GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started)找到完整的原始程式碼。 

## <a name="prerequisites"></a>必要條件

您 Azure 認知搜尋中的索引必須具有 [安全性欄位](search-security-trimming-for-azure-search.md) ，才能儲存具有檔讀取權限的群組身分識別清單。 此使用案例會假設安全性實體項目 (例如個人的大學應用程式) 與指定可存取該項目之人員 (許可人員) 的安全性欄位之間的一對一對應。

您必須具有在此逐步解說中建立使用者、群組和關聯所需的 Azure AD 系統管理員許可權。 

您的應用程式也必須以多租使用者應用程式的形式註冊 Azure AD，如下列程式所述。

### <a name="register-your-application-with-azure-active-directory"></a>使用 Azure Active Directory 註冊您的應用程式

此步驟會將您的應用程式與 Azure AD 整合，以接受使用者和群組帳戶的登入。 如果您不是組織中的租使用者系統管理員，您可能需要 [建立新的租](../active-directory/develop/quickstart-create-new-tenant.md) 使用者，才能執行下列步驟。

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找訂用帳戶的 Azure Active Directory 資源。

1. 在左側的 [ **管理**] 底下，選取 [ **應用程式註冊**]，然後選取 [ **新增註冊**]。

1. 為註冊提供名稱，可能是類似于搜尋應用程式名稱的名稱。 選取 [註冊]。

1. 一旦建立應用程式註冊，請複製應用程式識別碼。 您必須將此字串提供給您的應用程式。

   如果您正在逐步執行 [DotNetHowToSecurityTrimming](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)，請將此值貼入 **app.config** 檔案中。

   針對租使用者識別碼重複執行。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Essentials 區段中的應用程式識別碼":::

1. 選取左側的 [ **API 許可權** ]，然後選取 [ **新增許可權**]。 

1. 選取 **Microsoft Graph** 然後選取 [ **委派的許可權**]。

1. 搜尋並新增下列委派的許可權：

   - **Directory.ReadWrite.All**
   - **Group.ReadWrite.All**
   - **User.ReadWrite.All**

Microsoft Graph 提供可透過 REST API 以程式設計方式存取 Azure AD 的 API。 本逐步解說的程式碼範例會使用權限來呼叫 Microsoft Graph API，以建立群組、使用者和關聯。 API 也可用來快取群組識別碼以加速篩選。

## <a name="create-users-and-groups"></a>建立 [使用者和群組]

如果您要將搜尋新增至已建立的應用程式，您可能會在 Azure AD 中有現有的使用者和群組識別碼。 在此情況下，您可以跳過接下來的三個步驟。 

不過，如果您沒有現有的使用者，可以使用 Microsoft Graph API 來建立安全性主體。 下列程式碼片段示範如何產生識別碼，這會成為 Azure 認知搜尋索引中安全性欄位的資料值。 在我們假設的大學許可應用程式中，這會是許可人員適用的安全性識別碼。

使用者和群組成員資格可能很流暢，尤其是在大型組織中。 建置使用者和群組身分識別的程式碼應該經常執行，才能挑選組織成員資格中的變更。 同樣地，您的 Azure 認知搜尋索引也需要類似的更新排程，以反映所允許使用者和資源的目前狀態。

### <a name="step-1-create-group"></a>步驟1： [建立群組](/graph/api/group-post-groups) 

```csharp
private static Dictionary<Group, List<User>> CreateGroupsWithUsers(string tenant)
{
    Group group = new Group()
    {
        DisplayName = "My First Prog Group",
        SecurityEnabled = true,
        MailEnabled = false,
        MailNickname = "group1"
    };
```

### <a name="step-2-create-user"></a>步驟2： [建立使用者](/graph/api/user-post-users)

```csharp
User user1 = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = String.Format("user1@{0}", tenant),
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
```

### <a name="step-3-associate-user-and-group"></a>步驟 3：建立使用者和群組的關聯

```csharp
List<User> users = new List<User>() { user1, user2 };
Dictionary<Group, List<User>> groups = new Dictionary<Group, List<User>>() { { group, users } };
```

### <a name="step-4-cache-the-groups-identifiers"></a>步驟 4：快取群組識別碼

（選擇性）若要減少網路延遲，您可以快取使用者群組關聯，如此一來，當發出搜尋要求時，就會從快取傳回群組，以節省 Azure AD 的往返。 您可以使用 [Azure AD BATCH API](/graph/json-batching) 來傳送具有多位使用者的單一 Http 要求，並建立快取。

Microsoft Graph 依設計可處理大量的要求。 如果發生大量的要求，Microsoft Graph 會無法執行要求，並顯示 HTTP 狀態碼 429。 如需詳細資訊，請參閱 [Microsoft Graph 節流](/graph/throttling)。

## <a name="index-document-with-their-permitted-groups"></a>使用其允許的群組索引文件

Azure 認知搜尋中的查詢作業會透過 Azure 認知搜尋索引來執行。 在此步驟中，索引作業會將可搜尋資料匯入索引中，包含用來作為安全性篩選器的識別碼。 

Azure 認知搜尋不會驗證使用者身分識別，也不會提供邏輯來建立使用者有權查看的內容。 安全性調整的使用案例會假設您在機密文件與擁有該文件存取權的群組識別碼之間提供關聯，完整匯入搜尋索引。 

在假設的範例中，Azure 認知搜尋索引上的 PUT 要求本文會包含申請者的大學文章或文字記錄，以及具有查看該內容之許可權的群組識別碼。 

在本逐步解說用於程式碼範例的一般範例中，索引動作看起來可能會像這樣：

```csharp
private static void IndexDocuments(string indexName, List<string> groups)
{
    IndexDocumentsBatch<SecuredFiles> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new SecuredFiles()
            {
                FileId = "1",
                Name = "secured_file_a",
                GroupIds = new[] { groups[0] }
            }),
              ...
            };

IndexDocumentsResult result = searchClient.IndexDocuments(batch);
```

## <a name="issue-a-search-request"></a>發出搜尋要求

基於安全性調整的目的，您在索引之安全性欄位中的值會是靜態值，用來包含或排除搜尋結果中的文件。 例如，如果招生的群組識別碼是 "A11B22C33D44-E55F66G77-H88I99JKK"，則在安全性群組中具有該識別碼的 Azure 認知搜尋索引中的任何檔都會包含在傳送回要求者的搜尋結果中 (或排除) 。

若要篩選以發出要求的使用者群組作為基礎的搜尋結果中傳回之文件，請請檢閱下列的步驟。

### <a name="step-1-retrieve-users-group-identifiers"></a>步驟 1：擷取使用者的群組識別碼

如果尚未快取使用者的群組，或快取已過期，請發出 [群組](/graph/api/directoryobject-getmembergroups) 要求。

```csharp
private static async void RefreshCache(IEnumerable<User> users)
{
    HttpClient client = new HttpClient();
    var userGroups = await _microsoftGraphHelper.GetGroupsForUsers(client, users);
    _groupsCache = new ConcurrentDictionary<string, List<string>>(userGroups);
}
```

### <a name="step-2-compose-the-search-request"></a>步驟 2：撰寫搜尋要求

假設您具有使用者的群組成員資格，就可以使用適當的篩選器值來發出搜尋要求。

```csharp
private static void SearchQueryWithFilter(string user)
{
    // Using the filter below, the search result will contain all documents that their GroupIds field   
    // contain any one of the Ids in the groups list
    string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", String.Join(",", _groupsCache[user])));
    SearchOptions searchOptions =
        new SearchOptions()
        {
            Filter = filter
        };
    searchOptions.Select.Add("name");

    SearchResults<SecuredFiles> results = searchClient.Search<SecuredFiles>("*", searchOptions);

    Console.WriteLine("Results for groups '{0}' : {1}", _groupsCache[user], results.GetResults().Select(r => r.Document.Name));
}
```

### <a name="step-3-handle-the-results"></a>步驟 3：處理結果

回應包括文件的篩選清單，包含使用者有權限檢視的項目。 根據您建構搜尋結果頁面的方式，建議您包括視覺提示，以反映篩選的結果集。

## <a name="next-steps"></a>下一步

在本逐步解說中，您已瞭解使用 Azure AD 登入來篩選 Azure 認知搜尋結果中檔的模式，並修剪不符合要求中所提供之篩選的檔結果。 如需可能較簡單的替代模式或重新流覽其他安全性功能，請參閱下列連結。

- [用於修剪結果的安全性篩選](search-security-trimming-for-azure-search.md)
- [Azure 認知搜尋中的安全性](search-security-overview.md)