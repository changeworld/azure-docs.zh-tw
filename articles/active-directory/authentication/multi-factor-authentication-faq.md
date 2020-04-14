---
title: Azure 多重身份驗證常見問題解答 - Azure 活動目錄
description: 與 Azure Multi-Factor Authentication 相關的常見問題與答案。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d28f93f316ac2a63be6b3a8eb0b80678bd7607f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271398"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>與 Azure Multi-Factor Authentication 相關的常見問題

此常見問題集回答關於 Azure Multi-Factor Authentication 和使用 Multi-Factor Authentication 服務的常見問題。 並將問題細分為一般服務問題、計費模式、使用者體驗和疑難排解。

> [!IMPORTANT]
> 自 2019 年 7 月 1 日起,Microsoft 將不再為新部署提供 MFA 伺服器。 希望用戶進行多重身份驗證的新客戶應使用基於雲的 Azure 多重身份驗證。 在 7 月 1 日之前啟動 MFA 伺服器的現有客戶將能夠像往常一樣下載最新版本、將來的更新並生成啟動認證。
>
> 下面共用的有關 Azure 多重身份驗證伺服器的資訊僅適用於已運行 MFA 伺服器的使用者。
>
> 自 2018 年 9 月 1 日起,新客戶不再使用基於消費的許可。
> 自 2018 年 9 月 1 日起,可能不再創建新身份驗證提供者。 現有的驗證提供者可繼續使用與更新。 多重要素驗證將繼續為 Azure AD Premium 授權中的可用功能。

## <a name="general"></a>一般

* [Azure 多重身份驗證伺服器如何處理用戶數據?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [向使用者發送 SMS 訊息時使用了哪些 SMS 短代碼?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>Azure Multi-Factor Authentication Server 如何處理使用者資料？

使用多重身份驗證伺服器時,用戶數據僅存儲在本地伺服器上。 雲端中不會儲存任何持續性的使用者資料。 使用者執行雙步驟驗證時，Multi-Factor Authentication Server 會將資料傳送到 Azure Multi-Factor Authentication 雲端服務以供驗證。 Multi-Factor Authentication Server 與 Multi-Factor Authentication 雲端服務之間的通訊會透過傳出連接埠 443，使用安全通訊端層 (SSL) 或傳輸層安全性 (TLS) 通訊。

驗證要求傳送至雲端服務時，會收集資料以用於驗證和使用方式報告。 以下資料欄位包含在兩步驗證紀錄中:

* **唯一識別碼** (使用者名稱或內部部署 Multi-Factor Authentication Server 識別碼兩者之一)
* **名稱與姓氏**(選擇性的)
* **電子郵件地址**(選擇性的)
* **電話號碼** (進行語音通話或簡訊驗證時)
* **裝置權杖** (執行行動應用程式驗證時)
* **驗證模式**
* **驗證結果**
* **Multi-Factor Authentication Server 名稱**
* **Multi-Factor Authentication Server IP**
* **用戶端 IP** (如果有的話)

您可以在 Multi-Factor Authentication Server 中設定選擇性欄位。

驗證結果 (成功或拒絕) 和拒絕原因會與驗證資料一起儲存。 可在驗證和使用方式報告中取得此資料。

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>何種 SMS 簡短程式碼可用來傳送 SMS 訊息給我的使用者？

在美國,我們使用以下簡訊短代碼:

* *97671*
* *69829*
* *51789*
* *99399*

在加拿大,我們使用以下簡訊短代碼:

* *759731*
* *673801*

不能保證以相同號碼發送一致的 SMS 或基於語音的多重身份驗證提示傳遞。 為了使用者的利益,我們可以隨時添加或刪除短代碼,因為我們進行路線調整,以提高簡訊的傳遞能力。 我們不支持美國和加拿大以外的國家或地區的短代碼。

## <a name="billing"></a>計費

大多數計費問題可以通過引用[多重身份驗證定價頁](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)或[Azure 多重身份驗證版本和消費計劃](concept-mfa-licensing.md)的文檔來回答。

* [我的組織是否因發送用於身份驗證的電話和簡訊而收費?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [每個使用者的計費模型是否對所有已啟用的使用者向我收費,或者只向執行兩步驗證的用戶收費?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [多重身份驗證計費如何工作?](#how-does-multi-factor-authentication-billing-work)
* [是否有免費版本的 Azure 多重身份驗證?](#is-there-a-free-version-of-azure-multi-factor-authentication)
* [我的組織可以隨時在使用者和每個身份驗證消耗計費模型之間切換嗎?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [我的組織可以隨時在基於消耗的計費和訂閱(基於許可證的模型)之間切換?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [我的組織是否必須使用和同步標識才能使用 Azure 多重身份驗證?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>若為了驗證而撥打電話或傳送簡訊，我的組織是否須為此付費？

不可以,對於通過 Azure 多重身份驗證向使用者發送的單個電話呼叫或發送給使用者的簡訊,不收取費用。 如果使用每個身份驗證 MFA 提供程式,則對每個身份驗證收費,但不針對使用的方法收費。

根據不同的個人電話服務，您的使用者可能需要為接聽的電話或收到的簡訊支付費用。

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>若使用以使用者為單位的計費模式，我需要為所有啟用的使用者付費嗎？或是只須為執行雙步驟驗證的使用者付費？

無論使用者是否在該月執行雙步驟驗證，計費是以設定要使用 Multi-Factor Authentication 的使用者數量作為基礎。

### <a name="how-does-multi-factor-authentication-billing-work"></a>Multi-Factor Authentication 是如何計費？

當您建立每個使用者或每個授權的 MFA 提供者時，貴組織的 Azure 訂用帳戶會以使用方式每個月計費作為基礎。 此計費模型類似於 Azure 為虛擬機器和 Web 應用的使用計費方式。

當您購買 Azure Multi-factor Authentication 的訂用帳戶時，貴組織僅支付每位使用者的年度授權費。 MFA 授權和 Office 365、Azure AD Premium 或 Enterprise Mobility + Security 組合會以這種方式計費。

如需詳細資訊，請參閱[如何取得 Azure Multi-Factor Authentication](concept-mfa-licensing.md)。

### <a name="is-there-a-free-version-of-azure-multi-factor-authentication"></a>是否有免費版本的 Azure Multi-Factor Authentication？

可以在 Azure AD 免費層中啟用安全預設值。 使用安全預設值時,所有使用者都啟用使用 Microsoft 身份驗證器應用進行多重身份驗證。 不能使用簡訊或手機驗證與安全預設值,只有微軟身份驗證器應用程式。

有關詳細資訊,請參閱[什麼是安全默認值?](../fundamentals/concept-fundamentals-security-defaults.md)

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>我的組織是否能隨時在「每位使用者」與「每次驗證」耗用量計費模式之間切換？

如果貴組織購買的 MFA 是使用量計費型獨立服務，則您可以在建立 MFA 提供者時選擇計費模式。 創建 MFA 提供程式後,無法更改計費模型。 

如果 MFA 提供者*未*連結到 Azure AD 租戶,或者將新的 MFA 提供程式連結到其他 Azure AD 租戶,則不會傳輸使用者設置和配置選項。 此外，現有 Azure MFA 伺服器也需要使用新 MFA 提供者產生的啟用認證重新啟動。 重新啟用 MFA 伺服器以將其連結至新的 MFA 提供者，並不會影響電話和簡訊驗證，但所有使用者的行動裝置應用程式通知將會停止運作，直到他們重新啟動行動裝置應用程式。

於[開始使用 Azure Multi-Factor Auth 提供者](concept-mfa-authprovider.md)中深入了解 MFA 提供者。

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>我的組織是否能隨時在「根據使用量計費」和「訂用帳戶」(根據授權計費模式) 之間切換？

在某些情況下，是的。

如果您的目錄有*根據使用者計費*的 Azure Multi-Factor Authentication 提供者，您可以新增 MFA 授權。 擁有授權的使用者不會根據每位使用者使用量計費。 沒有授權的使用者仍可透過 MFA 提供者啟用 MFA。 如果您購買授權，並將其指派給所有設定使用 Multi-Factor Authentication 的使用者，您就可以刪除 Azure Multi-Factor Authentication 提供者。 如果您之後會有多於授權的使用者，您隨時都可以建立其他根據使用者計費的 MFA 提供者。

如果目錄具有*按身份驗證 Azure*多重身份驗證提供者,則只要 MFA 提供程式連結到訂閱,則始終會為每個身份驗證付費。 您可以將 MFA 授權指派給使用者，但您仍需要為每次雙步驟驗證要求支付費用，無論發出此要求的人是否擁有指派的 MFA 授權。

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication"></a>我的組織是否必須使用和同步處理身分識別才能使用 Azure Multi-Factor Authentication？

如果貴組織使用根據使用量計費的模型，則 Azure Active Directory 為選擇性項目，並非必要。 如果 MFA 提供程式未連結到 Azure AD 租戶,則只能在本地部署 Azure 多重身份驗證伺服器。

授權模式需要 Azure Active Directory，因為當您購買授權並指派給目錄中的使用者時，會將授權加入 Azure AD 租用戶。

## <a name="manage-and-support-user-accounts"></a>管理和支援使用者帳戶

* [如果用戶在手機上沒有收到回應,我應該告訴用戶怎麼做?](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [如果我的一個用戶無法進入他們的帳戶,我該怎麼辦?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [如果我的一個使用者丟失了使用應用密碼的手機,我該怎麼辦?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [如果用戶無法登錄到非瀏覽器應用,該怎麼辦?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [我的使用者說,有時他們沒有收到簡訊或驗證超時。](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [我可以更改使用者在系統超時之前必須從文本消息輸入驗證碼的時間量嗎?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [是否可以將硬體權杖與 Azure 多重身份驗證伺服器一起使用?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [我可以使用 Azure 多重身份驗證伺服器來保護終端服務嗎?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [我在 MFA 伺服器中配置了呼叫者 ID,但我的使用者仍收到來自匿名呼叫者的多重身份驗證呼叫。](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [為什麼會提示我的用戶註冊其安全資訊?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>如果用戶在手機上沒有收到回應,我應該告訴用戶怎麼做?

讓使用者在 5 分鐘內嘗試多達 5 次,以獲得電話或簡訊進行身份驗證。 Microsoft 會使用多個提供者提供電話及簡訊。 如果此方法不起作用,則打開支援案例以進一步排除故障。

第三方安全應用還可以阻止驗證碼簡訊或電話呼叫。 如果使用第三方安全應用,請嘗試禁用保護,然後請求發送另一個 MFA 驗證碼。

如果上述步驟不起作用,請檢查使用者是否配置為多個驗證方法。 請嘗試再次登錄,但在登錄頁面上選擇其他驗證方法。

有關詳細資訊,請參閱[最終使用者故障排除指南](../user-help/multi-factor-authentication-end-user-troubleshoot.md)。

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>如果我的使用者無法登入他們的帳戶，我該怎麼做？

您可以讓使用者再次進行註冊程序，以重設使用者的帳戶。 深入了解 [在雲端使用 Azure Multi-Factor Authentication 管理使用者和裝置設定](howto-mfa-userdevicesettings.md)。

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>如果我的其中一個使用者遺失手機，而該手機上正在使用應用程式密碼，我該怎麼做？

請刪除該使用者的所有應用程式密碼，以防止未經授權的存取。 當使用者有替代裝置後，他們可以重新建立密碼。 深入了解 [在雲端使用 Azure Multi-Factor Authentication 管理使用者和裝置設定](howto-mfa-userdevicesettings.md)。

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>如果使用者無法登入非瀏覽器的應用程式該怎麼辦？

如果貴組織仍然使用舊版的用戶端，但您已[允許使用應用程式密碼](howto-mfa-mfasettings.md#app-passwords)，則您的使用者無法使用他們的使用者名稱和密碼登入這些舊版用戶端。 相反地，他們需要[設定應用程式密碼](../user-help/multi-factor-authentication-end-user-app-passwords.md)。 您的使用者必須先清除 (刪除) 登入資訊、重新啟動應用程式，然後使用他們的使用者名稱和*應用程式密碼* (不是他們慣用的密碼) 登入。

如果您的組織沒有舊用戶端,則不應允許使用者創建應用密碼。

> [!NOTE]
> **適用於 Office 2013 用戶端的新式驗證**
>
> 只有不支援最新驗證方式的應用程式需要應用程式密碼。 Office 2013 用戶端支援最新的驗證通訊協定，但需要設定。 運行 Office 2013 3 月或更高版本更新的任何客戶都可以使用現代身份驗證。 有關詳細資訊,請參閱博客文章更新 Office [365 現代身份驗證](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/)。

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>我的使用者說,有時他們沒有收到簡訊或驗證超時。

SMS 消息的傳遞不保證,因為有一些無法控制的因素可能會影響服務的可靠性。 這些因素包括目的地國家或地區、行動電話營運商和信號強度。

第三方安全應用還可以阻止驗證碼簡訊或電話呼叫。 如果使用第三方安全應用,請嘗試禁用保護,然後請求發送另一個 MFA 驗證碼。

如果使用者在可靠地接收簡訊時經常遇到問題,請告訴他們改用 Microsoft 身份驗證器應用或電話呼叫方法。 Microsoft 身份驗證器可以通過蜂窩和 Wi-Fi 連接接收通知。 此外，即使裝置收不到訊號，行動應用程式可以產生驗證碼。 微軟身份驗證器應用程式可用於[安卓](https://go.microsoft.com/fwlink/?Linkid=825072)[,iOS](https://go.microsoft.com/fwlink/?Linkid=825073)和[Windows 手機](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)。

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>可變更使用者必須在系統逾時前，輸入簡訊上驗證碼的時間限制嗎？

在某些情況下是可以的。

若為具有 Azure MFA Server v7.0 或更高版本的單向 SMS，您可以設定登錄機碼來設定逾時設定。 MFA 雲端服務傳送文字訊息之後，驗證碼 (或單次密碼) 就會傳回 MFA 伺服器。 依預設，MFA 伺服器會將程式碼儲存在記憶體中達 300 秒。 如果使用者在 300 秒內未輸入代碼，則他們的驗證會遭到拒絕。 若要變更預設逾時設定，請使用下列步驟：

1. 移至 `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor` 。
2. 創建稱為*pfsvc_pendingSmsTimeoutSeconds***的 DWORD**註冊表項,並設置希望 Azure MFA 伺服器存儲一次性密碼的時間(以秒為單位)。

>[!TIP]
>
> 如果您有多個 MFA 伺服器，只有處理原始驗證要求的 MFA 伺服器會知道已傳送給使用者的驗證碼。 當使用者輸入程式碼時，用來進行驗證的驗證要求必須傳送到相同的伺服器。 如果程式碼驗證傳送到不同的伺服器，驗證會遭到拒絕。

如果使用者未在定義的逾時期限內回應簡訊，其驗證就會遭到拒絕。

對於雲端中具有 Azure MFA 的單向 SMS(包括 AD FS 適配器或網路策略伺服器擴展),無法配置超時設定。 Azure AD 會儲存驗證程式碼達 180 秒。

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>我是否可以搭配 Azure Multi-Factor Authentication Server 使用硬體權杖？

如果使用 Azure 多重身份驗證伺服器,則可以導入基於時間的基於第三方開放身份驗證 (OATH) 的一次性密碼 (TOTP) 令牌,然後使用它們進行兩步驗證。

如果您將秘密金鑰放在 CSV 檔案中，並匯入至 Azure Multi-Factor Authentication Server，則可以使用屬於 OATH TOTP 權杖的 ActiveIdentity 權杖。 只要用戶端系統可接受使用者輸入，您就可以搭配下列服務來使用 OATH 權杖：Active Directory Federation Services (ADFS)、Internet Information Server (IIS) 表單型驗證，以及遠端驗證撥號使用者服務 (RADIUS)。

您可以匯入下列格式的第三方 OATH TOTP 權杖︰  

- 可攜式對稱金鑰容器 (PSKC)
- 如果檔案包含序號、Base 32 格式的秘密金鑰和時間間隔，則為 CSV

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>我是否可以使用 Azure Multi-Factor Authentication Server 保護「終端機服務」的安全？

是,但如果您使用的是 Windows Server 2012 R2 或更高版本,則只能透過使用遠端桌面閘道 (RD 閘道)來保護終端服務。

Windows Server 2012 R2 中的安全性變更已改變 Azure Multi-Factor Authentication Server 在 Windows Server 2012 及舊版本中，對本機安全性授權 (LSA) 安全性套件的連接方式。 對於 Windows Server 2012 或較舊版本中的終端機服務版本，您可以[使用 Windows 驗證來保護應用程式](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)。 如果您使用的是 Windows 伺服器 2012 R2,則需要 RD 閘道。

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>我已在 MFA Server 中設定顯示來電號碼，但我的使用者仍接到使用匿名來電的 Multi-Factor Authentication。

當透過公共電話網路發出 Multi-Factor Authentication 通話時，有時會透過不支援來電顯示的電信業者路由傳送。 由於此運營商行為,即使多重身份驗證系統始終發送調用方 ID,也不能保證調用方 ID。

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>為什麼我的使用者收到須註冊安全性資訊的提示？

使用者收到須註冊安全性資訊的原因有下列幾項︰

- 使用者已透過他們 Azure AD 中的系統管理者啟用 MFA，但尚未在他們的帳戶中註冊安全性資訊。
- 使用者已在 Azure AD 中啟用自助式密碼重設。 如果他們之後忘記密碼，安全性資訊將會協助他們重設密碼。
- 使用者存取具有條件存取策略以需要 MFA 且以前未註冊 MFA 的應用程式。
- 使用者正在 Azure AD 註冊設備(包括 Azure AD 聯接),您的組織需要 MFA 進行設備註冊,但使用者以前尚未註冊 MFA。
- 使用者正在 Windows 10 中為企業生成 Windows Hello(這需要 MFA),並且以前尚未註冊 MFA。
- 組織建立已套用至使用者的 MFA 註冊原則並加以啟用。
- 使用者先前已註冊過 MFA，但選擇的驗證方法已由系統管理員停用。 使用者必須因此再次進行 MFA 註冊，以選取新的預設驗證方法。

## <a name="errors"></a>Errors

* [如果使用者在使用行動應用程式通知時看到「身份驗證請求不適用於已啟動的帳戶」錯誤訊息,該怎麼辦?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [如果使用者在登錄到非瀏覽器應用程式時看到 0x800434D4L 錯誤消息,該怎麼辦?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>如果使用者在使用行動應用程式通知時看到「身份驗證請求不適用於已啟動的帳戶」錯誤訊息,該怎麼辦?

要求使用者完成以下過程,從 Microsoft 身份驗證器中刪除其帳戶,然後再次添加:

1. 轉到[其 Azure 門戶配置檔](https://account.activedirectory.windowsazure.com/profile/)並使用組織帳戶登錄。
2. 選取 [其他安全性驗證]****。
3. 從 Microsoft 身份驗證器應用中刪除現有帳戶。
4. 按下「**設定**」,然後按照說明重新設定 Microsoft 身份驗證器。

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>如果使用者在登入非瀏覽器應用程式時看到 0x800434D4L 錯誤訊息，應該怎麼辦？

當您嘗試登錄到安裝在本地電腦上的非瀏覽器應用程式時,將發生*0x800434D4L*錯誤,該應用程式與需要兩步驗證的帳戶不起作用。

此種錯誤的因應措施是使用不同的使用者帳戶，進行系統管理和非系統管理作業。 您稍後可以連結系統管理帳戶與非系統管理帳戶之間的信箱，以便使用非系統管理帳戶登入 Outlook。 如需這個解決方案的詳細資訊，請了解如何[讓系統管理員能夠開啟及檢視使用者信箱的內容](https://help.outlook.com/141/gg709759.aspx?sl=1)。

## <a name="next-steps"></a>後續步驟

如果您的問題未在此處回答,則提供以下支援選項:

* 在 [Microsoft 支援知識庫](https://support.microsoft.com)搜尋常見技術問題的解決方案。
* 搜索和瀏覽社區中的技術問題和答案,或在[Azure 活動目錄 Q&A](https://docs.microsoft.com/answers/topics/azure-active-directory.html)中提出您自己的問題。
* 通過 Azure[多重身份驗證伺服器支援](https://support.microsoft.com/oas/default.aspx?prid=14947)與 Microsoft 專業人員聯繫。 連絡我們時，請盡量包含有關問題的最多資訊，這樣會十分有幫助。 您可以提供的資訊包含您看到錯誤的頁面、特定錯誤碼、特定工作階段識別碼，以及看到錯誤的使用者的識別碼。
* 如果您是舊版 PhoneFactor 客戶,並且對重置密碼有疑問或需要説明,[phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com)請使用 電子郵件地址打開支援案例。
