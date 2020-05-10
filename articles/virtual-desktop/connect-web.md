---
title: 連接 Windows 虛擬桌面 web 用戶端-Azure
description: 如何使用 web 用戶端連接到 Windows 虛擬桌面。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: f17d04d94546f31c5613fa9944c7399ea7db10ae
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006056"
---
# <a name="connect-with-the-web-client"></a>與 Web 用戶端連線

>[!IMPORTANT]
>此內容適用于具有 Azure Resource Manager Windows 虛擬桌面物件的春季2020更新。 如果您使用的是 Windows 虛擬桌面不含 Azure Resource Manager 物件的2019版，請參閱[這篇文章](./virtual-desktop-fall-2019/connect-web-2019.md)。
>
> Windows 虛擬桌面春季2020更新目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議針對生產環境工作負載使用。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

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

在瀏覽器中，流覽至[Windows 虛擬桌面 web 用戶端](https://rdweb.wvd.microsoft.com/arm/webclient)，並使用您的使用者帳戶登入。

>[!NOTE]
>如果您使用 Windows 虛擬桌面秋季2019版，請使用本文中的 web 用戶端連結來[連線到您](./virtual-desktop-fall-2019/connect-web-2019.md)的資源。

>[!NOTE]
>如果您已使用不同于您想要用於 Windows 虛擬桌面的 Azure Active Directory 帳戶登入，您應該登出或使用私用瀏覽器視窗。

登入之後，您現在應該會看到一份資源清單。 您可以藉由在 [**所有資源**] 索引標籤中選取一般應用程式，來啟動資源。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何使用 web 用戶端，請參閱[開始使用 web 用戶端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)。
