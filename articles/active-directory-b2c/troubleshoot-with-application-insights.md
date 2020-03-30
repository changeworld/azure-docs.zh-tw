---
title: 使用應用程式見解解決自訂策略
titleSuffix: Azure AD B2C
description: 如何設置應用程式見解以跟蹤自訂策略的執行。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403dbe6106cb7a1d277ba672112d2bc45dbc2987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186262"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>使用應用程式見解收集 Azure 活動目錄 B2C 日誌

本文提供了從活動目錄 B2C（Azure AD B2C） 收集日誌的步驟，以便您可以診斷自訂策略的問題。 Application Insights 提供方法來診斷例外狀況和將應用程式效能問題視覺化。 Azure AD B2C 包含用於將資料發送到應用程式見解的功能。

此處描述的詳細活動日誌**應僅在**自訂策略的開發期間啟用。

> [!WARNING]
> 請勿在生產中啟用開發模式。 日誌收集發送到標識提供程式並從標識提供程式發送的所有聲明。 作為開發人員，您負責在應用程式見解日誌中收集的任何個人資料。 僅當策略置於**開發人員模式**中時，才會收集這些詳細的日誌。

## <a name="set-up-application-insights"></a>設置應用程式見解

如果還沒有，請在訂閱中創建應用程式見解實例。

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 選擇頂部功能表中的**目錄 + 訂閱**篩選器，然後選擇包含 Azure 訂閱的目錄（而不是 Azure AD B2C 目錄）。
1. 選擇 **"在**左側導航功能表中創建資源"。
1. 搜索並選擇 **"應用程式見解**"，然後選擇 **"創建**"。
1. 完成表單，選擇 **"審閱 + 創建**"，然後選擇 **"創建**"。
1. 部署完成後，選擇 **"轉到資源**"。
1. 在 **"在**應用程式見解"功能表中配置，選擇 **"屬性**"。
1. 記錄用於後續步驟的 **"儀器"金鑰**。

## <a name="configure-the-custom-policy"></a>配置自訂策略

1. 打開依賴方 （RP） 檔，例如 *"登錄或Signin.xml*"。
1. 將下列屬性新增至 `<TrustFrameworkPolicy>` 元素：

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. 如果不存在，請向`<UserJourneyBehaviors>``<RelyingParty>`節點添加子節點。 它必須立即位於 之後`<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`。
1. 新增下列節點作為 `<UserJourneyBehaviors>` 元素的子節點。 請確保`{Your Application Insights Key}`替換為前面記錄的應用程式見解**檢測金鑰**。

    ```XML
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"`告訴應用程式見解通過處理管道加快遙測速度。 有利於開發，但受量限制。
    * `ClientEnabled="true"`發送用於跟蹤網頁檢視和用戶端錯誤的應用程式洞察用戶端腳本。 您可以在"應用程式見解"門戶中的**瀏覽器計時**表中查看這些內容。 通過設置`ClientEnabled= "true"`，您將應用程式見解添加到頁面腳本中，並獲取頁面載入和 AJAX 調用的計時、計數、瀏覽器異常和 AJAX 失敗的詳細資訊以及使用者和會話計數。 此欄位是**可選的**，預設情況下設置為`false`。
    * `ServerEnabled="true"` 會將現有的 UserJourneyRecorder JSON 當作自訂事件傳送至 Application Insights。

    例如：

    ```XML
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. 上傳原則。

## <a name="see-the-logs-in-application-insights"></a>查看 Application Insights 中的記錄

在應用程式見解中看到新日誌之前，有一個短暫的延遲（通常不到五分鐘）。

1. 開啟您在 [Azure 入口網站](https://portal.azure.com)中建立的 Application Insights 資源。
1. 在 **"概述"** 功能表中，選擇 **"分析**"。
1. 在 Application Insights 入口網站中開啟新的索引標籤。

下面是可用於查看日誌的查詢清單：

| 查詢 | 描述 |
|---------------------|--------------------|
`traces` | 查看 Azure AD B2C 產生的所有記錄 |
`traces | where timestamp > ago(1d)` | 查看 Azure AD B2C 在最後一天產生的所有記錄

此項目可能很長。 請將它匯出至 CSV 以仔細查看。

有關查詢的詳細資訊，請參閱 Azure[監視器 中的日誌查詢概述](../azure-monitor/log-query/log-query-overview.md)。

## <a name="next-steps"></a>後續步驟

該社群已開發了使用者旅程圖檢視器，可協助身分識別開發人員。 它會讀取您的 Application Insights 執行個體，並提供結構良好的使用者旅程圖事件檢視。 請取得原始碼，並將它部署在您自己的解決方案中。

使用者旅程播放機不受 Microsoft 的支援，並且嚴格按現有身份提供。

您可以在 GitHub 上查找從應用程式見解讀取事件的檢視器版本，如下所示：

[Azure-採樣/主動目錄-b2c 高級策略](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
