---
title: Azure Active Directory Identity Protection 詞彙
description: Azure Active Directory Identity Protection 詞彙
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a3e2df956aaa4f9fd0af83dd2a18e04d731c714
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232351"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory Identity Protection 詞彙

### <a name="at-risk-user"></a>有風險 (使用者)
具有一個或多個活動風險檢測的使用者。 

### <a name="atypical-sign-in-location"></a>非典型登入位置
從特定使用者、類似使用者或租用戶不常用的地理位置登入。

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Azure 活動目錄的安全模組，提供影響組織身份的風險檢測和潛在漏洞的整合視圖。

### <a name="conditional-access"></a>條件式存取
用來保護資源存取的原則。 條件訪問規則存儲在 Azure 活動目錄中，在授予對資源的訪問之前，Azure AD 會對其進行評估。  範例規則包括根據使用者位置、裝置健康狀態或使用者驗證方法來限制存取。

### <a name="credentials"></a>認證
包含識別碼以及用來取得存取本機和網路資源之識別證明的資訊。 認證範例包括使用者名稱和密碼、智慧卡和憑證。

### <a name="event"></a>事件
Azure Active Directory 中的活動記錄。

### <a name="false-positive-risk-detection"></a>誤報（風險檢測）
身份保護使用者手動設置的風險檢測狀態，指示風險檢測已過調查，並且錯誤地標記為風險檢測。

### <a name="identity"></a>身分識別
必須透過以密碼或憑證等準則為基礎的驗證作業來確認的個人或實體。

### <a name="identity-risk-detection"></a>身份風險檢測
Azure AD 事件被標識保護標記為異常，並可能表示標識已洩露。

### <a name="ignored-risk-detection"></a>忽略（風險檢測）
身份保護使用者手動設置的風險檢測狀態，指示風險檢測已關閉，無需執行補救操作。

### <a name="impossible-travel-from-atypical-locations"></a>不可能來自非典型位置的移動
當檢測到同一使用者的兩個登錄時觸發的風險檢測，其中至少有一個來自非典型登錄位置，並且登錄之間的時間短于在這些登錄之間實際移動所需的最短時間位置。  

### <a name="investigation"></a>調查
審查與風險檢測相關的活動、日誌和其他相關資訊的過程，以確定是否需要實施補救或緩解步驟，瞭解身份是否受損以及如何受損，並瞭解被洩露者的方式使用了標識。

### <a name="leaked-credentials"></a>認證外洩
當我們的研究人員在暗網中公開發布當前使用者憑據（使用者名和密碼）時觸發的風險檢測。

### <a name="mitigation"></a>降低
此動作可限制或消除攻擊者利用遭到入侵的身分識別或裝置的能力，但不需將身分識別或裝置還原至安全的狀態。 緩解不會解決以前與標識或設備關聯的風險檢測。

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
此種驗證方法需要兩個或更多驗證方法，其中可能包含使用者擁有的事物 (例如憑證)、使用者知道的事物 (例如使用者名稱、密碼或通關密語)、實體屬性 (例如指紋) 以及個人屬性 (例如個人簽章)。

### <a name="offline-detection"></a>離線偵測
針對已經發生的事件，偵測異常狀況及評估事件的風險 (例如事後的登入嘗試)。

### <a name="policy-condition"></a>原則條件
安全性原則的一部分，定義原則中包含或排除的實體 (群組、使用者、應用程式、裝置平台、裝置狀態、IP 範圍、用戶端類型)。

### <a name="policy-rule"></a>原則規則
安全性原則中描述將觸發策略的情況以及觸發策略時執行的操作的部分。

### <a name="prevention"></a>預防
預防藉由不當使用疑似或已知遭到入侵的身分識別或裝置來傷害組織的動作。 預防操作不保護設備或標識，並且不能解決以前的風險檢測。

### <a name="privileged-user"></a>特殊權限 (使用者)
在風險檢測時，對 Azure 活動目錄中的一個或多個資源（如全域管理員、計費管理員、服務管理員、使用者管理員和密碼）具有永久或臨時管理員許可權的使用者管理員。 

### <a name="real-time"></a>即時
請參閱即時偵測。

### <a name="real-time-detection"></a>即時偵測
在允許事件繼續進行之前，偵測異常狀況及評估事件的風險 (例如登入嘗試)。

### <a name="remediated-risk-detection"></a>修正（風險檢測）
由身份保護自動設置的風險檢測狀態，指示使用此類風險檢測的標準補救操作對風險檢測進行修正。 例如，重置使用者密碼時，許多指示前一個密碼已洩露的風險檢測將自動修復。

### <a name="remediation"></a>補救
用來保護先前疑似或已知遭到入侵的身分識別或裝置的動作。 修正操作將標識或設備還原到安全狀態，並解決以前與標識或設備關聯的風險檢測。

### <a name="resolved-risk-detection"></a>已解決（風險檢測）
身份保護使用者手動設置的風險檢測狀態，指示使用者在身份保護之外採取了適當的補救操作，並且風險檢測應視為已關閉。

### <a name="risk-detection-status"></a>風險檢測狀態
風險檢測的屬性，指示事件是否處於活動狀態，如果關閉，則指示關閉事件的原因。

### <a name="risk-detection-type"></a>風險偵測類型
用於風險檢測的類別，指示導致事件被視為有風險的異常類型。

### <a name="risk-level-risk-detection"></a>風險級別（風險檢測）
指示風險檢測的嚴重性（高、中或低），以説明身份保護使用者確定為減少組織風險而採取的行動的優先順序。 

### <a name="risk-level-sign-in"></a>風險層級 (登入)
指出特定登入 (其他人正嘗試使用使用者的身分識別) 的可能性 (高、中或低)。

### <a name="risk-level-user-compromise"></a>風險層級 (使用者入侵)
指出身分識別遭到入侵的可能性 (高、中或低)。

### <a name="risk-level-vulnerability"></a>風險層級 (弱點)
指出弱點的嚴重性 (高、中或低)，可協助 Identity Protection 使用者排定為了降低組織風險而採取之行動的優先順序。

### <a name="secure-identity"></a>安全 (身分識別)
採取補救動作 (例如變更密碼或機器重新安裝映像)，將可能遭到入侵的身分識別還原至未遭入侵的狀態。

### <a name="security-policy"></a>安全性原則
原則規則和條件的集合。 原則可以套用至各種實體，例如使用者、群組、應用程式、裝置、裝置平台、裝置狀態、IP 範圍和 Auth2.0 用戶端類型。 啟用原則後，就會在納入原則的實體被核發資源的權杖時進行評估。

### <a name="sign-in-v"></a>登入 (動詞)
在 Azure Active Directory 中驗證身分識別。

### <a name="sign-in-n"></a>登入 (名詞)
在 Azure Active Directory 中驗證身分識別的程序或動作，以及擷取這項作業的事件。

### <a name="sign-in-from-anonymous-ip-address"></a>從匿名 IP 位址登入
從標識為匿名代理 IP 位址的 IP 位址成功登錄後觸發的風險檢測。

### <a name="sign-in-from-infected-device"></a>從受感染的裝置登入
當登錄源自 IP 位址時觸發的風險檢測，已知 IP 位址由一個或多個受攻擊的設備使用，這些設備正在積極嘗試與機器人伺服器通信。

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>從具有可疑活動的 IP 位址登入
在從 IP 位址成功登錄後觸發的風險檢測，該 IP 位址在短時間內跨多個使用者帳戶的登錄嘗試失敗次數眾多。

### <a name="sign-in-from-unfamiliar-location"></a>從不熟悉的位置登入
當使用者從新位置（IP、緯度/經度和 ASN）成功登錄時觸發風險檢測。

### <a name="sign-in-risk"></a>登入風險
請參閱風險層級 (登入)

### <a name="sign-in-risk-policy"></a>登入風險原則
條件訪問策略，用於評估特定登錄的風險，並根據預定義的條件和規則應用緩解措施。

### <a name="user-compromise-risk"></a>使用者入侵風險
請參閱風險層級 (使用者入侵)

### <a name="user-risk"></a>使用者風險
請參閱風險層級 (使用者入侵)。

### <a name="user-risk-policy"></a>使用者風險原則
一種條件訪問策略，它考慮登錄並基於預定義的條件和規則應用緩解。

### <a name="users-flagged-for-risk"></a>標示有風險的使用者
具有風險檢測（處於活動狀態或已修復）的使用者

### <a name="vulnerability"></a>弱點
Azure Active Directory 中的組態或狀況，此組態或狀況會使目錄容易受到入侵或威脅的影響。

## <a name="see-also"></a>另請參閱

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
