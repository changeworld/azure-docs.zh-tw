---
title: 搭配 Azure Active Directory 條件式存取使用風險偵測
description: 在本快速入門中，您會了解如何設定 Azure Active Directory (Azure AD) 條件式存取原則，根據工作階段風險封鎖登入。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: be447b001b0b2e14999aac98ba2125f8cbfe9853
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186602"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>快速入門：當 Azure Active Directory 條件式存取偵測到工作階段風險時封鎖存取  

為了維護您環境的安全，您可以封鎖可疑使用者，不讓他們登入。 [Azure Active Directory (Azure AD) Identity Protection](../active-directory-identityprotection.md)會分析每一次登入，並且計算非由使用者帳戶合法擁有者嘗試執行登入的可能性。 可能性 (低、中、高) 是以稱為[登入風險等級](concept-conditional-access-conditions.md#sign-in-risk)的計算值形式表示。 藉由設定登入風險條件，您可以設定條件式存取原則來回應特定的登入風險等級。

本快速入門示範如何設定[條件式存取原則](../active-directory-conditional-access-azure-portal.md)，該原則會在偵測到已設定的登入風險等級時封鎖登入。

![建立原則](./media/app-sign-in-risk/1000.png)

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程中的案例，您需要：

- **Azure AD Premium P2 版本的存取權** - 雖然條件式存取是 Azure AD Premium P1 功能，但是因為本快速入門中的案例需要 Identity Protection，所以您需要 P2 版本。
- **Identity Protection** - 本快速入門中的案例需要啟用 Identity Protection。 如果您不知道如何啟用 Identity Protection，請參閱[啟用 Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md)。
- **Tor 瀏覽器** - [Tor 瀏覽器](https://www.torproject.org/projects/torbrowser.html.en)的設計目的是協助您保留您的線上隱私權。 Identity Protection 偵測到來自 Tor 瀏覽器的登入是從匿名 IP 位址登入，這種登入有中等風險等級。 如需詳細資訊，請參閱 [Azure Active Directory 風險偵測](../reports-monitoring/concept-risk-events.md)。  
- **稱為 Alain Charon 的測試帳戶** - 如果您不知道如何建立測試帳戶，請參閱[新增雲端式使用者](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)。

## <a name="test-your-sign-in"></a>測試您的登入

此步驟的目標是確保您的測試帳戶可以使用 Tor 瀏覽器存取租用戶。

**若要測試您的登入：**

1. 請以 **Alain Charon** 身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 登出。

## <a name="create-your-conditional-access-policy"></a>建立條件式存取原則

本快速入門中的案例會使用來自 Tor 瀏覽器的登入，以產生偵測到**從匿名 IP 位址登入**的風險偵測。 此風險偵測的風險等級是「中」。 若要回應此風險偵測，您可以將登入風險條件設定為「中」。 在生產環境中，您應該將登入風險條件設定為 「高」或「中高」。

本節說明如何建立必要的條件式存取原則。 為您的原則進行下列設定：

| 設定 | 值 |
| --- | --- |
| 使用者和群組 | Alain Charon  |
| 雲端應用程式 | 所有雲端應用程式 |
| 登入風險 | 中 |
| 授與 | 封鎖存取 |

![建立原則](./media/app-sign-in-risk/130.png)

**若要設定條件式存取原則：**

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分，登入 [Azure 入口網站](https://portal.azure.com)。
1. 在 Azure 入口網站的左側導覽列上，按一下 [Azure Active Directory]  。

   ![Azure Active Directory](./media/app-sign-in-risk/02.png)

1. 在 [Azure Active Directory]  頁面的 [安全性]  區段中，按一下 [條件式存取]  。

   ![條件式存取](./media/app-sign-in-risk/03.png)

1. 在 [條件式存取]  頁面頂端的工具列中，按一下 [新增]  。

   ![名稱](./media/app-sign-in-risk/108.png)

1. 在 [新增]  頁面上的 [名稱]  文字方塊中，鍵入**封鎖中等風險等級的存取**。

   ![名稱](./media/app-sign-in-risk/104.png)

1. 在 [指派]  區段中，按一下 [使用者和群組]  。

   ![使用者和群組](./media/app-sign-in-risk/06.png)

1. 在 [使用者和群組]  頁面上：

   ![條件式存取](./media/app-sign-in-risk/107.png)

   1. 按一下 [選取使用者和群組]  ，然後選取 [群組設定]  。
   1. 按一下 [選取]。 
   1. 在 [選取]  頁面上，選取 **Alain Charon**，然後按一下 [選取]  。
   1. 在 [使用者和群組]  頁面上，按一下 [完成]  。
1. 按一下 [雲端應用程式]  。

   ![雲端應用程式](./media/app-sign-in-risk/08.png)

1. 在 [雲端應用程式]  頁面上：

   ![條件式存取](./media/app-sign-in-risk/109.png)

   1. 按一下 [所有雲端應用程式]  。
   1. 按一下 [完成]  。
1. 按一下 [條件]  。

   ![存取控制](./media/app-sign-in-risk/19.png)

1. 在 [條件]  頁面上：

   ![登入風險層級](./media/app-sign-in-risk/21.png)

   1. 按一下 [登入風險]  。
   1. 針對 [設定]  ，按一下 [是]  。
   1. 針對登入風險等級，選取 [中]  。
   1. 按一下 [選取]。 
   1. 在 [條件]  頁面上，按一下 [完成]  。
1. 在 [存取控制]  區段中，按一下 [授與]  。

   ![存取控制](./media/app-sign-in-risk/10.png)

1. 在 [授與]  頁面上：

   ![條件式存取](./media/app-sign-in-risk/105.png)

   1. 選取 [封鎖存取]  。
   1. 按一下 [選取]。 
1. 在 [啟用原則]  區段中，按一下 [開啟]  。

   ![啟用原則](./media/app-sign-in-risk/18.png)

1. 按一下頁面底部的 [新增]  。

## <a name="evaluate-a-simulated-sign-in"></a>評估模擬的登入狀況

現在您已設定條件式存取原則，建議您查看它是否如預期般運作。 第一個步驟是使用條件式存取 **What If 原則工具**，模擬您測試使用者的登入情況。 該模擬可評估此登入對原則所造成的影響，並產生模擬報告。  

當您針對此案例執行 **What If 原則工具**時，**封鎖中等風險等級的存取**應該會列在 [要套用的原則]  底下。

![User](./media/app-sign-in-risk/117.png)

**若要評估條件式存取原則：**

1. 在[條件式存取 - 原則](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)頁面頂端的功能表中，按一下 [What If]  。  

   ![What If](./media/app-sign-in-risk/14.png)

1. 按一下 [使用者]  ，在 [使用者]  頁面上選取 **Alan Charon**，然後按一下 [選取]  。

   ![User](./media/app-sign-in-risk/116.png)

1. 針對 [登入風險]  選取 [中]  。

   ![User](./media/app-sign-in-risk/119.png)

1. 按一下 [What If]  。

## <a name="test-your-conditional-access-policy"></a>測試條件式存取原則

在上一節中，您已經學會如何評估模擬的登入狀況。 除了模擬，您也應該測試條件式存取原則，以確保如預期般運作。

若要測試您的原則，請嘗試使用 Tor 瀏覽器以 **Alan Charon** 身分登入 [Azure 入口網站](https://portal.azure.com)。 您的登入嘗試應該會遭到條件式存取原則封鎖。

![Multi-Factor Authentication](./media/app-sign-in-risk/118.png)

## <a name="clean-up-resources"></a>清除資源

當您不再需要測試使用者、Tor 瀏覽器與條件式存取原則時，即可予以刪除：

- 如果您不知道如何刪除 Azure AD 使用者，請參閱[從 Azure AD 刪除使用者](../fundamentals/add-users-azure-active-directory.md#delete-a-user)。
- 若要刪除原則，請選取您的原則，然後按一下快速存取工具列的 [刪除]  。

   ![Multi-Factor Authentication](./media/app-sign-in-risk/33.png)

- 如需移除 Tor 瀏覽器的指示，請參閱[解除安裝](https://tb-manual.torproject.org/uninstalling/)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [需要接受使用條款](require-tou.md)
> [需要特定應用程式的 MFA](app-based-mfa.md)
