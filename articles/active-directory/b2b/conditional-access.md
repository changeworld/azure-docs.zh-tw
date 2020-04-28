---
title: B2B 共同作業使用者的條件式存取-Azure AD
description: Azure Active Directory B2B 共同作業支援多重要素驗證 (MFA) 以對您的公司應用程式進行選擇性存取
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0b6ceba4c3c9202e2024b5c163c0e98bb6cbf55
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74273004"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>適用于 B2B 共同作業使用者的條件式存取

## <a name="multi-factor-authentication-for-b2b-users"></a>B2B 使用者的多重要素驗證
在使用 Azure AD B2B 共同作業的情況下，組織會為 B2B 使用者強制執行 Multi-Factor Authentication (MFA) 原則。 這些原則可以在租用戶層級、應用程式或個別使用者層級強制執行，也同樣針對組織的全職員工和成員啟用。 MFA 原則會在資源組織強制執行。

範例：
1. 公司 A 中的系統管理員或資訊工作者邀請公司 B 的使用者使用公司 A 的應用程式 *Foo*。
2. 公司 A 中的應用程式 *Foo* 是設定為要求在存取時使用 MFA。
3. 當公司 B 的使用者嘗試存取公司 A 租用戶的應用程式 *Foo* 時，系統會要求他們完成 MFA 查問。
4. 使用者可以設定他們的公司 A MFA，選擇其 MFA 選項。
5. 此案例對任何身分識別 (例如 Azure AD 或 MSA，若公司 B 的使用者使用社交識別碼來驗證) 都有效
6. 公司 A 必須有足夠支援 MFA 的 Azure AD Premium 授權。 公司 B 的使用者會取用公司 A 的授權。

發出邀請的租用一律必須負責夥伴組織使用者的 MFA，即使夥伴組織本身具有 MFA 能力。

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>為 B2B 共同作業使用者設定 MFA
若要了解為 B2B 共同作業使用者設定 MFA 有多簡單，請觀看下列影片：

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>供應項目兌換的 B2B 使用者 MFA 體驗
觀看下面的動畫以了解兌換體驗：

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>B2B 共同作業使用者的 MFA 重設
目前，系統管理員只能使用 PowerShell Cmdlet 要求 B2B 共同作業使用者重新證明：

1. 連接至 Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. 取得具有證明方法的所有使用者

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   範例如下：

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. 重設特定使用者的 MFA 方法，要求 B2B 共同作業使用者再次設定證明方法。 範例：

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>為什麼我們要在資源租用執行 MFA？

在目前的版本中，基於預測性的原因，MFA 一律位在資源租用中。 例如，假設 Contoso 使用者 (Sally) 受邀到 Fabrikam，而且 Fabrikam 已經為 B2B 使用者啟用 MFA。

如果 Contoso 為 App1 啟用了 MFA 原則，沒有為 App2 啟用，則若查閱權杖中的 Contoso MFA 宣告，可能會發現下列問題：

* 第 1 天：使用者有 Contoso 的 MFA 並存取 App1，Fabrikam 不顯示其他 MFA 提示。

* 第 2 天：使用者存取了 Contoso 的 App2，而當他們要存取 Fabrikam 時，必須註冊那裡的 MFA。

此程序令人混淆，而且可能會中斷登入作業。

此外，即使 Contoso 具有 MFA 功能，Fabrikam 也未必一定會信任 Contoso MFA 原則。

最後，資源租用戶 MFA 也適用於 MSA 和社交識別碼，以及未設定 MFA 的合作夥伴組織。

因此，針對 B2B 使用者的 MFA 建議是一律要求使用邀請方租用戶的 MFA。 在某些情況下，此要求可能會導致雙重 MFA，但每次存取邀請方租用戶時的使用者體驗都會是可預測的：Sally 必須向邀請方租用戶註冊 MFA。

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>適用于 B2B 使用者的裝置型、以位置為基礎和風險型條件式存取

當 Contoso 針對其公司資料啟用裝置型條件式存取原則時，會防止不受 Contoso 管理且不符合 Contoso 裝置原則的裝置進行存取。

如果 B2B 使用者的裝置未受 Contoso 管理，當 B2B 使用者從夥伴組織進行存取時，只要是在強制執行這些原則的內容中，系統就會封鎖其存取。 不過，Contoso 可以建立包含特定合作夥伴使用者的排除清單，以從裝置型條件式存取原則中排除這些專案。

#### <a name="mobile-application-management-policies-for-b2b"></a>適用于 B2B 的行動應用程式管理原則

條件式存取應用程式保護原則無法套用至 B2B 使用者，因為邀請的組織無法看到 B2B 使用者的主要組織。

#### <a name="location-based-conditional-access-for-b2b"></a>適用于 B2B 的以位置為基礎的條件式存取

如果邀請的組織能夠建立可定義其合作夥伴組織的信任 IP 位址範圍，就可以針對 B2B 使用者強制執行以位置為基礎的條件式存取原則。

#### <a name="risk-based-conditional-access-for-b2b"></a>B2B 的以風險為基礎的條件式存取

目前，風險型登入原則無法套用至 B2B 使用者，因為風險評估是在 B2B 使用者的主要組織執行。

## <a name="next-steps"></a>後續步驟

請參閱下列有關 Azure AD B2B 共同作業的文章：

* [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
* [Azure AD B2B 共同作業授權](licensing-guidance.md)
* [Azure Active Directory B2B 共同作業常見問題 (FAQ)](faq.md)
