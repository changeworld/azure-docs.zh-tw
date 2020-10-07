---
title: 在 Azure AD B2C 中設定 Identity Protection 和條件式存取
description: 了解如何為 Azure AD B2C 租用戶設定 Identity Protection 和條件式存取，以檢視風險性登入和其他風險性事件，並根據風險偵測來建立原則。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5554cfcde9aba1b0e5c9c8b60e2e6a7e9a8ba378
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "89270666"
---
# <a name="set-up-identity-protection-and-conditional-access-in-azure-ad-b2c"></a>在 Azure AD B2C 中設定 Identity Protection 和條件式存取

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Identity Protection 可為 Azure AD B2C 租用戶提供持續的風險偵測。 如果 Azure AD B2C 租用戶定價層為 Premium P2，您可以在 Azure 入口網站中檢視詳細的 Identity Protection 風險事件。 您可使用以這些風險偵測為基礎的[條件式存取](../active-directory/conditional-access/overview.md)原則，判斷動作並強制執行組織原則。

## <a name="prerequisites"></a>先決條件

- 您的 Azure AD B2C 租用戶必須[連結至 Azure AD 訂用帳戶](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription)。
- 需要有 Azure AD B2C Premium P2，才能使用登入和以使用者風險為基礎的條件式存取。 如有必要，[將 Azure AD B2C 定價層變更為 Premium P2](https://aka.ms/exid-pricing-tier)。 
- 若要在 B2C 租用戶中管理 Identity Protection 和條件式存取，您需要已獲派全域管理員角色或安全性管理員角色的帳戶。
- 若要在租用戶中使用這些功能，您必須先切換至 Azure AD B2C Premium P2 定價層。

## <a name="set-up-identity-protection"></a>設定 Identity Protection

Identity Protection 預設為開啟。 若要能夠在 Azure AD B2C 租用戶中檢視 Identity Protection 風險事件，只需將 Azure AD B2C 租用戶連結至 Azure AD 訂用帳戶，然後選取 Azure AD B2C Premium P2 定價層。 您可以在 Azure 入口網站中檢視詳細的風險事件報告。

### <a name="supported-identity-protection-risk-detections"></a>支援的 Identity Protection 風險偵測

Azure AD B2C 目前支援下列風險偵測：  

|風險偵測類型  |描述  |
|---------|---------|
| 非慣用登入位置     | 以使用者最近的登入為準，從非慣用的位置登入。        |
|匿名 IP 位址     | 從匿名 IP 位址登入 (例如：Tor 瀏覽器、Anonymizer VPN)        |
|不熟悉的登入屬性     | 以給定使用者近期未出現過的屬性登入。        |
|已連結惡意程式碼的 IP 位址     | 從已連結惡意程式碼的 IP 位址登入         |
|Azure AD 威脅情報     | Microsoft 的內部和外部威脅情報來源已識別出已知的攻擊模式        |

## <a name="view-risk-events-for-your-azure-ad-b2c-tenant"></a>檢視 Azure AD B2C 租用戶的風險事件

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 在入口網站工具列中選取**目錄 + 訂用帳戶**圖示，然後選取包含 Azure AD B2C 租用戶的目錄。

1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。

1. 在 [安全性] 底下，選取 [風險性使用者 (預覽)]。

   ![具風險使用者](media/conditional-access-identity-protection-setup/risky-users.png)

1. 在 [安全性] 底下，選取 [風險偵測 (預覽)]。

   ![風險偵測](media/conditional-access-identity-protection-setup/risk-detections.png)

## <a name="add-a-conditional-access-policy"></a>新增條件式存取原則 

若要根據 Identity Protection 風險偵測來新增條件式存取原則，請確定 Azure AD B2C 租用戶已停用安全性預設值，然後建立條件式存取原則。

### <a name="to-disable-security-defaults"></a>啟用安全性預設值

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 在入口網站工具列中選取**目錄 + 訂用帳戶**圖示，然後選取包含 Azure AD B2C 租用戶的目錄。

3. 在 Azure 入口網站中，搜尋並選取 [Azure Active Directory]。

4. 選取 [屬性]，然後選取 [管理安全性預設值]。

   ![停用安全性預設值](media/conditional-access-identity-protection-setup/disable-security-defaults.png)

5. 在 [啟用安全性預設值] 下，選取 [否]。 

   ![將 [啟用安全性預設值] 切換設為 [否]](media/conditional-access-identity-protection-setup/enable-security-defaults-toggle.png)

### <a name="to-create-a-conditional-access-policy"></a>建立條件式存取原則

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 在入口網站工具列中選取**目錄 + 訂用帳戶**圖示，然後選取包含 Azure AD B2C 租用戶的目錄。

1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。

1. 在 [安全性] 底下，選取 [條件式存取 (預覽)]。 [條件式存取原則] 頁面隨即開啟。 

1. 選取 [新增原則] 並遵循 Azure AD 條件式存取文件，建立新的原則。 以下是一個範例：

   - [登入風險型條件式存取：啟用條件式存取原則](../active-directory/conditional-access/howto-conditional-access-policy-risk.md#enable-with-conditional-access-policy)

   > [!IMPORTANT]
   > 選取您想要套用原則的使用者時，請勿僅選取 [所有使用者]，否則您可能封鎖自己而無法登入。

## <a name="test-the-conditional-access-policy"></a>測試條件式存取原則

1. 使用下列設定來建立條件式存取原則，如上所述：
   
   - 在 [使用者和群組] 底下，選取測試使用者。 請勿選取 [所有使用者]，否則您會封鎖自己而無法登入。
   - 針對 [雲端應用程式或動作]，選擇 [選取應用程式]，然後選擇您的信賴憑證者應用程式。
   - 針對 [條件]，選取 [登入風險] 及 [高]、[中] 和 [低] 風險層級。
   - 針對 [授與]，選擇 [封鎖存取]。

      ![選擇封鎖存取](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

1. 選取 [建立]，以啟用您的測試條件式存取原則。

1. 使用 [Tor 瀏覽器](https://www.torproject.org/download/)來模擬風險性登入。 

1. 在所嘗試登入的 jwt.ms 解碼權杖中，您應會看到已封鎖登入：

   ![測試封鎖的登入](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>檢閱稽核報告中的條件式存取結果

若要檢閱條件式存取事件的結果：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 在入口網站工具列中選取**目錄 + 訂用帳戶**圖示，然後選取包含 Azure AD B2C 租用戶的目錄。

3. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。

4. 在 [活動] 之下，選取 [稽核記錄]。

5. 將 [類別] 設定為 [B2C]，並將 [活動資源類型] 設定為 [IdentityProtection] 來篩選稽核記錄。 然後選取 [套用]。

6. 檢閱過去 7 天的稽核活動。 包含下列活動類型：

   - **評估條件式存取原則**：此稽核記錄項目表示在驗證期間執行了條件式存取評估。
   - **補救使用者**：此項目表示使用者符合條件式存取原則的授與或需求，而此活動已向風險引擎回報，以降低 (緩和) 使用者的風險。

7. 在清單中選取 [評估條件式存取原則] 記錄項目，以開啟 [活動詳細資料：稽核記錄] 頁面，其中顯示稽核記錄識別碼，以及 [其他詳細資料] 區段中的這項資訊：

   - ConditionalAccessResult：條件式原則評估所需的授與。
   - AppliedPolicies：所有符合條件並已開啟原則的條件式存取原則清單。
   - ReportingPolicies：設定為報告專用模式且符合條件的條件式存取原則清單。

## <a name="next-steps"></a>後續步驟

[將條件式存取新增至使用者流程](conditional-access-user-flow.md)。
