---
title: 使用 Active Directory 來修剪結果的安全性篩選
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋搜尋結果的檔層級安全性許可權，使用安全性篩選和 Azure Active Directory （AAD）身分識別。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: ee742eae38ae95756cf31d60b877f18629c569d4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85080500"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>使用 Active Directory 身分識別來修剪 Azure 認知搜尋結果的安全性篩選

本文示範如何使用 Azure Active Directory （AAD）安全性身分識別搭配 Azure 認知搜尋中的篩選準則，以根據使用者群組成員資格來修剪搜尋結果。

本文涵蓋下列工作：
> [!div class="checklist"]
> - 建立 AAD 群組和使用者
> - 將使用者與您建立的群組相關聯
> - 快取新的群組
> - 使用相關聯的群組索引文件
> - 使用群組識別碼篩選條件發出搜尋要求
> 
> [!NOTE]
> 本文中的範例程式碼片段是以 C# 撰寫。 您可以 [在 GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started)找到完整的原始程式碼。 

## <a name="prerequisites"></a>必要條件

您在 Azure 認知搜尋中的索引必須具有 [[安全性] 欄位](search-security-trimming-for-azure-search.md)，才能儲存具有檔讀取權限的群組身分識別清單。 此使用案例會假設安全性實體項目 (例如個人的大學應用程式) 與指定可存取該項目之人員 (許可人員) 的安全性欄位之間的一對一對應。

您必須擁有本逐步解說所需的 AAD 系統管理員權限，才能在 AAD 中建立使用者、群組和關聯。

您的應用程式必須也向 AAD 註冊，如下列程序中所述。

### <a name="register-your-application-with-aad"></a>向 AAD 註冊您的應用程式

這個步驟會就接受使用者和群組帳戶登入的目的，將您的應用程式與 AAD 整合。 如果您在貴組織中不是 AAD 系統管理員，可能需要[建立新的租用戶](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant)才能執行下列步驟。

1. 移至[**應用程式註冊入口網站**](https://apps.dev.microsoft.com)交集  >   **應用程式**  >  **新增應用**程式。
2. 輸入應用程式的名稱，然後按一下 [建立]****。 
3. 在 [我的應用程式] 頁面中，選取您新註冊的應用程式。
4. 在 [應用程式註冊] 頁面上 >**平臺**]  >  [**新增平臺**]，選擇 [ **Web API**]。
5. 仍在 [應用程式註冊] 頁面上，移至 > **Microsoft Graph [許可權**] [  >  **新增**]。
6. 在 [選取權限] 中，新增下列委派的權限，然後按一下 [確定]****：

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

Microsoft Graph 會提供一個 API，可透過 REST API 以程式設計方式存取 AAD。 本逐步解說的程式碼範例會使用權限來呼叫 Microsoft Graph API，以建立群組、使用者和關聯。 API 也可用來快取群組識別碼以加速篩選。

## <a name="create-users-and-groups"></a>建立 [使用者和群組]

如果您要將搜尋新增至已建立的應用程式，可能在 AAD 中已有現有的使用者和群組識別碼。 在此情況下，您可以跳過接下來的三個步驟。 

不過，如果您沒有現有的使用者，可以使用 Microsoft Graph API 來建立安全性主體。 下列程式碼片段示範如何產生識別碼，這會成為您 Azure 認知搜尋索引中 [安全性] 欄位的資料值。 在我們假設的大學許可應用程式中，這會是許可人員適用的安全性識別碼。

使用者和群組成員資格可能很流暢，尤其是在大型組織中。 建置使用者和群組身分識別的程式碼應該經常執行，才能挑選組織成員資格中的變更。 同樣地，您的 Azure 認知搜尋索引需要類似的更新排程，以反映允許的使用者和資源的目前狀態。

### <a name="step-1-create-aad-group"></a>步驟 1：建立 [AAD 群組](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) 
```csharp
// Instantiate graph client 
GraphServiceClient graph = new GraphServiceClient(new DelegateAuthenticationProvider(...));
Group group = new Group()
{
    DisplayName = "My First Prog Group",
    SecurityEnabled = true,
    MailEnabled = false,
    MailNickname = "group1"
}; 
Group newGroup = await graph.Groups.Request().AddAsync(group);
```
   
### <a name="step-2-create-aad-user"></a>步驟 2：建立 [AAD 使用者](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)
```csharp
User user = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = "User1@FirstUser.com",
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
User newUser = await graph.Users.Request().AddAsync(user);
```

### <a name="step-3-associate-user-and-group"></a>步驟 3：建立使用者和群組的關聯
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>步驟 4：快取群組識別碼
(選擇性) 若要降低網路延遲，您可以快取使用者群組關聯，以便在發出搜尋要求時從快取傳回群組，從而省下 AAD 的往返。 您可以使用[AAD BATCH API](https://developer.microsoft.com/graph/docs/concepts/json_batching)來傳送具有多位使用者的單一 Http 要求，並建立快取。

Microsoft Graph 依設計可處理大量的要求。 如果發生大量的要求，Microsoft Graph 會無法執行要求，並顯示 HTTP 狀態碼 429。 如需詳細資訊，請參閱 [Microsoft Graph 節流](https://developer.microsoft.com/graph/docs/concepts/throttling)。

## <a name="index-document-with-their-permitted-groups"></a>使用其允許的群組索引文件

Azure 認知搜尋中的查詢作業會透過 Azure 認知搜尋索引來執行。 在此步驟中，索引作業會將可搜尋資料匯入索引中，包含用來作為安全性篩選器的識別碼。 

Azure 認知搜尋不會驗證使用者身分識別，也不會提供邏輯來建立使用者有權查看的內容。 安全性調整的使用案例會假設您在機密文件與擁有該文件存取權的群組識別碼之間提供關聯，完整匯入搜尋索引。 

在假設的範例中，Azure 認知搜尋索引上的 PUT 要求主體會包含一份申請人的大學文章或文字記錄，以及具有可查看該內容之許可權的群組識別碼。 

在本逐步解說用於程式碼範例的一般範例中，索引動作看起來可能會像這樣：

```csharp
var actions = new IndexAction<SecuredFiles>[]
              {
                  IndexAction.Upload(
                  new SecuredFiles()
                  {
                      FileId = "1",
                      Name = "secured_file_a",
                      GroupIds = new[] { groups[0] }
                  }),
              ...
             };

var batch = IndexBatch.New(actions);

_indexClient.Documents.Index(batch);  
```

## <a name="issue-a-search-request"></a>發出搜尋要求

基於安全性調整的目的，您在索引之安全性欄位中的值會是靜態值，用來包含或排除搜尋結果中的文件。 例如，如果許可的群組識別碼是 "A11B22C33D44-E55F66G77-H88I99JKK"，則在傳回給要求者的搜尋結果中，會包含（或排除）在安全性記錄中具有該識別碼之 Azure 認知搜尋索引中的任何檔。

若要篩選以發出要求的使用者群組作為基礎的搜尋結果中傳回之文件，請請檢閱下列的步驟。

### <a name="step-1-retrieve-users-group-identifiers"></a>步驟 1：擷取使用者的群組識別碼

如果使用者的群組尚快取或是快取已過期，請發出[群組](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0)要求
```csharp
private static void RefreshCacheIfRequired(string user)
{
    if (!_groupsCache.ContainsKey(user))
    {
        var groups = GetGroupIdsForUser(user).Result;
        _groupsCache[user] = groups;
    }
}

private static async Task<List<string>> GetGroupIdsForUser(string userPrincipalName)
{
    List<string> groups = new List<string>();
    var allUserGroupsRequest = graph.Users[userPrincipalName].GetMemberGroups(true).Request();

    while (allUserGroupsRequest != null) 
    {
        IDirectoryObjectGetMemberGroupsRequestBuilder allUserGroups = await allUserGroupsRequest.PostAsync();
        groups = allUserGroups.ToList();
        allUserGroupsRequest = allUserGroups.NextPageRequest;
    }
    return groups;
}
``` 

### <a name="step-2-compose-the-search-request"></a>步驟 2：撰寫搜尋要求

假設您具有使用者的群組成員資格，就可以使用適當的篩選器值來發出搜尋要求。

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>步驟 3：處理結果

回應包括文件的篩選清單，包含使用者有權限檢視的項目。 根據您建構搜尋結果頁面的方式，建議您包括視覺提示，以反映篩選的結果集。

## <a name="conclusion"></a>結論

在本逐步解說中，您已瞭解如何使用 AAD 登入來篩選 Azure 認知搜尋結果中的檔，並修剪不符合要求中所提供篩選的檔結果。

## <a name="see-also"></a>另請參閱

+ [使用 Azure 認知搜尋篩選器的身分識別型存取控制](search-security-trimming-for-azure-search.md)
+ [Azure 認知搜尋中的篩選](search-filters.md)
+ [Azure 認知搜尋作業中的資料安全性和存取控制](search-security-overview.md)
