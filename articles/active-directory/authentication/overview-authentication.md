---
title: Azure Active Directory 驗證概觀
description: 了解使用 Azure Active Directory 的使用者登入適用的不同驗證方法和安全性功能。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 01/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4c2fa8488490561e8f11746e8e737718ee9f37
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "76261257"
---
# <a name="what-is-azure-active-directory-authentication"></a>什麼是 Azure Active Directory 驗證？

身分識別平台的主要功能之一，是在使用者登入裝置、應用程式或服務時確認或*驗證*認證。 在 Azure Active Directory (Azure AD) 中，驗證所做的不只是使用者名稱和密碼的驗證。 為了提升安全性並降低對技術支援中心的協助需求，Azure AD 驗證納入了下列元件：

* 自助式密碼重設
* Azure Multi-Factor Authentication
* 將密碼變更寫回內部部署環境的混合式整合
* 對內部部署環境強制執行密碼保護原則的混合式整合
* 無密碼驗證

## <a name="improve-the-end-user-experience"></a>改善使用者體驗

Azure AD 有助於保護使用者的身分識別及簡化其登入體驗。 自助式密碼重設之類的功能，可讓使用者從任何裝置使用網頁瀏覽器來更新或變更其密碼。 這項功能在使用者忘記其密碼或其帳戶遭到鎖定時特別有用。 使用者無須等待技術支援中心或系統管理員提供支援，即可自行解除封鎖並繼續工作。

Azure Multi-Factor Authentication 可讓使用者在登入期間選擇其他形式的驗證，例如電話或行動代理程式通知。 這項功能可減少對於單一、固定形式的次要驗證 (例如硬體權杖) 的需求。 如果使用者目前沒有某種形式的額外驗證，他們可以選擇不同的方法並繼續工作。

![在 [登入] 畫面使用中的驗證方法](media/concept-authentication-methods/overview-login.png)

使用無密碼驗證時，使用者無須建立並記住安全密碼。 Windows Hello 企業版或 FIDO2 安全性金鑰之類的功能，可讓使用者直接登入裝置或應用程式而無須使用密碼。 這項功能可降低在不同環境間管理密碼的複雜性。

## <a name="self-service-password-reset"></a>自助式密碼重設

自助式密碼重設可讓使用者直接變更或重設其密碼，而無需系統管理員或技術支援中心介入。 如果使用者的帳戶遭到鎖定，或使用者忘記其密碼，他們可以依照提示自行解除封鎖，並繼續工作。 當使用者無法登入其裝置或應用程式時，這項功能將可減少技術服務中心的通話量，並避免失去生產力。

自助式密碼重設適用於下列情況：

* **密碼變更 –** 當使用者知道其密碼，但想要變更為新密碼時。
* **密碼重設 -** 當使用者無法登入 (例如，當他們忘記密碼時)，而想要重設密碼時。
* **帳戶解除鎖定 -** 當使用者因其帳戶遭鎖定而無法登入，因此想要將其解除鎖定時。

當使用者使用自助式密碼重設來更新或重設其密碼時，該密碼也可以寫回內部部署 Active Directory 環境。 密碼回寫可確保使用者能夠立即將更新的認證用於內部部署裝置和應用程式。

## <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

多重要素驗證是使用者在登入過程中經提示而提供其他形式的識別 (例如，在其行動電話上輸入代碼或提供指紋掃描) 的程序。

如果僅使用密碼來驗證使用者，將會有不安全的媒介可能遭到攻擊。 如果密碼不嚴謹或已於他處公開，那麼以使用者名稱和密碼登入的究竟是使用者，還是攻擊者？ 當您要求第二種形式的驗證時，安全性將會增加，因為這項額外的因素並不容易讓攻擊者取得或複製。

![不同多重要素驗證形式的概念影像](./media/concept-mfa-howitworks/methods.png)

Azure Multi-Factor Authentication 的運作需要下列二種或更多的驗證方法：

* 您知道的某些資訊 (通常是密碼)。
* 您擁有的某些資訊，例如不容易複製的信任裝置 (如電話或硬體金鑰)。
* 代表您個人身分的某些資訊 - 指紋或臉部掃描之類的生物識別特徵。

使用者可以在單一步驟中將其身分註冊至自助式密碼重設和 Azure Multi-Factor Authentication，以簡化上線體驗。 系統管理員可定義可以使用的次要驗證形式。 在使用者執行自助式密碼重設以進一步保護該程序時，也可以要求 Azure Multi-Factor Authentication。

## <a name="password-protection"></a>密碼保護

根據預設，Azure AD 會封鎖弱式密碼，例如 *Password1*。 系統會自動更新並強制執行包含已知弱式密碼的全域禁用密碼清單。 Azure AD 使用者若嘗試將其密碼設定為其中一個弱式密碼，則會收到一則通知，要求他們選擇更安全的密碼。

若要提高安全性，您可以定義自訂密碼保護原則。 這些原則可以使用篩選條件來封鎖任何包含 *Contoso* 之類的名稱或 *London* 等位置的密碼變化。

為了達到混合式安全性，您可以整合 Azure AD 密碼保護與內部部署 Active Directory 環境。 安裝在內部部署環境中的元件，會取得 Azure AD 提供的全域禁用密碼清單和自訂密碼保護原則，且網域控制站會使用這些項目來處理密碼變更事件。 這種混合式方法可確保不論使用者變更認證的方式或位置為何，您都必須強制使用強式密碼。

## <a name="passwordless-authentication"></a>無密碼驗證

許多環境的最終目標，都是要在登入事件中去除密碼的使用。 Azure 密碼保護或 Azure Multi-Factor Authentication 之類的功能雖然有助於改善安全性，但使用者名稱和密碼仍是弱式形式的驗證，有可能暴露或遭到暴力破解攻擊。

![無密碼驗證程序的安全性與便利性](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

當您使用無密碼方法登入時，將會透過 Windows Hello 企業版或 FIDO2 安全性金鑰的生物識別特徵之類的方法來提供認證。 攻擊者無法輕易複製這些驗證方法。

Azure AD 提供了使用無密碼方法進行原生驗證的方式，以簡化使用者的登入體驗，並降低遭受攻擊的風險。

## <a name="next-steps"></a>後續步驟

若要開始使用，請參閱[自助式密碼重設的快速入門][quickstart-sspr]和 [Azure Multi-Factor Authentication 教學課程][tutorial-mfa-applications]。

若要深入了解自助式密碼重設的概念，請參閱 [Azure AD 自助式密碼重設的運作方式][concept-sspr]。

若要深入了解多重要素驗證的概念，請參閱 [Azure Multi-Factor Authentication 的運作方式][concept-mfa]。

<!-- INTERNAL LINKS -->
[quickstart-sspr]: quickstart-sspr.md
[tutorial-mfa-applications]: tutorial-mfa-applications.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
