---
title: 使用 Azure Active Directory 驗證 Azure Batch 服務解決方案 | Microsoft Docs
description: Batch 支援 Azure AD 從 Batch 服務進行驗證。
services: batch
documentationcenter: .net
author: ju-shim
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 01/28/2020
ms.author: jushiman
ms.openlocfilehash: ce3582539d6130e13ef205806d780164ba70c4fe
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842532"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>使用 Active Directory 驗證 Batch 服務解決方案

Azure Batch 支援使用[Azure Active Directory][aad_about] （Azure AD）進行驗證。 Azure AD 是 Microsoft 的多租用戶雲端型目錄和身分識別管理服務。 Azure 本身會使用 Azure AD 來驗證其客戶、服務管理員和組織的使用者。

搭配 Azure Batch 使用 Azure AD 驗證時，您可以使用下列其中一種方式進行驗證：

- 使用**整合式驗證**來驗證與應用程式互動的使用者。 使用整合式驗證的應用程式會蒐集使用者的認證，並使用這些認證來驗證對 Batch 資源的存取。
- 使用**服務主體**來驗證自動執行的應用程式。 服務主體會定義應用程式的原則和權限，以便在執行階段存取資源時代表應用程式。

若要深入了解 Azure AD，請參閱 [Azure Active Directory 文件](https://docs.microsoft.com/azure/active-directory/)。

## <a name="endpoints-for-authentication"></a>用於驗證的端點

若要使用 Azure AD 驗證 Batch 應用程式，您需要在程式碼中包含一些已知的端點。

### <a name="azure-ad-endpoint"></a>Azure AD 端點

基底 Azure AD 授權端點為：

`https://login.microsoftonline.com/`

若要使用 Azure AD 進行驗證，您可以同時使用此端點及租用戶識別碼 (目錄識別碼)。 租用戶識別碼會識別要用來驗證的 Azure AD 租用戶。 若要擷取租用戶識別碼，請依照[取得 Azure Active Directory 的租用戶識別碼](#get-the-tenant-id-for-your-active-directory)所述的步驟執行：

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> 當您使用服務主體進行驗證時，租用戶特定的端點是必要的。 
> 
> 當您使用整合式驗證進行驗證時，租用戶特定的端點是選擇性的，但建議使用。 不過，您也可以使用 Azure AD 的通用端點。 若未提供特定的租用戶，通用端點會提供一般認證蒐集介面。 通用端點為 `https://login.microsoftonline.com/common`。
>
>

如需 Azure AD 端點的詳細資訊，請參閱[Azure AD 的驗證案例][aad_auth_scenarios]。

### <a name="batch-resource-endpoint"></a>Batch 資源端點

使用 **Azure Batch 資源端點**來取得權杖，以驗證對 Batch 服務的要求：

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>向租用戶註冊您的應用程式

使用 Azure AD 進行驗證的第一個步驟是在 Azure AD 租用戶中註冊您的應用程式。 註冊您的應用程式，可讓您從程式碼中呼叫 [Azure Active Directory Authentication Library][aad_adal] (ADAL)。 ADAL 提供 API，從您的應用程式使用 Azure AD 進行驗證。 不論您是否計劃使用整合式驗證或服務主體，都需要註冊您的應用程式。

當您註冊應用程式時，會向 Azure AD 提供應用程式的相關資訊。 Azure AD 接著會提供您在執行階段用來將應用程式與 Azure AD 產生關聯的應用程式識別碼 (也稱為「用戶端識別碼」)。 若要深入了解應用程式識別碼，請參閱[Azure Active Directory 中的應用程式物件和服務主體物件之間的關聯性討論](../active-directory/develop/app-objects-and-service-principals.md)。

若要註冊您的 Batch 應用程式，請遵循[整合應用程式與 Azure Active Directory][aad_integrate]中的[新增應用程式](../active-directory/develop/quickstart-register-app.md)一節中的步驟。 如果您將應用程式註冊為原生應用程式，就能為**重新導向 URI** 指定任何有效的 URI。 它不需要是實際的端點。

註冊應用程式之後，您將會看到應用程式識別碼：

![向 Azure AD 註冊您的 Batch 應用程式](./media/batch-aad-auth/app-registration-data-plane.png)

如需向 Azure AD 註冊應用程式的詳細資訊，請參閱 [Azure AD 的驗證案例](../active-directory/develop/authentication-scenarios.md)。

## <a name="get-the-tenant-id-for-your-active-directory"></a>取得 Active Directory 的租用戶識別碼

租用戶識別碼會識別可為您的應用程式提供驗證服務的 Azure AD 租用戶。 若要取得租用戶識別碼，請遵循下列步驟：

1. 在 Azure 入口網站中，選取您的 Active Directory。
1. 選取 [屬性]。
1. 複製針對**目錄識別碼**提供的 GUID 值。 此值也稱為租用戶識別碼。

![複製目錄識別碼](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>使用整合式驗證

若要使用整合式驗證進行驗證，您需要為應用程式授與權限，以連接到 Batch 服務 API。 此步驟可讓您的應用程式使用 Azure AD 來驗證與對 Batch 服務 API 的呼叫。

一旦註冊您的應用程式之後，請在 Azure 入口網站中遵循下列步驟，以授與它存取 Batch 服務的權限：

1. 在 Azure 入口網站的左側導覽窗格中，選擇 [所有服務]。 選取 [應用程式註冊]。
1. 在應用程式註冊清單中搜尋您應用程式的名稱︰

    ![搜尋您的應用程式名稱](./media/batch-aad-auth/search-app-registration.png)

1. 選取應用程式，然後選取 [ **API 許可權**]。
1. 在 [ **API 許可權**] 區段中，選取 [**新增許可權**]。
1. 在 [選取 API] 中，搜尋 Batch API。 搜尋這些字串，直到您找到 API 為止：
    1. **Microsoft Azure Batch**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** 是 Batch API 的識別碼。
1. 一旦您找到 Batch API，請選取它，然後選取 [**選取**]。
1. 在 [**選取許可權**] 中，選取 [**存取 Azure Batch 服務**] 旁的核取方塊，然後選取 [**新增許可權**]。

[ **API 許可權**] 區段現在會顯示您的 Azure AD 應用程式可同時存取 Microsoft Graph 和 BATCH 服務 API。 當您第一次向 Azure AD 註冊應用程式時，系統會將許可權授與 Microsoft Graph。

![授與 API 權限](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>使用服務主體

若要驗證自動執行的應用程式，您可以使用服務主體。 註冊您的應用程式之後，請在 Azure 入口網站中遵循下列步驟來設定服務主體：

1. 要求應用程式的密碼。
1. 將角色型存取控制（RBAC）指派給您的應用程式。

### <a name="request-a-secret-for-your-application"></a>要求應用程式的密碼

當您的應用程式使用服務主體進行驗證時，它會將應用程式識別碼和密碼傳送至 Azure AD。 您必須建立並複製祕密金鑰，以便從程式碼中使用。

在 Azure 入口網站中遵循下列步驟：

1. 在 Azure 入口網站的左側導覽窗格中，選擇 [所有服務]。 選取 [應用程式註冊]。
1. 從應用程式註冊清單中選取您的應用程式。
1. 選取應用程式，然後選取 [**憑證 & 密碼**]。 在 [**用戶端密碼**] 區段中，選取 [**新增用戶端密碼**]。
1. 若要建立密碼，請輸入密碼的描述。 然後選取 [一年]、[兩年] 或 [沒有到期日] 的密碼過期。
1. 選取 [**新增**] 以建立並顯示密碼。 將 [秘密] 值複製到安全的位置，因為您在離開頁面之後將無法再存取它。

    ![建立祕密金鑰](./media/batch-aad-auth/secret-key.png)

### <a name="assign-rbac-to-your-application"></a>將 RBAC 指派給您的應用程式

若要向服務主體進行驗證，您必須將 RBAC 指派給您的應用程式。 請遵循下列步驟：

1. 在 Azure 入口網站中，瀏覽至應用程式所使用的 Batch 帳戶。
1. 在 Batch 帳戶的 [**設定**] 區段中，選取 [**存取控制（IAM）** ]。
1. 選取 [角色指派] 索引標籤。
1. 選取 [新增角色指派]。
1. 從 [角色] 下拉式清單中，選擇應用程式的 [參與者] 或 [讀者] 角色。 如需這些角色的詳細資訊，請參閱[在 Azure 入口網站中開始使用角色型存取控制](../role-based-access-control/overview.md)。  
1. 在 [選取] 欄位中，輸入應用程式的名稱。 從清單中選取您的應用程式，然後選取 [**儲存**]。

您的應用程式現在應該會以您指派的 RBAC 角色，出現在您的存取控制設定中。

![將 RBAC 角色指派給應用程式](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>指派自訂角色

自訂角色會授與使用者更細微的許可權，以提交作業、工作等等。 這可防止使用者執行會影響成本的作業，例如建立集區或修改節點。

您可以使用自訂角色，將許可權授與下列 RBAC 作業的 Azure AD 使用者、群組或服務主體：

- Microsoft.Batch/batchAccounts/pools/write
- Microsoft.Batch/batchAccounts/pools/delete
- Microsoft.Batch/batchAccounts/pools/read
- BatchAccounts/jobSchedules/write
- BatchAccounts/jobSchedules/delete
- BatchAccounts/jobSchedules/read
- Microsoft。 Batch/batchAccounts/作業/寫入
- Microsoft Batch/batchAccounts/job/delete
- Microsoft。 Batch/batchAccounts/作業/讀取
- Microsoft.Batch/batchAccounts/certificates/write
- Microsoft.Batch/batchAccounts/certificates/delete
- Microsoft.Batch/batchAccounts/certificates/read
- BatchAccounts/read （適用于任何讀取作業）
- BatchAccounts/listKeys/action （適用于任何作業）

自訂角色適用于 Azure AD 驗證的使用者，而不是 Batch 帳號憑證（共用金鑰）。 請注意，Batch 帳號憑證會授與 Batch 帳戶的完整許可權。 另請注意，使用 autopool 的作業需要集區層級許可權。

以下是自訂角色定義的範例：

```json
{
 "properties":{
    "roleName":"Azure Batch Custom Job Submitter",
    "type":"CustomRole",
    "description":"Allows a user to submit jobs to Azure Batch but not manage pools",
    "assignableScopes":[
      "/subscriptions/88888888-8888-8888-8888-888888888888"
    ],
    "permissions":[
      {
        "actions":[
          "Microsoft.Batch/*/read",
          "Microsoft.Authorization/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*",
          "Microsoft.Insights/alertRules/*"
        ],
        "notActions":[

        ],
        "dataActions":[
          "Microsoft.Batch/batchAccounts/jobs/*",
          "Microsoft.Batch/batchAccounts/jobSchedules/*"
        ],
        "notDataActions":[

        ]
      }
    ]
  }
}
```

如需有關建立自訂角色的一般資訊，請參閱[Azure 資源的自訂角色](../role-based-access-control/custom-roles.md)。

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>取得 Azure Active Directory 的租用戶識別碼

租用戶識別碼會識別可為您的應用程式提供驗證服務的 Azure AD 租用戶。 若要取得租用戶識別碼，請遵循下列步驟：

1. 在 Azure 入口網站中，選取您的 Active Directory。
1. 選取 [屬性]。
1. 複製針對**目錄識別碼**提供的 GUID 值。 此值也稱為租用戶識別碼。

![複製目錄識別碼](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>程式碼範例

本節中的程式碼範例示範如何使用整合式驗證搭配 Azure AD 進行驗證，以及使用服務主體進行驗證。 這些程式碼範例大部分會使用 .NET，但概念類似其他語言。

> [!NOTE]
> Azure AD 驗證權杖會在一小時後過期。 使用長時間執行 **BatchClient** 物件時，我們建議您在每個要求從 ADAL 擷取權杖，以確保您一律擁有有效的權杖。 
>
>
> 若要在 .NET 中達到此目的，撰寫可從 Azure AD 中擷取權杖的方法，並傳遞該方法至 **BatchTokenCredentials** 物件做為委派。 每個要求都會對 Batch 服務呼叫委派方法，以確保已提供有效的權杖。 根據預設，ADAL 會快取權杖，因此只在必要時才會從 Azure AD 擷取新的權杖。 如需 Azure AD 中之權杖的詳細資訊，請參閱[Azure AD 的驗證案例][aad_auth_scenarios]。
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>程式碼範例︰搭配 Batch .NET 使用 Azure AD 整合式驗證

若要從 Batch .NET 使用整合式驗證進行驗證，請參考 [Azure Batch .NET (英文)](https://www.nuget.org/packages/Microsoft.Azure.Batch/) 封裝和 [ADAL (英文)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 封裝。

在您的程式碼中，包括下列 `using` 陳述式︰

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

在您的程式碼中參考 Azure AD 端點，包括租用戶識別碼。 若要擷取租用戶識別碼，請依照[取得 Azure Active Directory 的租用戶識別碼](#get-the-tenant-id-for-your-active-directory)所述的步驟執行：

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

參考 Batch 服務資源端點：

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

參考您的 Batch 帳戶：

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

指定您應用程式的應用程式識別碼 (用戶端識別碼)。 應用程式識別碼可在 Azure 入口網站中，從您的應用程式註冊取得：

```csharp
private const string ClientId = "<application-id>";
```

如果您以原生應用程式的形式來註冊應用程式，也請複製指定的重新導向 URI。 您程式碼中指定的重新導向 URI 必須符合您註冊應用程式時所提供的重新導向 URI：

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

撰寫回呼方法，以從 Azure AD 取得驗證權杖。 此處所示的 **GetAuthenticationTokenAsync** 回呼方法會呼叫 ADAL，來驗證與應用程式互動的使用者。 ADAL 所提供的 **AcquireTokenAsync** 方法會提示使用者輸入其認證，而應用程式會在使用者提供認證之後繼續進行 (除非它已經快取了認證)：

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

建構接受委派做為參數的 **BatchTokenCredentials** 物件。 使用這些認證來開啟 **BatchClient** 物件。 您可以針對 Batch 服務的後續作業使用該 **BatchClient** 物件：

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>程式碼範例︰搭配 Batch .NET 使用 Azure AD 服務主體

若要從 Batch .NET 使用服務主體進行驗證，請參考 [Azure Batch .NET (英文)](https://www.nuget.org/packages/Azure.Batch/) 封裝和 [ADAL (英文)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 封裝。

在您的程式碼中，包括下列 `using` 陳述式︰

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

在您的程式碼中參考 Azure AD 端點，包括租用戶識別碼。 使用服務主體時，您必須提供租用戶特定的端點。 若要擷取租用戶識別碼，請依照[取得 Azure Active Directory 的租用戶識別碼](#get-the-tenant-id-for-your-active-directory)所述的步驟執行：

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

參考 Batch 服務資源端點：  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

參考您的 Batch 帳戶：

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

指定您應用程式的應用程式識別碼 (用戶端識別碼)。 應用程式識別碼可在 Azure 入口網站中，從您的應用程式註冊取得：

```csharp
private const string ClientId = "<application-id>";
```

指定您從 Azure 入口網站中複製的祕密金鑰：

```csharp
private const string ClientKey = "<secret-key>";
```

撰寫回呼方法，以從 Azure AD 取得驗證權杖。 此處所示的 **GetAuthenticationTokenAsync** 回呼方法會呼叫 ADAL 進行自動驗證：

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

建構接受委派做為參數的 **BatchTokenCredentials** 物件。 使用這些認證來開啟 **BatchClient** 物件。 然後針對 Batch 服務的後續作業使用該 **BatchClient** 物件：

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>程式碼範例︰搭配 Batch Python 使用 Azure AD 服務主體

若要從 Batch Python 向服務主體進行驗證，請安裝並參考 [azure-batch](https://pypi.org/project/azure-batch/) 和 [azure-common](https://pypi.org/project/azure-common/) 模組。

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

使用服務主體時，您必須提供租用戶識別碼。 若要擷取租用戶識別碼，請依照[取得 Azure Active Directory 的租用戶識別碼](#get-the-tenant-id-for-your-active-directory)所述的步驟執行：

```python
TENANT_ID = "<tenant-id>"
```

參考 Batch 服務資源端點：  

```python
RESOURCE = "https://batch.core.windows.net/"
```

參考您的 Batch 帳戶：

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

指定您應用程式的應用程式識別碼 (用戶端識別碼)。 應用程式識別碼可在 Azure 入口網站中，從您的應用程式註冊取得：

```python
CLIENT_ID = "<application-id>"
```

指定您從 Azure 入口網站中複製的祕密金鑰：

```python
SECRET = "<secret-key>"
```

建立 **ServicePrincipalCredentials** 物件：

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

使用服務主體認證來開啟 **BatchServiceClient** 物件。 接著，針對 Batch 服務的後續作業使用該 **BatchServiceClient** 物件。

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure AD，請參閱 [Azure Active Directory 文件](https://docs.microsoft.com/azure/active-directory/)。 [Azure 程式碼範例](https://azure.microsoft.com/resources/samples/?service=active-directory)程式庫中有深入的範例示範如何使用 ADAL。

- 若要深入了解服務主體，請參閱 [Azure Active Directory 中的應用程式和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)。 若要使用 Azure 入口網站建立服務主體，請參閱[使用入口網站來建立可存取資源的 Active Directory 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。 您也可以使用 PowerShell 或 Azure CLI 來建立服務主體。

- 若要使用 Azure AD 驗證 Batch Management 應用程式，請參閱[使用 Active Directory 驗證 Batch Management 解決方案](batch-aad-auth-management.md)。

- 如需如何建立 Batch 用戶端 (使用 Azure AD 權杖進行驗證) 的 Python 範例，請參閱[使用 Python 指令碼部署 Azure Batch 自訂映像](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md)範例。

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "什麼是 Azure Active Directory？"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Azure AD 的驗證案例"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "整合應用程式與 Azure Active Directory"
[azure_portal]: https://portal.azure.com
