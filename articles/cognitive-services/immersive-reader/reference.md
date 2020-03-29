---
title: 沉浸式讀取器 SDK 參考
titleSuffix: Azure Cognitive Services
description: 沉浸式閱讀器 SDK 包含一個 JavaScript 庫，允許您將沉浸式閱讀器集成到應用程式中。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: b20a3e6dd3b32b183bbf34dbefd76f0e4cd56b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156398"
---
# <a name="immersive-reader-sdk-reference-guide"></a>沉浸式讀卡機 SDK 參考指南

沉浸式閱讀器 SDK 包含一個 JavaScript 庫，允許您將沉浸式閱讀器集成到應用程式中。

## <a name="functions"></a>函式

SDK 公開功能：

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>發射Async

在 Web 應用程式中啟動`iframe`沉浸式閱讀器。

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>參數

| 名稱 | 類型 | 描述 |
| ---- | ---- |------------ |
| `token` | 字串 | Azure AD 身份驗證權杖。 |
| `subdomain` | 字串 | Azure 中沉浸式讀取器資源的自訂子域。 |
| `content` | [內容](#content) | 包含要在沉浸式閱讀器中顯示的內容的物件。 |
| `options` | [選項](#options) | 用於配置沉浸式讀取器的某些行為的選項。 選擇性。 |

### <a name="returns"></a>傳回值

返回`Promise<LaunchResponse>`一個，該解析在載入沉浸式讀取器時。 對`Promise`[`LaunchResponse`](#launchresponse)物件的解析。

### <a name="exceptions"></a>例外狀況

如果沉`Promise`浸式讀取器無法[`Error`](#error)載入，則返回的物件將被拒絕。 有關詳細資訊，請參閱[錯誤代碼](#error-codes)。

## <a name="close"></a>關閉

關閉沉浸式讀取器。

此函數的示例用例是，如果退出按鈕通過在```hideExitButton: true```[選項](#options)中設置而隱藏。 然後，當按一下該按鈕時，可以調用其他```close```按鈕（例如移動標頭的後箭頭）。

```typescript
close(): void;
```

## <a name="renderbuttons"></a>渲染按鈕

此功能可樣式並更新文檔的沉浸式讀取器按鈕元素。 如果```options.elements```提供，則此函數將在 中```options.elements```呈現按鈕。 否則，按鈕將在具有 類```immersive-reader-button```的文件項目中呈現。

當視窗載入時，SDK 會自動調用此功能。

有關更多渲染選項[，請參閱可選屬性](#optional-attributes)。

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>參數

| 名稱 | 類型 | 描述 |
| ---- | ---- |------------ |
| `options` | [渲染按鈕選項](#renderbuttonsoptions) | 用於配置呈現按鈕函數的某些行為的選項。 選擇性。 |

## <a name="types"></a>型別

### <a name="content"></a>內容

包含要在沉浸式閱讀器中顯示的內容。

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>區塊

單個資料塊，將傳遞到沉浸式讀取器的內容中。

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>啟動回應

包含從調用 到`ImmersiveReader.launchAsync`的回應。

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>Cookie 政策枚舉

用於設置沉浸式閱讀器 Cookie 使用策略的枚舉。 請參閱[選項](#options)。

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>支援的 MIME 類型

| MIME 類型 | 描述 |
| --------- | ----------- |
| text/plain | 純文字。 |
| text/html | HTML 內容。 [深入了解](#html-support)|
| 應用程式/mathml_xml | 數學標記語言（數學ML）。 [深入了解](./how-to/display-math.md)。
| 應用程式/vnd.openxml 格式-辦公室文檔.文書處理ml.document | 微軟 Word .docx 格式文檔。

### <a name="html-support"></a>HTML 支援

| HTML | 支援的內容 |
| --------- | ----------- |
| 字體樣式 | 粗體、斜體、底線、代碼、打擊、超編、下標 |
| 未排序清單 | 光碟， 圓， 方形， 圓， 圓， 圓， 圓， 圓， 圓 |
| 已訂購清單 | 十進位， 上阿爾法， 下阿爾法， 上羅馬， 下羅馬 |
| 超連結 | 敬請期待 |

不支援的標記將呈現比較。 當前不支援圖像和表。

### <a name="options"></a>選項。

包含配置沉浸式讀取器某些行為的屬性。

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
    cookiePolicy?: CookiePolicy; // Setting for the Immersive Reader's cookie usage (default is CookiePolicy.Disable). It's the responsibility of the host application to obtain any necessary user consent in accordance with EU Cookie Compliance Policy.
}
```

### <a name="renderbuttonsoptions"></a>渲染按鈕選項

用於渲染沉浸式讀取器按鈕的選項。

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

### <a name="error"></a>錯誤

包含有關錯誤的資訊。

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>錯誤碼

| 程式碼 | 描述 |
| ---- | ----------- |
| BadArgument | 提供的參數無效，請參閱`message`詳細資訊。 |
| 逾時 | 沉浸式讀取器無法在指定的超時內載入。 |
| TokenExpired | 提供的權杖已過期。 |
| 調整執行速度 | 已超出呼叫速率限制。 |

## <a name="launching-the-immersive-reader"></a>啟動沉浸式閱讀器

SDK 為啟動沉浸式讀取器的按鈕提供預設樣式。 使用`immersive-reader-button`類屬性啟用此樣式。 有關詳細資訊，請參閱[此文章](./how-to-customize-launch-button.md)。

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>可選屬性

使用以下屬性配置按鈕的外觀。

| 屬性 | 描述 |
| --------- | ----------- |
| `data-button-style` | 設置按鈕的樣式。 可以是 `icon`、`text` 或 `iconAndText`。 預設為 `icon`。 |
| `data-locale` | 設置地區設定。 例如，`en-US` 或 `fr-FR`。 預設為英語`en`。 |
| `data-icon-px-size` | 設置以圖元為單位的圖示大小。 預設值為 20px。 |

## <a name="browser-support"></a>瀏覽器支援

使用以下瀏覽器的最新版本，獲得沉浸式閱讀器的最佳體驗。

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>後續步驟

* 探索 [GitHub 上的沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk)
* [快速入門：創建啟動沉浸式閱讀器 （C#） 的 Web 應用](./quickstart.md)
