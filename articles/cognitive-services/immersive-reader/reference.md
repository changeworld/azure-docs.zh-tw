---
title: 沉浸式讀取器 SDK 參考
titleSuffix: Azure Cognitive Services
description: 沉浸式讀取器 SDK 包含 JavaScript 程式庫，可讓您將沉浸式讀取器整合到您的應用程式中。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 879834567b6905a070aada3dae2a41a672635c6c
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267234"
---
# <a name="immersive-reader-sdk-reference-guide"></a>沉浸式讀取器 SDK 參考指南

沉浸式讀取器 SDK 包含 JavaScript 程式庫，可讓您將沉浸式讀取器整合到您的應用程式中。

## <a name="functions"></a>函式

SDK 會公開函式：

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

`iframe`在您的 web 應用程式中，啟動中的沉浸式讀取器。

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>參數

| 名稱 | 類型 | 描述 |
| ---- | ---- |------------ |
| `token` | 字串 | Azure AD 驗證 token。 |
| `subdomain` | 字串 | Azure 中您的沉浸式讀取器資源的自訂子域。 |
| `content` | [內容](#content) | 物件，包含要在沉浸式讀取器中顯示的內容。 |
| `options` | [選項](#options) | 設定沉浸式讀取器特定行為的選項。 選擇性。 |

### <a name="returns"></a>傳回值

傳回 `Promise<LaunchResponse>` ，它會在載入沉浸式讀取器時解析。 會 `Promise` 解析為 [`LaunchResponse`](#launchresponse) 物件。

### <a name="exceptions"></a>例外狀況

`Promise` [`Error`](#error) 如果無法載入沉浸式讀取器，則傳回的將會被物件拒絕。 如需詳細資訊，請參閱[錯誤碼](#error-codes)。

## <a name="close"></a>關閉

關閉沉浸式讀取器。

此函式的範例使用案例是，如果在 [選項] 中設定隱藏 [結束] 按鈕，則為 ```hideExitButton: true``` 。 [options](#options) 然後，另一個按鈕（例如，行動電話標頭的上一頁箭號）可以 ```close``` 在按一下時呼叫這個函式。

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

此函式會將檔的樣式和更新內容， 如果 ```options.elements``` 提供，則此函式會在內呈現按鈕 ```options.elements``` 。 否則，按鈕將會在具有類別的檔元素中轉譯 ```immersive-reader-button``` 。

當視窗載入時，SDK 會自動呼叫此函式。

如需更多呈現選項，請參閱[選擇性屬性](#optional-attributes)。

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>參數

| 名稱 | 類型 | 說明 |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | 設定 renderButtons 函數之特定行為的選項。 選擇性。 |

## <a name="types"></a>類型

### <a name="content"></a>內容

包含要顯示在沉浸式讀取器中的內容。

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>區塊

資料的單一區塊，會傳遞給沉浸式讀取器的內容。

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>LaunchResponse

包含呼叫的回應 `ImmersiveReader.launchAsync` 。

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>CookiePolicy 列舉

用來為沉浸式讀取器的 cookie 使用方式設定原則的列舉。 請參閱[選項](#options)。

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>支援的 MIME 類型

| MIME 類型 | 說明 |
| --------- | ----------- |
| text/plain | 純文字。 |
| text/html | HTML 內容。 [深入了解](#html-support)|
| application/mathml + xml | 數學標記語言（MathML）。 [深入了解](./how-to/display-math.md)。
| 應用程式/application. vnd.openxmlformats-officedocument.spreadsheetml.sheet. wordprocessingml 檔 | Microsoft Word .docx 格式檔。

### <a name="html-support"></a>HTML 支援

| HTML | 支援的內容 |
| --------- | ----------- |
| 字型樣式 | 粗體、斜體、底線、程式碼、刪除線、上標、下標 |
| 未排序清單 | 光碟、圓形、正方形 |
| 排序清單 | 十進位、上 Alpha、小寫、英數位元、上下羅馬字母、小寫 |

不支援的標記會以同等方式呈現。 目前不支援影像和資料表。

### <a name="options"></a>選項。

包含屬性，可設定沉浸式讀取器的特定行為。

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

### <a name="renderbuttonsoptions"></a>RenderButtonsOptions

呈現沉浸式讀取器按鈕的選項。

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

### <a name="error"></a>錯誤

包含錯誤的相關資訊。

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>錯誤碼

| 程式碼 | 說明 |
| ---- | ----------- |
| BadArgument | 提供的引數無效 `message` 。如需詳細資料，請參閱。 |
| 逾時 | 沉浸式讀取器無法在指定的超時時間內載入。 |
| TokenExpired | 提供的權杖已過期。 |
| 調整執行速度 | 已超過呼叫率限制。 |

## <a name="launching-the-immersive-reader"></a>啟動沉浸式讀取器

SDK 會為啟動沉浸式讀取器的按鈕提供預設樣式。 使用 `immersive-reader-button` 類別屬性來啟用此樣式。 如需詳細資訊，請參閱[這篇文章](./how-to-customize-launch-button.md)。

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>選擇性屬性

使用下列屬性來設定按鈕的外觀與風格。

| 屬性 | 描述 |
| --------- | ----------- |
| `data-button-style` | 設定按鈕的樣式。 可以是 `icon`、`text` 或 `iconAndText`。 預設為 `icon`。 |
| `data-locale` | 設定地區設定。 例如，`en-US` 或 `fr-FR`。 預設為英文 `en` 。 |
| `data-icon-px-size` | 設定圖示的大小（以圖元為單位）。 預設為20px。 |

## <a name="browser-support"></a>瀏覽器支援

使用下列瀏覽器的最新版本，取得沉浸式讀取器的最佳體驗。

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>後續步驟

* 探索 [GitHub 上的沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk)
* [快速入門：建立可啟動沉浸式讀取器的 web 應用程式（c #）](./quickstarts/client-libraries.md?pivots=programming-language-csharp)
