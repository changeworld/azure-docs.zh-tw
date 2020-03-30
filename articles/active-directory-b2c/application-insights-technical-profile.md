---
title: 在自訂策略中定義應用程式見解技術設定檔
titleSuffix: Azure AD B2C
description: 在 Azure 活動目錄 B2C 中的自訂策略中定義應用程式見解技術設定檔。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80108571"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自訂策略中定義應用程式見解技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure 活動目錄 B2C（Azure AD B2C）支援使用提供給 Azure AD B2C 的檢測金鑰將事件資料直接發送到[應用程式見解](../azure-monitor/app/app-insights-overview.md)。  借助應用見解技術設定檔，您可以獲取使用者旅程的詳細和自訂事件日誌，以便：

* 深入了解使用者行為。
* 在開發或實際執行時對您自己的原則進行疑難排解。
* 測量效能。
* 從 Application Insights 建立通知。


## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `Proprietary`。 **處理常式**屬性必須包含 Azure AD B2C 用於應用程式見解的協定處理常式程式集的完全限定名稱：`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

下面的示例顯示了常見的應用程式見解技術設定檔。 其他應用程式見解技術設定檔包括 Azure Insights 通用以利用其配置。  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含要發送到應用程式見解的聲明清單。 您還可以將聲明的名稱映射到您希望顯示在應用程式見解中的名稱。 下面的示例演示如何向應用程式見解發送遙測資料。 事件的屬性通過語法`{property:NAME}`添加，其中 NAME 是添加到事件的屬性。 預設值可以是靜態值，也可以是由受支援[的聲明解析器](claim-resolver-overview.md)之一解析的值。

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

**InputClaims轉換**元素可能包含**一個輸入聲明轉換**元素的集合，這些元素用於修改輸入聲明或在發送到應用程式見解之前生成新聲明。

## <a name="persist-claims"></a>保存宣告

不使用持久聲明元素。

## <a name="output-claims"></a>輸出宣告

不使用"輸出聲明"和"輸出聲明轉換"元素。

## <a name="cryptographic-keys"></a>密碼編譯金鑰

不使用 CryptographicKeys 元素。


## <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| InstrumentationKey| 是 | 應用程式見解[檢測金鑰](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)，用於記錄事件。 | 
| DeveloperMode| 否 | 指示是否已啟用開發人員模式的布林。 可能的值：`true`或`false`（預設值）。 此中繼資料控制事件如何緩衝。 在事件卷最少的開發環境中，啟用開發人員模式會導致事件立即發送到應用程式見解。|  
|禁用遙測 |否 |指示是否應啟用遙測的布林。 可能的值：`true`或`false`（預設值）。| 


## <a name="next-steps"></a>後續步驟

- [創建應用程式見解資源](../azure-monitor/app/create-new-resource.md)
- 瞭解如何[使用應用程式見解跟蹤 Azure 活動目錄 B2C 中的使用者行為](analytics-with-application-insights.md)
