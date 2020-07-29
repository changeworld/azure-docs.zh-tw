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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74232351"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory Identity Protection 詞彙

### <a name="at-risk-user"></a>有風險 (使用者)
具有一或多個作用中風險偵測的使用者。 

### <a name="atypical-sign-in-location"></a>非典型登入位置
從特定使用者、類似使用者或租用戶不常用的地理位置登入。

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Azure Active Directory 的安全性模組，可讓您匯總查看風險偵測，以及影響組織身分識別的潛在弱點。

### <a name="conditional-access"></a>條件式存取
用來保護資源存取的原則。 條件式存取規則會儲存在 Azure Active Directory 中，並在授與資源的存取權之前 Azure AD 評估。  範例規則包括根據使用者位置、裝置健康狀態或使用者驗證方法來限制存取。

### <a name="credentials"></a>認證
包含識別碼以及用來取得存取本機和網路資源之識別證明的資訊。 認證範例包括使用者名稱和密碼、智慧卡和憑證。

### <a name="event"></a>事件
Azure Active Directory 中的活動記錄。

### <a name="false-positive-risk-detection"></a>假-正面（風險偵測）
由 Identity Protection 使用者手動設定的風險偵測狀態，表示已調查風險偵測，並不正確地將其標示為風險偵測。

### <a name="identity"></a>身分識別
必須透過以密碼或憑證等準則為基礎的驗證作業來確認的個人或實體。

### <a name="identity-risk-detection"></a>身分識別風險偵測
Identity Protection 標示為異常的 Azure AD 事件，可能表示身分識別已遭入侵。

### <a name="ignored-risk-detection"></a>已忽略（風險偵測）
由 Identity Protection 使用者手動設定的風險偵測狀態，表示風險偵測已關閉，而不採取補救動作。

### <a name="impossible-travel-from-atypical-locations"></a>不可能來自非典型位置的移動
當偵測到相同使用者的兩次登入時（其中至少有一個是來自非典型登入位置），以及登入之間的時間短于在這些位置之間實際移動所需的最短時間時，就會觸發風險偵測。  

### <a name="investigation"></a>調查
檢查活動、記錄和與風險偵測相關的其他相關資訊，以決定是否需要補救或緩和步驟，瞭解身分識別是否及如何遭到入侵，以及瞭解如何使用遭盜用的身分識別。

### <a name="leaked-credentials"></a>認證外洩
當我們的研究人員在深色網站公開張貼目前的使用者認證（使用者名稱和密碼）時，就會觸發風險偵測。

### <a name="mitigation"></a>降低
此動作可限制或消除攻擊者利用遭到入侵的身分識別或裝置的能力，但不需將身分識別或裝置還原至安全的狀態。 緩和措施並不會解決先前與身分識別或裝置相關聯的風險偵測。

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
此種驗證方法需要兩個或更多驗證方法，其中可能包含使用者擁有的事物 (例如憑證)、使用者知道的事物 (例如使用者名稱、密碼或通關密語)、實體屬性 (例如指紋) 以及個人屬性 (例如個人簽章)。

### <a name="offline-detection"></a>離線偵測
針對已經發生的事件，偵測異常狀況及評估事件的風險 (例如事後的登入嘗試)。

### <a name="policy-condition"></a>原則條件
安全性原則的一部分，定義原則中包含或排除的實體 (群組、使用者、應用程式、裝置平台、裝置狀態、IP 範圍、用戶端類型)。

### <a name="policy-rule"></a>原則規則
安全性原則的一部分，描述會觸發原則的情況，以及觸發原則時所採取的動作。

### <a name="prevention"></a>預防
預防藉由不當使用疑似或已知遭到入侵的身分識別或裝置來傷害組織的動作。 預防動作不會保護裝置或身分識別，也不會解決先前的風險偵測。

### <a name="privileged-user"></a>特殊權限 (使用者)
在風險偵測時的使用者，對 Azure Active Directory 中的一或多個資源具有永久或暫時的系統管理許可權，例如全域管理員、計費管理員、服務管理員、使用者管理員和密碼管理員。 

### <a name="real-time"></a>即時
請參閱即時偵測。

### <a name="real-time-detection"></a>即時偵測
在允許事件繼續進行之前，偵測異常狀況及評估事件的風險 (例如登入嘗試)。

### <a name="remediated-risk-detection"></a>已補救（風險偵測）
Identity Protection 自動設定的風險偵測狀態，表示已使用此類型風險偵測的標準補救動作來補救風險偵測。 例如，重設使用者密碼時，會自動補救許多表示先前密碼遭到入侵的風險偵測。

### <a name="remediation"></a>修復
用來保護先前疑似或已知遭到入侵的身分識別或裝置的動作。 補救動作會將身分識別或裝置還原至安全狀態，並解決先前與身分識別或裝置相關聯的風險偵測。

### <a name="resolved-risk-detection"></a>已解決（風險偵測）
由 Identity Protection 使用者手動設定的風險偵測狀態，表示使用者在 Identity Protection 外部採取適當的補救動作，而且應該將風險偵測視為已關閉。

### <a name="risk-detection-status"></a>風險偵測狀態
風險偵測的屬性，指出事件是否為作用中，如果已關閉，則為關閉的原因。

### <a name="risk-detection-type"></a>風險偵測類型
風險偵測的類別，表示導致事件被視為有風險的異常類型。

### <a name="risk-level-risk-detection"></a>風險層級（風險偵測）
風險偵測嚴重性的指示（高、中或低），可協助 Identity Protection 使用者排定所採取的動作，以降低組織的風險。 

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
從已識別為匿名 proxy IP 位址的 IP 位址成功登入之後，所觸發的風險偵測。

### <a name="sign-in-from-infected-device"></a>從受感染的裝置登入
當登入源自于 IP 位址（已知由一或多個遭入侵的裝置所使用，而這會主動嘗試與 bot 伺服器通訊）時，就會觸發風險偵測。

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>從具有可疑活動的 IP 位址登入
在一小段時間內，從多個使用者帳戶嘗試登入失敗次數過高的 IP 位址，就會觸發風險偵測。

### <a name="sign-in-from-unfamiliar-location"></a>從不熟悉的位置登入
當使用者從新的位置（IP、緯度/經度和 ASN）成功登入時，就會觸發風險偵測。

### <a name="sign-in-risk"></a>登入風險
請參閱風險層級 (登入)

### <a name="sign-in-risk-policy"></a>登入風險原則
條件式存取原則，可評估特定登入的風險，並根據預先定義的條件和規則來套用緩和措施。

### <a name="user-compromise-risk"></a>使用者入侵風險
請參閱風險層級 (使用者入侵)

### <a name="user-risk"></a>使用者風險
請參閱風險層級 (使用者入侵)。

### <a name="user-risk-policy"></a>使用者風險原則
條件式存取原則，可根據預先定義的條件和規則，考慮登入並套用緩和措施。

### <a name="users-flagged-for-risk"></a>標示有風險的使用者
有風險偵測的使用者（作用中或已補救）

### <a name="vulnerability"></a>弱點
Azure Active Directory 中的組態或狀況，此組態或狀況會使目錄容易受到入侵或威脅的影響。

## <a name="see-also"></a>另請參閱

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
