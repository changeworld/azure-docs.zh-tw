---
title: Azure Active Directory Identity Protection 是什麼？
description: 使用 Azure AD Identity Protection 偵測、補救、調查和分析風險
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 08/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.custom: contperf-fy21q1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 242e0e4614994c30d0a14b8fe3d7a5c2b217bb5a
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033335"
---
# <a name="what-is-identity-protection"></a>什麼是 Identity Protection？

Identity Protection 是一個可讓組織完成三項主要工作的工具：

- 自動偵測及補救以身分識別為基礎的風險。
- 使用入口網站中的資料調查風險。
- 將風險偵測資料匯出至第三方公用程式，以進行進一步的分析。

Identity Protection 使用 Microsoft 從 Azure AD 的組織中、Microsoft 帳戶的取用者空間，以及 Xbox 的遊戲中獲得的經驗，來保護您的使用者。 Microsoft 每天會分析 6.5 兆個信號，以識別威脅並保護客戶免於遭受威脅。

由 Identity Protection 產生及送至該處的信號，可進一步送至條件式存取之類的工具以做出存取決策，或送回至安全性資訊和事件管理 (SIEM) 工具，以根據您的組織強制執行的原則進行深入調查。

## <a name="why-is-automation-important"></a>自動化有何重要性？

Alex Weinert 是 Microsoft 身分識別安全性與保護小組的領導人，他在其 [2018 年 10 月的部落格文章](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843)中，說明了自動化在處理大量事件時的重要性：

> 我們的機器學習和啟發式系統每天都會針對超過 8 億個不同的帳戶提供 180 億次登入嘗試的風險分數，其中有 3 億次出自敵人之手，例如犯罪執行者、駭客等實體。
>
> 在去年的 Ignite 中，我談到了我們的身分識別系統遭受到的前 3 大攻擊。 以下是這些攻擊最近的數據
>   
>   - **缺口重新執行**：在 2018 年 5 月偵測到 4.6BN 次攻擊
>   - **密碼噴灑**：2018 年 4 月 350k 次
>   - **網路釣魚**：這很難精確量化，但我們在 2018 年 3 月觀察到 23M 個風險事件，其中有許多都與網路釣魚有關

## <a name="risk-detection-and-remediation"></a>風險偵測和補救

Identity Protection 會識別下列分類的風險：

| 風險偵測類型 | 描述 |
| --- | --- |
| 非慣用登入位置 | 以使用者最近的登入為準，從非慣用的位置登入。 |
| 匿名 IP 位址 | 從匿名 IP 位址登入 (例如：Tor 瀏覽器、Anonymizer VPN)。 |
| 不熟悉的登入屬性 | 以給定使用者近期未出現過的屬性登入。 |
| 已連結惡意程式碼的 IP 位址 | 從已連結惡意程式碼的 IP 位址登入。 |
| 認證外洩 | 指出使用者的有效認證已外洩。 |
| 密碼噴灑 | 指出有攻擊者正以一致的暴力密碼破解方式，使用常見的密碼來攻擊多個使用者名稱。 |
| Azure AD 威脅情報 | Microsoft 的內部和外部威脅情報來源已識別出已知的攻擊模式。 |

如需這些風險的詳細資訊及其計算方式/時機，請參閱[什麼是風險](concept-identity-protection-risks.md)一文。

風險信號可觸發補救工作，例如要求使用者：執行 Azure AD Multi-Factor Authentication、使用自助式密碼重設進行密碼重設，或在系統管理員採取動作之前進行封鎖。

## <a name="risk-investigation"></a>風險調查

系統管理員可以檢閱偵測，並視需要對其採取手動動作。 在 Identity Protection 中，系統管理員主要會使用三項個報告進行調查：

- 具風險使用者
- 有風險的登入
- 風險偵測

如需詳細資訊，請參閱下列文章：[如何：調查風險](howto-identity-protection-investigate-risk.md)。

### <a name="risk-levels"></a>風險層級

身分識別保護會將風險分類成三個層級：低、中和高。 

雖然 Microsoft 並未提供如何計算風險的特定詳細資料，但我們會假設每個層級針對使用者或登入遭到入侵時都具備更高的信賴度。 例如，相較於使用者的其中一個執行個體出現不熟悉的登入屬性，將認證洩漏給其他使用者的情況比較嚴重。

## <a name="exporting-risk-data"></a>匯出風險資料

Identity Protection 中的資料可匯出至其他工具進行封存，以及供進一步的調查和相互關聯使用。 以 Microsoft Graph 為基礎的 API 可讓組織收集這項資料，以便在 SIEM 之類的工具中進一步處理。 如需如何存取 Identity Protection API 的相關資訊，請參閱[開始使用 Azure Active Directory Identity Protection 和 Microsoft Graph](howto-identity-protection-graph-api.md) 一文

如需整合 Identity Protection 資訊與 Azure Sentinel 的相關資訊，請參閱[從 Azure AD Identity Protection 連接資料](../../sentinel/connect-azure-ad-identity-protection.md)一文。

## <a name="permissions"></a>權限

Identity Protection 要求使用者必須是安全性讀取者、安全性操作員、安全性系統管理員、全域讀取者或全域管理員，才能加以存取。

| 角色 | 可以執行 | 不可執行 |
| --- | --- | --- |
| 全域管理員 | 完整存取 Identity Protection |   |
| 安全性系統管理員 | 完整存取 Identity Protection | 重設使用者的密碼 |
| 安全性操作員 | 查看所有身分識別保護報告和 [概觀] 刀鋒視窗 <br><br> 解除使用者風險、確認安全登入、確認洩漏 | 設定或變更原則 <br><br> 重設使用者的密碼 <br><br> 設定警示 |
| 安全性讀取者 | 查看所有身分識別保護報告和 [概觀] 刀鋒視窗 | 設定或變更原則 <br><br> 重設使用者的密碼 <br><br> 設定警示 <br><br> 在偵測上提供意見反應 |

目前，安全性操作員角色無法存取有風險的登入報告。

條件式存取系統管理員也可以建立原則來將登入風險視為條件。 在下列文章中尋找詳細資訊：[條件式存取：條件](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)。

## <a name="license-requirements"></a>授權需求

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| 功能 | 詳細資料  | Azure AD Free / Microsoft 365 應用程式 | Azure AD Premium P1|Azure AD Premium P2 |
| --- | --- | --- | --- | --- |
| 風險原則 | 使用者風險原則 (透過 Identity Protection)  | 否 | 否 |是 | 
| 風險原則 | 登入風險原則 (透過 Identity Protection 或條件式存取)  | 否 |  否 |是 |
| 安全性報告 | 概觀 |  否 | 否 |是 |
| 安全性報告 | 具風險使用者  | 有限資訊。 只會顯示具有中等和高風險的使用者。 沒有詳細資料選單，也沒有風險歷程記錄。 | 有限資訊。 只會顯示具有中等和高風險的使用者。 沒有詳細資料選單，也沒有風險歷程記錄。 | 完整存取|
| 安全性報告 | 有風險的登入  | 有限資訊。 不會顯示任何風險詳細資料或風險層級。 | 有限資訊。 不會顯示任何風險詳細資料或風險層級。 | 完整存取|
| 安全性報告 | 風險偵測   | 否 | 有限資訊。 沒有詳細資料選單。| 完整存取|
| 通知 | 偵測到具風險使用者的警示  | 否 | 否 |是 |
| 通知 | 每週提要| 否 | 否 | 是 | 
| | MFA 註冊原則 | 否 | 否 | 是 |

如需這些豐富報告的詳細資訊，請參閱下列文章：[如何：調查風險](howto-identity-protection-investigate-risk.md#navigating-the-reports)。

## <a name="next-steps"></a>後續步驟

- [安全性概觀](concept-identity-protection-security-overview.md)

- [什麼是風險](concept-identity-protection-risks.md)

- [可用來降低風險的原則](concept-identity-protection-policies.md)
