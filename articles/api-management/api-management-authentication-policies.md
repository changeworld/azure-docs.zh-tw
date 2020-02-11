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
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 5ca153f0d52b65aa1ee56d5757381f1f31c7eeb5
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120814"
---
# <a name="api-management-authentication-policies"></a>API 管理驗證原則
本主題提供下列 API 管理原則的參考。 如需有關新增和設定原則的資訊，請參閱 [API 管理中的原則](https://go.microsoft.com/fwlink/?LinkID=398186)。

##  <a name="AuthenticationPolicies"></a> 驗證原則

-   [使用基本驗證進行驗證](api-management-authentication-policies.md#Basic) - 使用基本驗證來驗證後端服務。

-   [使用用戶端憑證進行驗證](api-management-authentication-policies.md#ClientCertificate) - 使用用戶端憑證來驗證後端服務。

-   [使用受控識別進行驗證](api-management-authentication-policies.md#ManagedIdentity)-使用 API 管理服務的[受控識別](../active-directory/managed-identities-azure-resources/overview.md)進行驗證。

##  <a name="Basic"></a> 使用基本驗證進行驗證
 使用 `authentication-basic` 原則以利用「基本」驗證向後端服務進行驗證。 此原則會將「HTTP 授權」標頭有效地設定為與此原則中所提供認證對應的值。

### <a name="policy-statement"></a>原則陳述式

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>範例

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>元素

|名稱|描述|必要項|
|----------|-----------------|--------------|
|authentication-basic|根元素。|是|

### <a name="attributes"></a>屬性

|名稱|描述|必要項|預設|
|----------|-----------------|--------------|-------------|
|username|指定「基本驗證」認證的使用者名稱。|是|N/A|
|密碼|指定「基本驗證」認證的密碼。|是|N/A|

### <a name="usage"></a>使用方式
 此原則可用於下列原則[區段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[範圍](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)。

-   **原則區段︰** inbound

-   **原則範圍：** 所有範圍

##  <a name="ClientCertificate"></a> 使用用戶端憑證進行驗證
 使用 `authentication-certificate` 原則以利用用戶端憑證向後端服務進行驗證。 憑證必須先[安裝到 API 管理中](https://go.microsoft.com/fwlink/?LinkID=511599)且會以其指紋作為識別。

### <a name="policy-statement"></a>原則陳述式

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>範例

在此範例中，用戶端憑證是由其指紋來識別。
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
在此範例中，用戶端憑證是以資源名稱來識別。
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>元素  
  
|名稱|描述|必要項|  
|----------|-----------------|--------------|  
|authentication-certificate|根元素。|是|  
  
### <a name="attributes"></a>屬性  
  
|名稱|描述|必要項|預設|  
|----------|-----------------|--------------|-------------|  
|thumbprint|用戶端憑證的指紋。|必須要有 `thumbprint` 或 `certificate-id`。|N/A|  
|憑證識別碼|憑證資源名稱。|必須要有 `thumbprint` 或 `certificate-id`。|N/A|  
  
### <a name="usage"></a>使用方式  
 此原則可用於下列原則[區段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[範圍](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)。  
  
-   **原則區段︰** inbound  
  
-   **原則範圍：** 所有範圍  

##  <a name="ManagedIdentity"></a>使用受控識別進行驗證  
 使用 `authentication-managed-identity` 原則，以使用 API 管理服務的受控識別來向後端服務進行驗證。 此原則基本上會使用受控識別從 Azure Active Directory 取得存取權杖，以存取指定的資源。 成功取得權杖之後，原則會使用 `Bearer` 配置，在 `Authorization` 標頭中設定權杖的值。
  
### <a name="policy-statement"></a>原則陳述式  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>範例  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>使用受控識別向後端服務進行驗證
```xml  
<authentication-managed-identity resource="https://graph.windows.net"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Busr-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>在傳送要求原則中使用受控識別
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>元素  
  
|名稱|描述|必要項|  
|----------|-----------------|--------------|  
|驗證-受控-身分識別 |根元素。|是|  
  
### <a name="attributes"></a>屬性  
  
|名稱|描述|必要項|預設|  
|----------|-----------------|--------------|-------------|  
|resource|字串。 Azure Active Directory 中目標 Web API （受保護的資源）的應用程式識別碼。|是|N/A|  
|輸出-token-變數-名稱|字串。 內容變數的名稱，將會以物件類型 `string`的形式接收 token 值。 |否|N/A|  
|ignore-error|布林值 (Boolean)： 如果設定為 `true`，即使未取得存取權杖，原則管線仍會繼續執行。|否|false|  
  
### <a name="usage"></a>使用方式  
 此原則可用於下列原則[區段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[範圍](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)。  
  
-   **原則區段︰** inbound  
  
-   **原則範圍：** 所有範圍

## <a name="next-steps"></a>後續步驟
如需使用原則的詳細資訊，請參閱︰

+ [API 管理中的原則](api-management-howto-policies.md)
+ [轉換 API](transform-api.md)
+ [原則參考文件](api-management-policy-reference.md)，取得原則陳述式及其設定的完整清單
+ [原則範例](policy-samples.md)
