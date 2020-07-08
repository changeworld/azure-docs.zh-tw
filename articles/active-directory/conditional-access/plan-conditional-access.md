---
title: 規劃 Azure Active Directory 條件式存取部署
description: 了解如何設計條件式存取原則，並在您的組織中有效地進行部署。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 06/09/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83cc75c1d69ee7232edf0c21643d25027b97f088
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85339732"
---
# <a name="plan-a-conditional-access-deployment"></a>規劃條件式存取部署

規劃條件式存取部署對於實現組織對應用程式與資源的存取策略非常重要。

在行動優先、雲端至上的世界中，您的使用者可以使用各種裝置與應用程式，從任何位置存取您組織的資源。 因此，僅將焦點放在誰可以存取資源，已不再足夠。 您也必須考慮使用者的所在位置、使用中的裝置、要存取的資源等等。 

Azure Active Directory (Azure AD) 條件式存取 (CA) 會分析訊號 (例如使用者、裝置與位置) 以自動進行決策制訂並強制執行組織的資源存取原則。 您可以使用 CA 原則來套用存取控制，例如 Multi-Factor Authentication (MFA)。 CA 原則可讓您在需要安全性時提示使用者進行 MFA，並在不需要時避免造成使用者的困擾。

![條件式存取概觀](./media/plan-conditional-access/conditional-access-overview-how-it-works.png)

Microsoft 提供名為[安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md) \(部分機器翻譯\) 的標準條件式原則，以確保基本層級的安全性。 不過，您的組織所需的靈活性可能比安全性預設值所能提供的更高。 您可以使用條件式存取，以更細微的方式自訂安全性預設值，以及設定符合您需求的新原則。

## <a name="learn"></a>學習

在開始之前，請務必了解[條件式存取](overview.md)的運作方式與使用時機。

### <a name="benefits"></a>優點

部署條件式存取的優點如下：

* 提高生產力。 僅在有一或多個訊號許可時，才中斷使用 MFA 之類登入條件的使用者。 CA 原則可讓您控制何時提示使用者進行 MFA、何時封鎖存取，以及何時必須使用受信任的裝置。

* 管理風險。 使用原則條件進行自動化的風險評估，表示可以立即識別並補救或封鎖有風險的登入。 結合條件式存取與可偵測異常和可疑事件的 [Identity Protection](../identity-protection/overview-identity-protection.md)，可讓您確定何時封鎖或限制對資源的存取。 

* 處理合規性與治理。 條件式存取可讓您稽核對應用程式的存取權、提出使用規定供使用者同意，以及根據合規性原則來限制存取。

* 管理成本。 將存取原則轉移到 Azure AD，可減少對條件式存取的自訂或內部部署解決方案的依賴及其基礎結構成本。

### <a name="license-requirements"></a>授權需求

請參閱[條件式存取授權需求](overview.md)。

如果需要其他的功能，您可能也需要相關的授權。 如需詳細資訊，請參閱 [Azure Active Directory 價格](https://azure.microsoft.com/pricing/details/active-directory/)。

### <a name="prerequisites"></a>Prerequisites

* 已啟用 Azure AD Premium 或試用版授權的運作中 Azure AD 租用戶。 如有需要，[請建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 具有條件式存取管理員權限的帳戶。

* 具有已知密碼的非管理員使用者，例如 testuser。 如果您需要建立使用者，請參閱 [快速入門：將使用者新增至 Azure Active Directory](../fundamentals/add-users-azure-active-directory.md) 中的資訊。

* 非管理員使用者為其成員的群組。 如果您需要建立群組，請參閱[在 Azure Active Directory 中建立群組並新增成員](../fundamentals/active-directory-groups-create-azure-portal.md)。

### <a name="training-resources"></a>訓練資源

當您學習條件式存取時，下列資源可能會很有用：


#### <a name="videos"></a>影片

* [何謂條件式存取？](https://youtu.be/ffMAw2IVO7A)
* [如何部署條件式存取？](https://youtu.be/c_izIRNJNuk)
* [如何向終端使用者推出 CA 原則？](https://youtu.be/0_Fze7Zpyvc)
* [使用裝置控制措施的條件式存取](https://youtu.be/NcONUf-jeS4)
* [使用 Azure MFA 的條件式存取](https://youtu.be/Tbc-SU97G-w)
* [Enterprise Mobility + Security 中的條件式存取](https://youtu.be/A7IrxAH87wc)


#### <a name="online-courses-on-pluralsight"></a>Pluralsight 線上課程

* [設計 Microsoft Azure 中的身分識別管理](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design) \(英文\)
* [設計 Microsoft Azure 的驗證](https://www.pluralsight.com/courses/microsoft-azure-authentication-design) \(英文\)
* [設計 Microsoft Azure 的授權](https://www.pluralsight.com/courses/microsoft-azure-authorization-design) \(英文\)

## <a name="plan-the-deployment-project"></a>規劃部署專案

在決定環境中此部署的策略時，請考慮您的組織需求。

### <a name="engage-the-right-stakeholders"></a>包含正確的專案關係人

當技術專案失敗時，通常是因為對影響、結果與責任的預期不符所造成。 若要避免這些錯誤，請[確保您吸引合適的專案關係人](https://aka.ms/deploymentplans) \(英文\)，且專案角色是明確的。

### <a name="plan-communications"></a>規劃通訊

溝通對於任何新服務的成功非常重要。 主動與您的使用者溝通其體驗將如何改變、何時會改變，以及如何在遇到問題時取得支援。

### <a name="plan-a-pilot"></a>規劃試驗

為您的環境準備好新原則時，請分階段在生產環境中加以部署。 首先，將原則套用至測試環境中的一小部分使用者，然後驗證該原則的行為是否符合預期。 請參閱[試驗的最佳做法](../fundamentals/active-directory-deployment-plans.md) \(部分機器翻譯\)。

> [!NOTE]
> 若要推出不是管理員特定的新原則，請排除所有管理員。 這可確保管理員仍然可以存取原則，並在發生重大影響時進行變更或將其撤銷。 將原則套用至所有使用者之前，請一律以一小群使用者進行驗證。

## <a name="understand-ca-policy-components"></a>了解 CA 原則元件
CA 原則是 if-then 語句：如果符合指派，則套用這些存取控制。

設定 CA 原則時，條件稱為「指派」。 CA 原則可讓您根據特定指派，對貴組織的應用程式強制執行存取控制。


如需詳細資訊，請參閱[建置 CA 原則](concept-conditional-access-policies.md) \(部分機器翻譯\)。

![建立原則畫面](media/plan-conditional-access/create-policy.png)

[指派](concept-conditional-access-policies.md#assignments) \(部分機器翻譯\) 定義

* 受原則影響的[使用者和群組](concept-conditional-access-users-groups.md)

* 將套用原則的[雲端應用程式或動作](concept-conditional-access-cloud-apps.md) 

* 將套用原則的[條件](concept-conditional-access-conditions.md)。

[存取控制](concept-conditional-access-policies.md) \(部分機器翻譯\) 設定會決定如何強制執行原則：

* [授與或封鎖](concept-conditional-access-grant.md) \(部分機器翻譯\) 雲端應用程式的存取權。

* [工作階段控制措施](concept-conditional-access-session.md) \(部分機器翻譯\) 可讓您限制特定雲端應用程式內的體驗。

### <a name="ask-the-right-questions-to-build-your-policies"></a>詢問適當的問題以建置您的原則

原則會回答有關誰應該存取您的資源、應該存取哪些資源，以及在哪些條件下存取資源的問題。 原則可以設計來授與存取權，或用來封鎖存取。 請務必詢問有關您的原則嘗試達成哪些目標的適當問題。 

在建置每個原則的問題之前，請先記錄其解答。 

#### <a name="common-questions-about-assignments"></a>有關指派的常見問題

[使用者和群組](concept-conditional-access-users-groups.md)

* 哪些使用者與群組會包含在原則中或從原則中排除？

* 此原則是否包括所有使用者、特定的使用者群組、目錄角色或外部使用者？

[雲端應用程式或動作](concept-conditional-access-cloud-apps.md) \(部分機器翻譯\)

* 將套用原則的應用程式為何？

* 將受此原則約束的使用者動作為何？

[條件](concept-conditional-access-conditions.md)

* 將包含在原則中或從原則中排除的裝置平台為何？

* 組織的信任位置為何？

* 將包含在原則中或從原則中排除的位置為何？

* 將包含在原則中或從原則中排除的用戶端應用程式類型 (瀏覽器、行動裝置、桌上型電腦用戶端、使用舊版驗證方法的應用程式) 為何？

* 您是否有會導致從原則中排除已加入 Azure AD 之裝置，或已加入混合式 Azure AD 之裝置的原則？ 

* 如果使用 [Identity Protection](../identity-protection/concept-identity-protection-risks.md)，您是否要併入登入風險保護？

#### <a name="common-questions-about-access-controls"></a>存取控制的相關常見問題

[授與或封鎖](concept-conditional-access-grant.md) \(部分機器翻譯\) 

您是否要透過要求下列一或多個項目來授與對資源的存取權？

* 需要 MFA

* 裝置需要標記為符合規範

* 需要已加入混合式 Azure AD 的裝置

* 需要已核准的用戶端應用程式

* 需要應用程式保護原則

[工作階段控制措施](concept-conditional-access-session.md) \(部分機器翻譯\)

您是否要對雲端應用程式強制執行下列任何存取控制？

* 使用應用程式強制權限

* 使用條件式存取應用程式控制

* 強制執行登入頻率

* 使用持續性瀏覽器工作階段

### <a name="access-token-issuance"></a>存取權杖發行

請務必了解如何發行存取權杖。 

![存取權杖發行圖表](media/plan-conditional-access/CA-policy-token-issuance.png)

> [!NOTE]
> 如果不需要指派，且沒有任何 CA 原則生效，則預設行為是發出存取權杖。 

例如，假設有一個原則，其中：

如果使用者位於群組 1，則強制以 MFA 存取應用程式 1。

如果不在群組 1 中的使用者嘗試存取應用程式，則不會滿足 “if’ 條件，而會發出權杖。 若要排除群組 1 以外的使用者，則需要個別的原則來封鎖所有其他使用者。

## <a name="follow-best-practices"></a>遵循最佳做法

條件式存取架構可為您提供絕佳的設定彈性。 不過，絕佳的彈性也表示您應該先仔細地檢閱每個設定原則，然後才加以發行，以避免產生不想要的結果。

### <a name="apply-ca-policies-to-every-app"></a>將 CA 原則套用至每個應用程式

如果 CA 原則條件不會觸發存取控制，則預設會發出存取權杖。 請確定每個應用程式都已套用至少一個條件式存取原則

> [!IMPORTANT]
> 在單一原則中使用區塊與所有應用程式要非常小心。 這可能會將管理員鎖定在 Azure 管理入口網站之外，而且無法為重要端點 (例如 Microsoft Graph) 設定排除項。

### <a name="minimize-the-number-of-ca-policies"></a>將 CA 原則數目減到最少

為每個應用程式建立原則效率不高，而且會導致管理困難。 條件式存取將僅對每個使用者套用前 195 個原則。 我們建議您分析您的應用程式，並將其分組為對相同使用者具有相同資源需求的應用程式。 例如，如果所有 Microsoft 365 應用程式或所有 HR 應用程式的相同使用者都有相同的需求，請建立單一原則，並包含它所套用的所有應用程式。 

### <a name="set-up-emergency-access-accounts"></a>設定緊急存取帳戶

如果您設定了錯誤的原則，則可能會將組織鎖定在 Azure 入口網站之外。 透過在組織中建立兩個或多個[緊急存取帳戶](../users-groups-roles/directory-emergency-access.md) \(部分機器翻譯\)，來減輕管理員意外遭鎖定的影響。

* 建立一個專門用來管理原則，且從您的所有原則排除的使用者帳戶。

* 中斷混合式環境的半透明案例：

  * 建立內部部署安全性群組，並將其與 Azure AD 同步。 安全性群組應包含您的專用原則管理帳戶。 

   * 豁免此安全性群組會構成所有 CA 原則。

   * 發生服務中斷時，請適當地將其他管理員新增至內部部署群組，並強制執行同步處理。這會以動畫方式呈現 CA 原則的豁免。

### <a name="set-up-report-only-mode"></a>設定報告專用模式

可能很難預測一般部署計畫所影響的使用者數目與名稱，例如：

* 封鎖舊版驗證
* 要求使用 MFA
* 實作登入風險原則

[報告專用模式](concept-conditional-access-report-only.md) \(部分機器翻譯\) 可讓管理員在於 CA 原則的環境中加以啟用之前，先評估其影響。

了解如何[在 CA 原則上設定報告專用模式](howto-conditional-access-report-only.md) \(部分機器翻譯\)。

### <a name="plan-for-disruption"></a>針對中斷進行規劃

如果您倚賴單一存取控制措施 (例如 MFA 或網路位置) 來保護您的 IT 系統，則單一存取控制措施無法使用或設定不正確時，很容易發生存取失敗。 為了降低在未預期的中斷期間鎖定的風險，請[規劃適用於您的組織的策略](../authentication/concept-resilient-controls.md) \(部分機器翻譯\)。

### <a name="set-naming-standards-for-your-policies"></a>設定原則的命名標準

命名標準可協助您尋找原則並了解其用途，而不需要在 Azure 管理入口網站中開啟它們。 我們建議您為您的原則命名，以顯示：

* 序號

* 其適用的雲端應用程式

* 回應

* 適用的人員

* 適用時機 (如果適用)

![命名標準](media/plan-conditional-access/11.png)

**範例**；要求從外部網路存取 Dynamics CRP 應用程式之行銷使用者使用 MFA 的原則可能是：

![命名標準](media/plan-conditional-access/naming-example.png)

描述性名稱可協助您大致了解條件式存取的實作。 如果您需要參考交談中的原則，序號會很有用。 例如，如果您透過電話與管理員交談，您可以要求其開啟原則 CA01 來解決問題。

#### <a name="naming-standards-for-emergency-access-controls"></a>緊急存取控制措施的命名標準

除了作用中的原則外，也實作已停用的原則，作為[中斷或緊急狀況的次要復原性存取控制](../authentication/concept-resilient-controls.md) \(部分機器翻譯\)。 您用於應變原則的命名標準應該包含：
* 開頭的 ENABLE IN EMERGENCY，使這個名稱在其他原則中脫穎而出。

* 應該在中斷時套用的名稱。

* 排序的序號，可協助系統管理員了解啟用原則的順序。

**範例**

下列名稱指出，如果發生 MFA 中斷，此原則是要啟用的四個原則中的第一個：

EM01 - ENABLE IN EMERGENCY：MFA 中斷 [1/4] - Exchange SharePoint：需要 VIP 使用者的混合式 Azure AD Join。

### <a name="exclude-countries-from-which-you-never-expect-a-sign-in"></a>排除您從未預期登入的國家/地區。

Azure Active Directory 可讓您建立[具名位置](location-condition.md) \(部分機器翻譯\)。 建立具名位置，其中包含您永遠不會預期登入發生的所有國家/地區。 然後，針對所有應用程式建立原則，以封鎖　來自該具名位置的登入。 **請確保您的管理員不受此原則約束**。

### <a name="plan-your-policy-deployment"></a>規劃您的原則部署

針對您的環境準備好新原則時，請確保在發布每個原則之前先對其進行檢閱，以避免產生不想要的結果。 請參閱下列文件，以了解有關如何套用原則以及如何避免問題的重要資訊

* [您應該知道的事情](best-practices.md) \(部分機器翻譯\)

* [您應該避免做的事](best-practices.md) \(部分機器翻譯\)

## <a name="common-policies"></a>常用原則

在規劃您的 CA 原則解決方案時，請評估您是否需要建立原則來達到下列結果。

* [需要 MFA](#require-mfa)
* [回應可能遭盜用的帳戶](#respond-to-potentially-compromised-accounts)
* [需要受控裝置](#require-managed-devices)
* [需要核准的用戶端應用程式](#require-approved-client-apps)
* [封鎖存取](#block-access)

### <a name="require-mfa"></a>需要 MFA

要求 MFA 存取的常見使用案例：

* [系統管理員](howto-conditional-access-policy-admin-mfa.md)

* [存取特定應用程式](app-based-mfa.md)

* [適用於所有使用者](howto-conditional-access-policy-all-users-mfa.md) \(部分機器翻譯\)

* [來自不信任的網路位置](untrusted-networks.md) \(部分機器翻譯\)

* [適用於 Azure 管理](howto-conditional-access-policy-azure-management.md) \(部分機器翻譯\)

### <a name="respond-to-potentially-compromised-accounts"></a>回應可能遭盜用的帳戶

透過 CA 原則，您可以針對使用可能遭盜用之身分識別的登入實作自動化回應。 帳戶遭到盜用的可能性會以風險層級的形式來表示。 Identity Protection 會計算兩種風險層級：登入風險與使用者風險。 下面是可以啟用的三個預設原則。

* [要求所有使用者註冊 MFA](howto-conditional-access-policy-risk.md) \(部分機器翻譯\)

* [要求有高風險使用者進行密碼變更](howto-conditional-access-policy-risk.md) \(部分機器翻譯\)

* [要求具有中等或高登入風險的使用者使用 MFA](howto-conditional-access-policy-risk.md) \(部分機器翻譯\)

### <a name="require-managed-devices"></a>需要受控裝置

在可存取雲端資源的裝置種類激增的情況下，使用者的生產力得以提升。 您應該不希望具有未知保護層級的裝置存取環境中的特定資源。 針對那些資源，[要求使用者只能使用受控裝置來存取](require-managed-devices.md) \(部分機器翻譯\)。

### <a name="require-approved-client-apps"></a>需要已核准的用戶端應用程式

員工使用其行動裝置來處理個人與工作事務。 針對 BYOD 案例，您必須決定是要管理整部裝置，或只管理其中的資料。 如果僅管理資料與存取權，您可以[要求已核准的雲端應用程式](app-based-conditional-access.md) \(部分機器翻譯\)，以保護您的公司資料。 例如，您可以要求僅透過 Outlook Mobile 存取電子郵件，而不是透過一般郵件程式來存取。

### <a name="block-access"></a>封鎖存取

[封鎖所有存取](howto-conditional-access-policy-block-access.md) \(部分機器翻譯\) 選項功能強大。 例如，當您將應用程式移轉至 Azure AD，但尚未準備好讓任何人登入時，就可以加以使用。 封鎖存取： 

* 覆寫使用者的所有其他指派

* 具有封鎖您整個組織登入租用戶的能力

> [!IMPORTANT]
> 如果您建立原則來封鎖所有使用者的存取權，請務必排除緊急存取帳戶，並考慮從原則中排除所有管理員。

您可以封鎖使用者存取的其他常見案例包括：

* [封鎖特定網路位置](howto-conditional-access-policy-location.md) \(部分機器翻譯\)，使來自該位置的裝置無法存取您的雲端應用程式。 您可以使用此原則來封鎖不應產生流量來源的特定國家/地區。

* Azure AD 支援舊版驗證。 不過，舊版驗證不支援 MFA，而許多環境要求使用其來處理身分識別安全性。 在此情況下，您可以[使用舊版驗證封鎖應用程式](block-legacy-authentication.md) \(部分機器翻譯\)，使應用程式無法存取您的租用戶資源。

## <a name="build-and-test-policies"></a>建置及測試原則

在您部署的每個階段中，請確定您持續評估結果符合預期。 

準備好新原則時，請分階段在生產環境中加以部署：

* 提供內部變更通訊給終端使用者。

* 從較少的使用者開始，並確認原則如預期般運作。

* 當您將原則擴大到更多使用者的原則時，繼續排除所有的系統管理員。 排除系統管理員可確保在需要變更時，某些人仍然有原則的存取權。

* 只有在徹底測試之後，才將原則套用至所有使用者。 請確定您至少有一個不適用原則的管理員帳戶。

### <a name="create-test-users"></a>建立測試使用者

建立一組測試使用者，以反映生產環境中的使用者。 建立測試使用者可讓您驗證原則如預期般運作，且避免影響實際使用者以及中斷其應用程式與資源存取的可能性。

某些組織有適用於此用途的測試租用戶。 不過，在測試租用戶中可能難以重新建立所有條件和應用程式，以完整測試原則的結果。

### <a name="create-a-test-plan"></a>建立測試計劃

測試計畫非常重要，它可用來比較預期結果和實際結果。 您應該一律先做出預期，然後再測試某些項目。 下表概述範例測試案例。 根據您 CA 原則設定的方式，調整案例和預期的結果。

| 原則| 狀況| 預期的結果 |
| - | - | - |
| [不在公司時要求 MFA](untrusted-networks.md)| 已授權的使用者在信任的位置/公司登入應用程式| 不會提示使用者進行 MFA |
| [不在公司時要求 MFA](untrusted-networks.md)| 已授權的使用者不是在信任的位置/公司登入應用程式| 提示使用者進行 MFA 且成功登入 |
| [要求 MFA (適用於管理員)](howto-baseline-protect-administrators.md)| 全域管理員登入應用程式| 提示管理員進行 MFA |
| [有風險的登入](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)| 使用者使用未核准的瀏覽器登入應用程式| 提示管理員進行 MFA |
| [裝置管理](require-managed-devices.md)| 已授權的使用者嘗試從已授權的裝置登入| 授與存取權 |
| [裝置管理](require-managed-devices.md)| 已授權的使用者嘗試從未經授權的裝置登入| 封鎖存取權 |
| [具風險使用者的密碼變更](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)| 已授權的使用者嘗試以被盜用的認證登入 (高風險登入)| 根據您的原則，系統會提示使用者變更密碼或封鎖存取 |


### <a name="configure-the-test-policy"></a>設定測試原則

在 [Azure 入口網站](https://portal.azure.com/)中，您可以在 [Azure Active Directory] > [安全性] > [條件式存取] 下設定 CA 原則。

如果您想要深入了解如何建立 CA 原則，請參閱此範例：[當使用者登入 Azure 入口網站時，用來提示進行 MFA 的 CA 原則](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)。 本快速入門可協助您：

* 熟悉使用者介面

* 取得條件式存取如何運作的第一印象

### <a name="enable-the-policy-in-report-only-mode"></a>在報告專用模式中啟用原則

若要評估原則的影響，請從在[報告專用模式](concept-conditional-access-report-only.md) \(部分機器翻譯\) 中啟用原則開始。 在登入期間會評估報告專用原則，但不會強制執行授與控制措施和工作階段控制措施。 一旦您在報告專用模式中儲存原則之後，就可以在登入記錄中看到對即時登入的影響。 從登入記錄中選取事件，然後瀏覽至 [報告專用] 索引標籤，以查看每個報告專用原則的結果。


![報告專用模式 ](media/plan-conditional-access/report-only-mode.png)

選取原則，您也可以查看使用 [原則詳細資料] 畫面來評估原則之指派和存取控制的方式。 為了使原則套用至登入，必須滿足每個已設定的指派。 

### <a name="understand-the-impact-of-your-policies-using-the-insights-and-reporting-workbook"></a>使用 [見解與報告] 活頁簿了解原則的影響

您可以在 [見解與報告] 活頁簿中，檢視條件式存取原則的彙總影響。 若要存取活頁簿，您需要 Azure 監視器訂用帳戶，而且您必須[將登入記錄串流至 Log Analytics 工作區](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) \(部分機器翻譯\)。 

### <a name="simulate-sign-ins-using-the-what-if-tool"></a>使用 what-if 工具模擬登入

驗證條件式存取原則的另一種方法是使用 [what-if 工具](troubleshoot-conditional-access-what-if.md) \(部分機器翻譯\)，其模擬哪些原則適用於在假設情況下登入的使用者。 選取您要測試的登入屬性 (例如使用者、應用程式、裝置平台與位置)，並查看將會套用哪些原則。

> [!NOTE] 
> 雖然模擬執行可讓您了解 CA 原則的影響，但其不會取代實際的測試回合。

### <a name="test-your-policy"></a>測試您的原則

使用測試使用者執行測試方案中的每個測試。

**確定您會測試原則的排除準則**。 例如，您可能會從原則排除需要 MFA 的使用者或群組。 測試是否會對排除的使用者提示進行 MFA，因為其他原則的組合可能要求那些使用者進行 MFA。

### <a name="roll-back-policies"></a>復原原則

如果您需要復原新實作的原則，請使用下列一或多個選項：

* **停用原則**。 停用原則可確保其不會在使用者嘗試登入時套用。 當您想要使用原則時，您隨時都可以回到這裡啟用原則。

![啟用原則影像](media/plan-conditional-access/enable-policy.png)

* **從原則中排除使用者或群組**。 如果使用者無法存取應用程式，您可以選擇從原則排除使用者。

![排除使用者與群組](media/plan-conditional-access/exclude-users-groups.png)

> [!NOTE]
>  您應該盡量避免使用此選項，只有在使用者可信任的情況時才使用。 您應該儘速將使用者加回原則或群組中。

* **刪除原則**。 如果已不再需要原則，請將其[刪除](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)。

## <a name="manage-access-to-cloud-apps"></a>管理雲端應用程式的存取權

使用下列管理選項來控制及管理您的 CA 原則：

![manage-access](media/plan-conditional-access/manage-access.png)


### <a name="named-locations"></a>具名位置

CA 原則的位置條件可讓您將存取控制設定和使用者的網路位置結合。 使用[具名位置](location-condition.md) \(部分機器翻譯\)，您可以建立 IP 位址範圍、國家與地區的邏輯分組。

### <a name="custom-controls"></a>自訂控制項

[自訂控制措施](controls.md)會將使用者重新導向至相容的服務，以滿足 Azure AD 之外的驗證需求。 為了滿足此控制措施，系統會將使用者的瀏覽器重新導向至外部服務、執行任何必要的驗證，再將瀏覽器重新導向回 Azure AD。 Azure AD 會確認回應，而且使用者如果成功通過驗證或確認，就會繼續進行條件式存取流程。

### <a name="terms-of-use"></a>使用規定

在存取您環境中的特定雲端應用程式之前，您可以藉由讓使用者接受您的使用規定 (ToU) 來取得其同意。 遵循此[快速入門，以建立使用規定](require-tou.md)。

### <a name="classic-policies"></a>傳統原則

在 [Azure 入口網站](https://portal.azure.com/)中，您可以在 [Azure Active Directory] > [安全性] > [條件式存取] 下找到您的 CA 原則。 您的組織可能也會有非使用此頁面建立的舊版 CA 原則。 這些原則也稱為「傳統原則」。 我們建議您[考慮在 Azure 入口網站中移轉這些傳統原則](best-practices.md) \(部分機器翻譯\)。

## <a name="troubleshoot-conditional-access"></a>條件式存取的疑難排解

當使用者遇到 CA 原則的問題時，請收集下列資訊以協助進行疑難排解。

* 使用者主體名稱

* 使用者顯示名稱

* 作業系統名稱

* 時間戳記 (接受近似值)

* 目標應用程式

* 用戶端應用程式類型 (瀏覽器與用戶端)

* 相互關聯識別碼 (這是登入所獨有的)

如果使用者收到包含更多詳細資料連結的訊息，其可以為您收集大部分的資訊。

![無法取得應用程式錯誤訊息](media/plan-conditional-access/cant-get-to-app.png)

收集資訊之後，請參閱下列資源：

* [條件式存取的登入問題](troubleshoot-conditional-access.md) \(部分機器翻譯\) – 使用錯誤訊息和 Azure AD 登入記錄，了解與條件式存取相關的非預期登入結果。

* [使用 What-If 工具](troubleshoot-conditional-access-what-if.md) \(部分機器翻譯\) - 了解為什麼在特定情況下將原則套用於使用者或不套用於使用者，或者原則是否會在已知狀態下套用。

## <a name="next-steps"></a>後續步驟

[深入了解 Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) \(部分機器翻譯\)

[深入了解 Identity Protection](../identity-protection/overview-identity-protection.md)

[使用 Microsoft Graph API 來管理 CA 原則](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta.md) \(英文\)
