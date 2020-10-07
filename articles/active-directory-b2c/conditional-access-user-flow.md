---
title: 將條件式存取新增至 Azure AD B2C 中的使用者流程
description: 了解如何將條件式存取新增至 Azure AD B2C 使用者流程。 在使用者流程中設定多重要素驗證 (MFA) 設定和條件式存取原則，以強制執行原則並補救風險性登入。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60bfac3b80e772e7b359b1e926d5fb84e447a8fb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "89270678"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>將條件式存取新增至 Azure Active Directory B2C 中的使用者流程

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

條件式存取可新增至 Azure Active Directory B2C 使用者流程，以管理應用程式的風險性登入。 Azure AD B2C 中的 Identity Protection 和條件式存取整合，可讓您設定原則來識別風險性登入行為，並強制執行需要使用者或系統管理員採取進一步動作的原則。 這些元件可讓您在 Azure AD B2C 使用者流程中啟用條件式存取：

- **使用者流程**。 建立使用者流程，以引導使用者完成登入和註冊程序。 在使用者流程設定中，指出是否要在使用者遵循使用者流程時啟用條件式存取原則。
- **將使用者導向至使用者流程的應用程式**。 設定應用程式，藉由在應用程式中指定使用者流程端點，將使用者導向至適當的註冊和登入使用者流程。
- **條件式存取原則**。 [建立條件式存取原則](conditional-access-identity-protection-setup.md)並指定您要套用原則的應用程式。 當使用者遵循您應用程式的登入或註冊使用者流程時，條件式存取原則會使用 Identity Protection 訊號來識別風險性登入，並在必要時提供適當的補救動作。

最新版的使用者流程支援條件式存取。 您可以在建立新的使用者流程時，將條件式存取原則新增至該流程，或者只要版本支援條件式存取，就可以將其新增至現有的使用者流程。 將條件式存取新增至現有的使用者流程時，您需要檢閱兩項設定：

- **Multi-Factor Authentication (MFA)** ：使用者現在可以透過簡訊或語音使用一次性驗證碼，或透過電子郵件使用一次性密碼，以進行多重要素驗證。 MFA 設定與條件式存取設定無關。 您可將 MFA 設定為 [一律開啟]，因此不論您的條件式存取設定為何，一律都需要 MFA。 或者，您可以將 MFA 設定為 [條件式]，只有當作用中的條件式存取原則有需要時，才需要 MFA。

- **條件式存取**：此設定應該一律為 [開啟]。 通常您只會在疑難排解或移轉期間，或針對舊版實作，[關閉] 此設定。

深入了解 Azure AD B2C 中的 [Identity Protection 和條件式存取](conditional-access-identity-protection-overview.md)，或參閱[其設定方式](conditional-access-identity-protection-setup.md)。

## <a name="add-conditional-access-to-a-new-user-flow"></a>將條件式存取新增至新的使用者流程

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在入口網站工具列中選取**目錄 + 訂用帳戶**圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
1. 在 [原則] 底下，選取 [使用者流程]，然後選取 [新使用者流程]。
1. 在 [建立使用者流程] 頁面上，選取使用者流程類型。
1. 在 [選取版本] 底下，選取 [建議]，然後選取 [建立]。 ([深入了解](user-flow-versions.md)使用者流程版本。)

    ![Azure 入口網站中的 [建立使用者流程] 頁面，已醒目提示屬性](./media/tutorial-create-user-flows/select-version.png)

1. 輸入使用者流程的 [名稱]。 例如，*signupsignin1*。
1. 在 [識別提供者] 區段中，選取您想允許此使用者流程的識別提供者。
2. 在 [多重要素驗證] 區段中，選取所需的 [MFA 方法]，然後在 [MFA 強制] 之下選取 [條件式 (建議)]。
 
   ![設定多重要素驗證](media/conditional-access-user-flow/configure-mfa.png)

1. 在 [條件式存取] 區段中，選取 [強制執行條件式存取原則] 核取方塊。

   ![設定條件式存取設定](media/conditional-access-user-flow/configure-conditional-access.png)

1. 在 [使用者屬性與宣告] 區段中，選擇在註冊期間您要收集和從使用者傳送的宣告和屬性。 例如，選取 [顯示更多]，然後選擇 [國家/地區]和 [顯示名稱] 的屬性和宣告。 選取 [確定]  。

    ![已選取三個宣告的 [屬性和宣告選取] 頁面](./media/conditional-access-user-flow/configure-user-attributes-claims.png)

1. 按一下 [建立]  新增使用者流程。 名稱前面會自動加上前置詞 *B2C_1*。

## <a name="add-conditional-access-to-an-existing-user-flow"></a>將條件式存取新增至現有使用者流程

> [!NOTE]
> 現有的使用者流程必須是支援條件式存取的版本。 這些使用者流程版本會標示 [建議]。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在入口網站工具列中選取**目錄 + 訂用帳戶**圖示，然後選取包含 Azure AD B2C 租用戶的目錄。

1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。

1. 在 [原則] 底下，選取 [使用者流程]。 然後選取使用者流程。

1. 選取 [屬性] 並確定使用者流程支援條件式存取，方法是選取 [屬性] 並尋找標示 [條件式存取] 的設定。
 
   ![在屬性中設定 MFA 和條件式存取](media/conditional-access-user-flow/add-conditional-access.png)

1. 在 [多重要素驗證] 區段中，選取所需的 [MFA 方法]，然後在 [MFA 強制] 之下選取 [條件式 (建議)]。
 
1. 在 [條件式存取] 區段中，選取 [強制執行條件式存取原則] 核取方塊。

1. 選取 [儲存]。

## <a name="test-the-user-flow"></a>測試使用者流程

若要測試使用者流程中的條件式存取，請[建立條件式存取原則](conditional-access-identity-protection-setup.md)並在使用者流程中啟用條件式存取 (如上所述)。 

### <a name="prerequisites"></a>先決條件

- 需要有 Azure AD B2C Premium 2，才能建立風險性登入原則。 Premium P1 租用戶可以建立位置、應用程式或以群組為基礎的原則。
- 基於測試目的，您可以[註冊測試 Web 應用程式](tutorial-register-applications.md)`https://jwt.ms`，這是 Microsoft 擁有的 Web 應用程式，可顯示已解碼的權杖內容 (權杖內容永遠不會離開您的瀏覽器)。 
- 若要模擬有風險性登入，請下載 TOR 瀏覽器並嘗試登入使用者流程端點。
- 使用下列設定，[建立條件式存取原則](conditional-access-identity-protection-setup.md)：
   
   - 針對 [使用者和群組]，選取測試使用者 (請勿選取 [所有使用者]，否則您可能封鎖自己而無法登入)。
   - 針對 [雲端應用程式或動作]，選擇 [選取應用程式]，然後選擇您的信賴憑證者應用程式。
   - 針對 [條件]，選取 [登入風險] 及 [高]、[中] 和 [低] 風險層級。
   - 針對 [授與]，選擇 [封鎖存取]。

      ![風險偵測](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

### <a name="run-the-user-flow"></a>執行使用者流程

1. 選取您建立的使用者流程來開啟其 [概觀] 頁面，然後選取 [執行使用者流程]。 在 [應用程式] 之下，選取 [webapp1]。 **Reply URL** 應顯示 `https://jwt.ms`。

   ![入口網站中的 [執行使用者流程] 頁面，已醒目提示 [執行使用者流程] 按鈕](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. 複製 [執行使用者流程端點] 之下的 URL。

1. 若要模擬風險性登入，請開啟 [Tor 瀏覽器](https://www.torproject.org/download/) 並使用您在預覽步驟中複製的 URL 來登入已註冊的應用程式。

1. 在登入頁面中輸入要求的資訊，然後嘗試登入。 權杖會傳回到 `https://jwt.ms`，而且應該會向您顯示。 在 jwt.ms 解碼權杖中，您應會看到已封鎖登入：

   ![測試封鎖的登入](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="next-steps"></a>後續步驟

[在 Azure AD B2C 使用者流程中自訂使用者介面](customize-ui-overview.md)
