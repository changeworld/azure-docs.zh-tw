---
title: 設定自訂 Azure Active Directory 密碼保護清單
description: 在本教學課程中，您將了解如何設定 Azure Active Directory 的自訂禁用密碼保護清單，以限制您環境中的常見字組。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91cb1a0819902d2dfa27718ccfe39b175959a16c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963834"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>教學課程：設定 Azure Active Directory 密碼保護的自訂禁用密碼

使用者通常會建立使用常見當地字組 (例如學校、運動團隊或知名人士) 的密碼。 這些密碼很容易猜測，且容易遭受字典型攻擊。 為了在您的組織中強制使用強式密碼，Azure Active Directory (Azure AD) 自訂禁用密碼清單可讓您新增要評估和封鎖的特定字串。 如果自訂禁用密碼清單中有相符項目，密碼變更要求就會失敗。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 啟用自訂禁用密碼
> * 在自訂禁用密碼清單中新增項目
> * 使用禁用密碼測試密碼變更

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要下列資源和權限：

* 至少已啟用 Azure AD Premium P1 或試用版授權的有效 Azure AD 租用戶。
    * 如有需要，[請建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 具有「全域系統管理員」  權限的帳戶。
* 具有已知密碼的非系統管理員使用者，例如 testuser  。 在本教學課程中，您會使用此帳戶來測試密碼變更事件。
    * 如果您需要建立使用者，請參閱 [快速入門：將使用者新增至 Azure Active Directory](../fundamentals/add-users-azure-active-directory.md) 中的資訊。
    * 若要使用禁用密碼來測試密碼變更作業，Azure AD 租用戶必須先[設定為可進行自助式密碼重設](tutorial-enable-sspr.md)。

## <a name="what-are-banned-password-lists"></a>什麼是禁用密碼清單？

Azure AD 包含全域禁用密碼清單。 全域禁用密碼清單的內容不是以任何外部資料來源為基礎。 相反地，全域禁用密碼清單是以 Azure AD 安全性遙測和分析的持續結果為基礎。 當使用者或系統管理員嘗試變更或重設其認證時，系統會根據禁止密碼清單來檢查所需的密碼。 如果全域禁用密碼清單中有相符項目，密碼變更要求就會失敗。 您無法編輯此預設全域禁用密碼清單。

為了讓您有彈性決定允許的密碼，您也可以定義自訂禁用密碼清單。 自訂禁用密碼清單會與全域禁用密碼清單一起運作，以在組織中強制使用強式密碼。 您可將組織特有的字詞新增到自訂禁用密碼清單，例如下列範例：

* 品牌名稱
* 產品名稱
* 位置，例如公司總部
* 公司特有的內部字詞
* 具有特定公司意義的縮寫

當使用者嘗試將密碼重設為全域或自訂禁用密碼清單上的某個項目時，他們會看到下列其中一則錯誤訊息：

* *不幸的是，您的密碼包含單字、片語或模式，可輕易猜到您的密碼。請使用不同的密碼再試一次。*
* *很遺憾，因為密碼包含系統管理員已封鎖的文字或字元，所以無法使用。請使用不同的密碼再試一次。*

自訂禁用密碼清單的上限為 1000 個字詞。 其設計訴求並非用來封鎖大型密碼清單。 若要充分發揮自訂禁用密碼清單的優點，請參閱[自訂禁用密碼清單概念](concept-password-ban-bad.md#custom-banned-password-list)和[密碼評估演算法總覽](concept-password-ban-bad.md#how-are-passwords-evaluated)。

## <a name="configure-custom-banned-passwords"></a>設定自訂禁用密碼

讓我們啟用自訂禁用密碼清單並新增一些項目。 您可隨時在自訂禁用密碼清單中新增其他項目。

若要啟用自訂禁用密碼清單並在其中新增項目，請完成下列步驟：

1. 使用具備「全域系統管理員」權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 **Azure Active Directory**，然後從左側功能表中選擇 [安全性]。
1. 在 [管理] 功能表標題底下，選取 [驗證方法]，然後選取 [密碼保護]。
1. 將 [強制使用自訂清單] 選項設定為 [是]。
1. 在 [自訂禁用密碼清單] 中新增字串 (每行一個字串)。 自訂禁用密碼清單適用下列考量與限制：

    * 自訂禁用密碼清單最多可以包含 1000 個字詞。
    * 自訂禁用密碼清單不會區分大小寫。
    * 自訂禁用密碼清單會考量常見字元替代，例如 "o" 與 "0" 或 "a" 與 "\@"。
    * 最小字串長度為 4 個字元，最大長度為 16 個字元。

    指定您要禁止的自有自訂密碼，如下列範例所示

    [ ![在 Azure 入口網站中的 [驗證方法] 下修改自訂禁用密碼清單](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png) ](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. 讓 [在 Windows Server Active Directory 上啟用密碼保護] 選項保留為 [否]。
1. 若要啟用自訂禁用密碼和您的項目，請選取 [儲存]。

可能需要數小時才會套用自訂禁用密碼清單的更新。

針對混合式環境，您也可以[將 Azure AD 密碼保護部署到內部部署環境](howto-password-ban-bad-on-premises-deploy.md)。 相同的全域和自訂禁用密碼清單會使用於雲端和內部部署密碼變更要求。

## <a name="test-custom-banned-password-list"></a>測試自訂禁用密碼清單

若要查看作用中的自訂禁用密碼清單，請嘗試將密碼變更為在上一節中新增項目的變化。 當 Azure AD 嘗試處理密碼變更時，系統會將密碼與自訂禁用密碼清單中的項目進行比對。 接著會向使用者顯示錯誤。

> [!NOTE]
> Azure AD 租用戶必須先[設定為可進行自助式密碼重設](tutorial-enable-sspr.md)，使用者才能在 Web 架構入口網站中重設其密碼。 如有需要，使用者可以在 [註冊 SSPR https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)。

1. 移至 [我的應用程式] 頁面 (位於 [https://myapps.microsoft.com](https://myapps.microsoft.com))。
1. 在右上角選取您的名稱，然後從下拉式功能表中選擇 [設定檔]。

    ![選取設定檔](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. 在 [設定檔] 頁面上，選取 [變更密碼]。
1. 在 [變更密碼] 頁面上，輸入現有 (舊的) 密碼。 在上一節中定義的自訂禁用密碼清單上，輸入並確認新密碼，然後選取 [提交]。
1. 系統會傳回一則錯誤訊息，告訴您系統管理員已封鎖該密碼，如下列範例所示：

    ![當您嘗試使用屬於自訂禁用密碼清單的密碼時顯示的錯誤訊息](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>清除資源

如果您不想再使用您在本教學課程中所設定的自訂禁用密碼清單，請完成下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 **Azure Active Directory**，然後從左側功能表中選擇 [安全性]。
1. 在 [管理] 功能表標題底下，選取 [驗證方法]，然後選取 [密碼保護]。
1. 將 [強制使用自訂清單] 選項設定為 [否]。
1. 若要更新自訂禁用密碼組態，請選取 [儲存]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已啟用並設定 Azure AD 的自訂密碼保護清單。 您已了解如何︰

> [!div class="checklist"]
> * 啟用自訂禁用密碼
> * 在自訂禁用密碼清單中新增項目
> * 使用禁用密碼測試密碼變更

> [!div class="nextstepaction"]
> [啟用風險型 Azure Multi-Factor Authentication](./tutorial-enable-azure-mfa.md)