---
title: Azure AD 的無密碼 authentication
titleSuffix: Active Directory
description: 本指南可協助 Ceo、Cio、Ciso、首席身分識別架構設計人員、企業架構設計人員，以及負責為其 Azure Active Directory 執行選擇無密碼驗證方法的 IT 決策者。
keywords: 無密碼、azuread
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: 5fc6a4a23573995cf791a21ec7cf3a7d68d048e8
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064473"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>沒有密碼的世界 Azure Active Directory

那就是與密碼中斷關係的時候了。 過去的密碼是很好的，但在現今的數位工作場所，他們已成為駭客的相當容易攻擊的向量。 駭客喜歡密碼，而且當您認為 Azure Active Directory （Azure AD）中最常遭到拒絕的密碼包括年份、月份、季或當地運動團隊等詞彙時，並不容易看出。 此外，[研究已顯示](https://aka.ms/passwordguidance)傳統的密碼管理建議，例如長度需求、複雜度需求和變更頻率，會因各種與人類性質相關的原因而敵對。

通常用來危害使用者帳戶的三種攻擊類型包括密碼噴灑、網路釣魚和缺口重新執行。 Azure AD 功能（例如[智慧鎖定](../../active-directory/authentication/howto-password-smart-lockout.md)、[禁用密碼](../../active-directory/authentication/concept-password-ban-bad-on-premises.md)和[密碼保護](../../active-directory/authentication/concept-password-ban-bad-on-premises.md)）可以協助防止這類攻擊。 同樣地，執行[多重要素驗證](../../active-directory/authentication/concept-mfa-howitworks.md)（MFA）或雙步驟驗證，會藉由要求第二種形式的驗證來提供額外的安全性。 但是在長期執行時，無密碼解決方案是確保最安全驗證方法的最佳解決方案。

本文是您旅程的開端，可協助您瞭解及執行 Microsoft 的無密碼解決方案，並協助您在下列一個或多個選項之間做選擇：

* **Windows Hello 企業版**。 在 Windows 10 中，Windows Hello 企業版會以電腦和行動裝置上的強式雙因素驗證取代密碼。 這項驗證是由一種新的使用者認證類型所組成，該認證系結至裝置，並使用生物識別辨識或 PIN。

* **使用 Microsoft Authenticator 無密碼登入**。 Microsoft Authenticator 應用程式可在不使用密碼的情況下，用來登入 Azure AD 帳戶。 與 Windows Hello 企業版的技術類似，Microsoft Authenticator 使用以金鑰為基礎的驗證來啟用系結至裝置的使用者認證，並使用生物特徵辨識或 PIN。

* **FIDO2 安全性金鑰**。 FIDO2 提供在每個網站中都是唯一的密碼編譯登入認證，並儲存在本機裝置上，例如 Windows Hello 或外部安全性金鑰。 這些安全性金鑰可對抗網路釣魚、密碼竊取和重新執行攻擊的風險。 透過生物識別或 PIN 與使用者驗證結合，解決方案是符合新式安全性需求的雙因素驗證。

## <a name="the-future-of-passwordless-authentication"></a>無密碼 authentication 的未來

這些日子、銀行、信用卡公司，以及其他組織和線上服務通常會要求您驗證您的身分識別兩次來保護您的帳戶：一次使用您的密碼，然後再按電話、文字或應用程式。 雖然多重要素驗證會解決共用、遭竊或猜到之密碼的安全性問題，但並不會解決嘗試記住它們的不便因素。 現今雲端時代所想要的使用者和組織，都是無密碼的驗證方法，非常安全*且*方便。

![便利性 vs security](./media/ad-passwordless/azure-ad-pwdless-image1.png)

Windows Hello 企業版、使用 Microsoft Authenticator 的無密碼登入，以及 FIDO2 安全性金鑰，全都共用一個簡單的通用架構，可為使用者提供高度安全且方便使用的驗證方法。 這三個都是以公開/私密金鑰技術為基礎，需要本機手勢（例如生物特徵辨識或 PIN 碼）和私密金鑰系結至單一裝置，並安全地儲存且永遠不共用。

## <a name="windows-hello-for-business"></a>Windows Hello 企業版

在 Windows 10 中，Windows Hello 企業版會以電腦和裝置上的強式雙因素驗證取代密碼。 驗證封裝含一種新類型的使用者認證，系結至裝置，並使用生物識別手勢或 PIN，讓使用者能夠向 Azure AD 和內部部署 Active Directory 進行驗證。 只要使用 Windows Hello 企業版登入裝置很容易。 您可以使用 PIN 或生物識別手勢，例如指紋或臉部辨識。

### <a name="windows-hello-for-business-scenarios"></a>Windows Hello 企業版案例

Windows Hello 企業版適用于擁有自己指定 Windows 電腦的資訊工作者。 生物識別和認證會直接系結至使用者的電腦，以防止擁有者以外的任何人進行存取。 透過 PKI 整合和單一登入（SSO）的內建支援，Windows Hello 企業版提供簡單且便利的方法，讓您無縫存取內部部署和雲端中的公司資源。

### <a name="windows-hello-for-business-deployment-considerations"></a>Windows Hello 企業版部署考慮

Windows Hello 企業版是一種分散式系統，其使用數個元件來完成裝置註冊、布建和驗證。 因此，部署需要在組織內的多個小組之間進行適當的規劃。 Windows Hello 企業版[規劃指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide)可用來協助您決定要使用的 Windows Hello 企業版部署類型，以及您需要考慮的選項。

部署 Windows Hello 企業版時，有許多選項可供您選擇。 提供多個選項可確保幾乎每個組織都可以部署 Windows Hello 企業版。 請考慮下列支援的部署類型：

* [混合式 Azure AD 聯結的金鑰信任部署](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [混合式 Azure AD 加入的憑證信任部署](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Azure AD 加入單一登入部署指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [內部部署金鑰信任部署](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [內部部署憑證信任部署](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

提供許多選項會讓部署變得複雜。 不過，大部分的組織可能會判斷他們已經實作為 Windows Hello 企業版部署所依賴的大部分基礎結構。 無論如何，請務必瞭解 Windows Hello 企業版是分散式系統，建議您進行適當的規劃。

我們建議您閱讀[規劃 Windows Hello 企業版部署](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide)，協助您決定最適合您特定組織的部署模型。 然後，根據您所做的規劃，請參閱[Windows Hello 企業版部署指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)，以協助確保在您現有的環境中成功部署 Windows Hello 企業版。

### <a name="how-windows-hello-for-business-works"></a>Windows Hello 企業版的運作方式

#### <a name="user-sets-up-windows-hello-for-business"></a>使用者設定 Windows Hello 企業版

使用者在註冊期間的初始兩步驟驗證完成後，會在使用者的裝置上設定 Windows Hello，而 Windows 會要求使用者設定手勢，這可以是生物識別，例如指紋或臉部辨識，或 PIN。 一旦設定之後，使用者會提供手勢來驗證其身分識別。 Windows 接著會使用 Windows Hello 來驗證使用者。

根據您 Windows 10 裝置的功能，您將會有內建的安全記憶體保護區，也就是所謂的硬體「可信賴平臺模組」（TPM）或軟體 TPM。 TPM 會儲存私密金鑰，此金鑰需要您的臉部、指紋或 PIN 才能解除鎖定。 生物識別資料不會漫遊，而且永遠不會傳送到外部裝置或伺服器。 由於 Windows Hello 只會將生物識別識別資料儲存在裝置上，因此攻擊者可能不會洩露任何單一收集點。

> [!TIP]
> 就表面而言，PIN 看起來像是密碼，但實際上比較安全。 密碼和 PIN 之間的重要差異在於，PIN 會系結至其設定所在的特定裝置。 竊取您的密碼的人可以從任何地方登入您的帳戶。 但是，如果他們竊取您的 PIN，他們也必須竊取您的實體裝置！ 此外，由於 PIN 是裝置的本機位置，因此不會在任何地方傳輸，因此無法在傳輸時或從伺服器遭竊中攔截。

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>使用 Windows Hello 企業版進行登入的使用者

Windows Hello 企業版生物識別和 Pin 會使用非對稱（公開/私密金鑰）加密來進行驗證。 在驗證期間，加密會系結至 TLS 工作階段金鑰，以確保驗證程式的安全，讓中間人（MiTM）攻擊無法竊取產生的安全性權杖或成品，並從其他地方重新執行。

Windows Hello 企業版提供便利的登入體驗，可驗證使用者以 Azure AD 及 Active Directory 資源。 已加入 Azure AD 的裝置會在登入期間向 Azure 進行驗證，而且可以選擇性地向 Active Directory 進行驗證。 混合式 Azure Active Directory 加入的裝置會在登入期間向 Active Directory 進行驗證，並在背景中驗證 Azure Active Directory。

![查看來源映射](./media/ad-passwordless/azure-ad-pwdless-image2.jpeg)

下列步驟說明 Azure Active Directory 的登入驗證。

![Windows 10 鎖定畫面](./media/ad-passwordless/azure-ad-pwdless-image3.png)

1. 使用者使用生物特徵辨識或 PIN 手勢登入 Windows。 筆勢會解除鎖定 Windows Hello 企業版的私密金鑰，並傳送至雲端驗證安全性支援提供者，稱為雲端 AP 提供者。

2. 雲端 AP 提供者會向 Azure Active Directory 要求 nonce。

3. Azure AD 傳回的 nonce 有效期限為5分鐘。

4. 雲端 AP 提供者會使用使用者的私密金鑰來簽署 nonce，並將簽署的 nonce 傳回到 Azure Active Directory。

5. Azure Active Directory 會使用使用者的安全註冊公開金鑰，來驗證簽署的 nonce 是否符合 nonce 簽章。 驗證簽章之後，Azure AD 接著會驗證傳回的簽署 nonce。 驗證 nonce 之後，Azure AD 會建立 PRT，並將工作階段金鑰加密為裝置的傳輸金鑰，並將它傳回給雲端 AP 提供者。

6. 雲端 AP 提供者會使用工作階段金鑰接收加密的 PRT。 使用裝置的私用傳輸金鑰，雲端 AP 提供者會解密工作階段金鑰，並使用裝置的 TPM 來保護工作階段金鑰。

7. 雲端 AP 提供者會將成功的驗證回應傳回給 Windows，之後使用者就能存取 Windows 以及雲端和內部部署應用程式，而不需要再次驗證（SSO）。

如需深入瞭解 Windows Hello 企業版相關的其他案例中的驗證程式，請參閱[Windows Hello 企業版和驗證](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#azure-ad-join-authentication-to-active-directory-using-a-key)。

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>使用者管理其 Windows Hello 企業版認證

[MICROSOFT PIN 重設服務](/windows/security/identity-protection/hello-for-business/hello-feature-pin-reset)是 Azure AD 中的一項功能，可讓使用者視需要重設其 PIN。 系統管理員可以使用群組原則、Microsoft Intune 或相容的 MDM，將 Windows 10 裝置設定為安全地使用 Microsoft PIN 重設服務，讓使用者可以透過設定或在鎖定畫面上重設忘記的 PIN，而不需要重新註冊。

有時候使用者必須切換回使用密碼。 [自助式密碼重設](../../active-directory/authentication/howto-sspr-deployment.md)（SSPR）是另一項 Azure AD 功能，可讓使用者重設其密碼，而不需要洽詢 IT 人員。 使用者必須註冊或註冊自助式密碼重設，才能使用服務。 在註冊期間，使用者會選擇其組織所啟用的一或多個驗證方法。 SSPR 可讓使用者無論在何處或當天的時間，都能快速地解除封鎖並繼續工作。 藉由允許使用者解除封鎖自己，您的組織可以減少非生產力的時間，以及最常見的密碼相關問題的高支援成本。

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>使用 Microsoft Authenticator 無密碼登入

使用 Microsoft Authenticator 的無密碼登入是另一個無密碼解決方案，可以用來登入使用手機登入 Azure AD 帳戶。 您仍然必須提供您知道的專案以及您擁有的專案來驗證您的身分識別，但手機登入可讓您略過輸入您的密碼，並使用您的指紋、臉部或 PIN 在行動裝置上執行所有身分識別驗證。

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Microsoft Authenticator 無密碼案例

Microsoft Authenticator 應用程式可讓使用者驗證其身分識別，並向其公司或個人帳戶進行驗證。 它也可以用來以一次性密碼或推播通知來增加密碼，或完全取代密碼的需求。 使用者不使用密碼，而是透過指紋掃描、臉部或鳶尾花辨識或 PIN，使用行動電話來確認其身分識別。 這項工具是以類似 Windows Hello 使用的安全技術為基礎，封裝到行動裝置上的簡單應用程式中，讓使用者成為方便使用的選項。 Microsoft Authenticator 應用程式適用于 Android 和 iOS。

### <a name="microsoft-authenticator-deployment-considerations"></a>Microsoft Authenticator 部署考慮

使用 Microsoft Authenticator 應用程式執行無密碼登入 Azure AD 的必要條件包括下列各項：

* 終端使用者已啟用 Azure 多重要素驗證

* 建議使用者使用 Microsoft Intune 或協力廠商行動裝置管理（MDM）解決方案來註冊其裝置，以簡化應用程式的部署

假設符合這些需求，系統管理員就可以使用[Windows PowerShell](../../active-directory/authentication/howto-authentication-passwordless-phone.md) ，在租使用者中啟用無密碼電話登入。 在租使用者中啟用手機登入之後，終端使用者可以在應用程式的 [**帳戶**] 畫面上選取其公司或學校帳戶，然後選取 [**啟用手機登入**]，以選擇使用其電話登入。

假設系統管理員已啟用無密碼登入，則終端使用者必須符合下列需求：

* 已在 Azure 多因素驗證中註冊

* 在執行 iOS 8.0 或更新版本的裝置上安裝的最新版 Microsoft Authenticator，或 Android 6.0 或更高版本

* 已將推播通知新增至應用程式的工作或學校帳戶

若要避免您的帳戶遭到鎖定，或必須在新裝置上重新建立帳戶，建議您使用 Microsoft Authenticator 將[帳號憑證備份](../../active-directory/user-help/user-help-auth-app-backup-recovery.md)到雲端。 備份之後，您也可以使用此應用程式在新裝置上復原您的資訊，這有可能避免遭到鎖定或者必須重新建立帳戶。

由於大部分使用者習慣僅使用密碼來進行驗證，因此您的組織必須教授與此程式相關的使用者。 對於使用 Microsoft Authenticator 應用程式登入相關的任何[問題](../../active-directory/authentication/howto-authentication-passwordless-phone.md)，認知可以降低使用者打電話給技術支援中心的可能性。

> [!NOTE]
> 此解決方案的潛在失敗點是當漫遊使用者位於沒有網際網路連線的位置時。 FIDO2 安全性金鑰和 Windows Hello 企業版不受相同的限制。

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>無密碼使用 Microsoft Authenticator 登入的運作方式

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>使用者使用 Microsoft Authenticator 設定無密碼登入

在 Microsoft Authenticator 應用程式可以用來作為登入 Azure AD 帳戶的無密碼解決方案之前，必須由系統管理員和使用者執行步驟。

首先，系統管理員必須使用 Windows PowerShell，在租使用者中[啟用應用程式作為認證](../../active-directory/authentication/howto-authentication-passwordless-phone.md)。 系統管理員也需要啟用 Azure 多重要素驗證（Azure MFA）的使用者，並將 Microsoft Authenticator 應用程式設定為其中一種[驗證方法](../../active-directory/authentication/howto-mfa-mfasettings.md#verification-methods)。

終端使用者必須[下載並安裝](../../active-directory/user-help/user-help-auth-app-download-install.md)Microsoft Authenticator 應用程式，並[將其帳戶設定](../../active-directory/user-help/security-info-setup-auth-app.md)為使用 Microsoft Authenticator 應用程式作為其中一個驗證方法。

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>使用 Microsoft Authenticator 進行無密碼登入的使用者

Microsoft Authenticator 應用程式可用來登入任何 Azure AD 帳戶 (而不需使用密碼)。 雖然 Windows 10 鎖定畫面並未包含 Microsoft Authenticator 應用程式做為登入選項，但使用者仍然可以輸入其使用者名稱，然後在行動裝置上收到推播通知，以確認目前狀態。 使用者藉由比對登入畫面上的數位來確認其目前狀態，然後提供臉部掃描、指紋或 PIN 碼來解除鎖定私密金鑰並完成驗證。 這個多重要素驗證方法比密碼更安全，而且比輸入密碼和程式碼更方便。

![驗證器登入](./media/ad-passwordless/azure-ad-pwdless-image4.png)

使用 Microsoft Authenticator 的無密碼 authentication 遵循與 Windows Hello 企業版相同的基本模式，但稍微複雜一點，因為必須識別使用者，才能讓 Azure AD 找到 Microsoft Authenticator 應用程式版本作為.

![驗證器進程](./media/ad-passwordless/azure-ad-pwdless-image5.png)

1. 使用者輸入其使用者名稱。

2. Azure AD 偵測到使用者具有強式認證，並啟動強式認證流程。

3. 通知會透過 iOS 裝置上的 Apple Push Notification Service （APNS），或透過 Android 裝置上的 Firebase 雲端通訊（FCM）傳送至應用程式。

4. 使用者會收到推播通知，並開啟應用程式。

5. 應用程式會呼叫 Azure AD 並接收證明的挑戰和 nonce。

6. 使用者藉由輸入其生物識別或 PIN 碼來解除鎖定私密金鑰，來完成這項挑戰。

7. Nonce 是以私密金鑰簽署，並傳送回 Azure AD。

8. Azure AD 會執行公開/私密金鑰驗證，並傳回權杖。

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>使用者使用 Microsoft Authenticator 認證來管理其無密碼登入

透過[合併註冊](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md)，使用者可以註冊並取得 Azure 多重要素驗證和自助式密碼重設的優點。 使用者可以藉由流覽至 [[我的設定檔] 頁面](https://aka.ms/mysecurityinfo)來註冊和管理這些設定。 除了啟用 SSPR，合併註冊也支援多種驗證方法和動作。

## <a name="fido2-security-keys"></a>FIDO2 安全性金鑰

FIDO2 是最新版本的 FIDO 聯盟標準，而且有兩個元件： W3C's Web 驗證（WebAuthN）標準和對應的 FIDO 聯盟用戶端對驗證者通訊協定（CTAP2）。 FIDO2 標準可讓使用者利用以硬體、行動和生物識別為基礎的驗證器，在行動和桌面環境中輕鬆地向許多應用程式和網站進行驗證。

Microsoft 和產業合作夥伴已在 FIDO2 安全性裝置上與 Windows Hello 合作，以在共用裝置上啟用簡單且安全的驗證。 FIDO2 安全性金鑰可讓您與您一起攜帶認證，並安全地向您組織中已加入[Azure AD](https://aka.ms/azuread418)的 Windows 10 裝置進行驗證。

WebAuthN 定義了一個 API，可讓 web 應用程式和服務進行強式、無密碼驗證的開發和執行。 CTAP 通訊協定可讓外部裝置（例如 FIDO 相容的安全性金鑰）與 WebAuthN 搭配使用，並作為驗證器。 透過 Web 驗證，使用者可以使用其臉部、指紋、PIN 或可攜的 FIDO2 安全性金鑰來登入線上服務，利用強式公開金鑰認證而非密碼。 Microsoft Edge 支援目前的 WebAuthN，而 Chrome 和 Firefox 的支援正在開發中。

遵守 FIDO2、WebAuthN 和 CTAP 通訊協定的裝置和權杖，會帶出強式驗證的跨平臺解決方案，而不會使用密碼。 Microsoft 合作夥伴正致力於各種安全性金鑰的外型規格，例如 USB 安全性金鑰和具備 NFC 功能的智慧卡。

### <a name="fido2-security-keys-scenarios"></a>FIDO2 安全性金鑰案例

FIDO2 安全性金鑰可以用來登入 Azure AD，方法是在 Windows 10 鎖定畫面上選擇安全性金鑰做為認證提供者。 使用者名稱或密碼並不是必要的，這讓它成為在多個使用者之間共用電腦之第一行工作者的理想解決方案。 當公司原則規定使用者的認證必須與裝置實際分開時，它們也是絕佳的驗證選項。 使用者也可以選擇使用其在 Windows 10 1809 版或更高版本上的 Microsoft Edge 瀏覽器中的 FIDO2 安全性金鑰來登入網站。

### <a name="fido2-security-keys-deployment-considerations"></a>FIDO2 安全性金鑰部署考慮

系統管理員可以在 Azure AD 中啟用 FIDO2 支援，並將功能指派給使用者或群組。 也可以建立金鑰的布建方式和設定限制，例如允許或封鎖一組特定的硬體安全性金鑰。 金鑰必須實體散發給終端使用者。

**啟用無密碼使用 FIDO2 安全性金鑰登入 Azure AD 和網站的需求包括下列各項：**

* Azure AD

* Azure Multi-Factor Authentication

* 合併的註冊預覽

* FIDO2 安全性金鑰預覽需要相容的 FIDO2 安全性金鑰

* Web 驗證（WebAuthN）需要 Windows 10 1809 版或更高版本上的 Microsoft Edge

* 以 FIDO2 為基礎的 Windows 登入需要 Azure AD 加入 Windows 10 1809 版或更高版本（最佳體驗是在 Windows 10 1903 版或更新版本上）

符合 FIDO2 規範的外型規格包括 USB、NFC 和藍牙裝置。 我們建議您選擇符合特定需求的外型規格，因為有些平臺和瀏覽器尚未 FIDO2 相容。

我們也建議每個組織建立一個通訊協定，讓使用者和系統管理員在安全性金鑰遺失或遭竊時，遵循。 使用者應該報告遺失或遭竊的金鑰，讓系統管理員或使用者可以從使用者的設定檔中刪除其安全性金鑰，並布建一個新的金鑰。

### <a name="how-fido2-security-keys-works"></a>FIDO2 安全性金鑰的運作方式

#### <a name="user-sets-up-fido2-security-key"></a>使用者設定 FIDO2 安全性金鑰

雖然系統管理員可以手動布建[金鑰](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)，並將其散發給使用者，但在 Windows 10 鎖定畫面上布建和啟用 FIDO2 認證提供者將透過[Intune](https://docs.microsoft.com/intune/windows-enrollment-methods)提供支援。 系統管理員也必須使用[Azure 入口網站](https://portal.azure.com/)來啟用硬體權杖裝置，作為無密碼的驗證方法。

部署 FIDO2 安全性金鑰也需要使用者使用[合併註冊](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md)來註冊其金鑰。 透過合併註冊，使用者只需註冊一次，即可獲得 Azure 多重要素驗證和單一登入密碼重設（SSPR）的優點。

除了選取硬體權杖做為預設的多重要素驗證方法，建議您也選取其他驗證選項。

* Microsoft Authenticator--通知

* 驗證器應用程式或硬體權杖--程式碼

* 通話

* 簡訊

#### <a name="user-using-fido2-security-key-for-sign-in"></a>使用 FIDO2 安全性金鑰進行登入的使用者

FIDO2 會在用來做為驗證器和公開/私密金鑰加密的表單係數之間提供抽象層，以啟用內建的平臺驗證器（例如 Windows Hello 和 security key）來解析為私密金鑰，並傳遞可用來做為存取外部資源識別碼的公開金鑰。 FIDO2 安全性金鑰具有自己內建的安全記憶體保護區，可儲存私密金鑰，而且需要生物識別或 PIN 才能將其解除鎖定。 認證無法在服務之間重複使用、重新執行或共用，而且不受網路釣魚和 MiTM 攻擊或伺服器缺口的威脅。

![FIDO2 登入](./media/ad-passwordless/azure-ad-pwdless-image6.png)

FIDO2 安全性金鑰可提供安全驗證，與外型規格無關。 安全性金鑰會保存認證，而且應該使用額外的第二個因素加以保護，例如指紋（整合到安全性金鑰），或是要在 Windows 登入時輸入的 PIN。 Microsoft 合作夥伴正致力於各種安全性金鑰的外型規格。 其中一些範例包括 USB 安全性金鑰，以及已啟用 NFC 的智慧卡。

> [!NOTE]
> 安全性金鑰必須將來自 FIDO2 CTAP 通訊協定的特定功能和延伸模組，部署為與[Microsoft 相容](https://aka.ms/fido2securitykeys)。 Microsoft 已測試過與 Windows 10 和 Azure Active Directory 的相容性解決方案。

![FIDO2 登入程式](./media/ad-passwordless/azure-ad-pwdless-image9.png)

1. 使用者會將 FIDO2 裝置插入電腦中。

2. Windows 會偵測 FIDO2 安全性金鑰。

3. Windows 會傳送驗證要求。

4. Azure AD 會傳回 nonce。

5. 使用者完成其手勢，將儲存在 FIDO2 安全性金鑰安全記憶體保護區中的私密金鑰解除鎖定。

6. FIDO2 安全性金鑰會使用私密金鑰來簽署 nonce。

7. 具有簽署 nonce 的 PRT token 要求會傳送至 Azure AD。

8. Azure AD 使用 FIDO2 公開金鑰來驗證簽署的 nonce。

9. Azure AD 會傳回 PRT，以啟用內部部署資源的存取權。

#### <a name="user-manages-their-fido2-security-key-credentials"></a>使用者管理其 FIDO2 安全性金鑰認證

類似于 Microsoft Authenticator 應用程式，FIDO2 安全性金鑰的認證管理會依賴使用者的結合註冊體驗。

## <a name="deciding-a-passwordless-method"></a>決定無密碼方法

在這三種無密碼選項之間進行選擇，取決於您公司的安全性、平臺和應用程式需求。

以下是您在選擇 Microsoft 無密碼技術時應考慮的一些因素：

||**Windows Hello 企業版**|**無密碼使用 Microsoft Authenticator 應用程式登入**|**FIDO2 安全性金鑰**|
|:-|:-|:-|:-|
|**先決條件**| Windows 10 1809 版或更新版本<br>Azure Active Directory| Microsoft Authenticator 應用程式<br>電話（執行 Android 6.0 或更新版本的 iOS 和 Android 裝置）。|Windows 10 1809 版或更新版本<br>Azure Active Directory|
|**模式**|平台|軟體|硬體|
|**系統和裝置**|具有內建信賴平臺模組（TPM）的電腦<br>PIN 和生物識別辨識 |電話上的 PIN 和生物識別辨識|FIDO2 與 Microsoft 相容的安全性裝置|
|**使用者體驗**|使用 PIN 或生物識別辨識（臉部、鳶尾花或指紋）來登入 Windows 裝置。<br>Windows Hello 驗證會系結至裝置;使用者需要裝置和登入元件（例如 PIN 或生物特徵辨識因素）來存取公司資源。|使用具有指紋掃描、臉部或鳶尾花辨識或 PIN 的行動電話登入。<br>使用者從他們的電腦或行動電話登入公司或個人帳戶。|使用 FIDO2 安全性裝置（生物識別、PIN 和 NFC）登入<br>使用者可以根據組織控制來存取裝置，並根據 PIN 進行驗證、使用 USB 安全性金鑰和具備 NFC 功能的智慧卡、金鑰或穿戴式裝置等裝置進行生物識別。|
|**啟用的案例**| Windows 裝置的密碼較少體驗。<br>適用于具有單一登入裝置和應用程式功能的專用工作電腦。|使用行動電話的無密碼位置解決方案。<br>適用于從任何裝置存取 web 上的工作或個人應用程式。|使用生物識別、PIN 和 NFC 之背景工作角色的密碼較少體驗。<br>適用于共用的電腦，且行動電話不是可行的選項（例如技術支援人員、公用 kiosk 或醫院團隊）|

使用下表來選擇哪些方法將支援您的需求和使用者。

|生活|狀況|環境|無密碼技術|
|:-|:-|:-|:-|
|**管理員**|保護裝置的存取權以進行管理工作|指派的 Windows 10 裝置|Windows Hello 企業版和（或） FIDO2 安全性金鑰|
|**管理員**|非 Windows 裝置上的管理工作| 行動或非 windows 裝置|無密碼使用 Microsoft Authenticator 應用程式登入|
|**資訊工作者**|生產力工作|指派的 Windows 10 裝置|Windows Hello 企業版和（或） FIDO2 安全性金鑰|
|**資訊工作者**|生產力工作| 行動或非 windows 裝置|無密碼使用 Microsoft Authenticator 應用程式登入|
|**第一線背景工作**|工廠、工廠、零售或資料輸入中的 kiosk|共用的 Windows 10 裝置|FIDO2 安全性金鑰|

## <a name="getting-started"></a>開始使用

無密碼 authentication 是未來的一波，也是更安全環境的路徑。 建議組織開始規劃這項變更，並減少其對密碼的相依性。 若要開始使用，請考慮下列目標：

* 啟用 MFA + Microsoft Authenticator 應用程式 + 條件式存取的使用者。

* 首度發行 Azure AD 密碼保護 + 更新原則。

* 讓使用者能夠使用合併的註冊進行 SSPR。

* 部署適用于行動的 Microsoft Authenticator 應用程式。

* 部署 Windows Hello 企業版（1909：保持最新）。

* 為無法使用電話的使用者部署 FIDO2 裝置。

* 可能的話，請停用密碼型驗證。

若要達成這些目標，我們建議採用下列方法：

1. 讓 Azure Active Directory 能夠充分利用 Azure MFA 和條件式存取等功能。

2. 啟用多重要素驗證以提供額外的保護。

3. 在使用者需要切換回使用密碼的事件中，啟用自助式密碼重設。

4. 部署 Microsoft Authenticator 手機登入以新增行動性。

5. 將 Windows Hello 企業版部署到您的所有 Windows 10 裝置。

6. 準備 FIDO2 安全性金鑰。

> [!NOTE]
> 如需無密碼方法的授權需求詳細資料，請參閱 Azure Active Directory[授權頁面](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="conclusion"></a>結論

過去幾年來，Microsoft 一直致力於在沒有密碼的情況下實現世界。 在 Windows 10 中，Microsoft 引進了 Windows Hello 企業版，這是一種強式硬體保護的雙因素認證，可讓您單一登入 Azure Active Directory 和 Active Directory。 與 Windows Hello 企業版的技術類似，Microsoft Authenticator 應用程式會使用以金鑰為基礎的驗證來啟用系結至行動裝置的使用者認證，並使用生物識別或 PIN。 現在 FIDO2 安全性金鑰可讓您攜帶認證並登入 Azure AD，方法是在 Windows 10 鎖定畫面上選擇安全性金鑰做為認證提供者。 這三個無密碼解決方案都會降低網路釣魚、密碼噴灑和重新執行攻擊的風險，並為使用者提供高度安全且方便的方式，讓您可以從任何地方登入和存取資料。

採用現代化的多重要素驗證技術（例如生物識別和公開金鑰加密）在廣泛存取的裝置中，是其中一個最影響力的步驟，可以有意義地降低公司的身分識別風險。 無密碼是一種長期的安全驗證方法，而且仍在發展中。 有了新興的需求，組織可以藉由規劃開始移至無密碼技術來準備自己。

## <a name="next-steps"></a>後續步驟

* 概述[什麼是無密碼？](../../active-directory/authentication/concept-authentication-passwordless.md)
* [如何在 Azure AD 中啟用無密碼](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
