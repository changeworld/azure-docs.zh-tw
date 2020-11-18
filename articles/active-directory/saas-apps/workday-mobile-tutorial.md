---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Workday 行動應用程式整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Workday 行動應用程式之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: e706649957bf427cd577d7995fb9ce104c687f4b
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378987"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Workday 行動應用程式整合

在本教學課程中，您將了解如何整合 Azure Active Directory (Azure AD)、條件式存取和 Intune 與 Workday 行動應用程式。 當您將 Workday 行動應用程式與 Microsoft 整合時，您可以：

* 在登入之前就確定裝置符合您的原則。
* 在 Workday 行動應用程式中新增控制項，以確保使用者能安全地存取公司資料。 
* 在 Azure AD 中控制可存取 Workday 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Workday。
* 在 Azure 入口網站中集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

開始進行之前：

* 整合 Workday 與 Azure AD。
* 請參閱 [Azure Active Directory 單一登入 (SSO) 與 Workday 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會使用 Workday 行動應用程式來設定及測試 Azure AD 條件式存取原則和 Intune。

若要啟用單一登入 (SSO)，您可以使用 Azure AD 設定 Workday 同盟應用程式。 如需詳細資訊，請參閱 [Azure Active Directory 單一登入 (SSO) 與 Workday 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)。

> [!NOTE] 
> Workday 不支援 Intune 的應用程式防護原則。 您必須使用行動裝置管理來利用條件式存取功能。


## <a name="ensure-users-have-access-to-workday-mobile-application"></a>確保使用者可以存取 Workday 行動應用程式

設定 Workday 以允許存取其行動應用程式。 您需要為 Workday 行動設定下列原則：

1. 存取功能區域報告的網域安全性原則。
1. 選取適當的安全性原則：
    * 行動使用量 - Android
    * 行動使用量 - iPad
    * 行動使用量 - iPhone
1. 選取 [編輯權限]。
1. 選取 [檢視或修改] 核取方塊，向安全性群組授與報告或工作安全性實體項目的存取權。
1. 選取 [取得或放置] 核取方塊，向安全性群組授與整合的存取權以及報告或工作安全性實體動作的存取權。

藉由執行 **啟用擱置安全性原則變更** 來啟用擱置安全性原則變更。

## <a name="open-workday-sign-in-page-in-workday-mobile-browser"></a>在 Workday 行動瀏覽器中開啟 Workday 登入頁面

若要將條件式存取套用至 Workday 行動應用程式，您必須在外部瀏覽器中開啟應用程式。 在 [編輯租用戶設定 - 安全性] 中，選取 [啟用原生應用程式的行動瀏覽器 SSO]。 這需要您已在 iOS 的裝置上和 Android 的工作設定檔中安裝 Intune 核准的瀏覽器。

![Workday 行動瀏覽器登入的螢幕擷取畫面。](./media/workday-tutorial/mobile-browser.png)

## <a name="set-up-conditional-access-policy"></a>設定條件式存取原則

此原則只會影響 iOS 或 Android 裝置上的登入。 如果您想要將其延伸至所有平台，請選取 [任何裝置]。 此原則會要求裝置必須符合原則，而且會透過 Intune 來確認這一點。 因為 Android 有工作設定檔，所以會封鎖任何使用者登入 Workday，除非他們是透過其工作設定檔登入，並已透過 Intune 公司入口網站安裝應用程式。 iOS 則還要進行一個步驟才能確保會套用相同的情況。

Workday 支援下列存取控制：
* 需要多重要素驗證
* 裝置需要標記為符合規範

Workday 應用程式不支援下列各項：
* 需要已核准的用戶端應用程式
* 需要應用程式保護原則 (預覽)

若要將 Workday 設定為受控裝置，請執行下列步驟：

![僅限受控裝置和雲端應用程式或動作的螢幕擷取畫面。](./media/workday-tutorial/managed-devices-only.png)

1. 選取 [首頁]  >  [Microsoft Intune]  >  [條件式存取原則]。 然後選取 [僅限受控裝置]。 

1. 在 [僅限受控裝置] 的 [名稱] 底下，選取 [僅限受控裝置]，然後選取 [雲端應用程式或動作]。

1. 在 [雲端應用程式或動作] 中：

    a. 將 [選取此原則的套用對象] 切換為 [雲端應用程式]。

    b. 在 [包含] 中，選擇 [選取應用程式]。

    c. 從 [選取] 清單中選擇 [Workday]。

    d. 選取 [完成]。

1. 將 [啟用原則] 切換為 [開啟]。

1. 選取 [儲存]。

針對 [授與] 存取權，請執行下列步驟：

![僅限受控裝置和授與的螢幕擷取畫面。](./media/workday-tutorial/managed-devices-only-2.png)

1. 選取 [首頁]  >  [Microsoft Intune]  >  [條件式存取原則]。 然後選取 [僅限受控裝置]。 

1. 在 [僅限受控裝置] 的 [名稱] 底下，選取 [僅僅限受控裝置]。 在 [存取控制]  底下，選取 [授與]  。

1. 在 [授與] 中：

    a. 選取要強制執行的控制項作為 [授與存取權]。

    b. 選取 [裝置需要標記為合規]  。

    c. 選取 [要求採用其中一個選取的控制項]。

    d. 選擇 [選取]。

1. 將 [啟用原則] 切換為 [開啟]。

1. 選取 [儲存]。

## <a name="set-up-device-compliance-policy"></a>設定裝置合規性政策

若要確保 iOS 裝置只能透過行動裝置管理所管理的 Workday 來登入，您必須將 **com.workday.workdayapp** 新增至受限制的應用程式清單，以封鎖 App Store 應用程式。 這樣可確保只有透過公司入口網站安裝 Workday 的裝置才能存取 Workday。 至於瀏覽器，則只有由 Intune 管理且使用受控瀏覽器的裝置才能存取 Workday。

![iOS 裝置合規性政策的螢幕擷取畫面。](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-intune-app-configuration-policies"></a>設定 Intune 應用程式組態原則

| 案例 | 索引鍵/值組 |
|----------------------------------------------------------------------------------------   |-----------|
| 自動為下列項目填入 [租用戶] 和 [網址] 欄位：<br>● Android 上的 Workday (當您啟用 Android 的工作設定檔時)。<br>● IPad 和 iPhone 上的 Workday。     | 使用這些值來設定您的租用戶： <br>● 設定機碼 = `UserGroupCode`<br>● 值類型 = 字串 <br>●   設定值 = 您的租用戶名稱。 範例： `gms`<br>使用這些值來設定您的網址：<br>● 設定機碼 = `AppServiceHost`<br>●   值類型 = 字串<br>●    設定值 = 租用戶的基底 URL。 範例： `https://www.myworkday.com`                                |   |
| 在 iPad 和 iPhone 上停用 Workday 的這些動作：<br>●    剪下、複製和貼上<br>●   列印                       | 將這些索引鍵上的值 (布林值) 設定為 `False` 以停用功能：<br>●   `AllowCutCopyPaste`<br>●    `AllowPrint`    |
| 停用 Android 上 Workday 的螢幕擷取畫面。 |將 `AllowScreenshots` 索引鍵上的值 (布林值) 設定為 `False` 以停用功能。|
| 為您的使用者停用建議的更新。|將 `AllowSuggestedUpdates` 索引鍵上的值 (布林值) 設定為 `False` 以停用功能。|
|自訂 App Store URL，以將行動使用者導向至您選擇的 App Store。|使用這些值來變更 App Store URL：<br>● 設定機碼 = `AppUpdateURL`<br>● 值類型 = 字串<br> ●   設定值 = App Store URL|
|       |


## <a name="ios-configuration-policies"></a>iOS 設定原則

1. 請移至 [Azure 入口網站](https://portal.azure.com/)並登入。
1. 搜尋 **Intune** 或選取清單中的小工具。
1. 前往 [用戶端應用程式]  >  [應用程式]  >  [應用程式組態原則]。 然後選取 [+ 新增]  >  [受控裝置]。
1. 輸入名稱。
1. 在 [平台] 下，選擇 [iOS/iPadOS]。
1. 在 [相關聯的應用程式] 下，選擇您新增的 Workday iOS 版應用程式。
1. 選取 [組態設定]。 在 [組態設定] 底下，選取 [輸入 XML 資料]。
1. 以下是範例 XML 檔案。 新增您想要套用的組態。 以您要使用的字串取代 `STRING_VALUE`。 以 `<true />` 或 `<false />` 取代 `<true /> or <false />`。 如果您未新增組態，此範例的類似功能就會設為 `True`。

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
1. 選取 [新增]  。
1. 重新整理頁面，然後選取新建立的原則。
1. 選取 [指派]，然後選擇您想要作為應用程式套用對象的使用者。
1. 選取 [儲存]。

## <a name="android-configuration-policies"></a>Android 設定原則

1. 請移至 [Azure 入口網站](https://portal.azure.com/)並登入。
2. 搜尋 **Intune** 或選取清單中的小工具。
3. 前往 [用戶端應用程式]  >  [應用程式]  >  [應用程式組態原則]。 然後選取 [+ 新增]  >  [受控裝置]。
5. 輸入名稱。 
6. 在 [平台] 下，選擇 [Android]。
7. 在 [相關聯的應用程式] 下，選擇您新增的 Workday Android 版應用程式。
8. 選取 [組態設定]。 在 [組態設定] 底下，選取 [輸入 JSON 資料]。

