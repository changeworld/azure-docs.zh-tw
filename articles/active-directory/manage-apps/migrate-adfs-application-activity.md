---
title: 使用活動報告將 AD FS 應用移動到 Azure 活動目錄 |微軟文檔
description: 活動目錄聯合服務 （AD FS） 應用程式活動報告允許您快速將應用程式從 AD FS 遷移到 Azure 活動目錄 （Azure AD）。 此 AD FS 遷移工具可識別與 Azure AD 的相容性，並提供有關遷移指南。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333e440fdd5f5062dda45fb12a83543c63e66c04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978024"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>使用 AD FS 應用程式活動報告（預覽）將應用程式遷移到 Azure AD

許多組織使用 Active 目錄聯合服務 （AD FS） 為雲應用程式提供單一登入。 將 AD FS 應用程式移動到 Azure AD 進行身份驗證具有顯著優勢，尤其是在成本管理、風險管理、生產力、合規性和治理方面。 但是，瞭解哪些應用程式與 Azure AD 相容並確定特定的遷移步驟可能非常耗時。

Azure 門戶中的 AD FS 應用程式活動報告（預覽）可讓您快速識別哪些應用程式能夠遷移到 Azure AD。 它評估所有 AD FS 應用程式與 Azure AD 的相容性，檢查任何問題，並提供有關準備各個應用程式進行遷移的指導。 使用 AD FS 應用程式活動報告，您可以：

* **瞭解 AD FS 應用程式並擴大遷移範圍。** AD FS 應用程式活動報告列出了組織中的所有 AD FS 應用程式，並指示它們準備遷移到 Azure AD。
* **優先處理應用程式的遷移。** 獲取過去 1、7 或 30 天內登錄到應用程式的唯一使用者數，以説明確定遷移應用程式的重要性或風險。
* **運行遷移測試並修復問題。** 報表服務自動運行測試以確定應用程式是否已準備好遷移。 結果在 AD FS 應用程式活動報表中顯示為遷移狀態。 如果確定了潛在的遷移問題，您將獲得有關如何解決這些問題的具體指導。

AD FS 應用程式活動資料可供分配這些管理員角色的使用者使用：全域管理員、報表閱讀器、安全讀取器、應用程式管理員或雲應用程式管理員。

## <a name="prerequisites"></a>Prerequisites

* 您的組織當前必須使用 AD FS 訪問應用程式。
* 必須在 Azure AD 租戶中啟用 Azure AD 連接運行狀況。
   * [瞭解有關 Azure AD 連接運行狀況的更多](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [開始設置 Azure AD 連接運行狀況](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>瞭解可遷移的 AD FS 應用程式 

AD FS 應用程式活動報告在 Azure**使用&見解**報告下的 Azure 門戶中可用。 AD FS 應用程式活動報告分析每個 AD FS 應用程式，以確定是否可以根據需要遷移，或者是否需要進行其他審核。 

1. 使用有權訪問 AD FS 應用程式活動資料的管理員角色（全域管理員、報表讀取器、安全讀取器、應用程式管理員或雲應用程式管理員）登錄到[Azure 門戶](https://portal.azure.com)。

2. 選擇**Azure 活動目錄**，然後選擇**企業應用程式**。

3. 在 **"活動**"下，選擇 **"使用&見解（預覽"），** 然後選擇**AD FS 應用程式活動**以打開組織中所有 AD FS 應用程式的清單。

   ![AD FS 應用程式活動](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. 對於 AD FS 應用程式活動清單中的每個應用程式，查看**遷移狀態**：

   * **準備遷移**意味著在 Azure AD 中完全支援 AD FS 應用程式佈建，可以按照現在的方式遷移。

   * **需求查看**意味著應用程式的某些設置可以遷移到 Azure AD，但您需要查看無法以方式遷移的設置。

   * **所需的其他步驟**意味著 Azure AD 不支援應用程式的某些設置，因此無法以目前狀態遷移應用程式。

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>評估應用程式的遷移準備情況 

1. 在 AD FS 應用程式活動清單中，按一下 **"遷移狀態**"列中的狀態以打開遷移詳細資訊。 您將看到通過的配置測試的摘要，以及任何潛在的遷移問題。

   ![移轉詳細資料](media/migrate-adfs-application-activity/migration-details.png)

2. 按一下一條消息以打開其他遷移規則詳細資訊。 有關測試的屬性的完整清單，請參閱下面的 AD [FS 應用程式佈建測試](#ad-fs-application-configuration-tests)表。

   ![遷移規則詳細資訊](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS 應用程式佈建測試

下表列出了在 AD FS 應用程式上執行的所有配置測試。

|結果  |通過/警告/失敗  |描述  |
|---------|---------|---------|
|測試-ADFSRP 附加身份驗證規則 <br> 檢測到至少一個不可遷移的規則用於其他身份驗證。       | 通過/警告          | 依賴方具有提示多重要素驗證 （MFA） 的規則。 要移動到 Azure AD，可以將這些規則轉換為條件訪問策略。 如果使用本地 MFA，我們建議您遷移到 Azure MFA。 [瞭解有關條件訪問 的更多。](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)        |
|測試-ADFSRP附加WS饋點 <br> 依賴方已設置為 true 的附加 WSFed 終結點。       | 通過/失敗          | AD FS 中的依賴方允許多個 WS-Fed 斷言終結點。目前，Azure AD 僅支援一個。如果您有此結果阻止遷移的情況，[請告訴我們](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)。     |
|測試-ADFSRP 允許的身份驗證類引用 <br> 依賴方已設置允許的身份驗證類引用。       | 通過/失敗          | AD FS 中的此設置允許您指定應用程式是否配置為僅允許某些身份驗證類型。 我們建議使用條件訪問來實現此功能。 如果您有此結果阻止遷移的情況，[請告訴我們](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)。  [瞭解有關條件訪問 的更多。](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)          |
|測試-ADFSRP始終需要認證 <br> 始終需要身份驗證檢查結果      | 通過/失敗          | 通過 AD FS 中的此設置，您可以指定應用程式是否配置為忽略 SSO cookies 和**始終提示身份驗證**。 在 Azure AD 中，可以使用條件訪問策略管理身份驗證會話以實現類似的行為。 [瞭解有關使用條件訪問配置身份驗證會話管理。](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)          |
|已啟用測試 ADFSRP 自動更新 <br> 依賴方已自動更新啟用設置為 true       | 通過/警告          | 通過 AD FS 中的此設置，您可以指定 AD FS 是否配置為根據聯合中繼資料中的更改自動更新應用程式。 Azure AD 今天不支援此功能，但不應阻止應用程式遷移到 Azure AD。           |
|測試-ADFSRPclaims提供程式名稱 <br> 依賴方已啟用多個索賠提供程式       | 通過/失敗          | AD FS 中的此設置會調用依賴方接受聲明的標識提供程式。 在 Azure AD 中，可以使用 Azure AD B2B 啟用外部協作。 [瞭解有關 Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)的更多。          |
|測試-ADFSRP 授權規則      | 通過/失敗          | 應用程式已定義自訂委派授權規則。 這是 Azure AD 通過使用現代身份驗證協定（如 OpenID 連接和 OAuth 2.0）支援的 WS 信任概念。 [瞭解有關微軟身份平臺的更多資訊](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)。          |
|測試-ADFSRP類比授權規則       | 通過/警告          | 應用程式已定義自訂類比授權規則。這是 Azure AD 通過使用現代身份驗證協定（如 OpenID 連接和 OAuth 2.0）支援的 WS 信任概念。 [瞭解有關微軟身份平臺的更多資訊](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)。          |
|測試-ADFSRP頒發授權規則 <br> 至少檢測到一個不可遷移的規則用於頒發授權。       | 通過/警告          | 該應用程式具有在 AD FS 中定義的自訂頒發授權規則。Azure AD 支援 Azure AD 條件訪問此功能。 [瞭解有關條件訪問 的更多。](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) <br> 您還可以限制分配給應用程式的使用者或組對應用程式的訪問。 [詳細瞭解如何分配使用者和組來訪問應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)。            |
|測試-ADFSRP發放轉換規則 <br> 至少檢測到一個不可遷移的規則用於頒發轉換。       | 通過/警告          | 該應用程式具有在 AD FS 中定義的自訂頒發轉換規則。 Azure AD 支援自訂權杖中發出的聲明。 要瞭解更多資訊，請參閱[為企業應用程式的 SAML 權杖中發出的自訂聲明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。           |
|已啟用測試 ADFSRP 監控 <br> 依託方已監控啟用設置為 true。       | 通過/警告          | 通過 AD FS 中的此設置，您可以指定 AD FS 是否配置為根據聯合中繼資料中的更改自動更新應用程式。 Azure AD 今天不支援此功能，但不應阻止應用程式遷移到 Azure AD。           |
|測試-ADFSRPnot前 <br> 未在支票結果之前      | 通過/警告          | AD FS 允許基於 SAML 權杖中的"之前"和"不OnOrOrAfter 時間"的時間偏斜。 預設情況下，Azure AD 會自動處理此問題。          |
|測試-ADFSRP請求來自索賠提供者 <br> 依託方要求的"請求"方方設置為 true。       | 通過/警告          | AD FS 中的此設置確定當使用者來自不同聲明提供程式時 MFA 的行為。 在 Azure AD 中，可以使用 Azure AD B2B 啟用外部協作。 然後，您可以應用條件訪問策略來保護來賓存取。 瞭解有關[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)和[條件訪問](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)的更多。          |
|測試-ADFSRP簽名沙姆請求要求 <br> 依賴方已簽署 Saml 請求要求設置為 true       | 通過/失敗          | 應用程式在 AD FS 中配置，以驗證 SAML 請求中的簽名。 Azure AD 接受已簽名的 SAML 請求;但是，它將不會驗證簽名。 Azure AD 具有不同的方法來防止惡意調用。 例如，Azure AD 使用應用程式中配置的回復 URL 來驗證 SAML 請求。 Azure AD 將僅發送權杖以答覆為應用程式佈建的 URL。 如果您有此結果阻止遷移的情況，[請告訴我們](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)。          |
|測試-ADFSRP權杖終身 <br> 權杖終身檢查結果        | 通過/警告         | 應用程式佈建為自訂權杖存留期。 AD FS 預設值為 1 小時。Azure AD 支援使用條件訪問此功能。 要瞭解更多資訊，請參閱[使用條件訪問配置身份驗證會話管理](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)。          |
|依賴方設置為加密索賠。 Azure AD 支援此功能       | 通過          | 使用 Azure AD，可以加密發送到應用程式的權杖。 要瞭解更多資訊，請參閱[配置 Azure AD SAML 權杖加密](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)。          |
|加密名稱 Id 必需檢查結果      | 通過/失敗          | 應用程式佈建為加密 SAML 權杖中的名稱 ID 聲明。使用 Azure AD，可以加密發送到應用程式的整個權杖。尚不支援對特定聲明進行加密。 要瞭解更多資訊，請參閱[配置 Azure AD SAML 權杖加密](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)。         |

## <a name="check-the-results-of-claim-rule-tests"></a>檢查聲明規則測試結果

如果您在 AD FS 中為應用程式佈建了聲明規則，則該體驗將為所有聲明規則提供精細分析。 您將看到哪些聲明規則可以移動到 Azure AD，哪些規則需要進一步查看。

1. 在 AD FS 應用程式活動清單中，按一下 **"遷移狀態**"列中的狀態以打開遷移詳細資訊。 您將看到通過的配置測試的摘要，以及任何潛在的遷移問題。

2. 在 **"遷移規則詳細資訊**"頁上，展開結果以顯示有關潛在遷移問題的詳細資訊，並獲得其他指導。 有關測試的所有聲明規則的詳細清單，請參閱下面的["檢查聲明規則測試結果](#check-the-results-of-claim-rule-tests)"表。

   下面的示例顯示了頒發轉換規則的遷移規則詳細資訊。 它列出了在將應用程式遷移到 Azure AD 之前需要審閱和解決的聲明的特定部分。

   ![遷移規則詳細介紹了其他指南](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>聲明規則測試

下表列出了在 AD FS 應用程式上執行的所有聲明規則測試。

|屬性  |描述  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | 條件陳述式使用正則運算式計算聲明是否與特定模式匹配。要在 Azure AD 中實現類似的功能，可以使用預定義的轉換，如 IfEmpty（）、StartWith（）、包含（等）。 有關詳細資訊，請參閱[為企業應用程式的 SAML 權杖中發出的自訂聲明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|UNSUPPORTED_CONDITION_CLASS      | 條件陳述式具有多個條件，在運行頒發語句之前需要對其進行評估。Azure AD 可能通過聲明的轉換函數支援此功能，您可以在其中評估多個聲明值。有關詳細資訊，請參閱[為企業應用程式的 SAML 權杖中發出的自訂聲明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|UNSUPPORTED_RULE_TYPE      | 無法識別聲明規則。 有關如何在 Azure AD 中配置聲明的詳細資訊，請參閱[為企業應用程式的 SAML 權杖中發出的自訂聲明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | 條件陳述式使用 Azure AD 中不支援的頒發者。目前，Azure AD 不會從不同活動目錄或 Azure AD 的存儲源聲明。 如果這阻止您將應用程式遷移到 Azure AD，[請告訴我們](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)。         |
|UNSUPPORTED_CONDITION_FUNCTION      | 條件陳述式使用彙總函式發出或添加單個聲明，而不考慮匹配數。在 Azure AD 中，可以計算使用者的屬性，以決定使用 IfEmpty（）、StartWith（）、包含（等函數）對聲明使用的值。有關詳細資訊，請參閱[為企業應用程式的 SAML 權杖中發出的自訂聲明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|RESTRICTED_CLAIM_ISSUED      | 條件陳述式使用在 Azure AD 中受到限制的聲明。 您可能能夠發出受限聲明，但不能修改其源或應用任何轉換。 有關詳細資訊，請參閱在[Azure AD 中為特定應用自訂在權杖中發出的聲明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)。          |
|EXTERNAL_ATTRIBUTE_STORE      | 頒發語句使用與活動目錄不同的屬性存儲。 目前，Azure AD 不會從不同活動目錄或 Azure AD 的存儲源聲明。 如果此結果阻止您將應用程式遷移到 Azure AD，[請告訴我們](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)。          |
|UNSUPPORTED_ISSUANCE_CLASS      | 發行語句使用 ADD 向傳入聲明集添加聲明。 在 Azure AD 中，這可能配置為多個聲明轉換。有關詳細資訊，請參閱[為企業應用程式的 SAML 權杖中發出的自訂聲明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)。         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | 頒發語句使用正則運算式來轉換要發出的聲明的值。要在 Azure AD 中實現類似的功能，可以使用預定義的轉換，如提取（）、修剪（）、ToLower 等。 有關詳細資訊，請參閱[為企業應用程式的 SAML 權杖中發出的自訂聲明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |


## <a name="next-steps"></a>後續步驟

- [視頻：如何使用 AD FS 活動報告遷移應用程式](https://www.youtube.com/watch?v=OThlTA239lU)
- [使用 Azure 活動目錄管理應用程式](what-is-application-management.md)
- [管理應用程式的存取權](what-is-access-management.md)
- [Azure AD Connect 同盟](../hybrid/how-to-connect-fed-whatis.md)
