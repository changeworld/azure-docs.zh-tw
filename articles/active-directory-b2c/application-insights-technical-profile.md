---
title: 在自訂原則中定義 Application Insights 技術設定檔
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自訂原則中定義 Application Insights 技術設定檔。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 77bb53e2605913fcee6999284acb04616efc53af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201407"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自訂原則中定義 Application Insights 技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 支援使用提供給 Application Insights 的檢測金鑰，將事件資料直接傳送至 [Azure AD B2C](../azure-monitor/app/app-insights-overview.md) 。  使用 Application Insights 技術設定檔，您可以取得使用者旅程的詳細和自訂事件記錄檔，以：

* 深入了解使用者行為。
* 在開發或實際執行時對您自己的原則進行疑難排解。
* 測量效能。
* 從 Application Insights 建立通知。


## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `Proprietary`。 **處理常式**屬性必須包含 Application Insights Azure AD B2C 所使用之通訊協定處理常式元件的完整名稱：`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

下列範例顯示 common Application Insights 技術設定檔。 其他 Application Insights 技術設定檔包含 AzureInsights-Common，以利用其設定。  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含要傳送至 Application Insights 的宣告清單。 您也可以將您的宣告名稱對應至您想要在 Application Insights 中顯示的名稱。 下列範例顯示如何將遙測傳送至 Application Insights。 事件的屬性是透過語法新增的 `{property:NAME}` ，其中 NAME 是要加入至事件的屬性。 DefaultValue 可以是靜態值或由其中一個支援的宣告 [解析](claim-resolver-overview.md)程式所解析的值。

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

**InputClaimsTransformations**元素可以包含**InputClaimsTransformation**專案的集合，這些元素可用來修改輸入宣告或產生新的宣告，然後再傳送至 Application Insights。

## <a name="persist-claims"></a>保存宣告

未使用 PersistedClaims 元素。

## <a name="output-claims"></a>輸出宣告

不會使用 OutputClaims 和 OutputClaimsTransformations 元素。

## <a name="cryptographic-keys"></a>密碼編譯金鑰

不使用 CryptographicKeys 元素。


## <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| InstrumentationKey| 是 | Application Insights [檢測金鑰](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)，將會用來記錄事件。 | 
| DeveloperMode| 否 | 指出是否已啟用開發人員模式的布林值。 可能的值： `true` 或 `false` (預設) 。 此中繼資料會控制如何緩衝處理事件。 在事件量極短的開發環境中，啟用開發人員模式會產生立即傳送給 Application Insights 的事件。|  
|DisableTelemetry |否 |指出是否應該啟用遙測的布林值。 可能的值： `true` 或 `false` (預設) 。| 


## <a name="next-steps"></a>後續步驟

- [建立 Application Insights 資源](../azure-monitor/app/create-new-resource.md)
- 瞭解如何 [使用 Application Insights 在 Azure Active Directory B2C 中追蹤使用者行為](analytics-with-application-insights.md)
