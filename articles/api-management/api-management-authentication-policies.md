---
title: Azure API 管理驗證原則 | Microsoft Docs
description: 了解「Azure API 管理」中可用的驗證原則。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/12/2020
ms.author: apimpm
ms.openlocfilehash: 44ebd2d3084ab8df63f2c941e6e924e6f2a86d65
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071280"
---
# <a name="api-management-authentication-policies"></a>API 管理驗證原則
本主題提供下列 API 管理原則的參考。 如需有關新增和設定原則的資訊，請參閱 [API 管理中的原則](./api-management-policies.md)。

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a> 驗證原則

-   [使用基本驗證進行驗證](api-management-authentication-policies.md#Basic) - 使用基本驗證來驗證後端服務。

-   [使用用戶端憑證進行驗證](api-management-authentication-policies.md#ClientCertificate) - 使用用戶端憑證來驗證後端服務。

-   [使用受控識別進行驗證](api-management-authentication-policies.md#ManagedIdentity) -使用 API 管理服務的 [受控識別](../active-directory/managed-identities-azure-resources/overview.md) 進行驗證。

##  <a name="authenticate-with-basic"></a><a name="Basic"></a> 使用基本驗證進行驗證
 使用 `authentication-basic` 原則以利用「基本」驗證向後端服務進行驗證。 此原則會將「HTTP 授權」標頭有效地設定為與此原則中所提供認證對應的值。

### <a name="policy-statement"></a>原則陳述式

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>範例

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>項目

|名稱|描述|必要|
|----------|-----------------|--------------|
|authentication-basic|根元素。|是|

### <a name="attributes"></a>屬性

|名稱|描述|必要|預設|
|----------|-----------------|--------------|-------------|
|username|指定「基本驗證」認證的使用者名稱。|是|N/A|
|密碼|指定「基本驗證」認證的密碼。|是|N/A|

### <a name="usage"></a>使用方式
 此原則可用於下列原則[區段](./api-management-howto-policies.md#sections)和[範圍](./api-management-howto-policies.md#scopes)。

-   **原則區段︰** inbound

-   **原則範圍：** 所有範圍

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a> 使用用戶端憑證進行驗證
 使用 `authentication-certificate` 原則以利用用戶端憑證向後端服務進行驗證。 憑證必須先[安裝到 API 管理中](./api-management-howto-mutual-certificates.md)且會以其指紋作為識別。

### <a name="policy-statement"></a>原則陳述式

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>範例

在此範例中，用戶端憑證是由其指紋來識別：

```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```

在此範例中，用戶端憑證是由資源名稱所識別：

```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
``` 

在此範例中，用戶端憑證是在原則中設定，而不是從內建的憑證存放區中取出：

```xml
<authentication-certificate body="@(context.Variables.GetValueOrDefault<byte[]>("byteCertificate"))" password="optional-certificate-password" />
```

### <a name="elements"></a>項目  
  
|名稱|描述|必要|  
|----------|-----------------|--------------|  
|authentication-certificate|根元素。|是|  
  
### <a name="attributes"></a>屬性  
  
|名稱|描述|必要|預設|  
|----------|-----------------|--------------|-------------|  
|thumbprint|用戶端憑證的指紋。|`thumbprint`或都 `certificate-id` 必須存在。|N/A|
|憑證識別碼|憑證資源名稱。|`thumbprint`或都 `certificate-id` 必須存在。|N/A|
|body|以位元組陣列形式提供的用戶端憑證。|否|N/A|
|密碼|用戶端憑證的密碼。|如果中指定的憑證受到 `body` 密碼保護，則使用。|N/A|
  
### <a name="usage"></a>使用方式  
 此原則可用於下列原則[區段](./api-management-howto-policies.md#sections)和[範圍](./api-management-howto-policies.md#scopes)。  
  
-   **原則區段︰** inbound  
  
-   **原則範圍：** 所有範圍  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a> 使用受控識別進行驗證  
 使用 `authentication-managed-identity` 原則，利用受控識別來向後端服務進行驗證。 此原則基本上會使用受控識別從 Azure Active Directory 取得存取權杖，以存取指定的資源。 成功取得權杖之後，原則會使用配置來設定標頭中的權杖值 `Authorization` `Bearer` 。

系統指派的身分識別和任何多個使用者指派的身分識別都可以用來要求權杖。 如果未 `client-id` 提供，則會假設為系統指派的身分識別。 如果 `client-id` 從 Azure Active Directory 針對該使用者指派的身分識別，要求提供變數的權杖
  
### <a name="policy-statement"></a>原則陳述式  
  
```xml  
<authentication-managed-identity resource="resource" client-id="clientid of user-assigned identity" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>範例  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>使用受控識別來向後端服務進行驗證
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Bus-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"/> <!--Your own Azure AD Application-->
```

#### <a name="use-managed-identity-and-set-header-manually"></a>使用受控識別並手動設定標頭

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"
   output-token-variable-name="msi-access-token" ignore-error="false" /> <!--Your own Azure AD Application-->
<set-header name="Authorization" exists-action="override">
   <value>@("Bearer " + (string)context.Variables["msi-access-token"])</value>
</set-header>
```

#### <a name="use-managed-identity-in-send-request-policy"></a>在傳送要求原則中使用受控識別
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>項目  
  
|名稱|描述|必要|  
|----------|-----------------|--------------|  
|驗證-受控-身分識別 |根元素。|是|  
  
### <a name="attributes"></a>屬性  
  
|名稱|描述|必要|預設|  
|----------|-----------------|--------------|-------------|  
|resource|字串。 目標 web API 的應用程式識別碼 (Azure Active Directory 中的安全資源) 。|是|N/A|
|client-id|字串。 Azure Active Directory 中使用者指派之身分識別的應用程式識別碼。|否|系統指派的身分識別|
|輸出-token-variable 名稱|字串。 將接收權杖值做為物件類型之內容變數的名稱 `string` 。 |否|N/A|  
|ignore-error|布林值。 如果設定為 `true` ，即使未取得存取權杖，原則管線仍將繼續執行。|否|false|  
  
### <a name="usage"></a>使用方式  
 此原則可用於下列原則[區段](./api-management-howto-policies.md#sections)和[範圍](./api-management-howto-policies.md#scopes)。  
  
-   **原則區段︰** inbound  
  
-   **原則範圍：** 所有範圍

## <a name="next-steps"></a>後續步驟
如需使用原則的詳細資訊，請參閱︰

+ [API 管理中的原則](api-management-howto-policies.md)
+ [轉換 API](transform-api.md)
+ [原則參考文件](./api-management-policies.md)，取得原則陳述式及其設定的完整清單
+ [原則範例](./policy-reference.md)