---
title: 在 Azure Active Directory 中規劃條件式存取原則 |Microsoft Docs
description: 在本文中，您將瞭解如何規劃 Azure Active Directory 的條件式存取原則。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1c75d5022432a9a57b30aabec4dd2c4f76f2f29
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78671822"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>如何：在 Azure Active Directory 中規劃條件式存取部署

規劃條件式存取部署非常重要，可確保您為組織中的應用程式和資源達成必要的存取策略。 在您的部署規劃階段，將大部分的時間花在設計您所需的各種原則，以在您選擇的條件下授與或封鎖使用者存取權。 本檔說明執行安全且有效的條件式存取原則時，應採取的步驟。 開始之前，請確定您瞭解條件式[存取](overview.md)的運作方式，以及應該使用的時機。

## <a name="what-you-should-know"></a>您應該知道的事情

將條件式存取視為一種架構，可讓您控制對組織應用程式和資源的存取，而不是獨立的功能。 因此，某些條件式存取設定需要設定額外的功能。 例如，您可以設定某個原則來回應特定的[登入風險層級](../identity-protection/howto-identity-protection-configure-risk-policies.md)。 不過，以登入風險層級為基礎的原則需要啟用 [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md)。

如果需要其他的功能，您可能也需要取得相關的授權。 例如，當條件式存取 Azure AD Premium P1 功能時，identity protection 需要 Azure AD Premium P2 授權。

條件式存取原則有兩種類型：基準和標準。 [基準原則](baseline-protection.md)是預先定義的條件式存取原則。 這些原則的目標是確保您的環境中至少會啟用基準層級的安全性。 基準原則。 所有 Azure AD 版本都可以使用基準原則，而這些原則僅提供有限的自訂選項。 如果案例需要更多彈性，您可以停用基準原則，然後在自訂標準原則中實作自己的需求。

在標準條件式存取原則中，您可以自訂所有設定，以根據您的業務需求調整原則。 標準原則需要 Azure AD Premium P1 授權。

>[!NOTE]
> 我們建議使用 Azure AD 裝置型條件式存取原則，以在初始裝置驗證之後取得最佳的強制執行。 這包括關閉會話（如果裝置不符合規範）和裝置程式碼流程。

## <a name="draft-policies"></a>草稿原則

Azure Active Directory 條件式存取可讓您將雲端應用程式的保護帶入新的層級。 在這個新的境界中，雲端應用程式的存取方式是根據動態原則評估，而不是靜態存取設定。 使用條件式存取原則時，您可以定義回應（**執行此**動作）至存取條件（**發生這種情況時**）。

![原因和回應](./media/plan-conditional-access/10.png)

定義您想要使用此規劃模型來執行的每個條件式存取原則。 規劃練習：

- 協助您針對每個原則擬出回應與條件的大綱。
- 會為您的組織產生記載良好的條件式存取原則類別目錄。 

您可以使用目錄來評估原則實作是否反映您組織的業務需求。 

使用下列範例範本來為您的組織建立條件式存取原則：

|發生「此情況」** 時：|則執行「此動作」**：|
|-|-|
|嘗試存取下列項目：<br>-*<br>依據使用者和群組的雲端應用程式*<br>使用：<br>- 條件 1 (例如在公司網路之外)<br>- 條件 2 (例如裝置平台)|封鎖對應用程式的存取|
|嘗試存取下列項目：<br>-*<br>依據使用者和群組的雲端應用程式*<br>使用：<br>- 條件 1 (例如在公司網路之外)<br>- 條件 2 (例如裝置平台)|授與存取權 (滿足所有需求)：<br>- 需求 1 (例如 MFA)<br>- 需求 2 (例如裝置合規性)|
|嘗試存取下列項目：<br>-*<br>依據使用者和群組的雲端應用程式*<br>使用：<br>- 條件 1 (例如在公司網路之外)<br>- 條件 2 (例如裝置平台)|授與存取權 (滿足需求之一)：<br>- 需求 1 (例如 MFA)<br>- 需求 2 (例如裝置合規性)|

**發生此情況時**至少要定義嘗試存取雲端應用程式 (**目標**) 的主體 (**人員**)。 如果有必要，您也可以包含執行存取嘗試的**方法**。 在條件式存取中，定義誰、what 和如何所謂條件的元素。 如需詳細資訊，請參閱[Azure Active Directory 條件式存取中的條件為何？](concept-conditional-access-conditions.md) 

透過**則執行此動作**，您要定義原則對於存取條件的回應。 在您的回應中，您可以搭配其他需求 (例如多重要素驗證 (MFA)) 來封鎖或授與存取權。 如需完整的總覽，請參閱[什麼是 Azure Active Directory 條件式存取中的存取控制？](controls.md)  

條件與存取控制的組合即代表了條件式存取原則。

![原因和回應](./media/plan-conditional-access/51.png)

如需詳細資訊，請參閱[讓原則運作的必要條件](best-practices.md#whats-required-to-make-a-policy-work)。

到此時，可以為您的原則決定命名標準。 命名標準可協助您尋找原則並了解其用途，而不需要在 Azure 管理入口網站中開啟它們。 為您的原則命名以顯示：

- 序號
- 其適用的雲端應用程式
- 回應
- 適用的人員
- 適用時機 (如果適用)
 
![命名標準](./media/plan-conditional-access/11.png)

雖然描述性名稱可協助您大致瞭解條件式存取的執行，但如果您需要參考對話中的原則，序號會很有説明。 例如，如果您在電話上與其他系統管理員交談，您可以要求他們開啟 [原則] EM063 以解決問題。

舉例來說，下列命名說明原則針對外部網路使用 Dynamics CRP 應用程式的行銷使用者要求 MFA：

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

除了作用中的原則外，建議您也實作已停用的原則，其作為[在中斷/緊急狀況時的次要復原性存取控制](../authentication/concept-resilient-controls.md)。 您用於應變原則的命名標準應該包含更多一些項目： 

- 開頭的 `ENABLE IN EMERGENCY`，可使這個名稱在其他原則中脫穎而出。
- 應該在中斷時套用的名稱。
- 排序的序號，可協助系統管理員了解啟用原則的順序。 

例如，下列名稱指出此原則是在 MFA 中斷時，您應該啟用的第四個原則：

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>規劃原則

規劃條件式存取原則解決方案時，請評估您是否需要建立原則來達成下列結果。 

### <a name="block-access"></a>封鎖存取

封鎖存取選項非常強大，因為它：

- 優先順序高過使用者的任何其他指派
- 具有封鎖您整個組織登入租用戶的能力
 
如果您想要封鎖所有使用者的存取，您在原則中應至少排除一個使用者 (通常是緊急存取帳戶)。 如需詳細資訊，請參閱[選取使用者和群組](block-legacy-authentication.md#select-users-and-cloud-apps)。  

### <a name="require-mfa"></a>需要 MFA

為了簡化使用者的登入體驗，您可以讓他們透過使用者名稱和密碼登入您的雲端應用程式。 不過，通常來說，至少有部分案例會建議需要更強的帳戶驗證表單。 使用條件式存取原則，您可以限制對特定案例的 MFA 需求。 

需要 MFA 來進行存取的常見使用案例：

- [系統管理員](howto-baseline-protect-administrators.md)
- [存取特定應用程式](app-based-mfa.md) 
- [來自不信任的網路位置](untrusted-networks.md)。

### <a name="respond-to-potentially-compromised-accounts"></a>回應可能遭盜用的帳戶

使用條件式存取原則，您可以從可能遭盜用的身分識別，對登入執行自動回應。 帳戶遭到盜用的可能性會以風險層級的形式來表示。 Identity Protection 會計算兩種風險層級：登入風險和使用者風險。 若要實作登入風險回應，您有兩個選項：

- 條件式存取原則中[的登入風險條件](concept-conditional-access-conditions.md#sign-in-risk)
- Identity Protection 中的 [登入風險原則](../identity-protection/howto-sign-in-risk-policy.md) 

將登入風險作為條件是慣用的方法，因為它可提供您更多自訂選項。

使用者風險層級只有在 Identity Protection 中才能作為[使用者風險原則](../identity-protection/howto-user-risk-policy.md)。 

如需詳細資訊，請參閱[什麼是 Azure Active Directory Identity Protection？](../identity-protection/overview.md) 

### <a name="require-managed-devices"></a>需要受控裝置

在可存取雲端資源的裝置種類激增的情況下，使用者的生產力得以提升。 而另一方面，您應該不希望環境中的特定資源在保護層級不確定的情況下受到裝置存取。 對於受影響的資源，您應要求使用者只能使用受控裝置加以存取。 如需詳細資訊，請參閱[如何要求受管理的裝置使用條件式存取進行雲端應用程式存取](require-managed-devices.md)。 

### <a name="require-approved-client-apps"></a>需要已核准的用戶端應用程式

針對攜帶您自己的裝置 (BYOD) 案例，您需要做的第一個決策之一是您要管理整個裝置，或是只管理裝置上的資料。 您的員工使用行動裝置來處理個人和工作事務。 在維護員工的生產力時，您也希望能預防資料遺失。 透過 Azure Active Directory （Azure AD）條件式存取，您可以將雲端應用程式的存取限制為可保護公司資料的已核准用戶端應用程式。 如需詳細資訊，請參閱[如何要求已核准的用戶端應用程式使用條件式存取進行雲端應用程式存取](app-based-conditional-access.md)。

### <a name="block-legacy-authentication"></a>封鎖舊式驗證

Azure AD 支援數個最常用的驗證和授權通訊協定，包括舊式驗證。 如何防止使用舊式驗證的應用程式存取您租用戶的資源？ 建議您只使用條件式存取原則來封鎖它們。 如有必要，您可以僅允許特定使用者和特定網路位置使用以舊式驗證為基礎的應用程式。 如需詳細資訊，請參閱[如何使用條件式存取封鎖舊版驗證 Azure AD](block-legacy-authentication.md)。

## <a name="test-your-policy"></a>測試您的原則

將原則推出到生產環境之前，您應該進行測試以驗證它如預期般運作。

1. 建立測試使用者
1. 建立測試計劃
1. 設定原則
1. 評估模擬的登入狀況
1. 測試您的原則
1. 清理

### <a name="create-test-users"></a>建立測試使用者

若要測試原則，請建立與環境中使用者相似的一組使用者。 建立測試使用者可讓您驗證原則如預期般運作，且避免影響實際使用者以及中斷其應用程式和資源存取的可能性。 

某些組織有適用於此用途的測試租用戶。 不過，在測試租用戶中可能難以重新建立所有條件和應用程式，以完整測試原則的結果。 

### <a name="create-a-test-plan"></a>建立測試計劃

測試計畫非常重要，它可用來比較預期結果和實際結果。 您應該一律先做出預期，然後再測試某些項目。 下表概述範例測試案例。 根據您 CA 原則設定的方式，調整案例和預期的結果。

|原則 |狀況 |預期的結果 | 結果 |
|---|---|---|---|
|[不在公司時要求 MFA](/azure/active-directory/conditional-access/untrusted-networks)|已授權的使用者在信任的位置/公司登入應用程式**|不會提示使用者進行 MFA| |
|[不在公司時要求 MFA](/azure/active-directory/conditional-access/untrusted-networks)|已授權的使用者不是在信任的位置/公司登入應用程式**|提示使用者進行 MFA 且成功登入| |
|[要求 MFA (適用於管理員)](/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|全域管理員登入應用程式**|提示管理員進行 MFA| |
|[有風險的登入](/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|使用者使用 [Tor 瀏覽器](/azure/active-directory/active-directory-identityprotection-playbook)登入應用程式**|提示管理員進行 MFA| |
|[裝置管理](/azure/active-directory/conditional-access/require-managed-devices)|已授權的使用者嘗試從已授權的裝置登入|授與存取權| |
|[裝置管理](/azure/active-directory/conditional-access/require-managed-devices)|已授權的使用者嘗試從未經授權的裝置登入|封鎖存取權| |
|[具風險使用者的密碼變更](/azure/active-directory/identity-protection/howto-user-risk-policy)|已授權的使用者嘗試以被盜用的認證登入 (高風險登入)|根據您的原則，系統會提示使用者變更密碼或封鎖存取| |

### <a name="configure-the-policy"></a>設定原則

管理條件式存取原則是手動工作。 在 Azure 入口網站中，您可以在一個集中位置管理條件式存取原則-[條件式存取] 頁面。 [條件式存取] 頁面的其中一個進入點是 [ **Active Directory** ] 流覽窗格中的 [**安全性**] 區段。 

![條件式存取](media/plan-conditional-access/03.png)

如果您想要深入瞭解如何建立條件式存取原則，請參閱[使用 Azure Active Directory 條件式存取來要求特定應用程式的 MFA](app-based-mfa.md)。 本快速入門可協助您：

- 熟悉使用者介面。
- 取得條件式存取如何運作的第一印象。 

### <a name="evaluate-a-simulated-sign-in"></a>評估模擬的登入狀況

現在您已設定條件式存取原則，建議您查看它是否如預期般運作。 第一個步驟是使用條件式存取[假設原則工具](what-if-tool.md)，模擬您測試使用者的登入。 該模擬可評估此登入對原則所造成的影響，並產生模擬報告。

>[!NOTE]
> 雖然模擬執行可讓您印象條件式存取原則所造成的影響，但它不會取代實際的測試回合。

### <a name="test-your-policy"></a>測試您的原則

根據您的測試計畫執行測試案例。 在此步驟中，您要針對測試使用者執行每個原則的端對端測試，以確保每個原則都正確地運作。 使用上述建立的案例，執行每個測試。

請務必確定您會測試原則的排除準則。 例如，您可能會從原則排除需要 MFA 的使用者或群組。 測試是否會提示已排除的使用者進行 MFA，因為其他原則的組合可能需要針對這些使用者進行 MFA。

### <a name="cleanup"></a>清理

清除程序由下列步驟組成：

1. 停用原則。
1. 移除已指派的使用者和群組。
1. 刪除測試使用者。  

## <a name="move-to-production"></a>移動至生產環境

為您的環境準備好新原則時，請分階段部署它們：

- 提供內部變更通訊給終端使用者。
- 從較少的使用者開始，並確認原則如預期般運作。
- 當您將原則擴大到更多使用者的原則時，繼續排除所有的系統管理員。 排除系統管理員可確保在需要變更時，某些人仍然有原則的存取權。
- 只在必要時，才將原則套用到所有使用者。

最佳做法是至少建立一個使用者帳戶，其：

- 專門用來管理原則
- 從您的原則中排除

## <a name="rollback-steps"></a>復原步驟

如果您需要復原新實作的原則，請使用下列一或多個選項來復原：

1. **停用原則**：停用原則可確保它不會在使用者嘗試登入時套用。 當您需要使用原則時，您隨時都可以回到這裡啟用原則。

   ![停用原則](media/plan-conditional-access/07.png)

1. **從原則排除使用者/群組**：如果使用者無法存取應用程式，您可以選擇從原則排除使用者

   ![排除使用者](media/plan-conditional-access/08.png)

   > [!NOTE]
   > 您應該盡量避免使用此選項，只有在使用者可信任的情況時才使用。 您應該儘速將使用者加回原則或群組中。

1. **刪除原則**：如果已不再需要原則，請將它刪除。

## <a name="next-steps"></a>後續步驟

請參閱 [Azure AD 條件式存取文件](index.yml)來取得可用資訊的概觀。
