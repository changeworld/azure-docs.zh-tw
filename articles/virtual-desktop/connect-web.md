---
title: 使用 web 用戶端連接到 Windows 虛擬桌面-Azure
description: 如何使用 Web 用戶端連線至 Windows 虛擬桌面。
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c8a4b22966c3d4db268e212bb3f2d1bbb78fee74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400631"
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

## <a name="using-an-input-method-editor"></a>使用輸入法編輯器

Web 用戶端支援在 **1.0.21.16 版或更新**版本的遠端會話中，使用輸入法編輯器 (IME) 。 您要在遠端會話中使用的鍵盤語言套件，必須安裝在主機虛擬機器上。 若要深入瞭解如何在遠端會話中設定語言套件，請參閱 [將語言套件新增至 Windows 10 多會話映射](language-packs.md)。

若要使用 web 用戶端啟用輸入法輸入：

1. 在連接到遠端會話之前，請移至 [web 用戶端 **設定** ] 面板。

2. 將 [ **啟用輸入方法編輯器** ] 設定切換為 [ **開啟**]。

3. 在下拉式功能表中，選取您將在遠端會話中使用的鍵盤。

4. 連接到遠端會話。

當您將焦點放在遠端會話時，web 用戶端會隱藏本機 IME 視窗。 一旦您已連線到遠端會話，變更 IME 設定就不會有任何作用。

>[!NOTE]
>如果未在主機虛擬機器上安裝語言套件，則遠端會話將預設為英文 (美國) 鍵盤。

## <a name="next-steps"></a>後續步驟

若要深入了解如何使用 Web 用戶端，請參閱[開始使用 Web 用戶端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)。
