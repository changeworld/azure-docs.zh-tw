---
title: B2B 共同作業使用者的條件式存取-Azure AD
description: 瞭解如何為 Azure Active Directory B2B 使用者強制執行多重要素驗證原則。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42b3c3d4d474c61cbe472b4122ac2f80f218bf8d
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797274"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>B2B 共同作業使用者的條件式存取

本文說明組織如何針對 B2B 來賓使用者的條件式存取範圍 (CA) 原則，以存取其資源。
>[!NOTE]
>這種驗證或授權流程與身分識別提供者的現有使用者 (IdP) 的來賓使用者稍有不同。

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>來自外部目錄之 B2B 來賓使用者的驗證流程

下圖說明流程：影像會 ![ 顯示來自外部目錄之 B2B 來賓使用者的驗證流程](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| 步驟 | 描述 |
|--------------|-----------------------|
| 1. | B2B 來賓使用者要求存取資源。 資源會將使用者重新導向至其資源租使用者，也就是信任的 IdP。|
| 2. | 資源租使用者會將使用者識別為外部使用者，並將使用者重新導向至 B2B 來賓使用者的 IdP。 使用者會在 IdP 中執行主要驗證。
| 3. | B2B 來賓使用者的 IdP 會向使用者發出權杖。 使用者會被重新導向回具有權杖的資源租使用者。 資源租使用者會驗證權杖，然後根據其 CA 原則來評估使用者。 例如，資源租使用者可能需要使用者執行 (AD) Multi-Factor Authentication 的 Azure Active Directory。
| 4. | 滿足所有資源租使用者 CA 原則之後，資源租使用者會發出自己的權杖，並將使用者重新導向至其資源。

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>具有單次密碼之 B2B 來賓使用者的驗證流程

下圖說明流程：影像會 ![ 顯示具有單次密碼之 B2B 來賓使用者的驗證流程](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| 步驟 | 描述 |
|--------------|-----------------------|
| 1. |使用者要求存取另一個租使用者中的資源。 資源會將使用者重新導向至其資源租使用者，也就是信任的 IdP。|
| 2. | 資源租使用者會將使用者識別為 [外部電子郵件一次性密碼 (OTP) 使用者](https://docs.microsoft.com/azure/active-directory/external-identities/one-time-passcode) ，並將具有 OTP 的電子郵件傳送給使用者。|
| 3. | 使用者會抓取 OTP 並提交程式碼。 資源租使用者會根據其 CA 原則來評估使用者。
| 4. | 滿足所有 CA 原則之後，資源租使用者會發出權杖，並將使用者重新導向至其資源。 |

>[!NOTE]
>如果是來自外部資源租使用者的使用者，則不可能也會評估 B2B 來賓使用者的 IdP CA 原則。 從今天起，只有資源租使用者的 CA 原則會套用至其來賓。

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>B2B 使用者的 Azure AD Multi-Factor Authentication

組織可以針對 B2B 來賓使用者強制執行多個 Azure AD Multi-Factor Authentication 原則。 這些原則可以在租使用者、應用程式或個別使用者層級強制執行，其方式與為組織的全職員工和成員啟用時相同。
資源租使用者一律負責 Azure AD 的 Multi-Factor Authentication 給使用者，即使來賓使用者的組織有 Multi-Factor Authentication 的功能。 範例如下：

1. 名為 Fabrikam 的公司中的系統管理員或資訊工作者邀請來自另一家名為 Contoso 的使用者，以使用其應用程式 Woodgrove。

2. Fabrikam 中的 Woodgrove 應用程式設定為需要 Azure AD Multi-Factor Authentication 存取權。

3. 當 Contoso 的 B2B 來賓使用者嘗試存取 Fabrikam 租使用者中的 Woodgrove 時，系統會要求他們完成 Azure AD Multi-Factor Authentication 挑戰。

4. 來賓使用者接著可以使用 Fabrikam 設定其 Azure AD Multi-Factor Authentication，然後選取選項。

5. 此案例適用于任何身分識別– Azure AD 或個人 Microsoft 帳戶 (MSA) 。 例如，如果 Contoso 中的使用者使用社交識別碼進行驗證。

6. Fabrikam 必須有足夠的 premium Azure AD 授權，以支援 Azure AD Multi-Factor Authentication。 然後，來自 Contoso 的使用者會從 Fabrikam 取用此授權。 如需 B2B 授權的詳細資訊，請參閱 Azure AD 外部身分識別的 [計費模型](https://docs.microsoft.com/azure/active-directory/external-identities/external-identities-pricing) 。

>[!NOTE]
>Azure AD Multi-Factor Authentication 是在資源租使用者中完成，以確保可預測性。

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>設定 B2B 使用者的 Azure AD Multi-Factor Authentication

若要設定 B2B 共同作業使用者的 Azure AD Multi-Factor Authentication，請觀看這段影片：

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>適用于供應專案兌換的 B2B 使用者 Azure AD Multi-Factor Authentication

若要深入瞭解 Azure AD Multi-Factor Authentication 兌換體驗，請觀賞這段影片：

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>B2B 使用者的 Azure AD Multi-Factor Authentication 重設

現在，您可以使用下列 PowerShell Cmdlet 來證明 B2B 來賓使用者：

1. 連接至 Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. 取得具有證明方法的所有使用者

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   請看以下範例：

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. 重設特定使用者的 Azure AD Multi-Factor Authentication 方法，以要求 B2B 共同作業使用者再次設定證明方法。 
   請看以下範例：

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>B2B 使用者的條件式存取

有各種因素會影響 B2B 來賓使用者的 CA 原則。

### <a name="device-based-conditional-access"></a>以裝置為基礎的條件式存取

在 CA 中，有一個選項可要求使用者的 [裝置符合規範或混合式 Azure AD 加入](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#device-state-preview)。 如果資源租使用者可以管理其裝置，則 B2B 來賓使用者只能滿足合規性。 一次只能有一個以上的組織管理裝置。 B2B 來賓使用者無法滿足混合式 Azure AD 聯結，因為他們沒有內部部署 AD 帳戶。 只有當來賓使用者的裝置未受管理時，他們才能在資源租使用者中註冊或註冊其裝置，然後使裝置符合規範。 然後，使用者就可以滿足 grant 控制項。

>[!Note]
>不建議針對外部使用者要求受控裝置。

### <a name="mobile-application-management-policies"></a>行動應用程式管理原則

CA 授與的控制項（例如 **要求已核准的用戶端應用程式** ，以及需要 **應用程式保護原則** ）需要在租使用者中註冊裝置。 這些控制項只能套用至 [iOS 和 Android 裝置](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#device-platforms)。 不過，如果使用者的裝置已由其他組織管理，則這些控制項都無法套用至 B2B 來賓使用者。 一次只能在一個以上的租使用者中註冊行動裝置。 如果行動裝置是由另一個組織所管理，將會封鎖該使用者。 只有當來賓使用者的裝置未受管理時，他們才能在資源租使用者中註冊其裝置。 然後，使用者就可以滿足 grant 控制項。  

>[!NOTE]
>不建議針對外部使用者要求應用程式保護原則。

### <a name="location-based-conditional-access"></a>以位置為基礎的條件式存取

如果邀請的組織可以建立定義其夥伴組織的信任 IP 位址範圍，就可以強制執行以 IP 範圍為基礎的 [位置原則](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#locations) 。

也可以根據 **地理位置** 強制執行原則。

### <a name="risk-based-conditional-access"></a>以風險為基礎的條件式存取

如果 B2B 來賓使用者滿足 grant 控制項，則會強制執行登 [入風險原則](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#sign-in-risk) 。 例如，組織可能需要 Azure AD Multi-Factor Authentication 進行中或高的登入風險。 不過，如果使用者先前尚未在資源租使用者中註冊 Azure AD Multi-Factor Authentication，將會封鎖該使用者。 這樣做的目的是為了防止惡意使用者在洩漏合法使用者的密碼時，註冊自己的 Azure AD Multi-Factor Authentication 認證。

但是，無法在資源租使用者中解析 [使用者風險原則](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#user-risk) 。 例如，如果您需要對高風險來賓使用者進行密碼變更，系統將會封鎖這些使用者，因為無法重設資原始目錄中的密碼。

### <a name="conditional-access-client-apps-condition"></a>條件式存取用戶端應用程式條件

對 B2B 來賓使用者而言，[用戶端應用程式](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps)的行為與針對任何其他類型的使用者所進行的行為相同。 例如，您可以防止來賓使用者使用舊版驗證通訊協定。

### <a name="conditional-access-session-controls"></a>條件式存取會話控制項

B2B 來賓使用者的[會話控制項](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-session)行為與針對任何其他類型的使用者一樣。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列有關 Azure AD B2B 協同作業的文章：

- [何謂 Azure AD B2B 共同作業？](https://docs.microsoft.com/azure/active-directory/external-identities/what-is-b2b)
- [Identity Protection 和 B2B 使用者](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-b2b)
- [外部身分識別定價](https://azure.microsoft.com/pricing/details/active-directory/)
- [常見問題集 (FAQ)](https://docs.microsoft.com/azure/active-directory/external-identities/faq)

