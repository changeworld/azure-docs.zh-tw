---
title: 啟用 Azure Multi-Factor Authentication
description: 在本教學課程中，您將了解如何為使用者群組啟用 Azure Multi-Factor Authentication，並在登入事件期間測試是否會出現次要因素提示。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddb252d7ba5534269d3da1e14064740690879816
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963800"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-multi-factor-authentication"></a>教學課程：使用 Azure Multi-Factor Authentication 來保護使用者登入事件

多重要素驗證 (MFA) 是一種程序，此程序會讓系統在登入事件期間提示使用者提供其他形式的識別。 此提示可能會要求使用者在行動電話上輸入代碼，也可能會要求其提供指紋掃描。 當您要求第二種形式的驗證時，安全性將會增加，因為這項額外的因素並不容易讓攻擊者取得或複製。

Azure Multi-Factor Authentication 和條件式存取原則可讓您在特定登入事件期間，彈性地為使用者啟用 MFA。

> [!IMPORTANT]
> 此教學課程將說明系統管理員如何啟用 Azure Multi-Factor Authentication。
>
> 如果您的 IT 小組尚未啟用使用 Azure Multi-Factor Authentication 的功能，或您在進行單一登入時遇到問題，請與您的技術服務人員聯繫以取得其他協助。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立條件式存取原則，來為一組使用者啟用 Azure Multi-Factor Authentication
> * 設定要提示 MFA 的原則條件
> * 以使用者身分測試 MFA 程序

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要下列資源和權限：

* 至少已啟用 Azure AD Premium P1 或試用版授權的有效 Azure AD 租用戶。
    * 如有需要，[請建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 具有「全域系統管理員」  權限的帳戶。
* 具有已知密碼的非系統管理員使用者，例如 testuser  。 在本教學課程中，您會使用此帳戶來測試使用者的 Azure Multi-Factor Authentication 體驗。
    * 如果您需要建立使用者，請參閱 [快速入門：將使用者新增至 Azure Active Directory](../fundamentals/add-users-azure-active-directory.md) 中的資訊。
* 非系統管理員使用者為其成員的群組，例如 MFA-Test-Group。 您會在本教學課程中為此群組啟用 Azure Multi-Factor Authentication。
    * 如果您需要建立群組，請參閱如何[在 Azure Active Directory 中建立群組和新增成員](../fundamentals/active-directory-groups-create-azure-portal.md)。

## <a name="create-a-conditional-access-policy"></a>建立條件式存取原則

建議您使用條件式存取原則來啟用和使用 Azure Multi-Factor Authentication。 條件式存取可讓您建立及定義會對登入事件做出反應的原則，並在要求使用者進行完其他動作後才對其授與應用程式或服務的存取權。

![條件式存取如何運作以保護登入程序的概觀圖表](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

條件式存取原則可以細微且明確，其目標在於既讓使用者隨時隨地都具有生產力，同時又能保護您的組織。 在本教學課程中，讓我們建立基本的條件式存取原則，以便在使用者登入 Azure 入口網站時提示 MFA。 在此系列稍後的教學課程中，您會使用風險型條件式存取原則來設定 Azure Multi-Factor Authentication。

首先，建立條件式存取原則，並指派使用者測試群組，如下所示：

1. 使用具備「全域系統管理員」權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 **Azure Active Directory**，然後從左側功能表中選擇 [安全性]。
1. 選取 [條件式存取]，然後選擇 [+ 新增原則]。
1. 輸入原則的名稱，例如「MFA 試驗」。
1. 在 [指派] 底下，選擇 [使用者和群組]，然後選擇 [選取使用者和群組] 選項按鈕。
1. 核取 [使用者和群組] 方塊，然後 [選取] 以瀏覽可用的 Azure AD 使用者和群組。
1. 瀏覽並選取 Azure AD 群組，例如 *MFA-Test-Group*，然後選擇 [選取]。

    [ ![選取要與條件式存取原則搭配使用的 Azure AD 群組](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png) ](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. 若要為群組套用條件式存取原則，請選取 [完成]。

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>設定多重要素驗證的條件

建立了條件式存取原則並已指派使用者測試群組後，現在請定義可觸發原則的雲端應用程式或動作。 這些雲端應用程式或動作是您決定需要額外處理的案例，例如需要提示 MFA。 例如，您可以決定在存取財務應用程式或使用管理工具時，需要有額外驗證的提示。

在本教學課程中，請設定條件式存取原則，使其會在使用者登入 Azure 入口網站時要求 MFA。

1. 選取 [雲端應用程式或動作]。 您可以選擇將條件式存取原則套用至 [所有雲端應用程式] 或 [選取應用程式]。 為了提供彈性，您也可以在原則中排除特定應用程式。

    在本教學課程中，請在 [包含] 頁面上，選擇 [選取應用程式] 選項按鈕。

1. 選擇 [選取]，然後瀏覽可使用的可用登入事件清單。

    在本教學課程中，請選擇 [Microsoft Azure 管理]，讓原則套用至 Azure 入口網站的登入事件。

1. 若要套用選取應用程式，請選擇 [選取]，然後選擇 [完成]。

    ![選取要包含在條件式存取原則中的 Microsoft Azure 管理應用程式](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

存取控制可讓您定義使用者要獲得存取權所需符合的需求，例如需要已核准的用戶端應用程式，或使用已加入混合式 Azure AD 的裝置。 在本教學課程中，請將存取控制設定為在 Azure 入口網站登入事件期間要求 MFA。

1. 在 [存取控制] 底下，選擇 [授與]，然後確定您已選取 [授與存取權] 選項按鈕。
1. 核取 [需要多重要素驗證] 方塊，然後選擇 [選取]。

如果您想要查看設定會如何影響使用者，則可以將條件式存取原則設定為 [報告專用]，如果您不想要立即使用原則，則可以設定為 [關閉]。 作為本教學課程的目標使用者測試群組，請啟用原則，然後測試 Azure Multi-Factor Authentication。

1. 將 [啟用原則] 切換為 [開啟]。
1. 若要套用條件式存取原則，請選取 [建立]。

## <a name="test-azure-multi-factor-authentication"></a>測試 Azure Multi-Factor Authentication

讓我們看看條件式存取原則和 Azure Multi-Factor Authentication 的運作方式。 首先，登入不需要 MFA 的資源，如下所示：

1. 在 InPrivate 或 Incognito 模式中開啟新的瀏覽器視窗，並瀏覽至 [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)
1. 使用非系統管理員測試使用者來登入，例如 testuser。 系統不會出現任何提示來要求您完成 MFA。
1. 關閉瀏覽器視窗。

現在，登入 Azure 入口網站。 由於 Azure 入口網站已在條件式存取原則中設定為需要額外驗證，因此您會看到 Azure Multi-Factor Authentication 提示。

1. 在 InPrivate 或 Incognito 模式中開啟新的瀏覽器視窗，並瀏覽至 [https://portal.azure.com](https://portal.azure.com)。
1. 使用非系統管理員測試使用者來登入，例如 testuser。 您必須註冊並使用 Azure Multi-Factor Authentication。 請遵循提示以完成此程序，並確認您已成功登入 Azure 入口網站。

    ![遵循瀏覽器提示，然後進入到您已註冊的多重要素驗證提示以便登入](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. 關閉瀏覽器視窗。

## <a name="clean-up-resources"></a>清除資源

如果您不想再使用條件式存取原則來啟用本教學課程中所設定的 Azure Multi-Factor Authentication ，請使用下列步驟來刪除原則：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 **Azure Active Directory**，然後從左側功能表中選擇 [安全性]。
1. 選取 [條件式存取]，然後選擇您建立的原則，例如「MFA 試驗」
1. 選擇 [刪除]，然後確認您想要刪除該原則。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已針對選取的使用者群組，使用條件式存取原則來啟用 Azure Multi-Factor Authentication。 您已了解如何︰

> [!div class="checklist"]
> * 建立條件式存取原則，來為一組 Azure AD 使用者啟用 Azure Multi-Factor Authentication
> * 設定要提示 MFA 的原則條件
> * 以使用者身分測試 MFA 程序

> [!div class="nextstepaction"]
> [針對自助式密碼重設 (SSPR) 啟用密碼回寫](./tutorial-enable-sspr-writeback.md)