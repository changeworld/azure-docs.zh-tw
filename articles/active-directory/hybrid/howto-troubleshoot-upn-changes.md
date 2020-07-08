---
title: 針對 Azure 使用者主體名稱（UPN）變更進行規劃和疑難排解
description: 瞭解 UPN 變更的已知問題和緩和措施
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 885d30305ba2b186052e17b9b455b2248bca541b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608512"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>針對 Azure Active Directory 中的使用者主體名稱變更進行規劃和疑難排解

使用者主要名稱（UPN）是使用者帳戶的網際網路通訊標準屬性。 UPN 是由 UPN 前置詞 (使用者帳戶名稱) 和 UPN 尾碼 (DNS 網域名稱) 所組成。 前置詞會使用 "@" 符號來聯結尾碼。 例如： someone@example.com 。 在目錄樹系的所有安全性主體物件之間，UPN 必須是唯一的。 

**本文假設您是使用 UPN 做為使用者識別碼。它會解決 UPN 變更的規劃，以及從 UPN 變更可能產生的問題中復原。**

> [!NOTE]
> 對於開發人員，建議您使用使用者 objectID 做為不可變的識別碼，而不是 UPN 或電子郵件地址，因為它們的值可能會變更。


## <a name="learn-about-upns-and-upn-changes"></a>瞭解 Upn 和 UPN 的變更
登入頁面通常會在必要值是其 UPN 時，提示使用者輸入其電子郵件地址。 因此，每當使用者的主要電子郵件地址變更時，您都應該務必變更他們的 UPN。

使用者的主要電子郵件地址可能會因為許多原因而變更：

* 公司品牌再造

* 員工移至不同的公司部門 

* 合併與收購

* 員工名稱變更

### <a name="types-of-upn-changes"></a>UPN 變更的類型

您可以藉由變更前置詞、尾碼或兩者來變更 UPN。

* **變更前置**詞。

   *  例如，如果某人的名稱已變更，您可能會變更其帳戶名稱：  
BSimon@contoso.com至BJohnson@contoso.com

   * 您也可以變更首碼的公司標準：  
Bsimon@contoso.com至Britta.Simon@contoso.com

* **變更尾碼**。 <br>

    例如，如果某人變更了部門，您可能會變更其網域： 

   * 按 Britta.Simon@contoso.com 移至 Britta.Simon@contosolabs.com <br>
     Or<br>
    * 按 Britta.Simon@corp.contoso.com 移至 Britta.Simon@labs.contoso.com 

我們建議您在每次更新主要電子郵件地址時，變更使用者的 UPN。

在從 Active Directory 到 Azure AD 的初始同步處理期間，請確定使用者的電子郵件與其 Upn 相同。

### <a name="upns-in-active-directory"></a>Active Directory 中的 Upn

在 Active Directory 中，預設的 UPN 尾碼是您建立使用者帳戶所在網域的 DNS 名稱。 在大部分情況下，這是您在網際網路上註冊為企業網域的功能變數名稱。 如果您在 contoso.com 網域中建立使用者帳戶，預設 UPN 為

username@contoso.com

 不過，您可以使用 Active Directory 網域和信任來[新增更多 UPN 尾碼](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)。 

例如，您可能會想要新增 labs.contoso.com，並讓使用者的 Upn 和電子郵件反映出來。 接著會變成

username@labs.contoso.com.

>[!IMPORTANT]
> 如果您要[變更 Active Directory 中的尾碼](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)，必須確定已[在 Azure AD 上新增和驗證](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)相符的自訂功能變數名稱。 

![已驗證網域的螢幕擷取畫面](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>Azure Active Directory 中的 Upn

使用者使用其 userPrincipalName 屬性中的值來登入 Azure AD。 

當您搭配內部部署 Active Directory 使用 Azure AD 時，會使用 Azure AD Connect 服務來同步處理使用者帳戶。 根據預設，Azure AD Connect wizard 會使用內部部署 Active Directory 中的 userPrincipalName 屬性，做為 Azure AD 中的 UPN。 您可以在自訂安裝中將它變更為不同的屬性。

當您更新單一使用者或整個組織的使用者主要名稱（UPN）時，請務必擁有已定義的程式。 

請參閱本檔中的已知問題和因應措施。

當您要將使用者帳戶從 Active Directory 同步處理到 Azure AD 時，請確定 Active Directory 中的 Upn 對應到 Azure AD 中已驗證的網域。

![已驗證網域的螢幕擷取畫面](./media/howto-troubleshoot-upn-changes/verified-domains.png)

如果 userPrincipalName 屬性的值未對應到 Azure AD 中已驗證的網域，則同步處理常式會將尾碼取代為 onmicrosoft.com 值。


### <a name="roll-out-bulk-upn-changes"></a>推出大量 UPN 變更

請遵循大量 UPN 變更[試驗的最佳做法](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans)。 如果您發現無法快速解決的問題，也可以使用已測試的復原計畫來還原 Upn。 一旦您的試驗執行完畢，您就可以開始將小型使用者的目標設為各種組織角色及其特定的應用程式或裝置集合。

經過第一個使用者子集，可讓您瞭解使用者在變更過程中應該預期的內容。 請將此資訊包含在您的使用者通訊上。

建立定義的程式，以在一般作業中變更個別使用者的 Upn。 我們建議您採用經過測試的程式，其中包含已知問題和因應措施的相關檔。

下列各節詳細說明 Upn 變更時的潛在已知問題和因應措施。

## <a name="apps-known-issues-and-workarounds"></a>應用程式的已知問題和因應措施

[軟體即服務（SaaS）](https://azure.microsoft.com/overview/what-is-saas/)和企業營運（LoB）應用程式通常依賴 upn 來尋找使用者並儲存使用者設定檔資訊，包括角色。 當使用者第[一次登](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)入應用程式時，使用即時布建來建立使用者設定檔的應用程式，可能會受到 UPN 變更的影響。

**已知問題**<br>
變更使用者的 UPN 可能會中斷 Azure AD 使用者與在應用程式上建立的使用者設定檔之間的關聯性。 如果應用[程式使用即時](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)布建，它可能會建立一個全新的使用者設定檔。 這將需要應用程式系統管理員進行手動變更，以修正此關聯性。

**因應措施**<br>
[Azure AD 自動化的使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)布建可讓您在支援的雲端應用程式中自動建立、維護和移除使用者身分識別。 在您的應用程式上設定自動使用者布建，會在應用程式上自動更新 Upn。 在漸進式推出過程中測試應用程式，以驗證它們不會受到 UPN 變更的影響。
如果您是開發人員，請考慮[將 SCIM 支援新增至您的應用程式](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups)，以啟用 Azure Active Directory 的自動使用者布建。 

## <a name="managed-devices-known-issues-and-workarounds"></a>受控裝置的已知問題和因應措施

藉由[讓您的裝置 Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview)，您可以透過跨雲端和內部部署資源的單一登入（SSO），將使用者的生產力最大化。

### <a name="azure-ad-joined-devices"></a>Azure AD 加入裝置

[Azure AD 加入](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join)的裝置會直接聯結至 Azure AD，並允許使用者使用其組織的身分識別來登入裝置。

**已知問題** <br>
使用者可能會遇到相依于驗證 Azure AD 之應用程式的單一登入問題。

**解決方案** <br>
這一節所提及的問題已在 Windows 10 5 月2020更新（2004）修正。

**因應措施** <br>
允許足夠的時間讓 UPN 變更同步處理 Azure AD。 一旦您確認新的 UPN 反映在 Azure AD 入口網站上，請要求使用者選取 [其他使用者] 磚，以使用新的 UPN 登入。 您也可以透過[PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)進行驗證。 使用新的 UPN 登入之後，舊 UPN 的參考可能仍會出現在 [存取公司或學校] Windows 設定上。

![已驗證網域的螢幕擷取畫面](./media/howto-troubleshoot-upn-changes/other-user.png)


### <a name="hybrid-azure-ad-joined-devices"></a>混合式 Azure AD 已加入裝置

[混合式 Azure AD 加入](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join-hybrid)的裝置會聯結至 Active Directory 和 Azure AD。 如果您的環境具有內部部署 Active Directory 的使用量，而且您也想要從 Azure AD 所提供的功能中獲益，您可以執行混合式 Azure AD 聯結。

**已知問題** 

Windows 10 混合式 Azure AD 加入的裝置可能會遇到非預期的重新開機和存取問題。

如果使用者在新的 UPN 同步處理到 Azure AD 之前登入 Windows，或繼續使用現有的 Windows 會話，則如果條件式存取已設定為強制使用混合式聯結的裝置來存取資源，他們可能會遇到單一登入的應用程式問題，而使用 Azure AD 進行驗證。 

此外，會出現下列訊息，強制在一分鐘後重新開機。 

「您的電腦會在一分鐘內自動重新開機。 Windows 遇到問題，需要重新開機。 您應該立即關閉此訊息，並儲存您的工作。

**解決方案** <br>
這一節所提及的問題已在 Windows 10 5 月2020更新（2004）修正。

**因應措施** 

裝置必須從 Azure AD 退出，然後重新開機。 重新開機之後，裝置會再次自動加入 Azure AD，而使用者必須選取 [其他使用者] 磚，以使用新的 UPN 登入。 若要從 Azure AD 退出裝置，請在命令提示字元中執行下列命令：

**dsregcmd.exe/leave**

如果使用中的 Windows Hello 企業版，使用者將需要[重新註冊](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision)。 在 UPN 變更之後，Windows 7 和8.1 裝置不會受到這個問題的影響。


## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Microsoft Authenticator 的已知問題和因應措施

您的組織可能需要使用[Microsoft Authenticator 應用](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview)程式來登入及存取組織應用程式和資料。 雖然使用者名稱可能會出現在應用程式中，但帳戶不會設定為做為驗證方法，直到使用者完成註冊程式為止。

[Microsoft Authenticator 應用程式](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview)有四個主要功能：

* 經由推播通知或驗證碼的多重要素驗證

* 作為 iOS 和 Android 裝置上的驗證代理人，為使用代理[驗證](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth)的應用程式提供單一登入

* 裝置註冊（也稱為 Workplace Join）來 Azure AD，這是 Intune 應用程式防護和裝置註冊/管理等其他功能的需求。

* 需要 MFA 和裝置註冊的電話登入。

### <a name="multi-factor-authentication-with-android-devices"></a>使用 Android 裝置的多重要素驗證

Microsoft Authenticator 應用程式提供頻外驗證選項。 [多重要素驗證（MFA）](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)不會在登入期間將自動通話或 SMS 放到使用者，而是將通知推送至使用者的 smartphone 或平板電腦上的 Microsoft Authenticator 應用程式。 使用者只需在應用程式中按 [核准] （或輸入 PIN 或生物識別，然後按 [驗證]）即可完成其登入。

**已知問題** 

當您變更使用者的 UPN 時，舊的 UPN 仍會顯示在使用者帳戶上，而且可能不會收到通知。 [驗證碼](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-faq)會繼續正常執行。

**因應措施**

如果收到通知，請指示使用者關閉通知，開啟驗證器應用程式，並按一下 [檢查通知] 選項，並核准 MFA 提示。 在此之後，帳戶上顯示的 UPN 將會更新。 請注意，更新的 UPN 可能會顯示為新的帳戶，這是因為正在使用其他驗證器功能。 如需詳細資訊，請參閱本文中的其他已知問題。

### <a name="brokered-authentication"></a>代理驗證

在 Android 和 iOS 上的代理程式，例如 Microsoft Authenticator 啟用：

* 單一登入（SSO）-您的使用者不需要登入每個應用程式。

* 裝置識別-訊息代理程式會存取在工作場所加入裝置時所建立的裝置憑證。

* 應用程式識別驗證-當應用程式呼叫訊息代理程式時，它會傳遞其重新導向 URL，而 broker 會進行驗證。

此外，它可讓應用程式參與更先進的功能，例如[條件式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/)，並支援[Microsoft Intune 案例](https://docs.microsoft.com/azure/active-directory/develop/msal-net-use-brokers-with-xamarin-apps)。

**已知問題**<br>
使用者會在使用訊息代理程式登入的新應用程式上顯示更多互動式驗證提示，這是因為應用程式所傳遞的 login_hint 與訊息代理程式所儲存的 UPN 不相符。

**因應措施** <br> 使用者必須以手動方式從 Microsoft Authenticator 移除帳戶，並從訊息代理程式輔助應用程式開始新的登入。 帳戶會在初始驗證之後自動新增。

### <a name="device-registration"></a>裝置註冊

Microsoft Authenticator 應用程式會負責註冊裝置以 Azure AD。 裝置註冊可讓裝置向 Azure AD 進行驗證，而且是下列案例的需求：

* Intune 應用程式保護

* Intune 裝置註冊

* 電話登入

**已知問題**<br>
當您變更 UPN 時，具有新 UPN 的新帳戶會出現在 Microsoft Authenticator 應用程式中，而具有舊 UPN 的帳戶仍然會列出。 此外，舊的 UPN 會顯示在 [裝置註冊] 區段上的 [應用程式設定]。 裝置註冊或相依案例的一般功能不會有任何變更。

**因應措施** <br> 若要移除 Microsoft Authenticator 應用程式上舊 UPN 的所有參考，請指示使用者從 Microsoft Authenticator 手動移除舊的和新的帳戶，重新註冊 MFA，然後重新加入裝置。

### <a name="phone-sign-in"></a>手機登入

電話登入可讓使用者在沒有密碼的情況下登入 Azure AD。 若要啟用手機登入，使用者必須使用驗證器應用程式註冊 MFA，然後直接在驗證器上啟用手機登入。 在設定過程中，裝置會向 Azure AD 註冊。

**已知問題** <br>
使用者不會收到任何通知，因此無法使用手機登入。 如果使用者按下 [檢查通知]，就會收到錯誤。

**因應措施**<br>
使用者必須在啟用手機登入的帳戶上選取下拉式功能表，然後選取 [停用手機登入]。 如有需要，可以再次啟用手機登入。

## <a name="security-key-fido2-known-issues-and-workarounds"></a>安全性金鑰（FIDO2）的已知問題和因應措施

**已知問題** <br>
當有多個使用者在相同的金鑰上註冊時，[登入] 畫面會顯示 [帳戶選取] 頁面，其中會顯示舊的 UPN。 使用安全性金鑰的登入不會受到 UPN 變更的影響。  

**因應措施**<br>
若要移除對舊 Upn 的參考，使用者必須[重設安全性金鑰，然後重新註冊](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key#known-issues)。

## <a name="onedrive-known-issues-and-workarounds"></a>OneDrive 的已知問題和因應措施

OneDrive 使用者已知在 UPN 變更之後會遇到問題。 如需詳細資訊，請參閱[UPN 變更對 ONEDRIVE URL 和 onedrive 功能的影響](https://docs.microsoft.com/onedrive/upn-changes)。

## <a name="next-steps"></a>後續步驟

請參閱下列資源：
* [Azure AD Connect：設計概念](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)

* [Azure AD UserPrincipalName 填入](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname)

* [Microsoft 身分識別平臺識別碼權杖](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
