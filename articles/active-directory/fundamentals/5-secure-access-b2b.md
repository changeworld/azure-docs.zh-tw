---
title: 使用 Azure Active Directory B2B 共同作業轉換為控管協同作業
description: 使用 Azure Ad B2B 共同作業移至受控共同作業。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96a671d44133a6f508cc5026e06fc6a56b9e6f20
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97743920"
---
# <a name="transition-to-governed-collaboration-with-azure-active-directory-b2b-collaboration"></a>使用 Azure Active Directory B2B 共同作業來轉換為控管共同作業 

讓您在掌控下共同作業是保護資源外部存取的關鍵。 在繼續本文之前，請確定您有：

* [判斷您的安全性狀態](1-secure-access-posture.md)

* [探索到您目前的狀態](2-secure-access-current-state.md)

* [已建立安全性方案](3-secure-access-plan.md)

* [瞭解群組和安全性如何一起運作](4-secure-access-groups.md)

完成這些動作之後，您就可以開始進行受控制的共同作業。 本文將引導您將所有外部共同作業移至 [AZURE ACTIVE DIRECTORY b2b](../external-identities/what-is-b2b.md) 共同作業 (Azure AD b2b) 。 Azure Ad B2B 是 [Azure AD 外部](../external-identities/compare-with-b2c.md)身分識別的功能。

## <a name="control-who-your-organization-collaborates-with"></a>控制組織的共同工作

您必須決定是否要限制使用者可以共同作業的組織，以及您組織中的誰可以起始共同作業。 大部分的組織都採用允許業務單位決定其共同作業的方法，並視需要委派核准和監督。 例如，某些政府、教育和金融服務組織不允許開放共同作業。 您可能會想要使用 Azure AD 功能來界定協同作業的範圍，如本節的其餘部分所述。

### <a name="determine-collaboration-partners"></a>判斷共同合作夥伴

首先，請確定您已記載目前正在共同作業的組織，以及這些組織使用者的網域。 一個共同作業夥伴可能會有多個網域。 例如，合作夥伴可能會有多個具有不同網域的業務單位。

接下來，請判斷您是否想要啟用未來的合作 

* 任何網域 (最內含) 

* 除了明確拒絕以外的所有網域 

* 只有特定網域 (限制最嚴格) 

> [!NOTE]
> 您的共同作業設定限制愈多，您的使用者就越可能在您核准的共同作業架構之外。 我們建議您啟用安全性需求最廣泛的共同作業，並仔細檢查該共同作業，而不是過於嚴格的合作。 

另請注意，限制在單一網域的情況下，可能會不慎防止與組織有其他不相關網域的公司進行授權的共同作業。 例如，如果與組織 Contoso 合作，則與 Contoso 的初始聯絡點可能是其中一個具有 ".com" 網域電子郵件的美國員工。 但是，如果您只允許 ".com" 網域，您可能會不小心略過其加拿大的員工（其為 "ca" 網域）。 

在某些情況下，您只想要允許特定的共同作業合作夥伴。 例如，大學系統可能只想要讓自己的教職員存取資源租使用者。 或者，集團可能只想要允許特定子公司彼此共同作業，以達成所需架構的合規性。

#### <a name="using-allow-and-deny-lists"></a>使用允許和拒絕清單

您可以使用允許清單或拒絕清單來限制來自特定組織的 [B2B 使用者的邀請](../external-identities/allow-deny-list.md) 。 您只能使用允許或拒絕清單，不能同時使用兩者。

* [允許清單](../external-identities/allow-deny-list.md)會將共同作業限制為僅限列出的網域;所有其他網域實際上都是在拒絕清單中。

* [拒絕清單](../external-identities/allow-deny-list.md)可以與不在拒絕清單中的任何網域進行共同作業。

> [!IMPORTANT]
> 這些清單並不適用于已在您目錄中的使用者。 它們也不適用於商務用 OneDrive 和 SharePoint 允許不同的拒絕清單。

某些組織會使用其受管理的安全性提供者所提供的已知「不良執行者」網域清單來取得其拒絕清單。 例如，如果組織合法地使用 Contoso 和 .com 網域來執行業務，則可能是使用 Contoso. org 網域以及嘗試網路釣魚攻擊來模擬 Contoso 員工的不相關組織。 

## <a name="control-how-external-users-gain-access"></a>控制外部使用者取得存取權的方式

使用 Azure AD B2B 來與外部夥伴共同作業的方式有很多種。 若要開始進行共同作業，您可以邀請或啟用您的合作夥伴以存取您的資源。 使用者可以藉由回應來取得存取權：

* 兌換透過 [電子郵件傳送的邀請](../external-identities/redemption-experience.md)，或 [共用資源的直接連結](../external-identities/redemption-experience.md) 。

* 透過您建立 [的應用程式](../external-identities/self-service-sign-up-overview.md) 要求存取權

* 透過 [我的存取權](../governance/entitlement-management-request-access.md) 入口網站要求存取權

當您啟用 Azure AD B2B 時，您將能夠透過預設的直接連結和電子郵件邀請來邀請來賓使用者。 透過電子郵件 OTP 和自助入口網站的邀請目前為預覽狀態，且必須在外部身分識別中啟用 |Azure AD 入口網站中的外部共同作業設定。

### <a name="control-who-can-invite-guest-users"></a>控制誰可以邀請來賓使用者

決定誰可以邀請來賓使用者存取資源。

* 最嚴格的設定是只允許系統管理員和授與 [來賓邀請者角色](../external-identities/delegate-invitations.md) 的使用者邀請來賓。

* 如果您的安全性需求允許，建議讓具有 userType 成員的所有使用者邀請來賓。

* 判斷您是否想要讓具有 Guest userType 的使用者（這是 Azure AD B2B 使用者的預設帳戶類型）能夠邀請其他來賓。 

![來賓邀請設定的螢幕擷取畫面。](media/secure-external-access/5-guest-invite-settings.png)

 

### <a name="collect-additional-information-about-external-users"></a>收集外部使用者的其他資訊

如果您使用 Azure AD 權利管理，您可以設定外部使用者的問題來回答。 然後會向核准者顯示問題，以協助他們做出決策。 您可以針對每個 [存取套件原則](../governance/entitlement-management-access-package-approval-policy.md) 設定不同的問題集，讓核准者可以有相關的資訊來存取他們所核准的存取權。 例如，如果某個存取套件適用于廠商存取，則可能會要求要求者提供其廠商合約號碼。 適用于供應商的不同存取套件可能會要求其來源國家/地區。

如果您使用自助入口網站，您可以使用 [API 連接器](../external-identities/api-connectors-overview.md) 來收集使用者註冊時的其他相關屬性。 然後，您可以使用這些屬性來指派存取權。 例如，如果您在註冊程式期間收集其供應商識別碼，您可以使用該屬性，將它們動態指派給該供應商的群組或存取套件。 您可以在 Azure 入口網站中建立自訂屬性，並在您的自助式註冊使用者流程中加以使用。 您也可以使用 [Microsoft Graph API](https://docs.microsoft.com/azure/active-directory-b2c/manage-user-accounts-graph-api) 來讀取和寫入這些屬性。 

### <a name="troubleshoot-invitation-redemption-to-azure-ad-users"></a>針對 Azure AD 使用者的邀請兌換進行疑難排解

有三個實例，當使用 Azure AD 的共同作業合作夥伴邀請來賓使用者時，會發生兌換邀請的問題。

* 如果使用允許清單，且使用者的網域未包含在允許清單中，則為。

* 如果共同作業合作夥伴的主租使用者有防止外部使用者共同作業的租使用者限制。

* 如果使用者不是合作夥伴 Azure AD 租使用者的一部分。 例如，在 contoso.com 的使用者中，只有 Active Directory (或其他內部部署 IdP) ，他們才能夠透過電子郵件 OTP 程式來兌換邀請。 如需詳細資訊，請參閱 [邀請兌換流程](../external-identities/redemption-experience.md)。

## <a name="control-what-external-users-can-access"></a>控制外部使用者可以存取的內容

大部分的組織都不是整合式。 也就是說，有一些資源可與外部使用者共用，而某些資源則不會讓外部使用者存取。 因此，您必須控制外部使用者的存取權。 請考慮使用 [權利管理和存取套件來控制](6-secure-access-entitlement-managment.md) 特定資源的存取權。

根據預設，來賓使用者可以查看租使用者成員和其他夥伴（包括群組成員資格）的相關資訊和屬性。 請考慮您的安全性需求是否呼叫，以限制外部使用者存取此資訊。

![設定外部共同作業設定的螢幕擷取畫面。](media/secure-external-access/5-external-collaboration-settings.png)

針對來賓使用者，我們建議下列限制。

* **限制來賓存取目錄中流覽群組和其他屬性**

   * 使用外部共同作業設定，以限制來賓讀取不是成員的群組的能力。

* **封鎖存取僅限員工的應用程式**。

   * 建立條件式存取原則，以封鎖僅適用于非來賓使用者的 Azure AD 整合式應用程式的存取。 

* **封鎖對 Azure 入口網站的存取。您可以進行罕見的必要例外** 狀況。 

   * 建立包含所有來賓和外部使用者的條件式存取原則，然後 [執行原則來封鎖存取](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)。

 

## <a name="remove-users-who-no-longer-need-access"></a>移除不再需要存取權的使用者

評估目前的存取權，讓您可以 [複習和移除不再需要存取權的使用者](../governance/access-reviews-external-users.md)。 將您租使用者中的外部使用者作為來賓，以及具有成員帳戶的使用者。 

某些組織將外部使用者（例如廠商、合作夥伴和承包商）新增為成員。 這些成員可以有特定的屬性，或以使用者名稱開頭的使用者名稱，例如

* v-適用于廠商

* p-適用于合作夥伴

* c-適用于承包商

評估任何具有成員帳戶的外部使用者，以判斷它們是否仍需要存取權。 若是如此，請將這些使用者轉換為 Azure AD B2B，如下一節所述。

您也可能有未受權利管理或 Azure AD B2B 邀請的來賓使用者

若要尋找這些使用者，您可以：

* [尋找未受權利管理邀請的來賓使用者](../governance/access-reviews-external-users.md)。

   * 我們提供 [範例 PowerShell 腳本。](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)

將這些使用者轉換為 Azure AD B2B 使用者，如下一節所述。

## <a name="transition-your-current-external-users-to-b2b"></a>將目前的外部使用者轉換為 B2B

如果您尚未使用 Azure AD B2B，您的租使用者可能有非員工的使用者。 建議您將這些帳戶轉換為 Azure AD B2B 外部使用者帳戶，然後將其 UserType 變更為來賓。 這可讓您利用許多方式 Azure AD 和 Microsoft 365 可讓您以不同的方式來處理外部使用者。 其中一些方法包括：

* 在條件式存取原則中輕鬆地包含或排除來賓使用者

* 輕鬆地包含或排除存取套件中的來賓使用者和存取權評論

* 輕鬆地包含或排除對小組、SharePoint 和其他資源的外部存取。

若要轉換這些內部使用者，同時維持其目前的存取權、UPN 和群組成員資格，請參閱 [邀請外部使用者進行 B2B](../external-identities/invite-internal-users.md)共同作業。

## <a name="decommission-undesired-collaboration-methods"></a>解除委任不希望的共同作業方法

若要完成轉換為控管的共同作業，您應該解除委任不想要的共同作業方法。 您解除委任的是根據您想要讓它進行共同作業的控制程度，以及您的安全性狀況。 如需有關它與使用者控制項的詳細資訊，請參閱 [判斷外部存取的安全性](1-secure-access-posture.md)狀態。

以下是您可能想要評估的協同作業車輛。

### <a name="direct-invitation-through-microsoft-teams"></a>透過 Microsoft 小組直接邀請

依預設，小組允許外部存取，這表示組織可以與所有外部網域通訊。 如果您想要限制或只允許小組進行特定網域，您可以在 [小組系統管理員入口網站](https://admin.teams.microsoft.com/company-wide-settings/external-communications)中這樣做。 


### <a name="direct-sharing-through-sharepoint-and-onedrive"></a>透過 SharePoint 和 OneDrive 的直接共用

透過 SharePoint 和 OneDrive 的直接共用可將使用者新增至權利管理程式之外。 若要深入瞭解這些設定，請參閱使用 [Microsoft 小組、SharePoint 和商務用 onedrive 來管理存取權](9-secure-access-teams-sharepoint.md) 。您也可以視需要 [封鎖使用者個人 OneDrive 的使用](https://docs.microsoft.com/office365/troubleshoot/group-policy/block-onedrive-use-from-office) 。

### <a name="sending-documents-through-email"></a>透過電子郵件傳送檔

您的使用者會透過電子郵件將檔傳送給外部使用者。 請考慮您要如何使用敏感度標籤來限制和加密這些檔的存取權。 如需詳細資訊，請參閱使用敏感度標籤管理存取權。

### <a name="unsanctioned-collaboration-tools"></a>待批准共同作業工具

協同作業工具的範圍很廣。 您的使用者可能會使用其官方責任的許多範圍，包括 Google 檔、DropBox、空隙或 Zoom 等平臺。 您可以從防火牆層級的公司網路，以及針對組織管理的裝置，使用行動應用程式管理來封鎖這類工具的使用。 不過，這也會封鎖這些平臺的任何獲批准實例，而且不會封鎖來自未受管理裝置的存取。 封鎖您不想在必要時使用的平臺，並針對您需要使用的平臺，建立無待批准使用的商務原則。 

如需管理待批准應用程式的詳細資訊，請參閱：

* [治理連線的應用程式](https://docs.microsoft.com/cloud-app-security/governance-actions)

* [批准和 unsanctioning 應用程式。](https://docs.microsoft.com/cloud-app-security/governance-discovery)

 
### <a name="next-steps"></a>後續步驟

請參閱下列文章，以保護對資源的外部存取。 建議您依列出的循序執行動作。

1. [判斷外部存取的安全性狀態](1-secure-access-posture.md)

2. [探索您目前的狀態](2-secure-access-current-state.md)

3. [建立治理計畫](3-secure-access-plan.md)

4. [使用群組進行安全性](4-secure-access-groups.md)

5. [轉換至 AZURE AD B2B](5-secure-access-b2b.md) (您在這裡。 ) 

6. [使用權利管理來保護存取權](6-secure-access-entitlement-managment.md)

7. [使用條件式存取原則保護存取](7-secure-access-conditional-access.md)

8. [使用敏感度標籤保護存取](8-secure-access-sensitivity-labels.md)

9. [安全存取 Microsoft 小組、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)