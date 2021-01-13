---
title: TrustFrameworkPolicy - Azure Active Directory B2C | Microsoft Docs
description: 指定 Azure Active Directory B2C 中自訂原則的 TrustFrameworkPolicy 元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a87b4c6b845006a9f9f3cf82815277c67c09bef0
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178835"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

自訂原則以一或多個 XML 格式的檔案表示，各檔案在階層鏈中彼此參考。 XML 元素會定義原則的元素，例如宣告結構描述、宣告轉換、內容定義、宣告提供者、技術設定檔、使用者旅程圖，和協調流程步驟。 每個原則檔定義在原則檔之 **TrustFrameworkPolicy** 元素的最上層。

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


**TrustFrameworkPolicy** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Yes | 用來執行此原則的結構描述版本。 值必須是 `0.3.0.0` |
| TenantObjectId | No | Azure Active Directory B2C (Azure AD B2C) 租使用者的唯一物件識別碼。 |
| TenantId | Yes | 此原則所屬之租用戶的唯一識別碼。 |
| PolicyId | Yes | 原則的唯一識別碼。 此識別碼必須加上前置詞 *B2C_1A_* |
| PublicPolicyUri | Yes | 原則的 URI，也就是租用戶識別碼和原則識別碼的組合。 |
| DeploymentMode | No | 可能的值： `Production` 、或 `Development` 。 `Production` 是預設值。 使用此屬性以偵錯您的原則。 如需詳細資訊，請參閱[收集記錄](troubleshoot-with-application-insights.md)。 |
| UserJourneyRecorderEndpoint | No | 用於記錄的端點。 如果屬性存在，此值必須設定為 `urn:journeyrecorder:applicationinsights` 。 如需詳細資訊，請參閱[收集記錄](troubleshoot-with-application-insights.md)。 |


下列範例顯示如何指定 **TrustFrameworkPolicy** 元素：

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

**TrustFrameworkPolicy** 元素包含下列元素：

| 元素 | 發生次數 | Description |
| ------- | ----------- | ----------- |
| BasePolicy| 0:1| 基底原則的識別碼。 |
| [BuildingBlocks](buildingblocks.md) | 0:1 | 原則的組建區塊。 |
| [ClaimsProviders](claimsproviders.md) | 0:1 | 宣告提供者的集合。 |
| [UserJourneys](userjourneys.md) | 0:1 | 使用者旅程的集合。 |
| [RelyingParty](relyingparty.md) | 0:1 | 信賴憑證者原則的定義。 |

若要從另一個原則繼承原則，必須在原則檔的 **TrustFrameworkPolicy** 元素下，宣告 **BasePolicy** 元素。 **BasePolicy** 元素是從中衍生此原則之基底原則的參考。

**BasePolicy** 元素包含下列元素：

| 元素 | 發生次數 | Description |
| ------- | ----------- | --------|
| TenantId | 1:1 | Azure AD B2C 租用戶的識別碼。 |
| PolicyId | 1:1 | 父代原則的識別碼。 |


下列範例顯示如何指定基底原則。 此 **B2C_1A_TrustFrameworkExtensions** 元則衍生自 **B2C_1A_TrustFrameworkBase** 原則。

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

