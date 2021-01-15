---
title: 按一下適用于 Application Insights JavaScript SDK 的 Analytics 自動收集外掛程式
description: 如何安裝和使用適用于 Application Insights JavaScript SDK 的 Click Analytics 自動集合外掛程式。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: lagayhar
ms.openlocfilehash: e69d5cc76f8f4b14ab87e13546c98859bb801418
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234753"
---
# <a name="click-analytics-auto-collection-plugin-for-application-insights-javascript-sdk"></a>按一下適用于 Application Insights JavaScript SDK 的 Analytics 自動收集外掛程式

按一下 [適用于 Application Insights JavaScript SDK 的分析自動集合外掛程式]，可讓您根據中繼標籤，自動追蹤網頁上的 click 事件 `data-*` 。 此外掛程式會使用 `data-*` 全域屬性來捕捉點擊事件並填入遙測資料。

## <a name="getting-started"></a>開始使用

使用者可以透過 npm 設定 Click Analytics 自動收集外掛程式。

### <a name="npm-setup"></a>npm 設定

安裝 npm 套件：

```bash
npm install --save @microsoft/applicationinsights-clickanalytics-js @microsoft/applicationinsights-web
```

```js

import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ClickAnalyticsPlugin } from '@microsoft/applicationinsights-clickanalytics-js';

const clickPluginInstance = new ClickAnalyticsPlugin();
// Click Analytics configuration
const clickPluginConfig = {
  autoCapture: true
};
// Application Insights Configuration
const configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: clickPluginConfig
  },
};

const appInsights = new ApplicationInsights({ config: configObj });
appInsights.loadAppInsights();
```

## <a name="how-to-effectively-use-the-plugin"></a>如何有效地使用外掛程式

1. 從 click 事件產生的遙測資料會儲存為 `customEvents` Azure 入口網站的 Application Insights 區段。
2. `name`CustomEvent 的會根據下列規則填入：
    1.  `id`中提供的 `data-*-id` 會用來做為 customEvent 名稱。 例如，如果按下的 HTML 元素有 "data-sample-id" = "button1" 屬性，則 "button1" 將會是 customEvent 名稱。
    2. 如果沒有這類屬性，而且 `useDefaultContentNameOrId` 設定中的設定為 `true` ，則會使用專案的 HTML 屬性 `id` 或元素的內容名稱做為 customEvent 名稱。
    3. 如果 `useDefaultContentNameOrId` 為 false，則 customEvent 名稱將會是 "not_specified"。

    > [!TIP]
    > 我們的建議是將設定 `useDefaultContentNameOrId` 為 true，以產生有意義的資料。  
3. `parentDataTag` 會執行兩個作業︰
    1. 如果有這個標記，外掛程式將會從已按下專案的 `data-*` 所有父 HTML 元素提取屬性和值。
    2. 為了提高效率，外掛程式會使用這個標記做為旗標，當遇到時，它將會阻止自己進一步處理 DOM (檔物件模型) 向上。
    
    > [!CAUTION]
    > 一旦 `parentDataTag` 使用之後，它會在整個應用程式中持續作用，而不只是您在中使用的 HTML 元素。
4. `customDataPrefix` 例如，使用者所提供的應該一律以開頭 `data-` `data-sample-` 。 在 HTML 中， `data-*` 全域屬性會形成一種稱為自訂資料屬性的屬性類別，可讓您以腳本在 HTML 與其 DOM 標記法之間交換專屬資訊。 舊版瀏覽器 (Internet Explorer，Safari) 會捨棄它不了解的屬性，除非開頭為 `data-` 。

    `*`在 `data-*` [XML 名稱的生產規則](https://www.w3.org/TR/REC-xml/#NT-Name)之後，可能會以任何名稱取代，但有下列限制：
    - 名稱不能以 "xml" 開頭，無論使用哪一種字母都是如此。
    - 名稱不能包含任何分號 (U + 003A) 。
    - 名稱不能包含大寫字元。

## <a name="configuration"></a>組態

| 名稱                  | 類型                               | 預設 | 描述                                                                                                                              |
| --------------------- | -----------------------------------| --------| ---------------------------------------------------------------------------------------------------------------------------------------- |
| autoCapture           | boolean                            | true    | 自動捕獲設定。                                                                                                         |
| 回撥              | [IValueCallback](#ivaluecallback)  | null    | 回呼設定。                                                                                                                 |
| pageTags              | 字串                             | null    | 頁面標記。                                                                                                                               |
| dataTags              | [ICustomDataTags](#icustomdatatags)| null    | 提供的自訂資料標記，可覆寫用來捕捉點擊資料的預設標記。                                                           |
| urlCollectHash        | boolean                            | false   | 可在 URL 的 "#" 字元之後記錄值。                                                                          |
| urlCollectQuery       | boolean                            | false   | 啟用記錄 URL 的查詢字串。                                                                                      |
| behaviorValidator     | 函式                           | null  | 要用於值驗證的回呼函數 `data-*-bhvr` 。 如需詳細資訊，請參閱 [behaviorValidator 一節](#behaviorvalidator)。|
| defaultRightClickBhvr | 字串 (或) 數位                 | ''      | 當滑鼠右鍵按下事件發生時的預設行為值。 如果專案具有屬性，則會覆寫此值 `data-*-bhvr` 。 |
| dropInvalidEvents     | boolean                            | false   | 用來卸載沒有有用的 click 資料之事件的旗標。                                                                                   |

### <a name="ivaluecallback"></a>IValueCallback

| 名稱               | 類型     | 預設 | 描述                                                                             |
| ------------------ | -------- | ------- | --------------------------------------------------------------------------------------- |
| pageName           | 函式 | null    | 覆寫預設 pageName 捕捉行為的函式。                           |
| pageActionPageTags | 函式 | null    | 回呼函式，用來增加在 pageAction 事件期間所收集的預設 pageTags。  |
| contentName        | 函式 | null    | 用來填入自訂 contentName 的回呼函數。                                 |

### <a name="icustomdatatags"></a>ICustomDataTags

| 名稱                      | 類型    | 預設   | 描述                                                                                       |
|---------------------------|---------|-----------|---------------------------------------------------------------------------------------------------|
| useDefaultContentNameOrId | boolean | false     | 當特定元素未標記為預設 customDataPrefix，或使用者未提供 customDataPrefix 時，此旗標會用來收集 contentName 的標準 HTML 屬性。 |
| customDataPrefix          | 字串  | `data-`   | 自動捕捉內容名稱，以及以提供的前置詞標記的元素值。       |
| aiBlobAttributeTag        | 字串  | `ai-blob` | 外掛程式支援 JSON blob 內容元資料標記，而不是個別的 `data-*` 屬性。 |
| metaDataPrefix            | 字串  | null      | 自動捕獲 HTML 標頭的中繼元素名稱和具有所提供前置詞的內容。 |
| captureAllMetaDataContent | 字串  | null      | 自動捕捉所有 HTML 標頭的中繼元素名稱和內容。 預設值為 false。 若已啟用，將會覆寫提供的 metaDataPrefix。 |
| parentDataTag             | 字串  | null      | 當遇到此標記時，會停止遍歷 DOM 來捕捉專案的內容名稱和值。|
| dntDataTag                | 字串  | `ai-dnt`  | 使用這個屬性的 HTML 元素將會被外掛程式忽略，以供用來捕捉遙測資料。|

### <a name="behaviorvalidator"></a>behaviorValidator

當您想要確保資料一致性時，您可以使用 behaviorValidator 函式，但會自動檢查程式碼中標記的行為是否符合企業內已知和接受之分類法的預先定義清單。 大部分的 Azure 監視器客戶都不需要或預期會使用這項功能，但可用於 advanced 案例。 有三個不同的 behaviorValidator 回呼函式會公開為此擴充功能的一部分。 但是，如果公開的函式無法解決您的需求，則使用者可以使用自己的回呼函式。 其目的是要攜帶您自己的行為資料結構，此外掛程式會在從資料標記中解壓縮行為時使用這個驗證程式函式。

| Name                   | 描述                                                                        |
| ---------------------- | -----------------------------------------------------------------------------------|
| BehaviorValueValidator | 如果您的行為資料結構是字串陣列，請使用此回呼函數。|
| BehaviorMapValidator   | 如果您的行為資料結構是字典，請使用此回呼函數。       |
| BehaviorEnumValidator  | 如果您的行為資料結構是列舉，請使用此回呼函數。            |

#### <a name="sample-usage-with-behaviorvalidator"></a>使用 behaviorValidator 的範例用法

```js
var clickPlugin = Microsoft.ApplicationInsights.ClickAnalyticsPlugin;
var clickPluginInstance = new clickPlugin();

// Behavior enum values
var behaviorMap = {
  UNDEFINED: 0, // default, Undefined

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Page Experience [1-19]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  NAVIGATIONBACK: 1, // Advancing to the previous index position within a webpage
  NAVIGATION: 2, // Advancing to a specific index position within a webpage
  NAVIGATIONFORWARD: 3, // Advancing to the next index position within a webpage
  APPLY: 4, // Applying filter(s) or making selections
  REMOVE: 5, // Applying filter(s) or removing selections
  SORT: 6, // Sorting content
  EXPAND: 7, // Expanding content or content container
  REDUCE: 8, // Sorting content
  CONTEXTMENU: 9, // Context Menu
  TAB: 10, // Tab control
  COPY: 11, // Copy the contents of a page
  EXPERIMENTATION: 12, // Used to identify a third party experimentation event
  PRINT: 13, // User printed page
  SHOW: 14, //  Displaying an overlay
  HIDE: 15, //  Hiding an overlay
  MAXIMIZE: 16, //  Maximizing an overlay
  MINIMIZE: 17, // Minimizing an overlay
  BACKBUTTON: 18, //  Clicking the back button

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Scenario Process [20-39]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  STARTPROCESS: 20, // Initiate a web process unique to adopter
  PROCESSCHECKPOINT: 21, // Represents a checkpoint in a web process unique to adopter
  COMPLETEPROCESS: 22, // Page Actions that complete a web process unique to adopter
  SCENARIOCANCEL: 23, // Actions resulting from cancelling a process/scenario

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Download [40-59]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  DOWNLOADCOMMIT: 40, // Initiating an unmeasurable off-network download
  DOWNLOAD: 41, // Initiating a download

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Search [60-79]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SEARCHAUTOCOMPLETE: 60, // Auto-completing a search query during user input
  SEARCH: 61, // Submitting a search query
  SEARCHINITIATE: 62, // Initiating a search query
  TEXTBOXINPUT: 63, // Typing or entering text in the text box

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Commerce [80-99]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIEWCART: 82, // Viewing the cart
  ADDWISHLIST: 83, // Adding a physical or digital good or services to a wishlist
  FINDSTORE: 84, // Finding a physical store
  CHECKOUT: 85, // Before you fill in credit card info
  REMOVEFROMCART: 86, // Remove an item from the cart
  PURCHASECOMPLETE: 87, // Used to track the pageView event that happens when the CongratsPage or Thank You page loads after a successful purchase
  VIEWCHECKOUTPAGE: 88, // View the checkout page
  VIEWCARTPAGE: 89, // View the cart page
  VIEWPDP: 90, // View a PDP
  UPDATEITEMQUANTITY: 91, // Update an item's quantity
  INTENTTOBUY: 92, // User has the intent to buy an item
  PUSHTOINSTALL: 93, // User has selected the push to install option

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Authentication [100-119]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNIN: 100, // User sign-in
  SIGNOUT: 101, // User sign-out

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Social [120-139]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SOCIALSHARE: 120, // "Sharing" content for a specific social channel
  SOCIALLIKE: 121, // "Liking" content for a specific social channel
  SOCIALREPLY: 122, // "Replying" content for a specific social channel
  CALL: 123, // Click on a "call" link
  EMAIL: 124, // Click on an "email" link
  COMMUNITY: 125, // Click on a "community" link

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Feedback [140-159]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VOTE: 140, // Rating content or voting for content
  SURVEYCHECKPOINT: 145, // reaching the survey page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Registration, Contact [160-179]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  REGISTRATIONINITIATE: 161, // Initiating a registration process
  REGISTRATIONCOMPLETE: 162, // Completing a registration process
  CANCELSUBSCRIPTION: 163, // Canceling a subscription
  RENEWSUBSCRIPTION: 164, // Renewing a subscription
  CHANGESUBSCRIPTION: 165, // Changing a subscription
  REGISTRATIONCHECKPOINT: 166, // Reaching the registration page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Chat [180-199]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  CHATINITIATE: 180, // Initiating a chat experience
  CHATEND: 181, // Ending a chat experience

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Trial [200-209]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  TRIALSIGNUP: 200, // Signing-up for a trial
  TRIALINITIATE: 201, // Initiating a trial

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Signup [210-219]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNUP: 210, // Signing-up for a notification or service
  FREESIGNUP: 211, // Signing-up for a free service

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Referals [220-229]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  PARTNERREFERRAL: 220, // Navigating to a partner's web property

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Intents [230-239]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  LEARNLOWFUNNEL: 230, // Engaging in learning behavior on a commerce page (ex. "Learn more click")
  LEARNHIGHFUNNEL: 231, // Engaging in learning behavior on a non-commerce page (ex. "Learn more click")
  SHOPPINGINTENT: 232, // Shopping behavior prior to landing on a commerce page

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Video [240-259]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIDEOSTART: 240, // Initiating a video
  VIDEOPAUSE: 241, // Pausing a video
  VIDEOCONTINUE: 242, // Pausing or resuming a video.
  VIDEOCHECKPOINT: 243, // Capturing predetermined video percentage complete.
  VIDEOJUMP: 244, // Jumping to a new video location.
  VIDEOCOMPLETE: 245, // Completing a video (or % proxy)
  VIDEOBUFFERING: 246, // Capturing a video buffer event
  VIDEOERROR: 247, // Capturing a video error
  VIDEOMUTE: 248, // Muting a video
  VIDEOUNMUTE: 249, // Unmuting a video
  VIDEOFULLSCREEN: 250, // Making a video full screen
  VIDEOUNFULLSCREEN: 251, // Making a video return from full screen to original size
  VIDEOREPLAY: 252, // Making a video replay
  VIDEOPLAYERLOAD: 253, // Loading the video player
  VIDEOPLAYERCLICK: 254, //  Click on a button within the interactive player
  VIDEOVOLUMECONTROL: 255, //  Click on video volume control
  VIDEOAUDIOTRACKCONTROL: 256, // Click on audio control within a video
  VIDEOCLOSEDCAPTIONCONTROL: 257, //  Click on the closed caption control
  VIDEOCLOSEDCAPTIONSTYLE: 258, //  Click to change closed caption style
  VIDEORESOLUTIONCONTROL: 259, //  Click to change resolution

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  //    Advertisement Engagement [280-299]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  ADBUFFERING: 283, // Ad is buffering
  ADERROR: 284, // Ad error
  ADSTART: 285, // Ad start
  ADCOMPLETE: 286, // Ad complete
  ADSKIP: 287, // Ad skipped
  ADTIMEOUT: 288, // Ad timed-out
  OTHER: 300 // Other
};

// Application Insights Configuration
var configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: {
      behaviorValidator: Microsoft.ApplicationInsights.BehaviorMapValidator(behaviorMap),
      defaultRightClickBhvr: 9
    },
  },
};
var appInsights = new Microsoft.ApplicationInsights.ApplicationInsights({
  config: configObj
});
appInsights.loadAppInsights();
```

## <a name="sample-app"></a>範例應用程式

[已啟用 Click Analytics 自動收集外掛程式的簡單 web 應用程式](https://go.microsoft.com/fwlink/?linkid=2152871)。

## <a name="next-steps"></a>後續步驟

- 使用 [使用量體驗中的事件分析](usage-segmentation.md) ，依可用的維度來分析點擊次數和配量。
- 在 [Log Analytics](../log-query/log-analytics-tutorial.md#write-a-query)的 CustomEvents 資料表中，于 [內容] 欄位底下的 [customDimensions 屬性] 中尋找 [按一下資料]
- 建立活頁 [簿](../platform/workbooks-overview.md) 以建立點擊資料的自訂視覺效果。
