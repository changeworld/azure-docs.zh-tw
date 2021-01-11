---
title: 'Internet Explorer ( # A0) 的相關問題 |蔚藍'
titleSuffix: Microsoft identity platform
description: '使用適用于 JavaScript 的 Microsoft 驗證程式庫 ( # A0) 搭配 Internet Explorer 瀏覽器。'
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 428405fc200751d2581ad9ca6e8ec97382594c09
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064839"
---
# <a name="known-issues-on-internet-explorer-browsers-msaljs"></a>Internet Explorer 瀏覽器 ( # A0) 的已知問題

適用于 JavaScript 的 Microsoft 驗證程式庫 ( # A0) 是針對 [JAVASCRIPT ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) 所產生，因此可在 Internet Explorer 中執行。 不過，有幾件事需要知道。

## <a name="run-an-app-in-internet-explorer"></a>在 Internet Explorer 中執行應用程式
如果您想要在可于 Internet Explorer 中執行的應用程式中使用 MSAL.js，您必須在參考 MSAL.js 腳本之前，先加入 polyfill 的參考。

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

這是因為 Internet Explorer 不支援以原生方式提供 JavaScript。

## <a name="debugging-an-application-running-in-internet-explorer"></a>在 Internet Explorer 中執行應用程式的偵錯工具

### <a name="running-in-production"></a>在生產環境中執行
將您的應用程式部署到 Azure Web apps 中的生產環境 () 通常可以正常運作，但前提是使用者已接受快顯視窗。 我們測試了 Internet Explorer 11。

### <a name="running-locally"></a>在本機執行
如果您想要在本機執行應用程式並在 Internet Explorer 中進行偵錯工具，您必須注意下列考慮 (假設您想要以) 的方式執行應用程式 *http://localhost:1234* ：

- Internet Explorer 具有名為「受保護模式」的安全性機制，可防止 MSAL.js 無法正常運作。 在您登入後的徵兆中，可以將頁面重新導向至 http://localhost:1234/null 。

- 若要在本機執行和偵錯工具，您必須停用此「受保護模式」。 針對此：

    1. 按一下齒輪圖示)  (Internet Explorer **工具** ]。
    1. 選取 [ **網際網路選項** ]，然後選取 [ **安全性** ] 索引標籤。
    1. 按一下 [ **網際網路** ] 區域，然後取消核取 [ **啟用受保護模式] (需要重新開機 Internet Explorer)**。 Internet Explorer 警告您的電腦已不再受到保護。 按一下 [確定]。
    1. 重新開機 Internet Explorer。
    1. 執行您的應用程式並進行偵錯工具。

當您完成時，請還原 Internet Explorer 的安全性設定。  選取 [**設定**  ->  **網際網路選項**  ->  **安全性**  ->  **將所有區域重設為預設層級**]。

## <a name="next-steps"></a>後續步驟
深入瞭解 [在 Internet Explorer 中使用 MSAL.js 時的已知問題](msal-js-use-ie-browser.md)。