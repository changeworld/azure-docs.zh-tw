---
title: 沈浸式閱讀程式 SDK 參考
titleSuffix: Azure Cognitive Services
description: 沈浸式閱讀程式 SDK 包含 JavaScript 程式庫，可讓您將沈浸式閱讀程式整合到您的應用程式中。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: dbd5724797fdaf44d147d2f29362b1e5092728dd
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761544"
---
# <a name="immersive-reader-javascript-sdk-reference-v11"></a>沈浸式閱讀程式 (v1.1) 的 JavaScript SDK 參考

沈浸式閱讀程式 SDK 包含 JavaScript 程式庫，可讓您將沈浸式閱讀程式整合到您的應用程式中。

## <a name="functions"></a>函式

SDK 會公開函式：

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

<br>

## <a name="launchasync"></a>launchAsync

`iframe`在您的 web 應用程式中啟動沈浸式閱讀程式。 請注意，您的內容大小上限為 50 MB。

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

#### <a name="launchasync-parameters"></a>launchAsync 參數

| 名稱 | 類型 | 描述 |
| ---- | ---- |------------ |
| `token` | 字串 | Azure AD 的驗證 token。 如需詳細資訊，請參閱 [如何建立沈浸式閱讀程式資源](./how-to-create-immersive-reader.md) 。 |
| `subdomain` | 字串 | 您在 Azure 中沈浸式閱讀程式資源的自訂子域。 如需詳細資訊，請參閱 [如何建立沈浸式閱讀程式資源](./how-to-create-immersive-reader.md) 。 |
| `content` | [內容](#content) | 物件，包含要在沈浸式閱讀程式中顯示的內容。 |
| `options` | [選項](#options) | 設定沈浸式閱讀程式特定行為的選項。 選擇性。 |

#### <a name="returns"></a>傳回

傳回 `Promise<LaunchResponse>` ，它會在載入沈浸式閱讀程式時解析。 會 `Promise` 解析為 [`LaunchResponse`](#launchresponse) 物件。

#### <a name="exceptions"></a>例外狀況

`Promise`如果沈浸式閱讀程式載入失敗，將會以物件拒絕傳回的 [`Error`](#error) 。 如需詳細資訊，請參閱 [錯誤碼](#error-codes)。

<br>

## <a name="close"></a>關閉

關閉沈浸式閱讀程式。

此函式的範例使用案例是，如果 [選項] 中的設定會隱藏 [結束] 按鈕，則為 ```hideExitButton: true``` 。 [options](#options) 然後，不同的按鈕 (例如，行動標題的向後箭號) 可以在按下時呼叫此函式 ```close``` 。

```typescript
close(): void;
```

<br>

## <a name="immersive-reader-launch-button"></a>沈浸式閱讀程式啟動按鈕

SDK 提供啟動沈浸式閱讀程式之按鈕的預設樣式。 您 `immersive-reader-button` 可以使用類別屬性來啟用這個樣式。 如需詳細資訊，請參閱 [如何自訂沈浸式閱讀程式按鈕](./how-to-customize-launch-button.md) 。

```html
<div class='immersive-reader-button'></div>
```

#### <a name="optional-attributes"></a>選擇性屬性

使用下列屬性來設定按鈕的外觀和風格。

| 屬性 | 描述 |
| --------- | ----------- |
| `data-button-style` | 設定按鈕的樣式。 可以是 `icon`、`text` 或 `iconAndText`。 預設值為 `icon`。 |
| `data-locale` | 設定地區設定。 例如，`en-US` 或 `fr-FR`。 預設為英文 `en` 。 |
| `data-icon-px-size` | 設定圖示的大小（以圖元為單位）。 預設值為20px。 |

<br>

## <a name="renderbuttons"></a>renderButtons

```renderButtons```如果您使用[如何自訂沈浸式閱讀程式按鈕](./how-to-customize-launch-button.md)指引，則不需要函式。

此函式會將檔的沈浸式閱讀程式按鈕元素樣式並加以更新。 如果 ```options.elements``` 提供，則按鈕會在中提供的每個專案內轉譯 ```options.elements``` 。 當您在 ```options.elements``` 檔中有多個要啟動沈浸式閱讀程式的區段，而且想要以相同的樣式來呈現多個按鈕，或想要以簡單且一致的設計模式轉譯按鈕時，使用參數會很有用。 若要搭配使用此函數與 [renderButtons options](#renderbuttons-options) 參數，請 ```ImmersiveReader.renderButtons(options: RenderButtonsOptions);``` 在頁面載入時呼叫，如下列程式碼片段所示。 否則，會在檔的元素內轉譯按鈕， ```immersive-reader-button``` 如 [如何自訂沈浸式閱讀程式按鈕](./how-to-customize-launch-button.md) 所示。

```typescript
// This snippet assumes there are two empty div elements in
// the page HTML, button1 and button2.
const btn1: HTMLDivElement = document.getElementById('button1');
const btn2: HTMLDivElement = document.getElementById('button2');
const btns: HTMLDivElement[] = [btn1, btn2];
ImmersiveReader.renderButtons({elements: btns});
```

如需更多呈現選項，請參閱上述 [選擇性屬性](#optional-attributes) 。 若要使用這些選項，請 ```HTMLDivElement``` 在網頁 HTML 中的每個選項加入任何選項屬性。

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

#### <a name="renderbuttons-parameters"></a>renderButtons 參數

| 名稱 | 類型 | 描述 |
| ---- | ---- |------------ |
| `options` | [renderButtons 選項](#renderbuttons-options) | 設定 renderButtons 函式之特定行為的選項。 選擇性。 |

### <a name="renderbuttons-options"></a>renderButtons 選項

用於呈現沈浸式閱讀程式按鈕的選項。

```typescript
{
    elements: HTMLDivElement[];
}
```

#### <a name="renderbuttons-options-parameters"></a>renderButtons 選項參數

| 設定 | 類型 | 描述 |
| ------- | ---- | ----------- |
| 元素 | HTMLDivElement [] | 要在其中呈現沈浸式閱讀程式按鈕的元素。 |

##### `elements`
```Parameters
Type: HTMLDivElement[]
Required: false
```

<br>

## <a name="launchresponse"></a>LaunchResponse

包含呼叫的回應 `ImmersiveReader.launchAsync` 。 請注意， `iframe` 包含沈浸式閱讀程式的參考可透過存取 `container.firstChild` 。

```typescript
{
    container: HTMLDivElement;
    sessionId: string;
}
```

#### <a name="launchresponse-parameters"></a>LaunchResponse 參數

| 設定 | 類型 | 說明 |
| ------- | ---- | ----------- |
| 容器 | HTMLDivElement | 包含沈浸式閱讀程式 iframe 的 HTML 元素。 |
| sessionID | 字串 | 此會話的全域唯一識別碼，用於進行偵錯工具。 |
 
## <a name="error"></a>錯誤

包含錯誤的相關資訊。

```typescript
{
    code: string;
    message: string;
}
```

#### <a name="error-parameters"></a>錯誤參數

| 設定 | 類型 | 說明 |
| ------- | ---- | ----------- |
| code | 字串 | 一組錯誤碼的其中一個。 請參閱[錯誤碼](#error-codes) \(英文\)。 |
| 訊息 | 字串 | 錯誤的人們可讀取標記法。 |

#### <a name="error-codes"></a>錯誤碼

| 程式碼 | 描述 |
| ---- | ----------- |
| BadArgument | 提供的引數無效，請參閱 `message` [錯誤](#error)的參數。 |
| 逾時 | 沈浸式閱讀程式在指定的超時時間內無法載入。 |
| TokenExpired | 提供的權杖已過期。 |
| 調整執行速度 | 已超過呼叫頻率限制。 |

<br>

## <a name="types"></a>類型

### <a name="content"></a>內容

包含要在沈浸式閱讀程式中顯示的內容。

```typescript
{
    title?: string;
    chunks: Chunk[];
}
```

#### <a name="content-parameters"></a>內容參數

| 名稱 | 類型 | 描述 |
| ---- | ---- |------------ |
| title | String | 顯示在沈浸式閱讀程式頂端的標題文字 (選擇性)  |
| 區塊 | [區塊 []](#chunk) | 區塊陣列 |

##### `title`
```Parameters
Type: String
Required: false
Default value: "Immersive Reader" 
```

##### `chunks`
```Parameters
Type: Chunk[]
Required: true
Default value: null 
```

<br>

### <a name="chunk"></a>區塊

單一資料區塊，將會傳遞至沈浸式閱讀程式的內容。

```typescript
{
    content: string;
    lang?: string;
    mimeType?: string;
}
```

#### <a name="chunk-parameters"></a>區塊參數

| 名稱 | 類型 | 描述 |
| ---- | ---- |------------ |
| 內容 | 字串 | 字串，其中包含傳送至沈浸式閱讀程式的內容。 |
| lang | 字串 | 文字的語言，其值為 IETF BCP 47 語言標記格式，例如 en、es。 如果未指定，則會自動偵測語言。 請參閱[支援的語言](#supported-languages)。 |
| mimeType | 字串 | 支援純文字、MathML、HTML & Microsoft Word .DOCX 格式。 如需詳細資訊，請參閱 [支援的 MIME 類型](#supported-mime-types) 。 |

##### `content`
```Parameters
Type: String
Required: true
Default value: null 
```

##### `lang`
```Parameters
Type: String
Required: false
Default value: Automatically detected 
```

##### `mimeType`
```Parameters
Type: String
Required: false
Default value: "text/plain"
```

#### <a name="supported-mime-types"></a>支援的 MIME 類型

| MIME 類型 | 描述 |
| --------- | ----------- |
| text/plain | 純文字。 |
| text/html | HTML 內容。 [深入了解](#html-support)|
| application/mathml + xml | 數學標記語言 (MathML) 。 [深入了解](./how-to/display-math.md)。
| 應用程式/vnd.openxmlformats-officedocument.wordprocessingml.doc>ument | Microsoft Word .docx 格式檔。


<br>

## <a name="options"></a>選項。

包含屬性，這些屬性會設定沈浸式閱讀程式的特定行為。

```typescript
{
    uiLang?: string;
    timeout?: number;
    uiZIndex?: number;
    useWebview?: boolean;
    onExit?: () => any;
    allowFullscreen?: boolean;
    hideExitButton?: boolean;
    cookiePolicy?: CookiePolicy;
    disableFirstRun?: boolean;
    readAloudOptions?: ReadAloudOptions;
    translationOptions?: TranslationOptions;
    displayOptions?: DisplayOptions;
    preferences?: string;
    onPreferencesChanged?: (value: string) => any;
    customDomain?: string;
}
```

#### <a name="options-parameters"></a>Options 參數

| 名稱 | 類型 | 描述 |
| ---- | ---- |------------ |
| uiLang | 字串 | UI 的語言，其值為 IETF BCP 47 語言標記格式，例如 en、es。 如果未指定，則預設為瀏覽器語言。 |
| timeout | 數字 | LaunchAsync 的持續時間 (以毫秒為單位) 在[launchAsync](#launchasync)失敗前發生逾時錯誤 (預設值為15000毫秒) 。 此超時只適用于初始啟動的 [讀取器] 頁面，其中會在 [讀取器] 頁面開啟時觀察到成功，並開始微調。 應該不需要調整時間。 |
| uiZIndex | 數字 | 將建立 (預設值為 1000) 的 iframe 的 Z-索引。 |
| useWebview | 布林值| 使用 web 程式標記而非 iframe，以與 Chrome 應用程式相容 (預設為 false) 。 |
| onExit | 函式 | 當沈浸式閱讀程式結束時執行。 |
| allowFullscreen | 布林值 | 切換全螢幕 (預設值的功能是) 。 |
| hideExitButton | 布林值 | 是否要隱藏沈浸式閱讀程式的離開按鈕箭號 (預設為 false) 。 如果有提供替代機制來結束沈浸式閱讀程式 (例如移動工具列的向後箭號) ，則應該只有 true。 |
| cookiePolicy | [CookiePolicy](#cookiepolicy-options) | 沈浸式閱讀程式的 cookie 使用量設定 (預設值為 *CookiePolicy。停* 用) 。 主機應用程式會負責根據歐盟 Cookie 合規性原則取得任何必要的使用者同意。 請參閱 [Cookie 原則選項](#cookiepolicy-options)。 |
| disableFirstRun | 布林值 | 停用第一次執行體驗。 |
| readAloudOptions | [ReadAloudOptions](#readaloudoptions) | 設定大聲讀出的選項。 |
| translationOptions | [TranslationOptions](#translationoptions) | 設定翻譯的選項。 |
| >displayoptions | [>displayoptions](#displayoptions) | 設定文字大小、字型等的選項。 |
| 偏好 | 字串 | 從 onPreferencesChanged 傳回的字串，代表使用者在沈浸式閱讀程式中的喜好設定。 如需詳細資訊，請參閱 [設定參數](#settings-parameters) 和 [如何儲存使用者喜好](./how-to-store-user-preferences.md) 設定。 |
| onPreferencesChanged | 函式 | 當使用者的喜好設定變更時執行。 如需詳細資訊，請參閱 [如何儲存使用者喜好](./how-to-store-user-preferences.md) 設定。 |
| customDomain | 字串 | 保留供內部使用。 主控沈浸式閱讀程式 webapp 的自訂網域 (預設為 null) 。 |

##### `uiLang`
```Parameters
Type: String
Required: false
Default value: User's browser language 
```

##### `timeout`
```Parameters
Type: Number
Required: false
Default value: 15000
```

##### `uiZIndex`
```Parameters
Type: Number
Required: false
Default value: 1000
```

##### `onExit`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `preferences`

> [!CAUTION]
> **重要** 請勿嘗試以程式設計方式變更在 `-preferences` 沈浸式閱讀程式應用程式中傳送的字串值，因為這可能會導致非預期的行為，而導致客戶的使用者體驗變差。 主機應用程式絕對不應指派自訂值給或操作 `-preferences` 字串。 使用 `-preferences` string 選項時，只使用回呼選項所傳回的確切值 `-onPreferencesChanged` 。

```Parameters
Type: String
Required: false
Default value: null
```

##### `onPreferencesChanged`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `customDomain`
```Parameters
Type: String
Required: false
Default value: null
```

<br>

## <a name="readaloudoptions"></a>ReadAloudOptions

```typescript
type ReadAloudOptions = {
    voice?: string;
    speed?: number;
    autoplay?: boolean;
};
```

#### <a name="readaloudoptions-parameters"></a>ReadAloudOptions 參數

| 名稱 | 類型 | 描述 |
| ---- | ---- |------------ |
| voice | 字串 | 語音，也就是「女性」或「男性」。 請注意，並非所有語言都支援這兩種性別。 |
| 速度 | 數字 | 播放速度必須介於0.5 到2.5 （含）之間。 |
| 播放 | 布林值 | 當沈浸式閱讀程式載入時，會自動開始大聲朗讀。 |

##### `voice`
```Parameters
Type: String
Required: false
Default value: "Female" or "Male" (determined by language) 
Values available: "Female", "Male"
```

##### `speed`
```Parameters
Type: Number
Required: false
Default value: 1
Values available: 0.5, 0.75, 1, 1.25, 1.5, 1.75, 2, 2.25, 2.5
```

> [!NOTE]
> 由於瀏覽器的限制，Safari 不支援自動播放。

<br>

## <a name="translationoptions"></a>TranslationOptions

```typescript
type TranslationOptions = {
    language: string;
    autoEnableDocumentTranslation?: boolean;
    autoEnableWordTranslation?: boolean;
};
```

#### <a name="translationoptions-parameters"></a>TranslationOptions 參數

| 名稱 | 類型 | 描述 |
| ---- | ---- |------------ |
| 語言 | 字串 | 設定翻譯語言，此值為 IETF BCP 47 語言標記格式，例如 fr-fr、es-MX、zh-Hans-CN。 自動啟用 word 或檔轉譯所需。 |
| autoEnableDocumentTranslation | 布林值 | 自動轉譯整份檔。 |
| autoEnableWordTranslation | 布林值 | 自動啟用文字轉譯。 |

##### `language`
```Parameters
Type: String
Required: true
Default value: null 
Values available: See the Supported Languages section
```

<br>

## <a name="displayoptions"></a>>displayoptions

```typescript
type DisplayOptions = {
    textSize?: number;
    increaseSpacing?: boolean;
    fontFamily?: string;
};
```

#### <a name="displayoptions-parameters"></a>>displayoptions 參數

| 名稱 | 類型 | 描述 |
| ---- | ---- |------------ |
| textSize | 數字 | 設定選擇的文字大小。 |
| increaseSpacing | 布林值 | 設定是否開啟或關閉文字間距。 |
| fontFamily | 字串 | 設定所選字型 ( "Calibri"、"ComicSans" 或 "Sitka" ) 。 |

##### `textSize`
```Parameters
Type: Number
Required: false
Default value: 20, 36 or 42 (Determined by screen size)
Values available: 14, 20, 28, 36, 42, 48, 56, 64, 72, 84, 96
```

##### `fontFamily`
```Parameters
Type: String
Required: false
Default value: "Calibri"
Values available: "Calibri", "Sitka", "ComicSans"
```

<br>

## <a name="cookiepolicy-options"></a>CookiePolicy 選項

```typescript
enum CookiePolicy { Disable, Enable }
```

**下列設定**僅供參考之用。 沈浸式閱讀程式會將其設定或使用者喜好設定儲存在 cookie 中。 此 *cookiePolicy* 選項預設會 **停** 用 cookie 的使用方式，以符合歐盟的 cookie 合規性法則。 如果您想要重新啟用 cookie，並還原沈浸式閱讀程式使用者喜好設定的預設功能，您必須確保您的網站或應用程式能取得使用者的適當同意，以啟用 cookie。 然後，若要在沈浸式閱讀程式中重新啟用 cookie，您必須明確地將 *cookiePolicy* 選項設定為 cookiePolicy，以在啟動沈浸式閱讀程式時 *啟用* 。 下表描述當啟用 *cookiePolicy* 選項時，沈浸式閱讀程式儲存在其 cookie 中的設定。

#### <a name="settings-parameters"></a>Settings 參數

| 設定 | 類型 | 描述 |
| ------- | ---- | ----------- |
| textSize | 數字 | 設定選擇的文字大小。 |
| fontFamily | 字串 | 設定所選字型 ( "Calibri"、"ComicSans" 或 "Sitka" ) 。 |
| textSpacing | 數字 | 設定是否開啟或關閉文字間距。 |
| formattingEnabled | 布林值 | 設定是否開啟或關閉 HTML 格式化。 |
| Theme - 佈景主題 | 字串 | 設定選擇的主題 (例如 "Light"、"深色" ... ) 。 |
| syllabificationEnabled | 布林值 | 設定 syllabification 是開啟或關閉。 |
| nounHighlightingEnabled | 布林值 | 這會設定是否開啟或關閉名詞反白顯示。 |
| nounHighlightingColor | 字串 | 設定選擇的名詞反白顯示色彩。 |
| verbHighlightingEnabled | 布林值 | 設定是否開啟或關閉動詞反白顯示。 |
| verbHighlightingColor | 字串 | 設定所選擇的動詞反白顯示色彩。 |
| adjectiveHighlightingEnabled | 布林值 | 設定是否開啟或關閉形容詞反白顯示。 |
| adjectiveHighlightingColor | 字串 | 設定所選擇的形容詞反白顯示色彩。 |
| adverbHighlightingEnabled | 布林值 | 設定是否開啟或關閉副詞反白顯示。 |
| adverbHighlightingColor | 字串 | 設定選擇的副詞反白顯示色彩。 |
| pictureDictionaryEnabled | 布林值 | 設定是否開啟或關閉相片字典。 |
| posLabelsEnabled | 布林值 | 設定是否要開啟或關閉每個醒目提示部分的上標文字標籤。  |

<br>

## <a name="supported-languages"></a>支援的語言

沈浸式閱讀程式的翻譯功能支援多種語言。 如需詳細資料，請參閱 [語言支援](./language-support.md) 。

<br>

## <a name="html-support"></a>HTML 支援

啟用格式化時，下列內容會轉譯為沈浸式閱讀程式中的 HTML。

| HTML | 支援的內容 |
| --------- | ----------- |
| 字型樣式 | 粗體、斜體、底線、程式碼、刪除線、上標、下標 |
| 未排序清單 | 光碟、圓形、正方形 |
| 排序的清單 | 十進位、大寫字母、小寫字母、右上角、下羅馬字 |

不支援的標記會以同等的呈現。 目前不支援影像和資料表。

<br>

## <a name="browser-support"></a>瀏覽器支援

使用下列瀏覽器的最新版本，以獲得沈浸式閱讀程式的最佳體驗。

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

<br>

## <a name="next-steps"></a>後續步驟

* 探索 [GitHub 上的沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk)
* [快速入門：建立 web 應用程式，以啟動沈浸式閱讀程式 (c # ) ](./quickstarts/client-libraries.md?pivots=programming-language-csharp)
