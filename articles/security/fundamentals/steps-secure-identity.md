---
title: 保護 Azure AD 標識基礎結構
titleSuffix: Azure Active Directory
description: 本文件概述系統管理員所應實作以便有助自身使用 Azure AD 功能來保護組織的重要動作清單
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: e0db8edfdfa380697a1d8d7e262a7a84da2fb7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565531"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>可保護身分識別基礎結構的五個步驟

如果您正在閱讀本文件，想必您已了解安全性有多重要。 您可能已承擔起保護組織的重任。 如果您需要說服他人，讓其了解安全性有多重要，請讓其閱讀最新的 [安全情報報告](https://go.microsoft.com/fwlink/p/?linkid=2073747)。

本文件會協助您使用 Azure Active Directory 的功能，透過有五個步驟的檢查清單讓組織預防網路攻擊來獲得更安全的狀態。

這份檢查清單會藉由說明如何執行下列作業，協助您快速部署重要的建議動作來立即保護組織：

* 強化認證。
* 減少受攻擊面。
* 將威脅回應自動化。
* 利用雲智慧。
* 啟用最終使用者自助服務。

請務必在閱讀此清單時跟蹤哪些功能和步驟已完成。

> [!NOTE]
> 這份文件中有諸多建議，僅對設定為以 Azure Active Directory 作為其識別提供者的應用程式有效。 為應用程式設定單一登入，可確保應用程式會享有認證原則、威脅偵測、稽核、記錄，以及其他已新增至這些應用程式的功能。 [透過 Azure Active Directory 的單一登入](../../active-directory/manage-apps/configure-single-sign-on-non-gallery-applications.md)是實現這些建議的基礎。

本文件中的建議事項是與[身分識別安全分數](../../active-directory/fundamentals/identity-secure-score.md)相互搭配，其能針對您 Azure AD 租用戶的身分識別安全設定進行自動化評估。 組織可以使用 Azure AD 門戶中的"身份安全分數"頁來查找其當前安全配置中的漏洞，以確保它們遵循 Microsoft 當前的安全[最佳實踐](identity-management-best-practices.md)。 實作 [安全分數] 頁面中的每個建議，將能提升您的分數並允許您追蹤自己的進度，並能協助您與業界其他類似規模的組織比較彼此的實作情況。

![身分識別安全分數](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> 此處描述的許多功能都需要 Azure AD 高級訂閱，而某些功能是免費的。 有關詳細資訊，請查看我們的[Azure 活動目錄定價](https://azure.microsoft.com/pricing/details/active-directory/)和[Azure AD 部署清單](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2)。

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>開始之前：使用 MFA 保護特殊權限帳戶

在開始進行此檢查清單之前，請確定您不會在閱讀此檢查清單時遭到入侵。 您必須先保護特殊權限帳戶。

攻擊者若掌控了特殊權限帳戶，將會造成極大的破壞，因此請務必先保護這些帳戶。 使用[Azure AD 安全預設值](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)或[條件訪問](../../active-directory/conditional-access/plan-conditional-access.md)為組織中的所有管理員啟用和要求[Azure 多重要素驗證](../../active-directory/authentication/multi-factor-authentication.md)（MFA）。 如果您尚未實作 MFA，請立即實作！ 這一點非常重要。

全都準備好了嗎？ 那就開始進行此檢查清單吧。

## <a name="step-1---strengthen-your-credentials"></a>步驟 1 - 強化認證

企業的安全性缺口，大多是因為帳戶遭到少數幾種入侵方法 (例如，密碼噴灑、入侵重播或網路釣魚) 入侵。 若要深入了解這些攻擊，請觀看這段影片 (45 分鐘)：
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>確保您的組織使用增強式驗證

有鑑於入侵者經常會猜測、誘騙或以惡意程式碼竊取密碼，使用者也經常會重複使用密碼，所以請務必使用某種形式的強式認證來支援密碼 - 詳情請參閱 [Azure Multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md)。

要輕鬆啟用基本級別的標識安全性，可以使用 Azure [AD 安全預設值](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)的一鍵啟用。 安全預設值強制租戶中的所有使用者使用 Azure MFA，並阻止來自租戶範圍的舊協定登錄。

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>開始禁用常常受到攻擊的密碼，並關閉傳統的複雜性規則及到期規則。

有許多組織都是使用傳統的複雜性規則 (要求特殊字元、數字、大寫及小寫) 和密碼到期規則。 [Microsoft 的研究](https://aka.ms/passwordguidance) \(英文\) 顯示，這些原則會導致使用者選擇較容易猜到的密碼。

Azure AD 的[動態禁用密碼](../../active-directory/authentication/concept-password-ban-bad.md)功能會使用目前的攻擊者行為來防止使用者設定可輕易猜到的密碼。 此功能先前在雲端中建立使用者時便已持續開啟，但現在也可供混合式組織在部署[適用於 Windows Server Active Directory 的 Azure AD 密碼保護](../../active-directory/authentication/concept-password-ban-bad-on-premises.md)時使用。 Azure AD 密碼保護會阻止使用者選擇這些常見密碼，並可延伸以封鎖包含您所指定之自訂關鍵字的密碼。 例如，您可以避免使用者選擇包含您公司產品名稱或當地球隊的密碼。

Microsoft 建議採用下列根據 [NIST 指導方針](https://pages.nist.gov/800-63-3/sp800-63b.html) \(英文\) 的新式密碼原則：

1. 要求密碼至少要有 8 個字元。 密碼並非越長越好，密碼越長會導致使用者選擇可預測的密碼、將密碼儲存在檔案中或寫下來。
2. 禁用過期規則，這驅使使用者輕鬆猜到密碼，如**Spring2019！**
3. 停用字元組合規定，防止使用者選擇常常受到攻擊的密碼，原因是這些規定會導致使用者在密碼中選擇可預測的替代字元。

如果您是直接在 Azure AD 中建立身分識別，則可以使用 [PowerShell 來防止使用者的密碼到期](../../active-directory/authentication/concept-sspr-policy.md)。 混合式組織應使用[網域群組原則設定](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) \(英文\) 或 [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy) \(英文\) 來實作這些原則。

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>防止認證外洩並新增遇到中斷時的復原能力

如果您的組織使用搭配傳遞驗證或同盟的混合式身分識別解決方案，則基於下列兩個原因，您應該啟用密碼雜湊同步處理：

* Azure AD 管理中的[認證外洩的使用者](../../active-directory/reports-monitoring/concept-risk-events.md)報告會就「暗網 (Dark Web)」上已公開的使用者名稱和密碼組向您發出警告。 數量驚人的密碼是透過網路釣魚、惡意程式碼，以及在第三方網站上重複使用密碼並於之後遭到入侵而外洩的。 Microsoft 已發現許多這樣的外洩認證，並會在此報告中告訴您它們是否與您組織的認證相符，但前提是您必須[啟用密碼雜湊同步處理](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)！
* 如果發生本地中斷（例如，在勒索軟體攻擊中），您可以使用[密碼雜湊同步切換到使用雲身份驗證](choose-ad-authn.md)。此備份身份驗證方法將允許您繼續訪問配置為使用 Azure 活動目錄進行身份驗證的應用，包括 Office 365。 在這種情況下，IT 人員無需求助於個人電子郵件帳戶來共用資料，直到本地中斷得到解決。

深入了解[密碼雜湊同步處理](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)的運作方式。

> [!NOTE]
> 如果您啟用密碼雜湊同步處理，並且要使用 Azure AD Domain Services，則 Kerberos (AES 256) 雜湊以及選擇性地 NTLM (RC4，沒有 salt) 雜湊也會加密並同步處理至 Azure AD。

### <a name="implement-ad-fs-extranet-smart-lockout"></a>實作 AD FS 外部網路智慧鎖定

將應用程式設定為直接向 Azure AD 進行驗證的組織，可受益於 [Azure AD 智慧鎖定](../../active-directory/authentication/concept-sspr-howitworks.md)。 如果您是在 Windows Server 2012R2 中使用 AD FS，請實作 AD FS [外部網路鎖定保護](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection) \(英文\)。 如果您是在 Windows Server 2016 上使用 AD FS，請實作[外部網路智慧鎖定](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016) \(機器翻譯\)。 AD FS 智慧外部網路鎖定可防範以 AD FS 目標的暴力密碼破解攻擊，並防止系統在 Active Directory 中鎖定使用者。

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>利用真正安全、更容易使用的認證

使用 [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)，即可在 PC 和行動裝置上將密碼改為強式雙因素驗證。 這個驗證包含與裝置安全繫結的新型使用者認證，且會使用生物特徵辨識或 PIN 碼。

## <a name="step-2---reduce-your-attack-surface"></a>步驟 2 - 減少受攻擊面

由於密碼入侵問題無所不在，所以盡量減少組織的受攻擊面就相當重要。 不要使用老舊且不安全的通訊協定、限制存取進入點，以及更加嚴格地控制資源的系統管理存取權，可協助減少受攻擊面。

### <a name="block-legacy-authentication"></a>封鎖舊式驗證

使用自有舊式方法來向 Azure AD 進行驗證和存取公司資料的應用程式，會對組織造成其他風險。 使用舊式驗證的應用程式範例包括 POP3、IMAP4 或 SMTP 用戶端。 舊式驗證應用程式會代表使用者進行驗證，而讓 Azure AD 無法進行進階的安全性評估。 替代的現代身份驗證將降低安全風險，因為它支援多重要素驗證和條件訪問。 建議您進行下列三個動作：

1. 封鎖[舊式驗證 (如果您使用 AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)。
2. 將 [SharePoint Online 和 Exchange Online 設定為使用新式驗證](../../active-directory/conditional-access/conditional-access-for-exo-and-spo.md)。
3. 如果您有 Azure AD 高級版，請使用[條件訪問策略](../../active-directory/conditional-access/overview.md)來阻止舊版身份驗證，否則請使用[Azure AD 安全預設值](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)。

### <a name="block-invalid-authentication-entry-points"></a>封鎖無效的驗證進入點

使用假想入侵心態，應該就能在使用者認證遭到入侵時降低其影響。 對於環境中的每個應用程式，請考慮有效的使用案例：哪些群組、哪些網路、哪些裝置和其他元素有獲得授權，然後將其餘項目封鎖。 使用[Azure AD 條件訪問](../../active-directory/conditional-access/overview.md)，您可以根據定義的特定條件控制授權使用者如何訪問其應用和資源。

### <a name="restrict-user-consent-operations"></a>限制使用者同意操作

瞭解各種[Azure AD 應用程式同意體驗](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience)、[許可權和同意的類型](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)及其對組織安全狀況的影響非常重要。 預設情況下，Azure AD 中的所有使用者可以授予利用 Microsoft 標識平臺訪問組織資料的應用程式。 雖然允許使用者自行同意，但允許使用者輕鬆獲取與 Microsoft 365、Azure 和其他服務集成的有用應用程式，但如果不仔細使用和監視，則可能會構成風險。

Microsoft 建議[禁用將來的使用者同意操作](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access#i-want-to-disable-all-future-user-consent-operations-to-any-application)，以説明減少您的表面積並減輕此風險。 如果禁用最終使用者同意，以前的同意授予仍將得到遵守，但以後的所有同意操作必須由管理員執行。 使用者可以通過集成[的管理員同意請求工作流](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)或通過您自己的支援流程要求管理員同意。 在禁用最終使用者同意之前，請使用我們[的建議](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-consent-requests)來規劃您組織的此更改。 對於您希望允許所有使用者訪問的應用程式，請考慮[代表所有使用者授予同意](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)，以確保尚未單獨同意的使用者能夠訪問該應用程式。 如果不希望這些應用程式在所有方案中都可供所有使用者使用，請使用[應用程式分配](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)和[條件訪問](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)來限制使用者對應用的訪問。

確保使用者可以要求管理批准新應用程式，以減少使用者摩擦，最大限度地減少支援量，並防止使用者使用非 Azure AD 憑據註冊應用程式。 一旦您規範了同意操作，管理員應定期審核應用並授予許可權。


### <a name="implement-azure-ad-privileged-identity-management"></a>實作 Azure AD Privileged Identity Management

「假想入侵」的另一個影響是需要將遭到入侵的帳戶可使用特殊權限角色進行操作的可能性降到最低。

[Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) 可協助您進行下列作業，讓您將帳戶特殊權限最小化：

* 識別和管理指派給系統管理角色的使用者。
* 了解您應該移除的未用或多餘特殊權限角色。
* 建立規則以確定特殊權限角色會受到多重要素驗證保護。
* 建立規則以確定所授與的特殊權限角色有效時間僅足以完成特殊權限工作。

啟用 Azure AD PIM，然後檢視已獲派系統管理角色的使用者，並將這些角色中的不必要帳戶移除。 對於其餘特殊權限使用者，則將它們從永久性角色改為合適角色。 最後，請建立適當原則，以確定當使用者需要存取那些特殊權限角色時，可以搭配必要的變更控制安全地進行。

作為部署特權帳戶過程的一部分，請遵循[最佳做法，至少創建兩個緊急帳戶](../../active-directory/users-groups-roles/directory-admin-roles-secure.md)，以確保在鎖定自己時仍可以訪問 Azure AD。

## <a name="step-3---automate-threat-response"></a>步驟 3 - 將威脅回應自動化

Azure Active Directory 有許多會自動攔截攻擊的功能，可讓偵測與回應之間沒有延遲。 若能減少罪犯可供用來將自身埋藏到您環境的時間，您就能降低成本和風險。 您可以採取的具體步驟如下。

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>使用 Azure AD Identity Protection 來實作使用者風險安全性原則

使用者風險表示使用者身份被洩露的可能性，並根據與使用者標識關聯的[使用者風險檢測](../../active-directory/identity-protection/overview.md)進行計算。 使用者風險策略是一種條件訪問策略，用於向特定使用者或組評估風險級別。 根據低、中、高風險層級，原則可以設定為封鎖存取，也可以設定為必須使用多重要素驗證來進行安全密碼變更。 Microsoft 會建議您要求高風險使用者必須進行安全密碼變更。

![標示有風險的使用者](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>使用 Azure AD Identity Protection 來實作登入風險原則

登入風險是指帳戶擁有者以外人士嘗試使用身分識別來登入的可能性。 [登錄風險策略](../../active-directory/identity-protection/overview.md)是條件訪問策略，用於向特定使用者或組評估風險級別。 根據風險層級 (高/中/低)，原則可以設定為封鎖存取，也可以設定為強制使用多重要素驗證。 請確定您有對中等以上風險的登入，強制使用多重要素驗證。

![從匿名 IP 登入](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>第 4 步 - 利用雲智慧

安全性相關事件的稽核和記錄及相關警示是有效保護策略的重要元件。 安全性記錄和報告會提供可疑活動的電子記錄，並協助您偵測可能指出從外部嘗試或成功滲透網路以及內部攻擊的模式。 您可以使用審核來監視使用者活動、記錄法規遵從性、進行取證分析等。 警示會提供安全性事件通知。

### <a name="monitor-azure-ad"></a>監視 Azure AD

Microsoft Azure 服務和功能提供可設定的安全性稽核和記錄選項，以協助您識別安全性原則和機制間的差距，並解決這些差距以協助防止破壞。 您可以使用 [Azure 記錄與稽核](log-audit.md)以及使用 [Azure Active Directory 入口網站中的稽核活動報告](../../active-directory/reports-monitoring/concept-audit-logs.md)。

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>在混合式環境中監視 Azure AD Connect Health

[使用 Azure AD Connect Health 監視 AD FS](../../active-directory/hybrid/how-to-connect-health-adfs.md) 可針對您的 AD FS 基礎結構，在潛在問題及攻擊的可見性上，為您提供更加深入的見解。 Azure AD Connect Health 會提供警示與詳細資料、解決步驟，以及相關文件的連結；提供數個驗證流量相關計量的使用情況分析；提供效能監控和報告。

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>監視 Azure AD Identity Protection 事件

[Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) 是一種通知、監視和報告工具，可用來偵測會影響組織身分識別的潛在弱點。 它檢測風險檢測，例如洩露的憑據、不可能的旅行以及來自受感染設備的登錄、匿名 IP 位址、與可疑活動關聯的 IP 位址以及未知位置。 請啟用通知警示，以接收有風險使用者的電子郵件和/或每週摘要電子郵件。

Azure AD Identity Protection 能提供兩個您應該每天監視的重要報告：
1. 具風險的登入報告將能顯示您應該調查的使用者登入活動，因為執行該登入的可能不是帳戶的合法使用者。
2. 具風險的使用者報告將能顯示可能已被入侵的使用者帳戶，例如偵測到外洩的認證，或是當使用者從兩個不可能在該時間範圍內於它們之間移動的不同位置登入時。

![標示有風險的使用者](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>稽核應用程式和已同意的權限

使用者可以被騙導航到受攻擊的網站或應用，這些網站或應用將訪問其個人資料資訊和使用者資料（如電子郵件）。 惡意的執行者可以使用其所接收到的已同意權限來對使用者的信箱內容進行加密，並要求使用者支付贖金以重新存取其信箱資料。 [管理員應查看和審核](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants)使用者授予的許可權，或禁用使用者預設同意的許可權。

除了審核使用者授予的許可權外，您還可以在高級環境中[找到有風險或不需要的 OAuth 應用程式](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth)。

## <a name="step-5---enable-end-user-self-service"></a>第 5 步 - 啟用最終使用者自助服務

請盡可能地在安全性與生產力之間取得平衡。 和運用打下長遠安全性基礎的心態來著手處理旅途的思路相同，您可以藉由既讓使用者獲得能力同時又保持警覺的方式，去除組織中的分歧意見。

### <a name="implement-self-service-password-reset"></a>實作自助密碼重設

Azure AD 的[自助服務密碼重設 （SSPR）](../../active-directory/authentication/quickstart-sspr.md)為 IT 管理員提供了一種簡單的方法，允許使用者重置或解鎖其密碼或帳戶，而無需説明台或管理員干預。 該系統包括詳細報告，跟蹤使用者重置密碼的時間，以及提醒您誤用或濫用的通知。

### <a name="implement-self-service-group-and-application-access"></a>實現自助服務組和應用程式訪問

Azure AD 為非管理員提供了使用安全性群組、Office 365 組、應用程式角色和訪問包目錄管理對資源的訪問的能力。  [自助服務組管理](../../active-directory/users-groups-roles/groups-self-service-management.md)使組擁有者能夠管理自己的組，而無需分配管理角色。 使用者還可以創建和管理 Office 365 組，而無需依賴管理員來處理其請求，未使用的組將自動過期。  [Azure AD 授權管理](../../active-directory/governance/entitlement-management-overview.md)進一步支援委派和可見度，具有全面的訪問請求工作流和自動過期功能。  您可以將配置自己的訪問包的能力委託給非管理員，以便為其擁有的組、團隊、應用程式和 SharePoint Online 網站配置自己的訪問包，並針對需要誰批准存取權限的自訂策略，包括配置員工的訪問經理和業務合作夥伴贊助者作為批准人。

### <a name="implement-azure-ad-access-reviews"></a>實作 Azure AD 存取權檢閱

使用[Azure AD 訪問審核](../../active-directory/governance/access-reviews-overview.md)，可以管理訪問包和組成員身份、對企業應用程式的訪問以及特權角色指派，以確保維護安全標準。  使用者本身、資源擁有者和其他檢閱者定期進行監督，確保使用者在不再需要存取權限時不會長時間保留存取權限。

## <a name="summary"></a>總結

安全的身分識別基礎結構涉及許多層面，但這份有五個步驟的檢查清單會協助您快速完成更安全可靠的身分識別基礎結構：

* 強化認證。
* 減少受攻擊面。
* 將威脅回應自動化。
* 利用雲智慧。
* 透過自助服務實現更具預測性且更完整的使用者安全性。

我們非常感謝您如此嚴肅地看待身分識別安全性，希望這份文件有助於讓貴組織達成更安全的狀態。

## <a name="next-steps"></a>後續步驟

如果您需要協助來規劃和部署各項建議，請參閱 [Azure AD 專案部署方案](https://aka.ms/deploymentplans)中的說明。

如果您確信所有這些步驟都已完成，請使用 Microsoft 的[標識安全分數](../../active-directory/fundamentals/identity-secure-score.md)，這將讓您瞭解[最新的最佳實踐](identity-management-best-practices.md)和安全威脅。
