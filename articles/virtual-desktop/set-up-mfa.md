---
title: 為 Windows 虛擬桌面設定 Azure 多重身份驗證 - Azure
description: 如何設置 Azure 多重身份驗證,提高 Windows 虛擬桌面的安全性。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0b3c47e1bbe5efdc5ee303305e52a785a49d0c00
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586869"
---
# <a name="set-up-azure-multi-factor-authentication"></a>設定 Azure 多重身份驗證

Windows 虛擬桌面的 Windows 用戶端是將 Windows 虛擬桌面與本地電腦整合的絕佳選項。 但是,當您將 Windows 虛擬桌面帳戶配置為 Windows 用戶端時,您需要採取某些措施來保護自己和使用者的安全。

首次登錄時,用戶端會要求您提供使用者名、密碼和 Azure MFA。 之後,下次登錄時,用戶端將從 Azure 活動目錄 (AD) 企業應用程式中記住權杖。 當您選擇 **「記住我」** 時,您的用戶可以在重新啟動用戶端後登錄,而無需重新輸入其認證。

雖然記住憑據很方便,但它也會使企業方案或個人設備上的部署的安全性降低。 為了保護使用者,您需要確保客戶端不斷要求 Azure 多重身份驗證 (MFA) 認證。 本文將介紹如何為 Windows 虛擬桌面設定條件存取策略以啟用此設定。

## <a name="prerequisites"></a>Prerequisites

以下是入門所需的內容:

- 為所有使用者配置以下授權之一:
  - 微軟 365 E3 或 E5
  - Azure 活動目錄進階 P1 或 P2
  - 企業移動性 + 安全 E3 或 E5
- 將使用者分配為組成員的 Azure 活動目錄組。
- 為所有用戶啟用 Azure MFA。 有關如何執行此操作的詳細資訊,請參閱[如何要求使用者進行兩步驗證](/active-directory/authentication/howto-mfa-userstates)。

>[!NOTE]
>以下設定也適用於[Windows 虛擬桌面 Web 用戶端](https://rdweb.wvd.microsoft.com/webclient/index.html)。

## <a name="opt-in-to-the-conditional-access-policy"></a>加入加入條件存取原則

1. 開啟**Azure 的項目目錄**。

2. 跳到「**所有應用程式**」 選項卡。在「應用程式類型」下拉選單中,選擇**企業應用程式**,然後搜索**Windows 虛擬桌面用戶端**。

    !["所有應用程式"選項卡的屏幕截圖。使用者將「視窗虛擬桌面用戶端」輸入到搜索欄中,並且應用已顯示在搜尋結果中。](media/all-applications-search.png)

3. 選擇**條件存取**。

    ![顯示使用者將滑鼠游標懸停在「條件存取」選項卡上的螢幕截圖。](media/conditional-access-location.png)

4. 選擇 **= 新原則**。

   ![條件訪問頁面的屏幕截圖。 用戶將滑鼠游標懸停在新策略按鈕上。](media/new-policy-button.png)

5. 輸入規則**的名稱**,**rule**然後**選擇**在先決條件中創建的**組的**的 @name。

6. **選擇 「選擇**」,然後選擇 **「完成**」 。

7. 接下來,打開**雲應用或操作**。

8. 在 **「選擇**」面板上,選擇**Windows 虛擬桌面**企業應用。

    ![雲應用或操作頁面的螢幕截圖。 使用者已選擇 Windows 虛擬桌面應用,選擇其旁邊的複選標記。 所選應用以紅色突出顯示。](media/cloud-apps-select.png)
    
    >[!NOTE]
    >您還應在螢幕左側看到選擇的 Windows 虛擬桌面用戶端應用,如下圖所示。 您需要 Windows 虛擬桌面和 Windows 虛擬桌面用戶端企業應用才能使策略正常工作。
    >
    > ![雲應用或操作頁面的螢幕截圖。 Windows 虛擬桌面和 Windows 虛擬桌面用戶端應用以紅色突出顯示。](media/cloud-apps-enterprise-selected.png)

9. **選擇**

10. 接下來,開啟**授予** 

11. 選擇 **"需要多重身份驗證**",然後選擇 **"需要其中一個選定的控制件**"。
   
    ![授予頁面的螢幕截圖。 選擇"需要多重身份驗證」。](media/grant-page.png)

    >[!NOTE]
    >如果您的組織中有 MDM 註冊的設備,並且不希望它們顯示 MFA 提示符,也可以選擇 **「要求設備標記為符合」。**

12. 選擇**工作階段**。

13. 將**登入頻率**設定為 **「活動**」,然後將其值更改為**1 小時**。

    ![會話頁的屏幕截圖。 會話選單顯示登錄頻率下拉菜單已更改為"1"和"小時"。](media/sign-in-frequency.png)
   
    >[!NOTE]
    >當您更改策略時,Windows 虛擬桌面環境中的活動作業階段將繼續工作。 但是,如果斷開連接或註銷,則需要在 60 分鐘后再次提供憑據。 更改設置時,可以根據需要延長超時期限(只要它與組織的安全策略保持一致)。
    >
    >默認設置為 90 天的滾動視窗,這意味著用戶端將請求使用者在電腦上處於 90 天或更長時間的非活動狀態後嘗試訪問資源時再次登錄。

14. 啟用策略。

15. 選擇 **「建立**」以確認策略。

您已大功告成！ 請隨意測試策略,以確保允許清單按預期工作。
