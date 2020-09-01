---
title: 使用 web 用戶端連接到 Windows 虛擬桌面-Azure
description: 如何使用 Web 用戶端連線至 Windows 虛擬桌面。
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2ca5a591d168e18181a29cf1c00a7d1fead54595
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226041"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>使用 web 用戶端連接到 Windows 虛擬桌面

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 (傳統版)，請參閱[這篇文章](./virtual-desktop-fall-2019/connect-web-2019.md)。

Web 用戶端可讓您從網頁瀏覽器存取您的 Windows 虛擬桌面資源，而不需要冗長的安裝過程。

>[!NOTE]
>Web 用戶端目前不支援行動作業系統。

## <a name="supported-operating-systems-and-browsers"></a>支援的作業系統和瀏覽器

雖然任何支援 HTML5 的瀏覽器應該皆可正常運作，但我們已正式支援下列作業系統和瀏覽器。

| 瀏覽器           | 支援的 OS                     | 注意               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | 11版或更新版本 |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows、macOS、Linux            | 55 版或更新版本 |
| Google Chrome     | Windows、macOS、Linux、Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>存取遠端資源摘要

在瀏覽器中，在 <https://rdweb.wvd.microsoft.com/arm/webclient> 導覽至 Windows 虛擬桌面 Web 用戶端的 Azure Resource Manager 整合版本，並使用您的使用者帳戶登入。

>[!NOTE]
>如果您使用 Windows 虛擬桌面 (傳統) 而沒有 Azure Resource Manager 整合，請改為連線至您的資源 <https://rdweb.wvd.microsoft.com/webclient> 。
>
> 如果您是使用 US Gov 入口網站，請使用 <https://rdweb.wvd.azure.us/arm/webclient/index.html> 。

>[!NOTE]
>如果您已使用不同於您想要用於 Windows 虛擬桌面的 Azure Active Directory 帳戶登入，請登出或使用私人瀏覽器視窗。

登入之後，您現在應該會看到一份資源清單。 您可以在 [所有資源] 索引標籤中，以選取一般應用程式的方式來啟動資源。

## <a name="next-steps"></a>後續步驟

若要深入了解如何使用 Web 用戶端，請參閱[開始使用 Web 用戶端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)。
