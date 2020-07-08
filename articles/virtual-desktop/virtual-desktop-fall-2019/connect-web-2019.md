---
title: 連線 Windows 虛擬桌面 Web 用戶端 - Azure
description: 如何使用 Web 用戶端連線至 Windows 虛擬桌面。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e95498498fd15d7a6e73630e218f9bdd1b892f09
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85204790"
---
# <a name="connect-with-the-web-client"></a>與 Web 用戶端連線

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 2019 年秋季版本。 如果您嘗試管理 2020 年春季版更新中引進的 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../connect-web.md)。

Web 用戶端可讓您從網頁瀏覽器存取您的 Windows 虛擬桌面資源，而不需要冗長的安裝過程。

>[!NOTE]
>Web 用戶端目前不支援行動作業系統。

## <a name="supported-operating-systems-and-browsers"></a>支援的作業系統和瀏覽器

雖然任何支援 HTML5 的瀏覽器應該皆可正常運作，但我們已正式支援下列作業系統和瀏覽器。

| 瀏覽器           | 支援的 OS                     | 注意               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows、macOS、Linux            | 55 版或更新版本 |
| Google Chrome     | Windows、macOS、Linux、Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>存取遠端資源摘要

在瀏覽器中，在 <https://rdweb.wvd.microsoft.com/webclient> 導覽至 Windows 虛擬桌面 Web 用戶端 ，並使用您的使用者帳戶登入。

>[!NOTE]
>如果您使用包含 Azure Resource Manager 整合的 Windows 虛擬桌面 2020 年春季版，請改為連線至 <https://rdweb.wvd.microsoft.com/arm/webclient> 的資源。

>[!NOTE]
>如果您已使用不同於您想要用於 Windows 虛擬桌面的 Azure Active Directory 帳戶登入，請登出或使用私人瀏覽器視窗。

登入之後，您現在應該會看到一份資源清單。 您可以在 [所有資源] 索引標籤中，以選取一般應用程式的方式來啟動資源。

## <a name="next-steps"></a>後續步驟

若要深入了解如何使用 Web 用戶端，請參閱[開始使用 Web 用戶端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)。
