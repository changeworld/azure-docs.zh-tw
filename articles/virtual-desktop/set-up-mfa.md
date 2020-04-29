---
title: 設定適用于 Windows 虛擬桌面的 Azure 多因素驗證-Azure
description: 如何設定 Azure 多重要素驗證，以提高 Windows 虛擬桌面的安全性。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b470f9278bdca94d1fe98c64b11b070fb36cb075
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80998483"
---
# <a name="set-up-azure-multi-factor-authentication"></a>設定 Azure Multi-Factor Authentication

適用于 Windows 虛擬桌面的 Windows 用戶端是將 Windows 虛擬桌面與本機電腦整合的絕佳選項。 不過，當您將 Windows 虛擬桌面帳戶設定為 Windows 用戶端時，您必須採取一些措施來保護自己和使用者的安全。

當您第一次登入時，用戶端會要求您提供使用者名稱、密碼和 Azure MFA。 之後，當您下次登入時，用戶端將會在您的 Azure Active Directory （AD）企業應用程式中記住您的權杖。 當您選取 [**記住我**] 時，您的使用者可以在重新開機用戶端之後登入，而不需要重新輸入其認證。

雖然記住認證很方便，但它也可以讓企業案例或個人裝置上的部署更不安全。 若要保護您的使用者，您必須確定用戶端持續要求 Azure 多重要素驗證（MFA）認證。 本文將說明如何設定 Windows 虛擬桌面的條件式存取原則，以啟用此設定。

## <a name="prerequisites"></a>先決條件

以下是您需要的入門：

- 將下列其中一個授權指派給您的所有使用者：
  - Microsoft 365 E3 或 E5
  - Azure Active Directory Premium P1 或 P2
  - Enterprise Mobility + Security E3 或 E5
- 已指派為群組成員之使用者的 Azure Active Directory 群組。
- 為您的所有使用者啟用 Azure MFA。 如需如何執行此動作的詳細資訊，請參閱[如何要求使用者使用雙步驟驗證](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)。

>[!NOTE]
>下列設定也適用于[Windows 虛擬桌面 web 用戶端](https://rdweb.wvd.microsoft.com/webclient/index.html)。

## <a name="opt-in-to-the-conditional-access-policy"></a>加入條件式存取原則

1. 開啟**Azure Active Directory**。

2. 移至 [**所有應用程式**] 索引標籤。在 [應用程式類型] 下拉式功能表中，選取 [**企業應用程式**]，然後搜尋**Windows 虛擬桌面用戶端**。

    ![[所有應用程式] 索引標籤的螢幕擷取畫面。使用者在搜尋列中輸入了「windows 虛擬桌面用戶端」，而應用程式已顯示在搜尋結果中。](media/all-applications-search.png)

3. 選取 [**條件式存取**]。

    ![螢幕擷取畫面，顯示使用者將滑鼠游標停留在 [條件式存取] 索引標籤上。](media/conditional-access-location.png)

4. 選取 [ **+ 新增原則**]。

   ![[條件式存取] 頁面的螢幕擷取畫面。 使用者將滑鼠游標停留在 [新增原則] 按鈕上方。](media/new-policy-button.png)

5. 輸入**規則**的 [**名稱**]，然後**選取**您在必要條件中建立之**群組**的 [名稱]。

6. 選取 [**選取**]，然後選取 [**完成**]。

7. 接下來，開啟 [**雲端應用程式] 或 [動作**]。

8. 在 [**選取**] 面板上，選取 [ **Windows 虛擬桌面**企業應用程式]。

    ![[雲端應用程式] 或 [動作] 頁面的螢幕擷取畫面。 使用者選取了 Windows 虛擬桌面應用程式，方法是選取它旁邊的核取記號。 選取的應用程式會以紅色反白顯示。](media/cloud-apps-select.png)
    
    >[!NOTE]
    >您也應該會看到在畫面左側選取的 [Windows 虛擬桌面用戶端應用程式]，如下圖所示。 您需要 Windows 虛擬桌面和 Windows 虛擬桌面用戶端企業應用程式，原則才能正常執行。
    >
    > ![[雲端應用程式] 或 [動作] 頁面的螢幕擷取畫面。 Windows 虛擬桌面和 Windows 虛擬桌面用戶端應用程式會以紅色反白顯示。](media/cloud-apps-enterprise-selected.png)

9. 選取 [**選取**]

10. 接下來，開啟**授**與 

11. 選取 [**需要多重要素驗證**]，然後選取 **[需要其中一個選取的控制項**]。
   
    ![[授與] 頁面的螢幕擷取畫面。 已選取 [需要多重要素驗證]。](media/grant-page.png)

    >[!NOTE]
    >如果您的組織中有已註冊 MDM 的裝置，而不想要讓他們顯示 MFA 提示，您也可以選取 [**需要將裝置標示為符合規範**]。

12. 選取 [**會話**]。

13. 將 [登**入頻率**] 設定為 [**作用中]，然後**將其值變更為 [ **1 小時**]。

    ![[會話] 頁面的螢幕擷取畫面。 [會話] 功能表會顯示 [登入頻率] 下拉式功能表已變更為 [1] 和 [小時]。](media/sign-in-frequency.png)
   
    >[!NOTE]
    >當您變更原則時，Windows 虛擬桌面環境中的使用中會話將繼續作用。 不過，如果您中斷連線或登出，您必須在60分鐘後再次提供認證。 當您變更設定時，您可以視需要延長超時時間（只要它符合您組織的安全性原則）。
    >
    >預設設定是90天的滾動時段，這表示用戶端會在其電腦上的非使用中時間超過90天后，要求使用者重新登入。

14. 啟用原則。

15. 選取 [**建立**] 以確認原則。

您已大功告成！ 請隨意測試原則，以確定您的允許清單可如預期運作。
