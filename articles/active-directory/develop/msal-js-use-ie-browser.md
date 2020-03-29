---
title: 關於互聯網瀏覽器的問題 （MSAL.js） |蔚藍
titleSuffix: Microsoft identity platform
description: 將 Microsoft 身份驗證庫用於 JavaScript （MSAL.js） 與 Internet 資源管理器瀏覽器。
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
ms.openlocfilehash: c4f3c4153e1404a5576427be7ef218f5a669387e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695852"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>在 Internet 資源管理器和微軟邊緣瀏覽器 （MSAL.js） 上的已知問題

為 JavaScript 生成 Microsoft 身份驗證庫 （MSAL.js）， 為[JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29)生成，以便它可以在 Internet 資源管理器中運行。 然而，有幾件事要知道。

## <a name="run-an-app-in-internet-explorer"></a>在 Internet 資源管理器中運行應用
如果要在可以在 Internet 資源管理器中運行的應用程式中使用 MSAL.js，則需要在引用 MSAL.js 腳本之前添加對承諾多邊形填充的引用。

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

這是因為 Internet 資源管理器不支援 JavaScript 本機承諾。

## <a name="debugging-an-application-running-in-internet-explorer"></a>調試在 Internet 資源管理器中運行的應用程式

### <a name="running-in-production"></a>在生產中運行
將應用程式部署到生產（例如，在 Azure Web 應用中）通常工作正常，前提是最終使用者已接受快顯視窗。 我們用互聯網瀏覽器11測試了它。

### <a name="running-locally"></a>在本地運行
如果要在 Internet 資源管理器中運行和調試本地應用程式，則需要注意以下注意事項（假設您要以 運行應用程式為*http://localhost:1234*：

- Internet Explorer 具有名為"受保護模式"的安全機制，可防止 MSAL.js 正常工作。 在症狀中，登錄後，頁面可以重定向到http://localhost:1234/null。

- 要在本地運行和調試應用程式，您需要禁用此"受保護模式"。 為此：

    1. 按一下 Internet 資源管理器**工具**（齒輪圖示）。
    1. 選擇 **"互聯網選項**"，然後選擇 **"安全**"選項卡。
    1. 按一下**Internet**區域，並取消選中**啟用保護模式（需要重新開機 Internet 資源管理器）。** Internet Explorer 警告您的電腦不再受到保護。 按一下 [確定]****。
    1. 重新開機 Internet 資源管理器。
    1. 運行並調試應用程式。

完成後，請還原 Internet 資源管理器安全設置。  選擇**設置** -> **互聯網選項** -> **安全** -> **將所有區域重置為預設級別**。

## <a name="next-steps"></a>後續步驟
在[Internet 資源管理器中使用 MSAL.js 時，瞭解有關已知問題](msal-js-use-ie-browser.md)的更多資訊。