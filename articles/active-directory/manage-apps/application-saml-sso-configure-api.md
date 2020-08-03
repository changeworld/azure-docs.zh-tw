---
title: 使用 Microsoft Graph API 來設定 SAML 型單一登入
titleSuffix: Azure Active Directory
description: 需要為應用程式的多個執行個體設定 SAML 型單一登入嗎？ 了解如何使用 Microsoft Graph API 來自動化 SAML 型單一登入的設定，以節省時間。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: bae5770baf8cfad7e5f28d5cc0499949912c1146
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513123"
---
# <a name="automate-saml-based-sso-app-configuration-with-microsoft-graph-api"></a>使用 Microsoft Graph API 將 SAML 型 SSO 應用程式設定自動化

在此文章中，您將了解如何從 Azure Active Directory (Azure AD) 資源庫建立及設定應用程式。 此文章使用 AWS 作為範例，但您可以將此文章中的步驟用於 Azure AD 資源庫中的任何 SAML 型應用程式。

**使用 Microsoft Graph API 來自動化 SAML 單一登入設定的步驟**

| 步驟  | 詳細資料  |
|---------|---------|
| [1.建立資源庫應用程式](#step-1-create-the-gallery-application) | 登入 API 用戶端 <br> 擷取資源庫應用程式 <br> 建立資源庫應用程式|
| [2.設定單一登入](#step-2-configure-single-sign-on) | 擷取應用程式物件識別碼和服務主體物件識別碼 <br> 設定單一登入模式 <br> 設定基本 SAML URL，例如識別碼、回覆 URL、登入 URL <br> 新增應用程式角色 (選擇性)|
| [3.設定宣告對應](#step-3-configure-claims-mapping) | 建立宣告對應原則 <br> 將宣告對應原則指派給服務主體|
| [4.設定簽署憑證](#step-4-configure-signing-certificate) | 建立憑證 <BR> 新增自訂簽署金鑰 <br> 啟用自訂簽署金鑰|
| [5.指派使用者](#step-5-assign-users) | 將使用者和群組指派給應用程式
| [6.設定應用程式端](#step-6-configure-the-application-side)| 取得 Azure AD SAML 中繼資料

**文件中所使用所有 API 的清單**

請確定您有呼叫下列 API 的對應權限。

|資源類型 |方法 |
|---------|---------|
|[applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta) \(英文\)|[列出 applicationTemplate](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) \(英文\) <br>[具現化 applicationTemplate](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) \(英文\)|
|[servicePrincipals](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-1.0) \(英文\)|[更新 servicePrincipal](https://docs.microsoft.com/graph/api/serviceprincipal-update?view=graph-rest-1.0&tabs=http) \(英文\) <br> [建立 appRoleAssignments](https://docs.microsoft.com/graph/api/serviceprincipal-post-approleassignments?view=graph-rest-1.0&tabs=http) \(英文\) <br> [指派 claimsMappingPolicies](https://docs.microsoft.com/graph/api/serviceprincipal-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http) \(英文\)|
|[應用程式](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0) \(英文\)|[更新應用程式](https://docs.microsoft.com/graph/api/application-update?view=graph-rest-1.0&tabs=http) \(英文\)|
|[claimsMappingPolicy](https://docs.microsoft.com/graph/api/resources/claimsmappingpolicy?view=graph-rest-beta) \(英文\)| [建立 claimsMappingPolicy](https://docs.microsoft.com/graph/api/claimsmappingpolicy-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http) \(英文\)

>[!NOTE]
>為了方便閱讀，此文章中顯示的回應物件可能會縮短。 所有屬性都會從實際的呼叫傳回。

## <a name="step-1-create-the-gallery-application"></a>步驟 1:建立資源庫應用程式

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>登入 Microsoft Graph 總管 (建議)、Postman，或您使用的任何其他 API 用戶端

1. 啟動 [Microsoft Graph 總管](https://developer.microsoft.com/graph/graph-explorer) \(英文\)
2. 選取 [使用 Microsoft 登入]，然後使用「Azure AD 全域管理員」或「應用程式管理員」認證登入。
3. 成功登入時，您會在左側窗格中看到使用者帳戶詳細資料。

### <a name="retrieve-the-gallery-application-template-identifier"></a>擷取資源庫應用程式範本識別碼

Azure AD 應用程式資源庫中的應用程式都有一個[應用程式範本](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) \(英文\)，可描述該應用程式的中繼資料。 使用此範本，您可以在租用戶中建立應用程式和服務主體的執行個體以進行管理。

#### <a name="request"></a>要求

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>回應

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>建立資源庫應用程式

使用您在上一個步驟中針對應用程式擷取的範本識別碼，在您的租用戶中[建立應用程式和服務主體的執行個體](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) \(英文\)。

> [!NOTE] 
> 您可以使用 applicationTemplate API 來具現化[非資源庫應用程式](add-non-gallery-app.md)。 使用 applicationTemplateId `8adf8e6e-67b2-4cf2-a259-e3dc5476c621` 。

> [!NOTE]
> 允許一段時間，將應用程式布建到您的 Azure AD 租使用者中。 不是瞬間。 其中一個策略是在應用程式/服務主體物件上每隔5-10 秒執行一個 GET 查詢，直到查詢成功為止。


#### <a name="request"></a>要求

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/8b1025e4-1dd2-430b-a150-2ef79cd700f5/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>回應


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-configure-single-sign-on"></a>步驟 2:設定單一登入

### <a name="retrieve-app-object-id-and-service-principal-object-id"></a>擷取應用程式物件識別碼和服務主體物件識別碼

使用先前呼叫中的回應，來擷取及儲存應用程式物件識別碼和服務主體物件識別碼。

```
"application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63"
}
"servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```
### <a name="set-single-sign-on-mode"></a>設定單一登入模式

在此範例中，您會在 [servicePrincipal 資源類型](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-1.0)中，設定 `saml` 作為單一登入模式。 您可以設定的其他 SAML SSO 屬性包括：`notificationEmailAddresses`、`loginUrl` 和 `samlSingleSignOnSettings.relayState`

在此查詢進行工作之前，您必須先在 [圖形瀏覽器] 的 [**修改許可權**] 索引標籤上提供同意。 此外，請確定您使用的是稍早取得的**servicePrincipal**識別碼。

#### <a name="request"></a>要求

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: servicePrincipal/json

{
    "preferredSingleSignOnMode": "saml"
}
```

#### <a name="response"></a>回應

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```

### <a name="set-basic-saml-urls-such-as-identifier-reply-url-sign-on-url"></a>設定基本 SAML URL，例如識別碼、回覆 URL、登入 URL

在應用程式物件中，設定 AWS 的識別碼和回覆 URL。

請確定您使用的是稍早取得的**應用程式**識別碼。

#### <a name="request"></a>要求

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/applications/cbc071a6-0fa5-4859-8g55-e983ef63df63
Content-type: applications/json

{
    "web": {
        "redirectUris": [
            "https://signin.aws.amazon.com/saml"
        ] 
    },
    "identifierUris": [
        "https://signin.aws.amazon.com/saml"
    ]    
}
```
#### <a name="response"></a>回應

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```
### <a name="add-app-roles-optional"></a>新增應用程式角色 (選擇性)

如果應用程式需要權杖中的角色資訊，請在應用程式物件中新增角色的定義。 針對 AWS，您可以[啟用使用者佈建](../app-provisioning/application-provisioning-configure-api.md)，以從該 AWS 帳戶擷取所有角色。 

如需詳細資訊，請參閱[設定在 SAML 權杖中發出的角色宣告](../develop/active-directory-enterprise-app-role-management.md)

> [!NOTE] 
> 新增應用程式角色時，請勿修改預設的應用程式角色 msiam_access。 

#### <a name="request"></a>要求

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/serviceprincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: serviceprincipals/json

{
"appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "454dc4c2-8176-498e-99df-8c4efcde41ef",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-admin,arn:aws:iam::212743507312:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Finance,WAAD",
            "displayName": "Finance,WAAD",
            "id": "8642d5fa-18a3-4245-ab8c-a96000c1a217",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-finance,arn:aws:iam::212743507312:saml-provider/WAAD"
        }
    ]

}
```

#### <a name="response"></a>回應

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-3-configure-claims-mapping"></a>步驟 3：設定宣告對應

### <a name="create-claims-mapping-policy"></a>建立宣告對應原則

除了基本宣告之外，請設定下列宣告，以便 Azure AD 在 SAML 權杖中加以發出：

| 宣告名稱 | 來源  |
|---------|---------|
| `https://aws.amazon.com/SAML/Attributes/Role` | assignedroles| 
| `https://aws.amazon.com/SAML/Attributes/RoleSessionName` | userprincipalname |
| `https://aws.amazon.com/SAML/Attributes/SessionDuration` | "900" |
| 角色 | assignedroles |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` | userprincipalname |

如需詳細資訊，請參閱[自訂權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)。

> [!NOTE]
> 宣告對應原則中的某些金鑰會區分大小寫（例如「版本」）。 如果您收到錯誤訊息，例如「屬性有不正確值」，則可能是區分大小寫的問題。

#### <a name="request"></a>要求

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/v1.0/policies/claimsMappingPolicies
Content-type: claimsMappingPolicies/json

{
    "definition":[
            "{\"ClaimsMappingPolicy\": {
                \"Version\": 1,
                \"IncludeBasicClaimSet\": \"true\",
                \"ClaimsSchema\": [
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/Role\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"900\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"appRoles\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"
                    }
                ]
            }
        }"

    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

#### <a name="response"></a>回應

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.claimsMappingPolicies",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: claimsMappingPolicies/json

{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#policies/claimsMappingPolicies/$entity",
    "id": "6b33aa8e-51f3-41a6-a0fd-d660d276197a",
    "definition": [
        "{\"ClaimsMappingPolicy\": {\"Version\": 1,\"IncludeBasicClaimSet\": \"true\",\"ClaimsSchema\": [{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"https://aws.amazon.com/SAML/Attributes/Role\"\r\n                    },{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"},{\"Source\": \"user\",\"ID\": \"900\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"},{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"appRoles\"},{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"}]}}"
    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

### <a name="assign-claims-mapping-policy-to-service-principal"></a>將宣告對應原則指派給服務主體

#### <a name="request"></a>要求

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/claimsMappingPolicies/$ref

Content-type: claimsMappingPolicies/json

{
  "@odata.id":"https://graph.microsoft.com/v1.0/policies/claimsMappingPolicies/6b33aa8e-51f3-41a6-a0fd-d660d276197a"
}
```

#### <a name="response"></a>回應

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-4-configure-signing-certificate"></a>步驟 4：設定簽署憑證

根據預設，使用 [applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta) \(英文\) API 不會建立簽署憑證。 建立您的自訂簽署憑證，並將其指派給應用程式。 

### <a name="create-a-custom-signing-certificate"></a>建立自訂簽署憑證

若要進行測試，您可以使用下列 PowerShell 命令來取得自我簽署憑證。 接著，您必須使用其他工具來操作並提取您需要的值。 使用您公司的最佳安全性做法，建立用於生產的簽署憑證。

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [string]$fqdn,
    [Parameter(Mandatory=$true)]
    [string]$pwd,
    [Parameter(Mandatory=$true)]
    [string]$location
) 

if (!$PSBoundParameters.ContainsKey('location'))
{
    $location = "."
} 

$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx" 

Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile
```

或者，您可以使用下列 c # 主控台應用程式做為概念證明，以瞭解如何取得所需的值。 請注意，此程式碼**僅供學習和參考**之用，不應在生產環境中以實際方式使用。

```csharp
using System;
using System.Security.Cryptography;
using System.Security.Cryptography.X509Certificates;
using System.Text;


/* CONSOLE APP - PROOF OF CONCEPT CODE ONLY!!
 * This code uses a self signed certificate and should not be used 
 * in production. This code is for reference and learning ONLY.
 */
namespace Self_signed_cert
{
    class Program
    {
        static void Main(string[] args)
        {
            // Generate a guid to use as a password and then create the cert.
            string password = Guid.NewGuid().ToString();
            var selfsignedCert = buildSelfSignedServerCertificate(password);

            // Print values so we can copy paste into the JSON fields.
            // Print out the private key in base64 format.
            Console.WriteLine("Private Key: {0}{1}", Convert.ToBase64String(selfsignedCert.Export(X509ContentType.Pfx, password)), Environment.NewLine);

            // Print out the start date in ISO 8601 format.
            DateTime startDate = DateTime.Parse(selfsignedCert.GetEffectiveDateString()).ToUniversalTime();
            Console.WriteLine("For All startDateTime: " + startDate.ToString("o"));

            // Print out the end date in ISO 8601 format.
            DateTime endDate = DateTime.Parse(selfsignedCert.GetExpirationDateString()).ToUniversalTime();
            Console.WriteLine("For All endDateTime: " + endDate.ToString("o"));

            // Print the GUID used for keyId
            string signAndPasswordGuid = Guid.NewGuid().ToString();
            string verifyGuid = Guid.NewGuid().ToString();
            Console.WriteLine("GUID to use for keyId for keyCredentials->Usage == Sign and passwordCredentials: " + signAndPasswordGuid);
            Console.WriteLine("GUID to use for keyId for keyCredentials->Usage == Verify: " + verifyGuid);

            // Print out the password.
            Console.WriteLine("Password is: {0}", password);

            // Print out a displayName to use as an example.
            Console.WriteLine("displayName to use: CN=Example");
            Console.WriteLine();

            // Print out the public key.
            Console.WriteLine("Public Key: {0}{1}", Convert.ToBase64String(selfsignedCert.Export(X509ContentType.Cert)), Environment.NewLine);
            Console.WriteLine();

            // Generate the customKeyIdentifier using hash of thumbprint.
            Console.WriteLine("You can generate the customKeyIdentifier by getting the SHA256 hash of the certs thumprint.\nThe certs thumbprint is: {0}{1}", selfsignedCert.Thumbprint, Environment.NewLine);
            Console.WriteLine("The hash of the thumbprint that we will use for customeKeyIdentifier is:");
            string keyIdentifier = GetSha256FromThumbprint(selfsignedCert.Thumbprint);
            Console.WriteLine(keyIdentifier);
        }

        // Generate a self-signed certificate.
        private static X509Certificate2 buildSelfSignedServerCertificate(string password)
        {
            const string CertificateName = @"Microsoft Azure Federated SSO Certificate TEST";
            DateTime certificateStartDate = DateTime.UtcNow;
            DateTime certificateEndDate = certificateStartDate.AddYears(2).ToUniversalTime();

            X500DistinguishedName distinguishedName = new X500DistinguishedName($"CN={CertificateName}");

            using (RSA rsa = RSA.Create(2048))
            {
                var request = new CertificateRequest(distinguishedName, rsa, HashAlgorithmName.SHA256, RSASignaturePadding.Pkcs1);

                request.CertificateExtensions.Add(
                    new X509KeyUsageExtension(X509KeyUsageFlags.DataEncipherment | X509KeyUsageFlags.KeyEncipherment | X509KeyUsageFlags.DigitalSignature, false));

                var certificate = request.CreateSelfSigned(new DateTimeOffset(certificateStartDate), new DateTimeOffset(certificateEndDate));
                certificate.FriendlyName = CertificateName;

                return new X509Certificate2(certificate.Export(X509ContentType.Pfx, password), password, X509KeyStorageFlags.Exportable);
            }
        }

        // Generate hash from thumbprint.
        public static string GetSha256FromThumbprint(string thumbprint)
        {
            var message = Encoding.ASCII.GetBytes(thumbprint);
            SHA256Managed hashString = new SHA256Managed();
            return Convert.ToBase64String(hashString.ComputeHash(message));
        }
    }
}
```

### <a name="add-a-custom-signing-key"></a>新增自訂簽署金鑰

將下列資訊新增至服務主體：

* 私密金鑰
* 密碼
* 公開金鑰 

擷取編碼自 PFX 檔案的私密和公開金鑰 Base64 編碼。 若要深入了解屬性，請參閱 [keyCredential 資源類型](https://docs.microsoft.com/graph/api/resources/keycredential?view=graph-rest-1.0) \(英文\)。

請確定 keyCredential 用於 "Sign" 的 keyId 符合 passwordCredential 的 keyId。 您可以藉由取得憑證指紋的雜湊來產生 `customkeyIdentifier`。 請參閱上述的 c # 參考程式碼。

#### <a name="request"></a>要求

> [!NOTE]
> 為了方便閱讀，keyCredentials 屬性中的 "key" 值已縮短。 此值為 base 64 編碼。 若為私密金鑰，此屬性 `usage` 為 "Sign"。 對於公開金鑰，此屬性 `usage` 為 "Verify"。

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "X509CertAndPassword",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=awsAPI"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=awsAPI"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "61891f4ee44d"
        }
    ]
}
```

#### <a name="response"></a>回應

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

### <a name="activate-the-custom-signing-key"></a>啟用自訂簽署金鑰

您必須將 `preferredTokenSigningKeyThumbprint` 屬性設定為您想要 Azure AD 用來簽署 SAML 回應的憑證指紋。 

#### <a name="request"></a>要求

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "preferredTokenSigningKeyThumbprint": "AC09FEF18DDE6983EE2A164FBA3C4DD7518BD787"
}
```

#### <a name="response"></a>回應

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```
## <a name="step-5-assign-users"></a>步驟 5：指派使用者

### <a name="assign-users-and-groups-to-the-application"></a>將使用者和群組指派給應用程式

將下列使用者指派給服務主體，並指派 AWS_Role1。 

| 名稱  | ID  |
|---------|---------|
| 使用者識別碼 (principalId) | 6cad4079-4e79-4a3f-9efb-ea30a14bdb26 |
| 類型 (principalType) | User |
| 應用程式角色識別碼 (appRoleId) | 454dc4c2-8176-498e-99df-8c4efcde41ef |
| servicePrincipalID (resourceId) | f47a6776-bca7-4f2e-bc6c-eec59d058e3e |

#### <a name="request"></a>要求

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/appRoleAssignments

Content-type: appRoleAssignments/json

{
  "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
  "principalType": "User",
  "appRoleId":"454dc4c2-8176-498e-99df-8c4efcde41ef",
  "resourceId":"f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```
#### <a name="response"></a>回應

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 201 
Content-type: appRoleAssignments/json

{
    "id": "rq7hyzl4yECaNZleMrTpDV-OCe5TEl5Ao_o76XMrRFU",
    "creationTimestamp": "2020-04-23T17:38:13.2508567Z",
    "appRoleId": "454dc4c2-8176-498e-99df-8c4efcde41ef",
    "principalDisplayName": "User 1",
    "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
    "principalType": "User",
    "resourceDisplayName": "AWS API Created",
    "resourceId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```

如需詳細資訊，請參閱 [appRoleAssignment](https://docs.microsoft.com/graph/api/resources/approleassignment?view=graph-rest-1.0) \(英文\) 資源類型。

## <a name="step-6-configure-the-application-side"></a>步驟 6：設定應用程式端

### <a name="get-azure-ad-saml-metadata"></a>取得 Azure AD SAML 中繼資料

使用下列 URL 來取得特定已設定應用程式的 Azure AD SAML 中繼資料。 中繼資料包含諸如簽署憑證、Azure AD entityID 和 Azure AD SingleSignOnService 等資訊。

https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id}

## <a name="next-steps"></a>後續步驟
- [使用 Microsoft Graph API 來設定使用者佈建](../app-provisioning/application-provisioning-configure-api.md)
- [使用 AD FS 應用程式活動報告將應用程式遷移至 Azure AD](migrate-adfs-application-activity.md)
