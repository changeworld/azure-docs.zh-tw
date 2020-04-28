---
title: 在自訂原則中定義 Application Insights 技術設定檔
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自訂原則中定義 Application Insights 的技術設定檔。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f50373b0841b7626bc405f121015c15ae1587a97
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80108571"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自訂原則中定義 Application Insights 技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C （Azure AD B2C）支援使用提供給 Azure AD B2C 的檢測金鑰，將事件資料直接傳送至[Application Insights](../azure-monitor/app/app-insights-overview.md) 。  透過 Application Insights 的技術設定檔，您可以為使用者旅程取得詳細且自訂的事件記錄檔，以：

* 深入了解使用者行為。
* 在開發或實際執行時對您自己的原則進行疑難排解。
* 測量效能。
* 從 Application Insights 建立通知。


## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `Proprietary`。 **處理常式**屬性必須包含 Azure AD B2C 用於 Application Insights 之通訊協定處理常式元件的完整名稱：`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

下列範例顯示常見的 Application Insights 技術設定檔。 其他 Application Insights 技術設定檔包含 Azure-insights 常見的，以運用其設定。  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含要傳送至 Application Insights 的宣告清單。 您也可以將宣告的名稱對應至您想要在 Application Insights 中顯示的名稱。 下列範例顯示如何將遙測傳送至 Application Insights。 事件的屬性會透過語法`{property:NAME}`加入，其中 NAME 是要新增至事件的屬性。 DefaultValue 可以是靜態值，或由其中[一個支援的宣告解析程式](claim-resolver-overview.md)解析的值。

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

**InputClaimsTransformations**元素可能包含**InputClaimsTransformation**元素的集合，這些專案是用來修改輸入宣告或在傳送至 Application Insights 之前產生新的。

## <a name="persist-claims"></a>保存宣告

不會使用 PersistedClaims 元素。

## <a name="output-claims"></a>輸出宣告

不會使用 OutputClaims 和 OutputClaimsTransformations 元素。

## <a name="cryptographic-keys"></a>密碼編譯金鑰

不使用 CryptographicKeys 元素。


## <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| InstrumentationKey| 是 | Application Insights[檢測金鑰](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)，將用來記錄事件。 | 
| DeveloperMode| 否 | 布林值，指出是否已啟用開發人員模式。 可能的值`true` ： `false`或（預設）。 此中繼資料會控制如何緩衝處理事件。 在具有最少事件量的開發環境中，啟用開發人員模式會導致事件立即傳送到 Application Insights。|  
|DisableTelemetry |否 |布林值，指出是否應該啟用遙測。 可能的值`true` ： `false`或（預設）。| 


## <a name="next-steps"></a>後續步驟

- [建立 Application Insights 資源](../azure-monitor/app/create-new-resource.md)
- 瞭解如何[在 Azure Active Directory B2C 中使用 Application Insights 追蹤使用者行為](analytics-with-application-insights.md)
