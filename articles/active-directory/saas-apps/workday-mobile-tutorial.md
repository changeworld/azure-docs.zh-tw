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
ms.openlocfilehash: 256da169761da486d8ac064a2f58a59be43bb5df
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754158"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Workday 行動應用程式整合

在本教學課程中，您將了解如何整合 Azure Active Directory (Azure AD)、條件式存取和 Intune 與 Workday 行動應用程式。 當您將 Workday 的行動應用程式與 Microsoft 整合時，您可以：

* 在登入之前就確定裝置符合您的原則。
* 在 Workday 的應用程式中新增控制項，以確保使用者能安全地存取公司資料。 
* 在 Azure AD 中控制可存取 Workday 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Workday。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* 整合 Workday 與 Azure AD
* 教學課程：[Azure Active Directory 單一登入 (SSO) 與 Workday 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會使用 Workday 的行動應用程式來設定及測試 Microsoft 的條件式存取原則和 Intune。

* Workday 同盟應用程式現在可以搭配 Azure AD 設定來啟用 SSO。 如需如何設定的詳細資訊，請遵循[此](workday-tutorial.md)連結。

> [!NOTE] 
> Workday 不支援 Intune 應用程式防護原則。 您必須使用行動裝置管理來利用條件式存取功能。


## <a name="ensure-users-have-access-to-the-workday-mobile-app"></a>確保使用者可以存取 Workday 行動應用程式：

請設定 Workday 以允許存取其行動應用程式供應項目。 您將需要為行動裝置設定下列原則：

您可以遵循下列指示來進行設定：

1. 存取功能區域報告的網域安全性原則。
2. 選取安全性原則。
    * 行動使用量 - Android
    * 行動使用量 - iPad
    * 行動使用量 - iPhone
3. 按一下 [編輯權限]。
4. 選取 [檢視] 或 [修改] 核取方塊，向安全性群組授與報告或工作安全性實體項目的存取權。
5. 選取 [取得] 或 [放置] 核取方塊，向安全性群組授與整合的存取權以及報告或工作安全性實體動作的存取權。

藉由執行 **啟用擱置安全性原則變更** 工作來啟用擱置安全性原則變更。

## <a name="open-workday-login-page-in-mobile-browser"></a>在行動瀏覽器中開啟 Workday 登入頁面：

若要將條件式存取套用至 Workday 的行動應用程式，應用程式必須在外部瀏覽器中開啟。 若要這麼做，請核取 [編輯租用戶設定 - 安全性] 中的 [啟用原生應用程式的行動瀏覽器 SSO] 方塊。 這需要您已在 iOS 的裝置上和 Android 的工作設定檔中安裝 Intune 核准的瀏覽器

![行動瀏覽器登入](./media/workday-tutorial/mobile-browser.png)

## <a name="setup-conditional-access-policy"></a>設定條件式存取原則：

此原則只會影響 iOS 或 Android 裝置上的登入。 如果您想要將影響範圍延伸至所有平台，則只要選取 [任何裝置] 即可。 此原則會要求裝置必須符合原則，而且會透過 Microsoft Intune 來確認這一點。 由於 Android 具有工作設定檔，除非使用者透過其工作設定檔登入，並已透過 Intune 公司入口網站安裝應用程式，否則此原則應該會封鎖任何使用者登入 Workday (Web 或應用程式)。 iOS 則還要進行一個步驟才能確保會套用相同的情況。 以下是條件式存取設定的一些螢幕擷取畫面。

**Workday 支援下列存取控制：**
* 需要多重要素驗證
* 裝置需要標記為符合規範

**Workday 應用程式不支援下列各項：**
* 需要已核准的用戶端應用程式
* 需要應用程式保護原則 (預覽)

若要將 **Workday** 設定為 **受控裝置** ，請執行下列步驟：

![設定條件式存取原則](./media/workday-tutorial/managed-devices-only.png)

1. 按一下 [首頁] > [Microsoft Intune] > [條件式存取原則] > [僅限受控裝置] 

1. 在 [僅限受控裝置] 頁面上，將 [名稱] 欄位值指定為 `Managed Devices Only`，然後按一下 [雲端應用程式或動作]。

1. 在 [雲端應用程式或動作] 中執行下列步驟。

    a. 將 [選取此原則的套用對象] 切換為 [雲端應用程式]。

    b. 在 [包含] 中，按一下 [選取應用程式]。

    c. 從選取清單中選擇 [Workday]。

    d. 按一下 [完成]  。

1. 開啟 [啟用原則]。

1. 按一下 [檔案]  。

針對 [授與] 存取權，請執行下列步驟：

![Workday 設定條件式存取原則](./media/workday-tutorial/managed-devices-only-2.png)

1. 按一下 [首頁] > [Microsoft Intune] > [條件式存取原則] > [僅限受控裝置] 

1. 在 [僅限受控裝置] 頁面上，將 [名稱] 欄位值指定為 `Managed Devices Only`，然後按一下 [存取原則] > [授與]。

1. 在 [授與] 頁面中執行下列步驟。

    a. 選取要強制執行的控制項作為 [授與存取權]。

    b. 核取 [裝置需要標記為符合規範]。

    c. 選取 [要求採用其中一個選取的控制項]。

    d. 按一下 [選取]。

1. 開啟 [啟用原則]。

1. 按一下 [儲存]

## <a name="set-up-device-compliance-policy"></a>設定裝置合規性政策：

若要確保 iOS 裝置只能透過 MDM 管理的 Workday 應用程式來登入，您必須將 **com.workday.workdayapp** 新增至受限制的應用程式清單，以封鎖 App Store 應用程式。 這可確保只有透過公司入口網站安裝 Workday 應用程式的裝置才能存取 Workday。 至於在瀏覽器上，則只有由 Intune 管理且使用受控瀏覽器的裝置才能存取 Workday。

![Workday 設定裝置合規性政策](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-microsoft-intune-app-configuration-policies"></a>設定 Microsoft Intune 應用程式組態原則：

| 案例 | 索引鍵/值組 |
|----------------------------------------------------------------------------------------   |-----------|
| 自動為下列項目填入 [租用戶] 和 [網址] 欄位：<br>● Android 上的 Workday (當您啟用 Android 的工作設定檔時)。<br>● IPad 和 iPhone 上的 Workday。     | 使用這些值來設定您的租用戶： <br>● 設定索引鍵 = UserGroupCode<br>●   值類型 = 字串 <br>●   設定值 = 您的租用戶名稱。 範例：gms<br>使用這些值來設定您的網址：<br>●    設定索引鍵 = AppServiceHost<br>● 值類型 = 字串<br>●    設定值 = 租用戶的基底 URL。 範例： https://www.myworkday.com                              |   |
| 在 iPad 和 iPhone 上停用 Workday 的這些動作：<br>●    剪下、複製和貼上<br>●   列印                       | 將這些索引鍵上的值 (布林值) 設定為 False 以停用功能：<br>● AllowCutCopyPaste<br>●  AllowPrint  |
| 停用 Android 上 Workday 的螢幕擷取畫面。 |將 AllowScreenshots 索引鍵上的值 (布林值) 設定為 False 以停用功能。|
| 為您的使用者停用建議的更新。|將 AllowSuggestedUpdates 索引鍵上的值 (布林值) 設定為 False 以停用功能。|
|自訂 App Store URL，以將行動使用者導向至您選擇的 App Store。|使用這些值來變更 App Store URL：<br>● 設定索引鍵 = AppUpdateURL<br>●   值類型 = 字串<br> ●   設定值 = App Store URL|
|       |


## <a name="ios-configuration-policies"></a>iOS 設定原則：

1. 移至 https://portal.azure.com/ 並登入
2. 搜尋 **Intune** 或按一下清單中的小工具。
3. 移至 [用戶端應用程式] -> [應用程式]-> [應用程式組態原則] -> [+ 新增] > [受控裝置]
4. 輸入名稱。
5. 在 [平台] 下，選擇 [iOS/iPadOS]
6. 在 [相關聯的應用程式] 下，選擇您新增的 Workday iOS 版應用程式
7. 按一下 [組態設定]，然後在 [組態設定格式] 下，選擇 [輸入 XML 資料]
8. 以下是範例 XML 檔案。 新增您想要套用的設定。 以您想要使用的字串取代 **STRING_VALUE** 。 以 `<true />` 或 `<false />` 取代 `<true />` 或 `<false />`。 如果您未新增設定，其運作方式會和設為 True 時一樣。

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
9. 按一下 [新增]
10. 重新整理頁面，然後按一下新建立的原則。
11. 按一下 [指派]，然後選擇您想要作為應用程式套用對象的使用者。
12. 按一下 [儲存]。

## <a name="android-configuration-policies"></a>Android 設定原則：

1. 移至 `https://portal.azure.com/` 並登入。
2. 搜尋 **Intune** 或按一下清單中的小工具。
3. 移至 [用戶端應用程式] -> [應用程式]-> [應用程式組態原則] -> [+ 新增] > [受控裝置]
5. 輸入名稱。 
6. 在 [平台] 下，選擇 [Android]
7. 在 [相關聯的應用程式] 下，選擇您新增的 Workday Android 版應用程式
8. 按一下 [組態設定]，然後在 [組態設定格式] 下，選擇 [輸入 JSON 資料]

