---
title: 連接 Windows 虛擬桌面 web 用戶端-Azure
description: 如何使用 web 用戶端連接到 Windows 虛擬桌面。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c264006d51f8f0f9ee2a56e994a0dc52d24bd7a8
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006027"
---
# <a name="connect-with-the-web-client"></a>與 Web 用戶端連線

>[!IMPORTANT]
>此內容適用于不支援 Azure Resource Manager Windows 虛擬桌面物件的秋季2019版。 如果您嘗試管理春季2020更新中引進的 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../connect-web.md)。

Web 用戶端可讓您從網頁瀏覽器存取您的 Windows 虛擬桌面資源，而不需要冗長的安裝程式。

>[!NOTE]
>Web 用戶端目前不支援行動作業系統。

## <a name="supported-operating-systems-and-browsers"></a>支援的作業系統與瀏覽器

雖然任何具備 HTML5 功能的瀏覽器應該都可以運作，但我們已正式支援下列作業系統和瀏覽器。

| 瀏覽器           | 支援的 OS                     | 注意               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows、macOS、Linux            | 55版或更新版本 |
| Google Chrome     | Windows、macOS、Linux、Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>存取遠端資源摘要

在瀏覽器中，流覽至[Windows 虛擬桌面 web 用戶端](https://rdweb.wvd.microsoft.com/webclient)，並使用您的使用者帳戶登入。

>[!NOTE]
>如果您使用的是 Windows 虛擬桌面春季2020版，請使用本文中的 web 用戶端連結來[連線到您](../connect-web.md)的資源。

>[!NOTE]
>如果您已使用不同于您想要用於 Windows 虛擬桌面的 Azure Active Directory 帳戶登入，您應該登出或使用私用瀏覽器視窗。

登入之後，您現在應該會看到一份資源清單。 您可以藉由在 [**所有資源**] 索引標籤中選取一般應用程式，來啟動資源。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何使用 web 用戶端，請參閱[開始使用 web 用戶端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)。
