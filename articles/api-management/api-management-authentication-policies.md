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
ms.openlocfilehash: 828f738ff8923dc8194e2449f5fb0be74ef45ad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473552"
---
# <a name="api-management-authentication-policies"></a>API 管理驗證原則
本主題提供下列 API 管理原則的參考。 如需有關新增和設定原則的資訊，請參閱 [API 管理中的原則](https://go.microsoft.com/fwlink/?LinkID=398186)。

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a> 驗證原則

-   [使用基本驗證進行驗證](api-management-authentication-policies.md#Basic) - 使用基本驗證來驗證後端服務。

-   [使用用戶端憑證進行驗證](api-management-authentication-policies.md#ClientCertificate) - 使用用戶端憑證來驗證後端服務。

-   [使用託管標識進行身份驗證](api-management-authentication-policies.md#ManagedIdentity)- 使用 API 管理服務的[託管標識](../active-directory/managed-identities-azure-resources/overview.md)進行身份驗證。

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

### <a name="elements"></a>元素

|名稱|描述|必要|
|----------|-----------------|--------------|
|authentication-basic|根元素。|是|

### <a name="attributes"></a>屬性

|名稱|描述|必要|預設|
|----------|-----------------|--------------|-------------|
|username|指定「基本驗證」認證的使用者名稱。|是|N/A|
|密碼|指定「基本驗證」認證的密碼。|是|N/A|

### <a name="usage"></a>使用量
 此原則可用於下列原則[區段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[範圍](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)。

-   **原則區段︰** inbound

-   **原則範圍：** 所有範圍

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a> 使用用戶端憑證進行驗證
 使用 `authentication-certificate` 原則以利用用戶端憑證向後端服務進行驗證。 憑證必須先[安裝到 API 管理中](https://go.microsoft.com/fwlink/?LinkID=511599)且會以其指紋作為識別。

### <a name="policy-statement"></a>原則陳述式

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>範例

在此示例中，用戶端憑證由其指紋標識。
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
在此示例中，用戶端憑證由資源名稱標識。
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>元素  
  
|名稱|描述|必要|  
|----------|-----------------|--------------|  
|authentication-certificate|根元素。|是|  
  
### <a name="attributes"></a>屬性  
  
|名稱|描述|必要|預設|  
|----------|-----------------|--------------|-------------|  
|thumbprint|用戶端憑證的指紋。|或者`thumbprint``certificate-id`必須存在。|N/A|  
|證書 id|證書資源名稱。|或者`thumbprint``certificate-id`必須存在。|N/A|  
  
### <a name="usage"></a>使用量  
 此原則可用於下列原則[區段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[範圍](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)。  
  
-   **原則區段︰** inbound  
  
-   **原則範圍：** 所有範圍  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a>使用託管標識進行身份驗證  
 使用策略`authentication-managed-identity`使用 API 管理服務的託管標識對後端服務進行身份驗證。 此策略基本上使用託管標識從 Azure 活動目錄獲取訪問權杖以訪問指定資源。 成功獲取權杖後，策略將使用`Authorization``Bearer`方案在標頭中設置權杖的值。
  
### <a name="policy-statement"></a>原則陳述式  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>範例  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>使用託管標識對後端服務進行身份驗證
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
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Busr-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>在發送請求策略中使用託管標識
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>元素  
  
|名稱|描述|必要|  
|----------|-----------------|--------------|  
|身份驗證託管標識 |根元素。|是|  
  
### <a name="attributes"></a>屬性  
  
|名稱|描述|必要|預設|  
|----------|-----------------|--------------|-------------|  
|resource|字串。 Azure 活動目錄中目標 Web API（安全資源）的應用 ID。|是|N/A|  
|輸出權杖變數名稱|字串。 將接收權杖值作為物件類型的`string`上下文變數的名稱。 |否|N/A|  
|ignore-error|布林值。 如果設置為`true`，則即使未獲取訪問權杖，策略管道仍將繼續執行。|否|false|  
  
### <a name="usage"></a>使用量  
 此原則可用於下列原則[區段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[範圍](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)。  
  
-   **原則區段︰** inbound  
  
-   **原則範圍：** 所有範圍

## <a name="next-steps"></a>後續步驟
如需使用原則的詳細資訊，請參閱︰

+ [API 管理中的原則](api-management-howto-policies.md)
+ [轉換 API](transform-api.md)
+ [原則參考文件](api-management-policy-reference.md)，取得原則陳述式及其設定的完整清單
+ [原則範例](policy-samples.md)
