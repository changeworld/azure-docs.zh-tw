---
title: 管理員角色描述和許可權 - Azure AD |微軟文件
description: 系統管理員角色可以新增使用者、指派系統管理角色、重設使用者密碼、管理使用者授權或管理網域。
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 04/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac49209fb1debca604a6aeb8ad3993ff898c331
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082997"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Azure Active Directory 中的系統管理員角色權限

使用 Azure 活動目錄 (Azure AD),可以指定有限的管理員以較低許可權的角色管理標識任務。 可以為管理員分配,以便添加或更改使用者、分配管理角色、重置使用者密碼、管理使用者許可證和管理網域名稱。 默認[使用者許可權](../fundamentals/users-default-permissions.md)只能在 Azure AD 中的使用者設置中更改。

## <a name="limit-use-of-global-administrator"></a>限制全域管理員的使用

分配給全域管理員角色的用戶可以讀取和修改 Azure AD 組織中的每個管理設置。 默認情況下,註冊 Azure 訂閱的人員將為其為 Azure AD 組織分配全域管理員角色。 只有全域管理員和特權角色管理員才能委派管理員角色。 為了降低企業的風險,我們建議您將此角色分配給組織中可能最少的人員。

作為最佳實踐,我們建議您將此角色分配給組織中少於五個人。 如果您的組織中已分配了五個以上的管理員到全域管理員角色,以下是減少其使用的一些方法。

### <a name="find-the-role-you-need"></a>尋找所需的角色

如果從多個角色清單中找到所需的角色令人沮喪,Azure AD 可以基於角色類別顯示角色子集。 檢視[Azure AD 角色和管理員](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)的新**類型**篩選器,僅顯示所選類型中的角色。

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>現在存在的角色在分配全域管理員角色時不存在

可能將角色或角色添加到 Azure AD 中,該角色或角色提供了更精細的許可權,而當您將某些使用者提升到全域管理員時,這些許可權不是選項。 隨著時間的推移,我們將推出其他角色,以完成只有全域管理員角色才能完成的任務。 您可以看到這些反映在以下[可用角色中](#available-roles)。

## <a name="assign-or-remove-administrator-roles"></a>指派或移除系統管理員角色

若要了解如何將系統管理角色指派給 Azure Active Directory 中的使用者，請參閱[在 Azure Active Directory 中檢視和指派系統管理員角色](directory-manage-roles-portal.md)。

## <a name="available-roles"></a>可用的角色

可用的系統管理員角色如下：

### <a name="application-administrator"></a>[應用程式管理員](#application-administrator-permissions)

此角色中的使用者可以建立和管理企業應用程式、應用程式註冊和應用程式 Proxy 設定的所有層面。 請注意,在創建新的應用程式註冊或企業應用程式時,分配給此角色的使用者不會添加為擁有者。

應用程式管理員可以管理應用程式認證,以允許他們類比應用程式。 因此,分配給此角色的使用者可以僅管理未分配給任何 Azure AD 角色的應用程式或僅分配給以下管理員角色的應用程式的應用程式認證:

* 應用程式系統管理員
* 應用程式開發人員
* 雲端應用程式系統管理員
* 目錄讀取器

如果應用程式分配給上述未提及的任何其他角色,則應用程式管理員無法管理該應用程式的憑據。

此角色還授予_同意_委派許可權和應用程式許可權的許可權的許可權,但 Microsoft 圖形 API 上的許可權除外。

> [!IMPORTANT]
> 此例外情況意味著您仍然可以同意_其他_應用的許可權(例如,已註冊的非 Microsoft 應用或應用),但不能同意對 Azure AD 本身的許可權。 您仍然可以在應用註冊時_請求_這些許可權,但_授予_(即同意)這些許可權需要 Azure AD 管理員。這意味著惡意用戶無法輕鬆提升其許可權,例如,通過創建和同意可以寫入整個目錄的應用,並通過該應用的許可權提升自己,成為全域管理員。

### <a name="application-developer"></a>[應用程式開發人員](#application-developer-permissions)

將「使用者可以註冊應用程式」設定設為「否」時，此角色中的使用者可以建立應用程式註冊。 當"使用者可以同意代表他們訪問公司數據的應用"設置設置為"否"時,此角色還授予代表自己同意的許可權。 創建新應用程式註冊或企業應用程式時,分配給此角色的用戶將作為擁有者添加。

### <a name="authentication-administrator"></a>[驗證管理員](#authentication-administrator-permissions)

具有此角色的使用者可以為某些使用者設置或重置非密碼認證,並可以更新所有使用者的密碼。 認證管理員可以要求非管理員或分配給某些角色的使用者根據現有非密碼認證(例如 MFA 或 FIDO)重新註冊,還可以撤銷**裝置上的記住 MFA,** 這提示在下一個登入時輸入 MFA。 這些操作僅適用於非管理員或分配了以下一個或多個角色的使用者:

* 驗證系統管理員
* 目錄讀取器
* 來賓邀請者
* 訊息中心讀取者
* 報告讀者

[特權身份驗證管理員](#privileged-authentication-administrator)角色具有許可權,可以強制所有用戶重新註冊和多重身份驗證。

> [!IMPORTANT]
> 對於可存取機密或私人資訊或 Azure Active Directory 內外重要組態的人員，具備此角色的使用者可以變更認證。 變更使用者的認證表示可承擔該使用者身分識別和權限。 例如：
>
>- 應用程式註冊和企業應用程式擁有者，他們可以管理他們自己的應用程式認證。 這些應用程式在 Azure AD 中可能有特殊權限，而在其他地方未授與驗證系統管理員。 通過此路徑,身份驗證管理員可能能夠假定應用程式擁有者的身份,然後通過更新應用程式的憑據進一步假定特權應用程式的身份。
>- Azure 訂用帳戶擁有者，他們具有機密或私人資訊或者 Azure 中重要組態的存取權。
>- 安全性群組和 Office 365 群組擁有者，他們可以管理群組成員資格。 這個群組可以存取機密或私人資訊或者 Azure AD 和其他位置中的重要組態。
>- Azure AD 外部其他服務 (例如，Exchange Online、Office 安全性與合規性中心和人力資源系統) 中的系統管理員。
>- 非系統管理員，例如主管、法律顧問和人力資源員工，他們可以存取機密或私人資訊。

### <a name="azure-devops-administrator"></a>[Azure 開發人員管理員](#azure-devops-administrator-permissions)

具有此角色的使用者可以管理 Azure DevOps 策略,以將新的 Azure DevOps 組織創建限制為一組可配置的使用者或組。 此角色中的使用者可以通過支援公司 Azure AD 組織的任何 Azure DevOps 組織管理此策略。

所有企業 Azure DevOps 策略都可以由在此角色中的使用者可以管理。

### <a name="azure-information-protection-administrator"></a>[Azure 資訊保護管理員](#azure-information-protection-administrator-permissions)

具有此角色的使用者在 Azure 資訊保護服務上擁有所有權限。 此角色允許設定「Azure 資訊保護」原則的標籤、管理保護範本，以及啟用保護。 此角色並未授與「Identity Protection 中心」、Privileged Identity Management、「監視 Office 365 服務健康情況」及「Office 365 安全與規範中心」中的任何權限。

### <a name="b2c-user-flow-administrator"></a>[B2C 使用者流管理員](#b2c-user-flow-administrator-permissions)

具有此角色的用戶可以在 Azure 門戶中創建和管理 B2C 使用者流(也稱為"內置"策略)。通過創建或編輯使用者流,這些使用者可以更改使用者體驗的 html/CSS/javascript 內容、更改每個使用者流的 MFA 要求、更改權杖中的聲明以及調整租戶中所有策略的作業階段設定。 另一方面,此角色不包括查看用戶數據或更改租戶架構中包含的屬性的能力。對身份體驗框架(也稱為自定義)策略的更改也不屬於此角色的範圍。

### <a name="b2c-user-flow-attribute-administrator"></a>[B2C 使用者流屬性管理員](#b2c-user-flow-attribute-administrator-permissions)

具有此角色的使用者添加或刪除租戶中所有使用者流可用的自定義屬性。因此,具有此角色的使用者可以更改或向最終用戶架構添加新元素,並影響所有使用者流的行為,並間接導致最終使用者可能要求哪些數據更改,並最終作為聲明發送到應用程式。此角色無法編輯使用者流。

### <a name="b2c-ief-keyset-administrator"></a>[B2C IEF 金鑰集管理員](#b2c-ief-keyset-administrator-permissions)

用戶可以創建和管理權杖加密、權杖簽名和聲明加密/解密的策略密鑰和機密。通過向現有密鑰容器添加新密鑰,此受限管理員可以根據需要滾動機密,而不會影響現有應用程式。此使用者可以查看這些機密的全部內容及其到期日期,即使在創建之後也是如此。

> [!IMPORTANT]
> 這是一個敏感的角色。在預生產和生產期間,應仔細審核和分配密鑰集管理員角色。

### <a name="b2c-ief-policy-administrator"></a>[B2C IEF 政策管理員](#b2c-ief-policy-administrator-permissions)

此角色中的使用者可以創建、讀取、更新和刪除 Azure AD B2C 中的所有自定義策略,因此可以完全控制相關 Azure AD B2C 租戶中的標識體驗框架。 通過編輯策略,此使用者可以與外部標識提供程式建立直接聯合,更改目錄架構,更改面向所有使用者的內容(HTML、CSS、JAvaScript)、更改要求以完成身份驗證、創建新使用者、將使用者數據發送到外部系統(包括完全遷移)以及編輯所有使用者資訊,包括密碼和電話號碼等敏感欄位。 相反,此角色無法更改加密密鑰或編輯用於租戶中聯合使用的秘密。

> [!IMPORTANT]
> B2 IEF 策略管理員是一個高度敏感的角色,應在非常有限的基礎上為生產中的租戶分配。應仔細審核這些用戶的活動,尤其是對於生產中的租戶。

### <a name="billing-administrator"></a>[計費管理員](#billing-administrator-permissions)

進行採購、管理訂用帳戶、管理支援票證，以及監控服務健全狀況。

### <a name="cloud-application-administrator"></a>[雲端應用程式系統管理員](#cloud-application-administrator-permissions)

此角色中的使用者具有與應用程式系統管理員角色相同的權限，但不包括管理應用程式 Proxy 的能力。 此角色會授與能力來建立和管理企業應用程式和應用程式註冊的所有層面。 此角色還授予同意委派許可權的許可權的許可權以及不包括 Microsoft 圖形 API 的應用程式許可權。 創建新應用程式註冊或企業應用程式時,分配給此角色的使用者不會添加為擁有者。

雲應用程式管理員可以管理應用程式認證,允許他們類比應用程式。 因此,分配給此角色的使用者可以僅管理未分配給任何 Azure AD 角色的應用程式或僅分配給以下管理員角色的應用程式的應用程式認證:

* 應用程式開發人員
* 雲端應用程式系統管理員
* 目錄讀取器

如果應用程式分配給上述未提及的任何其他角色,則雲應用程式管理員無法管理該應用程式的憑據。

### <a name="cloud-device-administrator"></a>[雲設備管理員](#cloud-device-administrator-permissions)

此角色的使用者可以啟用、停用和刪除 Azure AD 中的裝置，並在 Azure 入口網站中讀取 Windows 10 BitLocker 金鑰 (如果有的話)。 此角色不會授與可供管理裝置上任何其他屬性的權限。

### <a name="compliance-administrator"></a>[合規性管理員](#compliance-administrator-permissions)

具備此角色的使用者有權限管理 Microsoft 365 合規性中心、Microsoft 365 系統管理中心、Azure 和 Office 365 安全性與合規性中心中的合規性相關功能。 受讓人還可以管理 Exchange 管理中心中的所有功能,團隊& Skype 業務管理中心,併為 Azure 和 Microsoft 365 創建支援票證。 如需詳細資訊，請參閱[關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

在 | 可以執行
----- | ----------
[Microsoft 365 合規性中心](https://protection.office.com) | 跨 Microsoft 365 服務保護和管理組織的資料<br>管理合規性警示
[合規性管理員](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 追蹤、指派和確認您組織的法規合規性活動
[Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理資料治理<br>執行法律和資料的調查<br>管理資料主體要求<br><br>此角色具有與 Office 365 安全&合規性中心基於角色的訪問控制中的[合規性管理員角色組](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)相同的許可權。
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 檢視所有的 Intune 稽核資料
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 具有唯讀權限，並可管理警示<br>可建立和修改檔案原則，並允許檔案治理動作<br>可檢視 [資料管理] 下的所有內建報告

### <a name="compliance-data-administrator"></a>[相容性資料管理員](#compliance-data-administrator-permissions)

具有此角色的使用者具有在 Microsoft 365 合規性中心、Microsoft 365 管理中心和 Azure 中追蹤數據的許可權。 使用者還可以跟蹤 Exchange 管理中心、合規性管理器和 Skype 業務管理中心&團隊內的合規性數據,並為 Azure 和 Microsoft 365 創建支援票證。

在 | 可以執行
----- | ----------
[Microsoft 365 合規性中心](https://protection.office.com) | 監控 Microsoft 365 服務中與合規性相關的策略<br>管理合規性警示
[合規性管理員](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 追蹤、指派和確認您組織的法規合規性活動
[Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理資料治理<br>執行法律和資料的調查<br>管理資料主體要求<br><br>此角色具有與 Office 365 安全&合規性中心基於角色的訪問控制中的[合規性數據管理員角色組](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)相同的許可權。
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 檢視所有的 Intune 稽核資料
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 具有唯讀權限，並可管理警示<br>可建立和修改檔案原則，並允許檔案治理動作<br>可檢視 [資料管理] 下的所有內建報告

### <a name="conditional-access-administrator"></a>[條件存取管理員](#conditional-access-administrator-permissions)

具有此角色的用戶能夠管理 Azure 活動目錄條件訪問設置。
> [!NOTE]
> 要在 Azure 中部署 Exchange ActiveSync 條件存取策略,用戶還必須是全域管理員。

### <a name="customer-lockbox-access-approver"></a>[客戶密碼箱存取核准人](#customer-lockbox-access-approver-permissions)

管理您組織中的[客戶加密箱要求](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests)。 他們會收到「客戶加密箱」要求的電子郵件通知，並且可以核准和拒絕來自 Microsoft 365 系統管理中心的要求。 他們也可以開啟或關閉「客戶加密箱」功能。 只有全域管理員可以重設指派給此角色之人員的密碼。

### <a name="desktop-analytics-administrator"></a>[電腦分析系統管理員](#desktop-analytics-administrator-permissions)


此角色中的使用者可以管理桌面分析和辦公室自定義&策略服務。 對於桌面分析,這包括查看資產清單、創建部署計劃、查看部署和運行狀況。 對於 Office 自定義&策略服務,此角色使用戶能夠管理 Office 策略。

### <a name="device-administrator"></a>[裝置管理員](#device-administrators-permissions)

此角色是只能指派為[裝置設定](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)中的其他本機系統管理員。 具有此角色的使用者，會在已加入 Azure Active Directory 的所有 Windows 10 裝置上，成為本機電腦系統管理員。 它們並沒有在 Azure Active Directory 中管理裝置物件的能力。

### <a name="directory-readers"></a>[目錄讀取器](#directory-readers-permissions)

此角色中的用戶可以讀取基本目錄資訊。 此角色應用於:
* 授予一組特定的來賓使用者讀取訪問許可權,而不是將其授予所有來賓使用者。
* 將「僅限制對 Azure AD 門戶的訪問限制為管理員」設置為「是」時,授予一組特定的非管理員使用者訪問 Azure 門戶的許可權。
* 授予服務主體對目錄的訪問許可權,其中目錄.Read.All 不是一個選項。

### <a name="directory-synchronization-accounts"></a>[目錄同步處理帳戶](#directory-synchronization-accounts-permissions)

請勿使用。 此角色會自動指派給 Azure AD Connect 服務，不適用於也不支援任何其他用途。

### <a name="directory-writers"></a>[目錄撰寫者](#directory-writers-permissions)

這是舊版角色，用來指派給不支援[同意架構](../develop/quickstart-register-app.md)的應用程式。 不應將它指派給任何使用者。

### <a name="dynamics-365-administrator--crm-administrator"></a>[動態 365 管理員 / CRM 管理員](#crm-service-administrator-permissions)

此角色的使用者具有 Microsoft Dynamics 365 Online (如其存在) 的全域權限，並能管理支援票證及監視服務的健康情況。 如需詳細資訊，請參閱[使用服務管理員角色管理您的租用戶](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)。

> [!NOTE]
> 在 Microsoft 圖形 API 和 Azure AD PowerShell 中,此角色標識為「動態 365 服務管理員」。。 它是[Azure 門戶](https://portal.azure.com)中的「動態 365 管理員」。

### <a name="exchange-administrator"></a>[Exchange 系統管理員](#exchange-service-administrator-permissions)

此角色的使用者具有 Microsoft Exchange Online (如其存在) 的全域權限。 此外，也具備建立和管理所有「Office 365 群組」、管理支援票證，以及監視服務健康情況的能力。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

> [!NOTE]
> 在 Microsoft 圖形 API 和 Azure AD PowerShell 中,此角色標識為「Exchange 服務管理員」。 它是[Azure 門戶](https://portal.azure.com)中的『交換管理員』。 它是[交換管理中心](https://go.microsoft.com/fwlink/p/?LinkID=529144)中的「交換在線管理員」。

### <a name="external-identity-provider-administrator"></a>[外部識別提供者管理員](#external-identity-provider-administrator-permissions)

此管理員管理 Azure 活動目錄租戶和外部標識提供程式之間的聯合。通過此角色,使用者可以添加新標識提供程式並配置所有可用設置(例如身份驗證路徑、服務 ID、分配的密鑰容器)。此使用者可以使租戶信任來自外部標識提供程序的身份驗證。由此產生的對最終使用者體驗的影響取決於租戶的類型:

* Azure 活動目錄租戶的員工和合作夥伴:添加聯合(例如使用 Gmail)將立即影響尚未兌換的所有來賓邀請。 請參考[新增 Google 作為 B2B 來賓使用者的身份供應商](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)。
* Azure 活動目錄 B2C 租戶:添加聯合(例如,使用 Facebook 或其他 Azure AD 組織)不會立即影響最終使用者流,直到標識提供程式作為使用者流中的選項添加(也稱為內置策略)。 有關範例[,請參閱將 Microsoft 帳號設定為識別提供者](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app)。要更改使用者流,需要"B2C 使用者流管理員"的有限角色。

### <a name="global-administrator--company-administrator"></a>[全球管理員/公司管理員](#company-administrator-permissions)

具有此角色的使用者可以存取 Azure Active Directory 中所有的系統管理功能，以及使用 Azure Active Directory 身分識別的服務，例如 Microsoft 365 資訊安全中心、Microsoft 365 合規性中心、Exchange Online、SharePoint Online 和商務用 Skype Online。 註冊 Azure Active Directory 租用戶的人員會變成全域管理員。 您的公司可以有多位全域管理員。 全域系統管理員可以為任何使用者和所有其他系統管理員重設密碼。

> [!NOTE]
> 在 Microsoft 圖形 API 和 Azure AD PowerShell 中,此角色標識為"公司管理員"。 它是 [Azure 入口網站](https://portal.azure.com)中的「全域管理員」。
>
>

### <a name="global-reader"></a>[全域讀者](#global-reader-permissions)

此角色中的用戶可以讀取 Microsoft 365 服務的設置和管理資訊,但不能執行管理操作。 全域讀取器是全域管理員的唯讀對應器。 分配全域讀取器而不是全域管理員進行規劃、審核或調查。 將全域讀取器與其他有限的管理員角色(如 Exchange Admin)結合使用,使其在未分配全域管理員角色的情況下更輕鬆地完成工作。 全球閱讀器與Microsoft 365管理中心、交換管理中心、SharePoint管理中心、團隊管理中心、安全中心、合規性中心、Azure AD管理中心和設備管理管理中心合作。

> [!NOTE]
> 全球讀者角色現在有一些限制 -
>
>- [OneDrive 管理中心](https://admin.onedrive.com/)- OneDrive 管理中心不支援全域閱讀器角色。
>- [Azure AD 門戶](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)- 全域讀取器無法讀取企業應用的預配模式。
>- [M365 管理中心](https://admin.microsoft.com/Adminportal/Home#/homepage)- 全域讀取器無法讀取客戶密碼箱請求。 在 M365 管理中心左邊窗格中的 **「支援」** 下找不到 **「客戶密碼箱請求**」選項卡。
>- [M365 安全中心](https://security.microsoft.com/homepage)- 全域讀取器無法讀取靈敏度和保留標籤。 在 M365 安全中心的左邊窗格中找不到 **「敏感度」標籤**、**保留標籤**和**標籤分析**選項卡。
>- [辦公室安全&合規性中心](https://sip.protection.office.com/homepage)- 全域讀者無法讀取 SCC 審核日誌、執行內容搜索或查看安全分數。
>- [團隊管理中心](https://admin.teams.microsoft.com)- 全球閱讀器不能讀取**團隊生命週期**,**分析&報告****,IP 電話裝置管理和****應用程式目錄**。
>- [特權存取管理 (PAM)](https://docs.microsoft.com/office365/securitycompliance/privileged-access-management-overview)不支援全域讀取器角色。
>- [Azure 資訊保護](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)- 全域讀取器僅[支援中央報告](https://docs.microsoft.com/azure/information-protection/reports-aip),並且 Azure AD 組織不在[統一標籤平臺上](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)時。
>
> 這些功能目前正在開發中。
>

### <a name="groups-administrator"></a>[組管理員](#groups-administrator-permissions)

此角色中的使用者可以創建/管理組及其設置,如命名和過期策略。 請務必瞭解,將使用者分配給此角色,使他們能夠管理租戶中除 Outlook 之外的各種工作負載(如 Teams、SharePoint、Yammer)中的所有組。 此外,使用者將能夠管理各種管理門戶(如Microsoft管理中心、Azure門戶)以及各種組設置,以及特定於工作負荷的組設置,如團隊和SharePoint管理中心。

### <a name="guest-inviter"></a>[來賓邀請者](#guest-inviter-permissions)

當 [成員可邀請]**** 使用者設定為 [否] 時，此角色中的使用者可以管理 Azure Active Directory B2B 來賓使用者的邀請 在[關於 Azure AD B2B 共同作業](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)中查看 B2B 共同作業的詳細資訊。 這不包含任何其他權限。

### <a name="helpdesk-administrator"></a>[服務台系統管理員](#helpdesk-administrator-permissions)

具備此角色的使用者可以變更密碼、讓重新整理權杖失效、管理服務要求，以及監視服務健康情況。 讓重新整理權杖失效會強制使用者重新登入。 協助管理員可以重置密碼,使非管理員或僅分配以下角色的其他使用者的刷新權杖無效:

* 目錄讀取器
* 來賓邀請者
* 服務台系統管理員
* 訊息中心讀取者
* 報告讀者

> [!IMPORTANT]
> 具備此角色的使用者可以變更可存取機密或私人資訊或 Azure Active Directory 內外重要組態的人員密碼。 變更使用者的密碼表示可承擔該使用者身分識別和權限。 例如：
>
>- 應用程式註冊和企業應用程式擁有者，他們可以管理他們自己的應用程式認證。 這些應用程式在 Azure AD 中可能有特殊權限，而在其他地方未授與技術支援中心系統管理員。 技術支援中心系統管理員可以透過此路徑承擔應用程式擁有者的身分識別，然後藉由更新應用程式的認證，進一步承擔特殊權限應用程式的身分識別。
>- Azure 訂閱擁有者,他們有權訪問 Azure 中的敏感或私有資訊或關鍵配置。
>- 安全性群組和 Office 365 群組擁有者，他們可以管理群組成員資格。 這個群組可以存取機密或私人資訊或者 Azure AD 和其他位置中的重要組態。
>- Azure AD 外部其他服務 (例如，Exchange Online、Office 安全性與合規性中心和人力資源系統) 中的系統管理員。
>- 非系統管理員，例如主管、法律顧問和人力資源員工，他們可以存取機密或私人資訊。

[使用管理單位(現在在公共預覽版中)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units)可以將管理許可權委派給使用者子集並將策略應用於使用者子集。

此角色以前在[Azure 門戶](https://portal.azure.com/)中稱為"密碼管理員」。。 Azure AD 中的"幫助台管理員"名稱現在與其名稱在 Azure AD PowerShell 和 Microsoft 圖形 API 中匹配。

### <a name="hybrid-identity-administrator"></a>[混合識別管理員](#hybrid-identity-administrator-permissions)

此角色中的用戶可以在 Azure AD 中啟用、配置和管理與啟用混合標識相關的服務和設置。 此角色授予將 Azure AD 設定為三種受支援的身份驗證方法之一,即密碼哈希同步 (PHS)、直通身份驗證 (PTA) 或聯合身份驗證(AD FS 或第三方聯合提供程式),以及部署相關的本地基礎結構以啟用它們。 預置基礎結構包括預配和 PTA 代理。 此角色允許啟用無縫單一登錄 (S-SSO),以便在非 Windows 10 設備或非 Windows Server 2016 電腦上啟用無縫身份驗證。 此外,此角色還允許查看登錄日誌以及訪問運行狀況和分析以用於監視和故障排除目的。 

### <a name="intune-administrator"></a>[Intune 管理員](#intune-service-administrator-permissions)

此角色的使用者具有 Microsoft Intune Online (如其存在) 的全域權限。 此外，此角色包含管理使用者和裝置的能力，可相關聯原則以及建立和管理群組。 有關微軟[Intune 基於角色的管理控制 (RBAC)](https://docs.microsoft.com/intune/role-based-access-control)的更多資訊。

此角色可以創建和管理所有安全組。 但是,Intune 管理員對 Office 組沒有管理員許可權。 這意味著管理員無法更新租戶中所有 Office 組的所有者或成員身份。 但是,他/她可以管理他創建的 Office 組,該組是其最終用戶許可權的一部分。 因此,他/她創建的任何 Office 組(不是安全組)都應計入其配額 250。

> [!NOTE]
> 在 Microsoft 圖形 API 和 Azure AD PowerShell 中,此角色標識為"Intune 服務管理員" 它是[Azure 門戶](https://portal.azure.com)中的「Intune 管理員」。

### <a name="kaizala-administrator"></a>[凱紮拉管理員](#kaizala-administrator-permissions)

具有此角色的使用者具有全域許可權,可在存在服務時管理 Microsoft Kaizala 中的設置,以及管理支援票證和監視服務運行狀況的功能。 此外,用戶可以造訪與採用&组织成员使用 Kaizala 相關的報告,以及使用 Kaizala 操作生成的業務報告。

### <a name="license-administrator"></a>[授權管理員](#license-administrator-permissions)

此角色中的使用者可新增、移除和更新使用者和群組的授權指派 (使用群組型授權)，以及管理使用者的使用位置。 此角色不會授與購買或管理訂用帳戶、建立或管理群組，或在使用位置以外建立或管理使用者的能力。 這個角色沒有檢視、建立或管理支援票證的存取權。

### <a name="message-center-privacy-reader"></a>[訊息中心隱私閱讀器](#message-center-privacy-reader-permissions)

此角色中的使用者可以監視消息中心中的所有通知,包括數據隱私消息。 郵件中心隱私讀者收到電子郵件通知,包括與數據隱私相關的通知,他們可以使用郵件中心首選項取消訂閱。 只有全域管理員和郵件中心隱私讀取器才能讀取數據隱私消息。 此外,此角色包含查看組、域和訂閱的功能。 此角色無權查看、創建或管理服務請求。

### <a name="message-center-reader"></a>[訊息中心讀取者](#message-center-reader-permissions)

此角色中的使用者可以在 [Office 365 訊息中心](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)內，為他們的組織監視所設服務 (例如 Exchange、Intune 和 Microsoft Teams) 的通知和諮詢健康情況更新。 訊息中心讀者每週會收到貼文的電子郵件摘要和更新，並且可以在 Office 365 中分享訊息中心的貼文。 在 Azure AD 中，指派至此角色的使用者只會有 Azure AD 服務的唯讀存取權，與使用者和群組一樣。 這個角色沒有檢視、建立或管理支援票證的存取權。

### <a name="network-administrator"></a>[網路管理員](#network-administrator-permissions)

此角色中的使用者可以查看 Microsoft 基於使用者位置的網路遙測的網路外圍體系結構建議。 Office 365 的網路性能依賴於謹慎的企業客戶網路外圍體系結構,這些體系結構通常是特定於使用者位置的。 此角色允許編輯已發現的使用者位置並配置這些位置的網路參數,以便改進遙測測量和設計建議。 

### <a name="office-apps-administrator"></a>[辦公室應用管理員](#office-apps-administrator-permissions)

此角色中的使用者可以管理 Office 365 應用的雲端設置。 這包括管理雲策略、自助服務下載管理和查看 Office 應用相關報告的能力。 此角色還授予管理支援票證和監視主管理中心中的服務運行狀況的能力。 分配給此角色的使用者還可以管理 Office 應用中新功能的通信。 

### <a name="partner-tier1-support"></a>[合作夥伴第 1 層支援](#partner-tier1-support-permissions)

請勿使用。 此角色已被取代，而且未來將從 Azure AD 中移除。 此角色僅供少數 Microsoft 轉售合作夥伴使用，不適用於一般用途。

### <a name="partner-tier2-support"></a>[合作夥伴第 2 層支援](#partner-tier2-support-permissions)

請勿使用。 此角色已被取代，而且未來將從 Azure AD 中移除。 此角色僅供少數 Microsoft 轉售合作夥伴使用，不適用於一般用途。

### <a name="password-administrator"></a>[密碼管理員](#password-administrator-permissions)

具有此角色的使用者管理密碼的能力有限。 此角色不授予管理服務請求或監視服務運行狀況的許可權。 密碼管理員只能重置非管理員或以下角色成員的其他使用者的密碼:

* 目錄讀取器
* 來賓邀請者
* 密碼管理員

### <a name="power-bi-administrator"></a>[Power BI 管理員](#power-bi-service-administrator-permissions)

此角色的使用者具有 Microsoft Power BI (如其存在) 的全域權限，並能管理支援票證及監視服務的健康情況。 如需詳細資訊，請參閱[了解 Power BI 管理員角色](https://docs.microsoft.com/power-bi/service-admin-role)。

> [!NOTE]
> 在 Microsoft 圖形 API 和 Azure AD PowerShell 中,此角色標識為"Power BI 服務管理員"。 它是[Azure 門戶](https://portal.azure.com)中的「Power BI 管理員」。

### <a name="power-platform-administrator"></a>[電源平台管理員](#power-platform-administrator-permissions)

此角色中的用戶可以創建和管理環境的所有方面,PowerApps、流、數據丟失防護策略。 此外,具有此角色的用戶能夠管理支援票證並監視服務運行狀況。

### <a name="printer-administrator"></a>[印表機管理員](#printer-administrator-permissions)

此角色的用戶可以註冊印表機並管理 Microsoft 通用列印解決方案中所有印表機配置的所有方面,包括通用列印連接器設置。 他們可以同意所有委派的列印許可權請求。 印表機管理員還可以訪問列印報表。

### <a name="printer-technician"></a>[印表機技術員](#printer-technician-permissions)

具有此角色的用戶可以在 Microsoft 通用列印解決方案中註冊印表機和管理印表機狀態。 他們還可以讀取所有連接器資訊。 印表機技術人員無法執行的關鍵任務是設置印表機和共用印表機的用戶許可權。

### <a name="privileged-authentication-administrator"></a>[特權識別管理員](#privileged-authentication-administrator-permissions)

具有此角色的使用者可以為所有使用者(包括全域管理員)設置或重置非密碼認證,並可以更新所有使用者的密碼。 特權身份驗證管理員可以強制使用者根據現有非密碼認證(如MFA或 FIDO)重新註冊,並撤銷「記住裝置上的 MFA」,從而在所有使用者的下一次登錄時提示 MFA。 [認證管理員](#authentication-administrator)角色可以強制僅對分配給以下 Azure AD 角色的非管理員和使用者重新註冊和 MFA:

* 驗證系統管理員
* 目錄讀取器
* 來賓邀請者
* 訊息中心讀取者
* 報告讀者

### <a name="privileged-role-administrator"></a>[特殊權限角色管理員](#privileged-role-administrator-permissions)

具備此角色的使用者可以管理 Azure Active Directory 中，以及 Azure AD Privileged Identity Management 內的角色指派。 此外,該角色允許管理特權身份管理和行政單位的所有方面。

> [!IMPORTANT]
> 此角色授予管理所有 Azure AD 角色(包括全域管理員角色)的分配的能力。 此角色不包含 Azure AD 中的任何其他特殊權限能力，例如建立或更新使用者。 不過，指派給這個角色的使用者可以藉由指派額外的角色，來授與自己或其他人額外權限。

### <a name="reports-reader"></a>[報表讀取器](#reports-reader-permissions)

具有此角色的用戶可以在 Microsoft 365 管理中心查看使用方式報告數據和報告儀錶板,並在 Power BI 中查看採用上下文包。 此外，此角色還可讓使用者存取 Azure AD 中的登入報告與活動，以及 Microsoft Graph 報告 API 所傳回的資料。 獲指派「報告讀者」角色的使用者只能存取相關的使用情況和採用計量。 他們並不具備任何系統管理權限，因此無法進行設定或存取產品特定的系統管理中心 (例如 Exchange)。 這個角色沒有檢視、建立或管理支援票證的存取權。

### <a name="search-administrator"></a>[搜尋管理員](#search-administrator-permissions)

此角色中的用戶可以完全訪問 Microsoft 365 管理中心中的所有 Microsoft 搜索管理功能。 搜索管理員可以將搜索管理員和搜索編輯器角色委派給使用者,並創建和管理內容,如書籤、Q&As 和位置。 此外,這些使用者可以查看消息中心、監視服務運行狀況和創建服務請求。

### <a name="search-editor"></a>[搜尋編輯器](#search-editor-permissions)

此角色中的用戶可以在 Microsoft 365 管理中心創建、管理和刪除 Microsoft 搜尋的內容,包括書籤、Q&As 和位置。

### <a name="security-administrator"></a>[安全性系統管理員](#security-administrator-permissions)

具備此角色的使用者有權限管理 Microsoft 365 資訊安全中心、Azure Active Directory Identity Protection、Azure 資訊保護和 Office 365 安全性與合規性中心中的安全性相關功能。 關於 Office 365 權限的詳細資訊可在 [Office 365 安全性與法規遵循中心的權限](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)中取得。

在 | 可以執行
--- | ---
[Microsoft 365 資訊安全中心](https://protection.office.com) | 監視所有 Microsoft 365 服務的安全性相關原則<br>管理安全性威脅和警示<br>檢視報表
身分識別防護中心 | 「安全性讀取者」角色的所有權限<br>此外，還能夠執行除了重設密碼以外的所有身分識別防護中心作業
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | 「安全性讀取者」角色的所有權限<br>**無法**管理 Azure AD 角色分配或設定
[Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理安全性原則<br>檢視、調查及回應安全性威脅<br>檢視報表
Azure 進階威脅防護 | 監視及回應可疑的安全性活動
Windows Defender ATP 和 EDR | 指派角色<br>管理電腦群組<br>設定端點威脅偵測和自動補救<br>檢視、調查及回應警示
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 檢視使用者、裝置、註冊、設定及應用程式資訊<br>無法對 Intune 進行變更
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 新增管理員、新增原則和設定、上傳記錄及執行治理動作
[Azure 資訊安全中心](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | 可檢視安全性原則、檢視安全性狀態、編輯安全性原則、檢視警示和建議、關閉警示和建議
[Office 365 服務健康情況](https://docs.microsoft.com/office365/enterprise/view-service-health) | 檢視 Office 365 服務的健康情況

### <a name="security-operator"></a>[安全操作員](#security-operator-permissions)

具有此角色的使用者可以管理警報,並具有安全相關功能的全域唯讀訪問許可權,包括 Microsoft 365 安全中心、Azure 活動目錄、標識保護、特權標識管理和 Office 365 安全&合規性中心中的所有資訊。 關於 Office 365 權限的詳細資訊可在 [Office 365 安全性與法規遵循中心的權限](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center)中取得。

在 | 可以執行
--- | ---
[Microsoft 365 資訊安全中心](https://protection.office.com) | 「安全性讀取者」角色的所有權限<br>檢視、調查和回應安全威脅警報
身分識別防護中心 | 「安全性讀取者」角色的所有權限<br>此外，還能夠執行除了重設密碼以外的所有身分識別防護中心作業
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | 「安全性讀取者」角色的所有權限
[Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 「安全性讀取者」角色的所有權限<br>檢視、調查和回應安全警報
Windows Defender ATP 和 EDR | 「安全性讀取者」角色的所有權限<br>檢視、調查和回應安全警報
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 「安全性讀取者」角色的所有權限
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 「安全性讀取者」角色的所有權限
[Office 365 服務健康情況](https://docs.microsoft.com/office365/enterprise/view-service-health) | 檢視 Office 365 服務的健康情況

### <a name="security-reader"></a>[安全性讀取者](#security-reader-permissions)

具備此角色的使用者具有安全性相關功能的全域唯讀存取權 (含 Microsoft 365 資訊安全中心、Azure Active Directory、Identity Protection、Privileged Identity Management 中的所有資訊)，並能讀取 Azure Active Directory 登入報告與稽核記錄，且具有 Office 365 安全性與合規性中心的全域唯讀存取權。 關於 Office 365 權限的詳細資訊可在 [Office 365 安全性與法規遵循中心的權限](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)中取得。

在 | 可以執行
--- | ---
[Microsoft 365 資訊安全中心](https://protection.office.com) | 檢視所有 Microsoft 365 服務的安全性相關原則<br>檢視安全性威脅和警示<br>檢視報表
身分識別防護中心 | 讀取安全性功能的所有安全性報告和設定資訊<br><ul><li>反垃圾郵件<li>加密<li>資料外洩防護<li>反惡意程式碼<li>進階威脅防護<li>防網路釣魚<li>郵件流程規則
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | 已對 Azure AD 特權識別管理中顯示的所有資訊的唯讀存取許可權:Azure AD 角色分配和安全審查的策略和報告。<br>**無法**註冊 Azure AD 特權識別管理或對其進行任何更改。 在特權身份管理門戶或通過 PowerShell 中,如果使用者有資格擔任這些角色,則此角色中的某人可以啟動其他角色(例如,全域管理員或特權角色管理員)。
[Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 檢視安全性原則<br>檢視及調查安全性威脅<br>檢視報表
Windows Defender ATP 和 EDR | 查看和調查警報。 當您在 Windows Defender ATP 中打開基於角色的訪問控制項時,具有唯讀權限(如 Azure AD 安全讀取器角色)的使用者將無法存取,直到他們被分配到 Windows Defender ATP 角色。
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 檢視使用者、裝置、註冊、設定及應用程式資訊。 無法對 Intune 進行變更。
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 具有唯讀權限，並可管理警示
[Azure 資訊安全中心](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | 可檢視建議和警示、檢視安全性原則、檢視安全性狀態，但無法進行變更
[Office 365 服務健康情況](https://docs.microsoft.com/office365/enterprise/view-service-health) | 檢視 Office 365 服務的健康情況

### <a name="service-support-administrator"></a>[服務支援管理員](#service-support-administrator-permissions)

具有此角色的使用者可以使用 Microsoft 打開 Azure 和 Office 365 服務的支援請求,並在[Azure 門戶](https://portal.azure.com)和[Microsoft 365 管理中心](https://admin.microsoft.com)中查看服務儀錶板和消息中心。 有關詳細資訊[,有關管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

> [!NOTE]
> 以前,此角色在[Azure 門戶](https://portal.azure.com)和[Microsoft 365 管理中心](https://admin.microsoft.com)中稱為"服務管理員」。 我們將其重命名為「服務支援管理員」,以便與 Microsoft 圖形 API、Azure AD 圖形 API 和 Azure AD PowerShell 中的外行名稱保持一致。

### <a name="sharepoint-administrator"></a>[SharePoint 管理員](#sharepoint-service-administrator-permissions)

具備此角色的使用者在有 Microsoft SharePoint Online 服務時，於該服務內具有全域權限，以及建立和管理所有 Office 365 群組、管理支援票證和監控服務健康情況的能力。 有關詳細資訊[,有關管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

> [!NOTE]
> 在 Microsoft 圖形 API 和 Azure AD PowerShell 中,此角色標識為「SharePoint 服務管理員」。 它是[Azure 門戶](https://portal.azure.com)中的「共用點管理員」。

### <a name="skype-for-business--lync-administrator"></a>[Skype 業務 / Lync 管理員](#lync-service-administrator-permissions)

在有 Microsoft 商務用 Skype 服務時，具備此角色的使用者在該服務內會具有全域權限，以及在 Azure Active Directory 中管理 Skype 特定的使用者屬性。 此外，此角色會授與管理支援票證及監視服務健康情況的能力，以及存取 Microsoft Teams 和商務用 Skype 系統管理中心的能力。 此帳戶也必須獲得 Microsoft Teams 授權，否則就無法執行 Microsoft Teams PowerShell Cmdlet。 如需詳細資訊，請參閱[關於商務用 Skype 系統管理員角色](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5)，如需 Microsoft Teams 的授權資訊，請參閱[商務用 Skype 和 Microsoft Teams 附加授權](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> 在 Microsoft 圖形 API 和 Azure AD PowerShell 中,此角色標識為「Lync 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com/)中則是「商務用 Skype 管理員」。

### <a name="teams-communications-administrator"></a>[團隊通訊管理員](#teams-communications-administrator-permissions)

此角色的使用者可以管理 Microsoft Teams 在語音和電話語音相關工作負載的各個層面。 這包括電話號碼指派管理工具、語音和會議原則，以及呼叫分析工具組的完整存取權。

### <a name="teams-communications-support-engineer"></a>[團隊通訊支援工程師](#teams-communications-support-engineer-permissions)

此角色的使用者可以使用 Microsoft Teams 和商務用 Skype 系統管理中心內的使用者呼叫疑難排解工具，針對 Microsoft Teams 和商務用 Skype 內的通訊問題進行疑難排解。 此角色的使用者可以檢視所有相關參與者的完整呼叫記錄資訊。 這個角色沒有檢視、建立或管理支援票證的存取權。

### <a name="teams-communications-support-specialist"></a>[團隊通訊支援專家](#teams-communications-support-specialist-permissions)

此角色的使用者可以使用 Microsoft Teams 和商務用 Skype 系統管理中心內的使用者呼叫疑難排解工具，針對 Microsoft Teams 和商務用 Skype 內的通訊問題進行疑難排解。 此角色的使用者只能檢視其所查閱特定使用者的呼叫中所含有的使用者詳細資料。 這個角色沒有檢視、建立或管理支援票證的存取權。

### <a name="teams-service-administrator"></a>[Microsoft Teams 服務管理員](#teams-service-administrator-permissions)

此角色的使用者可以透過 Microsoft Teams 和商務用 Skype 系統管理中心以及個別的 PowerShell 模組，管理 Microsoft Teams 工作負載的所有層面。 這包括所有與電話語音、傳訊、會議和小組本身相關的管理工具以及其他領域。 這個角色會額外獲得授與建立和管理所有 Office 365 群組、管理支援票證，以及監視服務健康情況的能力。

### <a name="user-administrator"></a>[使用者管理員](#user-administrator-permissions)

具有此角色的使用者可以創建使用者,並管理具有某些限制的使用者的所有方面(見下文),並可以更新密碼過期策略。 此外，具有此角色的使用者可以建立與管理所有群組。 此角色也包含建立和管理使用者檢視、管理支援票證，以及監視服務健康情況的能力。 使用者管理員無權管理大多數管理員角色中使用者的某些使用者屬性。 具有此角色的用戶沒有管理 MFA 的子項。 下表列出了對此限制的例外角色。

| | |
| --- | --- |
|一般權限|<p>建立 [使用者和群組]</p><p>建立和管理使用者檢視</p><p>建立 Office 支援票證<p>更新密碼過期原則|
|<p>所有使用者，包括所有管理員</p>|<p>管理授權</p><p>管理使用者主體名稱以外的所有使用者屬性</p>
|只有非管理員或者下列任何有限管理員角色的使用者：<ul><li>目錄讀取器<li>來賓邀請者<li>服務台系統管理員<li>訊息中心讀取者<li>報告讀者<li>使用者管理員|<p>刪除及還原</p><p>停用和啟用</p><p>使重新整理權杖失效</p><p>管理包含使用者主體名稱的所有使用者屬性</p><p>重設密碼</p><p>更新 (FIDO) 裝置金鑰</p>|

> [!IMPORTANT]
> 具備此角色的使用者可以變更可存取機密或私人資訊或 Azure Active Directory 內外重要組態的人員密碼。 變更使用者的密碼表示可承擔該使用者身分識別和權限。 例如：
>
>- 應用程式註冊和企業應用程式擁有者，他們可以管理他們自己的應用程式認證。 這些應用程式在 Azure AD 中可能有特殊權限，而在其他地方未授與使用者系統管理員。 使用者系統管理員可以透過此路徑承擔應用程式擁有者的身分識別，然後藉由更新應用程式的認證，進一步承擔特殊權限應用程式的身分識別。
>- Azure 訂用帳戶擁有者，他們具有機密或私人資訊或者 Azure 中重要組態的存取權。
>- 安全性群組和 Office 365 群組擁有者，他們可以管理群組成員資格。 這個群組可以存取機密或私人資訊或者 Azure AD 和其他位置中的重要組態。
>- Azure AD 外部其他服務 (例如，Exchange Online、Office 安全性與合規性中心和人力資源系統) 中的系統管理員。
>- 非系統管理員，例如主管、法律顧問和人力資源員工，他們可以存取機密或私人資訊。

## <a name="role-permissions"></a>角色權限

下表說明 Azure Active Directory 中賦予每個角色的特定權限。 某些角色在 Azure Active Directory 以外的 Microsoft 服務中可能有額外的權限。

### <a name="application-administrator-permissions"></a>應用程式管理員權限

能夠建立及管理應用程式註冊與企業應用程式的所有層面。

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/應用程式/應用程式代理認證/更新 | 更新 Azure 活動目錄中的服務主體上的應用代理身份驗證屬性。 |
| 微軟.目錄/應用程式/應用程式代理Url設定/更新 | 在 Azure 活動目錄中更新應用程式代理內部和外部 URLS。 |
| 微軟.目錄/應用程式/應用程式代理/讀取 | 讀取所有應用代理屬性。 |
| 微軟.目錄/應用程式/應用程式代理/更新 | 更新所有應用代理屬性。 |
| 微軟.目錄/應用程式/觀眾/更新 | 在 Azure Active Directory 中更新 applications.audience 屬性。 |
| 微軟.目錄/應用程式/身份驗證/更新 | 在 Azure Active Directory 中更新 applications.authentication 屬性。 |
| 微軟.目錄/應用程式/基本/更新 | 更新 Azure Active Directory 中 Applications 的基本屬性。 |
| 微軟.目錄/應用程式/建立 | 在 Azure Active Directory 中建立應用程式。 |
| 微軟.目錄/應用程式/認證/更新 | 在 Azure Active Directory 中更新 applications.credentials 屬性。 |
| 微軟.目錄/應用程式/刪除 | 刪除 Azure Active Directory 中的應用程式。 |
| 微軟.目錄/應用程式/擁有者/更新 | 更新 Azure Active Directory 中的 applications.owners 屬性。 |
| 微軟.目錄/應用程式/許可權/更新 | 在 Azure Active Directory 中更新 applications.permissions 屬性。 |
| 微軟.目錄/應用程式/策略/更新 | 更新 Azure Active Directory 中的 applications.policies 屬性。 |
| 微軟.目錄/應用程式角色分配/創建 | 在 Azure Active Directory 中建立 appRoleAssignments。 |
| 微軟.目錄/應用程式角色分配/讀取 | 讀取 Azure Active Directory 中的 appRoleAssignments。 |
| 微軟.目錄/應用程式Role分配/更新 | 更新在 Azure Active Directory 中的 appRoleAssignments。 |
| 微軟.目錄/應用程式角色分配/刪除 | 刪除 Azure Active Directory 中的 appRoleAssignments。 |
| 微軟.目錄/稽核紀錄/所有屬性/讀取 | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| 微軟.目錄/連接器組/一切/讀取 | 讀取 Azure 活動目錄中的應用程式代理連接器組屬性。 |
| 微軟.目錄/連接器組/一切/更新 | 更新 Azure 活動目錄中的所有應用程式代理連接器組屬性。 |
| 微軟.目錄/連接器群組/建立 | 在 Azure 活動目錄中創建應用程式代理連接器組。 |
| 微軟.目錄/連接器群組/刪除 | 刪除 Azure 活動目錄中的應用程式代理連接器組。 |
| 微軟.目錄/連接器/一切/讀取 | 讀取 Azure 活動目錄中的所有應用程式代理連接器屬性。 |
| 微軟.目錄/連接器/建立 | 在 Azure 活動目錄中創建應用程式代理連接器。 |
| 微軟.目錄/策略/應用程式設定/基本/讀取 | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| 微軟.目錄/策略/應用程式配置/基本/更新 | 更新 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| 微軟.目錄/策略/應用程式設定/建立 | 在 Azure Active Directory 中建立原則。 |
| 微軟.目錄/策略/應用程式設定/刪除 | 刪除 Azure Active Directory 中的原則。 |
| 微軟.目錄/策略/應用程式設定/擁有者/讀取 | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| 微軟.目錄/策略/應用程式設定/擁有者/更新 | 更新 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.目錄/策略/應用程式設定/策略應用於/讀取 | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| 微軟.目錄/服務主體/應用程式角色分配到/更新 | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| 微軟.目錄/服務主體/應用程式角色分配/更新 | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| 微軟.目錄/服務主體/觀眾/更新 | 更新 Azure Active Directory 中的 servicePrincipals.audience 屬性。 |
| 微軟.目錄/服務主體/身份驗證/更新 | 更新 Azure Active Directory 中的 servicePrincipals.authentication 屬性。 |
| 微軟.目錄/服務主體/基本/更新 | 更新 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| 微軟.目錄/服務主體/創建 | 在 Azure Active Directory 中建立 servicePrincipals。 |
| 微軟.目錄/服務主體/憑據/更新 | 更新 Azure Active Directory 中的 servicePrincipals.credentials 屬性。 |
| 微軟.目錄/服務主體/刪除 | 刪除 Azure Active Directory 中的 servicePrincipals。 |
| 微軟.目錄/服務主體/擁有者/更新 | 更新 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.directory/服務主體/許可權/更新 | 更新 Azure Active Directory 中的 servicePrincipals.permissions 屬性。 |
| microsoft.directory/服務主體/策略/更新 | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| 微軟.目錄/簽署報告/所有屬性/讀取 | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="application-developer-permissions"></a>應用程式開發人員權限

可以獨立於「用戶可以註冊應用程式」設置創建應用程式註冊。

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/應用程式/建立擁有者 | 在 Azure Active Directory 中建立應用程式。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| 微軟.目錄/應用程式角色分配/建立擁有者 | 在 Azure Active Directory 中建立 appRoleAssignments。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| 微軟.directory/oAuth2許可權授予/創建擁有者 | 在 Azure Active Directory 中建立 oAuth2PermissionGrants。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.directory/服務主體/創建擁有者 | 在 Azure Active Directory 中建立 servicePrincipals。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |

### <a name="authentication-administrator-permissions"></a>認證管理員權限

可存取以檢視、設定及重設所有非管理員使用者的驗證方法資訊。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/使用者/無效所有刷新權杖 | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| 微軟.目錄/使用者/強身份驗證/更新 | 更新 MFA 認證資訊等增強式驗證屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| 微軟.目錄/使用者/密碼/更新 | 更新 Office 365 組織中所有用戶的密碼。 如需詳細資訊，請參閱線上文件。 |

### <a name="azure-devops-administrator-permissions"></a>Azure 開發人員管理員權限

可以管理 Azure DevOps 組織策略和設置。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 有關詳細資訊,請參閱上面[的角色描述](#azure-devops-administrator)。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.devOps/所有實體/所有任務 | 讀取和配置 Azure DevOps。 |

### <a name="azure-information-protection-administrator-permissions"></a>Azure 資訊保護管理員權限

可以管理 Azure 資訊保護服務的所有方面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 有關詳細資訊,請參閱上面[的角色描述](#)。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 資訊保護的所有層面。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="b2c-user-flow-administrator-permissions"></a>B2C 使用者流管理員權限

創建和管理使用者流的所有方面。

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.b2c/使用者流/所有任務 | 在 Azure 活動目錄 B2C 中讀取和配置使用者流。 |

### <a name="b2c-user-flow-attribute-administrator-permissions"></a>B2C 使用者流屬性管理員權限

創建和管理可供所有使用者流使用的屬性架構。

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.b2c/使用者屬性/所有任務 | 讀取和配置 Azure 活動目錄 B2C 中的使用者屬性。 |

### <a name="b2c-ief-keyset-administrator-permissions"></a>B2C IEF 金鑰集管理員權限

在身份體驗框架中管理聯合和加密的機密。

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.b2c/信任框架/金鑰集/所有任務 | 讀取和設定 Azure 活動目錄 B2C 中的金鑰集。 |

### <a name="b2c-ief-policy-administrator-permissions"></a>B2C IEF 政策管理員權限

在身份體驗框架中創建和管理信任框架策略。

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.b2c/信任框架/策略/所有任務 | 在 Azure 活動目錄 B2C 中讀取和設定自訂策略。 |

### <a name="billing-administrator-permissions"></a>計費管理員權限

能夠執行一般計費相關工作，例如更新付款資訊。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/組織/基本/更新 | 更新 Azure Active Directory 中 organization 的基本屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.commerce.billing/allEntities/allTasks | 管理 Office 365 帳單的所有層面。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="cloud-application-administrator-permissions"></a>雲應用程式管理員權限

能夠建立及管理應用程式註冊與企業應用程式的所有層面，但應用程式 Proxy 除外。

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/應用程式/觀眾/更新 | 在 Azure Active Directory 中更新 applications.audience 屬性。 |
| 微軟.目錄/應用程式/身份驗證/更新 | 在 Azure Active Directory 中更新 applications.authentication 屬性。 |
| 微軟.目錄/應用程式/基本/更新 | 更新 Azure Active Directory 中 Applications 的基本屬性。 |
| 微軟.目錄/應用程式/建立 | 在 Azure Active Directory 中建立應用程式。 |
| 微軟.目錄/應用程式/認證/更新 | 在 Azure Active Directory 中更新 applications.credentials 屬性。 |
| 微軟.目錄/應用程式/刪除 | 刪除 Azure Active Directory 中的應用程式。 |
| 微軟.目錄/應用程式/擁有者/更新 | 更新 Azure Active Directory 中的 applications.owners 屬性。 |
| 微軟.目錄/應用程式/許可權/更新 | 在 Azure Active Directory 中更新 applications.permissions 屬性。 |
| 微軟.目錄/應用程式/策略/更新 | 更新 Azure Active Directory 中的 applications.policies 屬性。 |
| 微軟.目錄/應用程式角色分配/創建 | 在 Azure Active Directory 中建立 appRoleAssignments。 |
| 微軟.目錄/應用程式Role分配/更新 | 更新在 Azure Active Directory 中的 appRoleAssignments。 |
| 微軟.目錄/應用程式角色分配/刪除 | 刪除 Azure Active Directory 中的 appRoleAssignments。 |
| 微軟.目錄/稽核紀錄/所有屬性/讀取 | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| 微軟.目錄/策略/應用程式設定/建立 | 在 Azure Active Directory 中建立原則。 |
| 微軟.目錄/策略/應用程式設定/基本/讀取 | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| 微軟.目錄/策略/應用程式配置/基本/更新 | 更新 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| 微軟.目錄/策略/應用程式設定/刪除 | 刪除 Azure Active Directory 中的原則。 |
| 微軟.目錄/策略/應用程式設定/擁有者/讀取 | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| 微軟.目錄/策略/應用程式設定/擁有者/更新 | 更新 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.目錄/策略/應用程式設定/策略應用於/讀取 | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| 微軟.目錄/服務主體/應用程式角色分配到/更新 | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| 微軟.目錄/服務主體/應用程式角色分配/更新 | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| 微軟.目錄/服務主體/觀眾/更新 | 更新 Azure Active Directory 中的 servicePrincipals.audience 屬性。 |
| 微軟.目錄/服務主體/身份驗證/更新 | 更新 Azure Active Directory 中的 servicePrincipals.authentication 屬性。 |
| 微軟.目錄/服務主體/基本/更新 | 更新 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| 微軟.目錄/服務主體/創建 | 在 Azure Active Directory 中建立 servicePrincipals。 |
| 微軟.目錄/服務主體/憑據/更新 | 更新 Azure Active Directory 中的 servicePrincipals.credentials 屬性。 |
| 微軟.目錄/服務主體/刪除 | 刪除 Azure Active Directory 中的 servicePrincipals。 |
| 微軟.目錄/服務主體/擁有者/更新 | 更新 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.directory/服務主體/許可權/更新 | 更新 Azure Active Directory 中的 servicePrincipals.permissions 屬性。 |
| microsoft.directory/服務主體/策略/更新 | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| 微軟.目錄/簽署報告/所有屬性/讀取 | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="cloud-device-administrator-permissions"></a>雲設備管理員權限

在 Azure AD 中管理裝置的完整存取。

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/稽核紀錄/所有屬性/讀取 | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| 微軟.目錄/裝置/位鎖復原金鑰/讀取 | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| 微軟.目錄/裝置/刪除 | 刪除 Azure Active Directory 中的 devices。 |
| 微軟.目錄/裝置/停用 | 停用 Azure Active Directory 中的 devices。 |
| 微軟.目錄/裝置/啟用 | 啟用 Azure Active Directory 中的裝置。 |
| 微軟.目錄/簽署報告/所有屬性/讀取 | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |

### <a name="company-administrator-permissions"></a>公司管理員權限

可管理使用 Azure AD 身分識別的 Azure AD 與 Microsoft 服務的所有層面。 此角色也稱為全域管理員角色。 

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | 建立和刪除所有資源，同時讀取及更新 microsoft.aad.cloudAppSecurity 中的標準屬性。 |
| microsoft.目錄/管理單位/所有屬性/所有任務 | 建立和刪除 administrativeUnits，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| 微軟.目錄/應用程式/所有屬性/所有任務 | 建立和刪除應用程式，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| 微軟.目錄/應用程式角色分配/所有屬性/所有任務 | 建立和刪除 appRoleAssignments，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| 微軟.目錄/稽核紀錄/所有屬性/讀取 | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| 微軟.目錄/連絡人/所有屬性/所有任務 | 建立和刪除合約，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| 微軟.目錄/合約/所有屬性/所有任務 | 建立和刪除合約，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| 微軟.目錄/裝置/所有屬性/所有任務 | 建立和刪除裝置，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.directory/目錄角色/所有屬性/所有任務 | 建立和刪除 directoryRoles，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| 微軟.目錄/目錄角色樣本/所有屬性/所有任務 | 建立和刪除 directoryRoleTemplates，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| 微軟.目錄/網域/所有屬性/所有任務 | 建立和刪除 domains，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| 微軟.目錄/組/所有屬性/所有任務 | 建立和刪除 groups，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| 微軟.目錄/組設定/所有屬性/所有任務 | 建立和刪除 groupSettings，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.目錄/組設定範本/所有屬性/所有任務 | 建立和刪除 groupSettingTemplates，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| 微軟.目錄/登錄租戶品牌/所有屬性/所有任務 | 建立和刪除 loginTenantBranding，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.directory/oAuth2許可權授予/所有屬性/所有任務 | 建立和刪除 oAuth2PermissionGrants，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| 微軟.目錄/組織/所有屬性/所有任務 | 建立和刪除 organization，同時讀取及更新 Azure Active Directory 中的所有屬性。 |
| microsoft.目錄/策略/所有屬性/所有任務 | 建立和刪除 policies，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.目錄/角色分配/所有屬性/所有任務 | 建立與刪除 roleAssignments，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| 微軟.目錄/角色定義/所有屬性/所有任務 | 建立與刪除 roleDefinitions，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.directory/作用域Role成員/所有屬性/所有任務 | 建立和刪除 scopedRoleMemberships，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.目錄/服務操作/啟動服務 | 可以在 Azure Active Directory 中執行 Activateservice 服務動作 |
| 微軟.目錄/服務操作/停用目錄功能 | 可以在 Azure Active Directory 中執行 Disabledirectoryfeature 服務動作 |
| 微軟.目錄/服務操作/啟用目錄功能 | 可以在 Azure Active Directory 中執行 Enabledirectoryfeature 服務動作 |
| 微軟.目錄/服務操作/取得可用屬性 | 可以在 Azure Active Directory 中執行 Getavailableextentionproperties 服務動作 |
| microsoft.目錄/服務主體/所有屬性/所有任務 | 建立和刪除 servicePrincipals，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| 微軟.目錄/簽署報告/所有屬性/讀取 | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| 微軟.目錄/訂閱Skus/所有屬性/所有任務 | 建立和刪除 subscribedSkus，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| 微軟.目錄/使用者/所有屬性/所有任務 | 建立和刪除 users，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| 微軟.directorySync/所有身份/所有任務 | 執行 Azure AD Connect 中的所有動作。 |
| microsoft.aad.identityProtection/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.aad.identityProtection 中的標準屬性。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 讀取 microsoft.aad.privilegedIdentityManagement 中的所有資源。 |
| microsoft.azure.advancedThreatProtection/allEntities/read | 讀取 microsoft.aad.advancedThreatProtection 中的所有資源。 |
| microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 資訊保護的所有層面。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.commerce.billing/allEntities/allTasks | 管理 Office 365 帳單的所有層面。 |
| microsoft.intune/allEntities/allTasks | 管理 Intune 的所有層面。 |
| microsoft.office365.complianceManager/allEntities/allTasks | 管理 Office 365 合規性管理員的所有層面 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | 管理電腦分析的所有層面。 |
| microsoft.office365.exchange/allEntities/allTasks | 管理 Exchange Online 的所有層面。 |
| microsoft.office365.lockbox/allEntities/allTasks | 管理 Office 365 客戶加密箱的所有層面 |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.messageCenter/securityMessages/read | 讀取 microsoft.office365.messageCenter 中的 securityMessages。 |
| microsoft.office365.protectionCenter/allEntities/allTasks | 管理 Office 365 防護中心的所有層面。 |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.office365.securityComplianceCenter 中的標準屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.sharepoint/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.office365.sharepoint 中的標準屬性。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理商務用 Skype Online 的所有層面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.powerApps.dynamics365/allEntities/allTasks | 管理 Dynamics 365 的所有層面。 |
| microsoft.powerApps.powerBI/allEntities/allTasks | 管理 Power BI 的所有層面。 |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | 讀取 microsoft.aad.defenderAdvancedThreatProtection 中的所有資源。 |

### <a name="compliance-administrator-permissions"></a>合規性管理員權限

可讀取和管理合規性設定及 Azure AD 與 Office 365 中的報告。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.complianceManager/allEntities/allTasks | 管理 Office 365 合規性管理員的所有層面 |
| microsoft.office365.exchange/allEntities/allTasks | 管理 Exchange Online 的所有層面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.sharepoint/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.office365.sharepoint 中的標準屬性。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理商務用 Skype Online 的所有層面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="compliance-data-administrator-permissions"></a>合規性資料管理員權限

創建和管理合規性內容。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | 讀取和配置 Microsoft 雲端應用安全性。 |
| microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 資訊保護的所有層面。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.complianceManager/allEntities/allTasks | 管理 Office 365 合規性管理員的所有層面 |
| microsoft.office365.exchange/allEntities/allTasks | 管理 Exchange Online 的所有層面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.sharepoint/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.office365.sharepoint 中的標準屬性。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理商務用 Skype Online 的所有層面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="conditional-access-administrator-permissions"></a>條件存取管理員權限

可以管理條件訪問功能。

| **動作** | **說明** |
| --- | --- |
| microsoft.目錄/策略/條件存取/基本/讀取 | 讀取 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft.目錄/策略/條件存取/基本/更新 | 更新 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft.目錄/政策/條件存取/創建 | 在 Azure Active Directory 中建立原則。 |
| microsoft.目錄/策略/條件存取/刪除 | 刪除 Azure Active Directory 中的原則。 |
| microsoft.目錄/策略/條件存取/擁有者/讀取 | 讀取 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft.目錄/策略/條件存取/擁有者/更新 | 更新 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft.目錄/策略/條件存取/策略應用於/讀取 | 讀取 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft.目錄/策略/條件存取/租戶預設/更新 | 更新 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |

### <a name="crm-service-administrator-permissions"></a>CRM 服務管理員權限

可管理 Dynamics 365 產品的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.powerApps.dynamics365/allEntities/allTasks | 管理 Dynamics 365 的所有層面。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="customer-lockbox-access-approver-permissions"></a>客戶密碼箱存取核准者權限

可核准 Microsoft 支援要求，以存取客戶組織的資料。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.lockbox/allEntities/allTasks | 管理 Office 365 客戶加密箱的所有層面 |

### <a name="desktop-analytics-administrator-permissions"></a>桌面分析管理員權限

可以管理桌面分析和辦公室自定義&策略服務。 對於桌面分析,這包括查看資產清單、創建部署計劃、查看部署和運行狀況。 對於 Office 自定義&策略服務,此角色使用戶能夠管理 Office 策略。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | 管理電腦分析的所有層面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="device-administrators-permissions"></a>裝置管理員權限

分配給此角色的使用者將添加到 Azure AD 加入設備上的本地管理員組。

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/組設置/基本/讀取 | 讀取 Azure Active Directory 中 groupSettings 的基本屬性。 |
| 微軟.目錄/組設置範本/基本/讀取 | 讀取 Azure Active Directory 中 groupSettingTemplates 的基本屬性。 |

### <a name="directory-readers-permissions"></a>目錄閱讀器權限
可讀取基本目錄資訊。 用來授與應用程式的存取權，不適用於使用者。

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/行政單位/基本/讀取 | 讀取 Azure Active Directory 中 administrativeUnits 的基本屬性。 |
| 微軟.目錄/行政單位/成員/閱讀 | 讀取 Azure Active Directory 中的 administrativeUnits.members 屬性。 |
| 微軟.目錄/應用程式/基本/讀取 | 讀取 Azure Active Directory 中 Applications 的基本屬性。 |
| 微軟.目錄/應用程式/擁有者/讀取 | 讀取 Azure Active Directory 中的 applications.owners 屬性。 |
| 微軟.目錄/應用程式/策略/讀取 | 讀取 Azure Active Directory 中的 applications.policies 屬性。 |
| 微軟.目錄/連絡人/基本/閱讀 | 讀取 Azure Active Directory 中 contacts 的基本屬性。 |
| 微軟.目錄/連絡人/成員/閱讀 | 讀取 Azure Active Directory 中的 contacts.memberOf 屬性。 |
| 微軟.目錄/合同/基本/閱讀 | 讀取 Azure Active Directory 中 contracts 的基本屬性。 |
| 微軟.目錄/裝置/基本/讀取 | 讀取 Azure Active Directory 中 devices 的基本屬性。 |
| 微軟.目錄/裝置/成員/讀取 | 讀取 Azure Active Directory 中的 devices.memberOf 屬性。 |
| 微軟.目錄/設備/註冊擁有者/閱讀 | 讀取 Azure Active Directory 中的 devices.registeredOwners 屬性。 |
| 微軟.目錄/裝置/註冊用戶/閱讀 | 讀取 Azure Active Directory 中的 devices.registeredUsers 屬性。 |
| 微軟.目錄/目錄角色/基本/讀取 | 讀取 Azure Active Directory 中 directoryRoles 的基本屬性。 |
| microsoft.directory/目錄角色/符合條件的成員/閱讀 | 讀取 Azure Active Directory 中的 directoryRoles.eligibleMembers 屬性。 |
| 微軟.目錄/目錄角色/成員/閱讀 | 讀取 Azure Active Directory 中的 directoryRoles.members 屬性。 |
| 微軟.目錄/域/基本/讀取 | 讀取 Azure Active Directory 中 domain 的基本屬性。 |
| 微軟.目錄/組/應用程式角色分配/讀取 | 讀取 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| 微軟.目錄/組/基本/讀取 | 讀取 Azure Active Directory 中 groups 的基本屬性。 |
| 微軟.目錄/組/成員/讀取 | 讀取 Azure Active Directory 中的 Read groups.memberOf 屬性。 |
| 微軟.目錄/組/成員/閱讀 | 讀取 Azure Active Directory 中的 groups.members 屬性。 |
| 微軟.目錄/組/擁有者/讀取 | 讀取 Azure Active Directory 中的 groups.owners 屬性。 |
| 微軟.目錄/組/設定/讀取 | 讀取 Azure Active Directory 中的 groups.settings 屬性。 |
| 微軟.目錄/組設置/基本/讀取 | 讀取 Azure Active Directory 中 groupSettings 的基本屬性。 |
| 微軟.目錄/組設置範本/基本/讀取 | 讀取 Azure Active Directory 中 groupSettingTemplates 的基本屬性。 |
| 微軟.directory/oAuth2許可授予/基本/閱讀 | 讀取 Azure Active Directory 中 oAuth2PermissionGrants 的基本屬性。 |
| 微軟.目錄/組織/基本/讀取 | 讀取 Azure Active Directory 中 organization 的基本屬性。 |
| 微軟.目錄/組織/受信任的「無密碼」/讀取 | 讀取 Azure Active Directory 中的 organization.trustedCAsForPasswordlessAuth 屬性。 |
| 微軟.目錄/角色分配/基本/讀取 | 讀取 Azure Active Directory 中 roleAssignments 的基本屬性。 |
| 微軟.目錄/角色定義/基本/讀取 | 讀取 Azure Active Directory 中 roleDefinitions 的基本屬性。 |
| 微軟.目錄/服務主體/應用程式角色分配/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| 微軟.目錄/服務主體/應用程式角色分配/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| 微軟.目錄/服務主體/基本/讀取 | 讀取 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| 微軟.目錄/服務主體/成員/閱讀 | 讀取 Azure Active Directory 中的 servicePrincipals.memberOf 屬性。 |
| 微軟.目錄/服務主體/oAuth2許可授予/基本/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 屬性。 |
| microsoft.directory/服務主體/擁有物件/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.ownedObjects 屬性。 |
| 微軟.目錄/服務主體/擁有者/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.directory/服務主體/策略/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| 微軟.目錄/訂閱Skus/基本/閱讀 | 讀取 Azure Active Directory 中 subscribedSkus 的基本屬性。 |
| 微軟.目錄/使用者/應用程式角色分配/讀取 | 讀取 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| 微軟.目錄/使用者/基本/閱讀 | 讀取 Azure Active Directory 中 users 的基本屬性。 |
| 微軟.目錄/使用者/直接報告/閱讀 | 讀取 Azure Active Directory 中的 users.directReports 屬性。 |
| 微軟.目錄/使用者/經理/閱讀 | 讀取 Azure Active Directory 中的 users.manager 屬性。 |
| 微軟.目錄/使用者/成員/閱讀 | 讀取 Azure Active Directory 中的 users.memberOf 屬性。 |
| 微軟.目錄/使用者/oAuth2許可授予/基本/閱讀 | 讀取 Azure Active Directory 中的 users.oAuth2PermissionGrants 屬性。 |
| 微軟.目錄/使用者/擁有裝置/讀取 | 讀取 Azure Active Directory 中的 users.ownedDevices 屬性。 |
| 微軟.目錄/使用者/擁有的物件/讀取 | 讀取 Azure Active Directory 中的 users.ownedObjects 屬性。 |
| 微軟.目錄/使用者/註冊設備/讀取 | 讀取 Azure Active Directory 中的 users.registeredDevices 屬性。 |

### <a name="directory-synchronization-accounts-permissions"></a>目錄同步帳戶權限

僅供 Azure AD Connect 服務使用。

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/組織/dirSync/更新 | 更新 Azure Active Directory 中的 organization.dirSync 屬性。 |
| 微軟.目錄/策略/建立 | 在 Azure Active Directory 中建立原則。 |
| 微軟.目錄/策略/刪除 | 刪除 Azure Active Directory 中的原則。 |
| 微軟.目錄/策略/基本/讀取 | 讀取 Azure Active Directory 中 policies 的基本屬性。 |
| 微軟.目錄/策略/基本/更新 | 更新 Azure Active Directory 中 policies 的基本屬性。 |
| 微軟.目錄/策略/擁有者/讀取 | 讀取 Azure Active Directory 中的 policies.owners 屬性。 |
| 微軟.目錄/策略/擁有者/更新 | 更新 Azure Active Directory 中的 policies.owners 屬性。 |
| microsoft.目錄/策略/策略應用於/讀取 | 讀取 Azure Active Directory 中的 policies.policiesAppliedTo 屬性。 |
| microsoft.目錄/策略/租戶預設/更新 | 更新 Azure Active Directory 中的 policies.tenantDefault 屬性。 |
| 微軟.目錄/服務主體/應用程式角色分配/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| 微軟.目錄/服務主體/應用程式角色分配到/更新 | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| 微軟.目錄/服務主體/應用程式角色分配/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| 微軟.目錄/服務主體/應用程式角色分配/更新 | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| 微軟.目錄/服務主體/觀眾/更新 | 更新 Azure Active Directory 中的 servicePrincipals.audience 屬性。 |
| 微軟.目錄/服務主體/身份驗證/更新 | 更新 Azure Active Directory 中的 servicePrincipals.authentication 屬性。 |
| 微軟.目錄/服務主體/基本/讀取 | 讀取 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| 微軟.目錄/服務主體/基本/更新 | 更新 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| 微軟.目錄/服務主體/創建 | 在 Azure Active Directory 中建立 servicePrincipals。 |
| 微軟.目錄/服務主體/憑據/更新 | 更新 Azure Active Directory 中的 servicePrincipals.credentials 屬性。 |
| 微軟.目錄/服務主體/成員/閱讀 | 讀取 Azure Active Directory 中的 servicePrincipals.memberOf 屬性。 |
| 微軟.目錄/服務主體/oAuth2許可授予/基本/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 屬性。 |
| 微軟.目錄/服務主體/擁有者/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| 微軟.目錄/服務主體/擁有者/更新 | 更新 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.directory/服務主體/擁有物件/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.ownedObjects 屬性。 |
| microsoft.directory/服務主體/許可權/更新 | 更新 Azure Active Directory 中的 servicePrincipals.permissions 屬性。 |
| microsoft.directory/服務主體/策略/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft.directory/服務主體/策略/更新 | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| 微軟.directorySync/所有身份/所有任務 | 執行 Azure AD Connect 中的所有動作。 |

### <a name="directory-writers-permissions"></a>目錄編寫器權限

可讀取和寫入基本目錄資訊。 用來授與應用程式的存取權，不適用於使用者。

| **動作** | **說明** |
| --- | --- |
| 微軟目錄/群組/建立 | 在 Azure Active Directory 中建立 groups。 |
| microsoft.目錄/組/建立擁有者 | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| 微軟.目錄/組/應用程式角色分配/更新 | 更新 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| 微軟.目錄/組/基本/更新 | 更新 Azure Active Directory 中 groups 的基本屬性。 |
| 微軟.目錄/組/成員/更新 | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| 微軟.目錄/組/擁有者/更新 | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| 微軟.目錄/組/設定/更新 | 更新 Azure Active Directory 中的 groups.settings 屬性。 |
| 微軟.目錄/組設置/基本/更新 | 更新 Azure Active Directory 中 groupSettings 的基本屬性。 |
| 微軟.目錄/組設定/建立 | 在 Azure Active Directory 中建立 groupSettings。 |
| 微軟.目錄/組設定/刪除 | 在 Azure Active Directory 中刪除 groupSettings。 |
| 微軟.目錄/使用者/應用程式角色分配/更新 | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| 微軟.目錄/使用者/配置授權 | 管理 Azure Active Directory 中的使用者授權。 |
| 微軟.目錄/使用者/基本/更新 | 更新 Azure Active Directory 中 users 的基本屬性。 |
| microsoft.directory/使用者/無效所有刷新權杖 | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| 微軟.目錄/使用者/經理/更新 | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| 微軟.目錄/使用者/使用者主名/更新 | 更新 Azure Active Directory 中的 users.userPrincipalName 屬性。 |

### <a name="exchange-service-administrator-permissions"></a>交換服務管理員權限

可管理 Exchange 產品的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| 微軟.目錄/組/統一/應用程式角色分配/更新 | 更新 Azure Active Directory 中的 groups.unified 屬性。 |
| 微軟.目錄/組/統一/基本/更新 | 更新 Office 365 群組的基本屬性。 |
| 微軟.目錄/組/統一/創建 | 建立 Office 365 群組。 |
| 微軟.目錄/組/統一/刪除 | 刪除 Office 365 群組。 |
| 微軟.目錄/組/統一/成員/更新 | 更新 Office 365 群組的成員資格。 |
| 微軟.目錄/組/統一/擁有者/更新 | 更新 Office 365 群組的擁有權。 |
| microsoft.office365.exchange/allEntities/allTasks | 管理 Exchange Online 的所有層面。 |
| 微軟.office365.網路/性能/所有屬性/讀取 | 閱讀 M365 管理中心中的網路性能頁面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="external-identity-provider-administrator-permissions"></a>外部識別提供者管理員權限

配置標識提供者以用於直接聯合。

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.b2c/身份提供者/所有任務 | 讀取和配置 Azure 活動目錄 B2C 中的標識提供者。 |

### <a name="global-reader-permissions"></a>全域讀取器權限
可以讀取全域管理員可以但無法編輯任何內容的所有內容。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 有關詳細資訊,請參閱上面[的角色描述](#global-reader)。
>
>

| **動作** | **說明** |
| --- | --- |
| 微軟.商業.計費/所有身份/閱讀    | 閱讀 Office 365 計費的所有方面。 |
| 微軟.目錄/行政單位/基本/讀取    | 讀取 Azure Active Directory 中 administrativeUnits 的基本屬性。 |
| 微軟.目錄/行政單位/成員/閱讀    | 讀取 Azure Active Directory 中的 administrativeUnits.members 屬性。 |
| 微軟.目錄/應用程式/基本/讀取    | 讀取 Azure Active Directory 中 Applications 的基本屬性。 |
| 微軟.目錄/應用程式/擁有者/讀取    | 讀取 Azure Active Directory 中的 applications.owners 屬性。 |
| 微軟.目錄/應用程式/策略/讀取    | 讀取 Azure Active Directory 中的 applications.policies 屬性。 |
| 微軟.目錄/連絡人/基本/閱讀    | 讀取 Azure Active Directory 中 contacts 的基本屬性。 |
| 微軟.目錄/連絡人/成員/閱讀    | 讀取 Azure Active Directory 中的 contacts.memberOf 屬性。 |
| 微軟.目錄/合同/基本/閱讀    | 讀取 Azure Active Directory 中 contracts 的基本屬性。 |
| 微軟.目錄/裝置/基本/讀取    | 讀取 Azure Active Directory 中 devices 的基本屬性。 |
| 微軟.目錄/裝置/成員/讀取    | 讀取 Azure Active Directory 中的 devices.memberOf 屬性。 |
| 微軟.目錄/設備/註冊擁有者/閱讀    | 讀取 Azure Active Directory 中的 devices.registeredOwners 屬性。 |
| 微軟.目錄/裝置/註冊用戶/閱讀    | 讀取 Azure Active Directory 中的 devices.registeredUsers 屬性。 |
| 微軟.目錄/目錄角色/基本/讀取    | 讀取 Azure Active Directory 中 directoryRoles 的基本屬性。 |
| microsoft.directory/目錄角色/符合條件的成員/閱讀    | 讀取 Azure Active Directory 中的 directoryRoles.eligibleMembers 屬性。 |
| 微軟.目錄/目錄角色/成員/閱讀    | 讀取 Azure Active Directory 中的 directoryRoles.members 屬性。 |
| 微軟.目錄/域/基本/讀取    | 讀取 Azure Active Directory 中 domain 的基本屬性。 |
| 微軟.目錄/組/應用程式角色分配/讀取    | 讀取 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| 微軟.目錄/組/基本/讀取    | 讀取 Azure Active Directory 中 groups 的基本屬性。 |
| 微軟.目錄/組/隱藏成員/閱讀    | 讀取 Azure Active Directory 中的 groups.hiddenMembers 屬性。 |
| 微軟.目錄/組/成員/讀取    | 讀取 Azure Active Directory 中的 Read groups.memberOf 屬性。 |
| 微軟.目錄/組/成員/閱讀    | 讀取 Azure Active Directory 中的 groups.members 屬性。 |
| 微軟.目錄/組/擁有者/讀取    | 讀取 Azure Active Directory 中的 groups.owners 屬性。 |
| 微軟.目錄/組/設定/讀取    | 讀取 Azure Active Directory 中的 groups.settings 屬性。 |
| 微軟.目錄/組設置/基本/讀取    | 讀取 Azure Active Directory 中 groupSettings 的基本屬性。 |
| 微軟.目錄/組設置範本/基本/讀取    | 讀取 Azure Active Directory 中 groupSettingTemplates 的基本屬性。 |
| 微軟.directory/oAuth2許可授予/基本/閱讀    | 讀取 Azure Active Directory 中 oAuth2PermissionGrants 的基本屬性。 |
| 微軟.目錄/組織/基本/讀取    | 讀取 Azure Active Directory 中 organization 的基本屬性。 |
| 微軟.目錄/組織/受信任的「無密碼」/讀取    | 讀取 Azure Active Directory 中的 organization.trustedCAsForPasswordlessAuth 屬性。 |
| 微軟.目錄/策略/標準/讀取    | 閱讀 Azure 活動目錄中的標準策略。 |
| 微軟.目錄/角色分配/基本/讀取    | 讀取 Azure Active Directory 中 roleAssignments 的基本屬性。 |
| 微軟.目錄/角色定義/基本/讀取    | 讀取 Azure Active Directory 中 roleDefinitions 的基本屬性。 |
| 微軟.目錄/服務主體/應用程式角色分配/讀取    | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| 微軟.目錄/服務主體/應用程式角色分配/讀取    | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| 微軟.目錄/服務主體/基本/讀取    | 讀取 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| 微軟.目錄/服務主體/成員/閱讀    | 讀取 Azure Active Directory 中的 servicePrincipals.memberOf 屬性。 |
| 微軟.目錄/服務主體/oAuth2許可授予/基本/讀取    | 讀取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 屬性。 |
| microsoft.directory/服務主體/擁有物件/讀取    | 讀取 Azure Active Directory 中的 servicePrincipals.ownedObjects 屬性。 |
| 微軟.目錄/服務主體/擁有者/讀取    | 讀取 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.directory/服務主體/策略/讀取    | 讀取 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| 微軟.目錄/簽署報告/所有屬性/讀取    | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| 微軟.目錄/訂閱Skus/基本/閱讀    | 讀取 Azure Active Directory 中 subscribedSkus 的基本屬性。 |
| 微軟.目錄/使用者/應用程式角色分配/讀取    | 讀取 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| 微軟.目錄/使用者/基本/閱讀    | 讀取 Azure Active Directory 中 users 的基本屬性。 |
| 微軟.目錄/使用者/直接報告/閱讀    | 讀取 Azure Active Directory 中的 users.directReports 屬性。 |
| 微軟.目錄/使用者/經理/閱讀    | 讀取 Azure Active Directory 中的 users.manager 屬性。 |
| 微軟.目錄/使用者/成員/閱讀    | 讀取 Azure Active Directory 中的 users.memberOf 屬性。 |
| 微軟.目錄/使用者/oAuth2許可授予/基本/閱讀    | 讀取 Azure Active Directory 中的 users.oAuth2PermissionGrants 屬性。 |
| 微軟.目錄/使用者/擁有裝置/讀取    | 讀取 Azure Active Directory 中的 users.ownedDevices 屬性。 |
| 微軟.目錄/使用者/擁有的物件/讀取    | 讀取 Azure Active Directory 中的 users.ownedObjects 屬性。 |
| 微軟.目錄/使用者/註冊設備/讀取    | 讀取 Azure Active Directory 中的 users.registeredDevices 屬性。 |
| 微軟.目錄/使用者/強身份驗證/讀取    | 讀取強身份驗證屬性,如 MFA 認證資訊。 |
| 微軟.office365.交換/所有實體/讀    | 閱讀在線交換的所有方面。 |
| microsoft.office365.messageCenter/messages/read    | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.messageCenter/securityMessages/read    | 讀取 microsoft.office365.messageCenter 中的 securityMessages。 |
| 微軟.office365.網路/性能/所有屬性/讀取 | 閱讀 M365 管理中心中的網路性能頁面。 |
| microsoft.office365.protectionCenter/allEntities/read    | 讀取 Office 365 防護中心的所有層面。 |
| 微軟.office365.安全合規中心/所有實體/讀取    | 閱讀 Microsoft.office365.安全合規中心中的所有標準屬性。 |
| microsoft.office365.usageReports/allEntities/read    | 讀取 Office 365 使用量報告。 |
| 微軟.office365.WebPortal/所有實體/標準/閱讀    | 讀取 Microsoft.office365.webPortal 中所有資源的標準屬性。 |

### <a name="groups-administrator-permissions"></a>群組管理員權限
可以管理組和組設置的所有方面,如命名和過期策略。

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/組/基本/讀取 | 讀取 Azure Active Directory 中 Groups 的標準屬性。  |
| 微軟.目錄/組/基本/更新 | 更新 Azure Active Directory 中 groups 的基本屬性。 |
| 微軟目錄/群組/建立 | 在 Azure Active Directory 中建立 groups。 |
| microsoft.目錄/組/建立擁有者 | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| 微軟.目錄/群組/刪除 | 刪除 Azure Active Directory 中的 groups。 |
| 微軟.目錄/組/隱藏成員/閱讀 | 讀取 Azure Active Directory 中的 groups.hiddenMembers 屬性。 |
| 微軟.目錄/組/成員/更新 | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| 微軟.目錄/組/擁有者/更新 | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| 微軟.目錄/組/恢復 | 還原 Azure Active Directory 中的 groups。 |
| 微軟.目錄/組/設定/更新 | 更新 Azure Active Directory 中的 groups.settings 屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="guest-inviter-permissions"></a>來賓邀請者權限
可以邀請來賓用戶獨立於「會員可以邀請客人」 的設置。

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/使用者/應用程式角色分配/讀取 | 讀取 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| 微軟.目錄/使用者/基本/閱讀 | 讀取 Azure Active Directory 中 users 的基本屬性。 |
| 微軟.目錄/使用者/直接報告/閱讀 | 讀取 Azure Active Directory 中的 users.directReports 屬性。 |
| 微軟.目錄/使用者/邀請來賓 | 邀請 Azure Active Directory 中的來賓使用者。 |
| 微軟.目錄/使用者/經理/閱讀 | 讀取 Azure Active Directory 中的 users.manager 屬性。 |
| 微軟.目錄/使用者/成員/閱讀 | 讀取 Azure Active Directory 中的 users.memberOf 屬性。 |
| 微軟.目錄/使用者/oAuth2許可授予/基本/閱讀 | 讀取 Azure Active Directory 中的 users.oAuth2PermissionGrants 屬性。 |
| 微軟.目錄/使用者/擁有裝置/讀取 | 讀取 Azure Active Directory 中的 users.ownedDevices 屬性。 |
| 微軟.目錄/使用者/擁有的物件/讀取 | 讀取 Azure Active Directory 中的 users.ownedObjects 屬性。 |
| 微軟.目錄/使用者/註冊設備/讀取 | 讀取 Azure Active Directory 中的 users.registeredDevices 屬性。 |

### <a name="helpdesk-administrator-permissions"></a>說明台管理員權限

能夠為非系統管理員與技術服務人員系統管理員重設密碼。

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/裝置/位鎖復原金鑰/讀取 | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft.directory/使用者/無效所有刷新權杖 | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| 微軟.目錄/使用者/密碼/更新 | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="hybrid-identity-administrator-permissions"></a>混合識別管理員權限

啟用、部署、配置、管理、監視和排除雲端配置和身份驗證服務。 

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| 微軟.目錄/應用程式/觀眾/更新  | 在 Azure Active Directory 中更新 applications.audience 屬性。 |
| 微軟.目錄/應用程式/身份驗證/更新 | 在 Azure Active Directory 中更新 applications.authentication 屬性。  |
| 微軟.目錄/應用程式/基本/更新 | 更新 Azure Active Directory 中 Applications 的基本屬性。 |
| 微軟.目錄/應用程式/建立 | 在 Azure Active Directory 中建立應用程式。 |
| 微軟.目錄/應用程式/認證/更新 | 在 Azure Active Directory 中更新 applications.credentials 屬性。 |
| 微軟.目錄/應用程式/刪除 | 刪除 Azure Active Directory 中的應用程式。 |
| 微軟.目錄/應用程式/擁有者/更新 | 更新 Azure Active Directory 中的 applications.owners 屬性。 |
| 微軟.目錄/應用程式/許可權/更新 | 在 Azure Active Directory 中更新 applications.permissions 屬性。 |
| 微軟.目錄/應用程式/策略/更新 | 更新 Azure Active Directory 中的 applications.policies 屬性。 |
| 微軟.目錄/應用程式範本/實例化 | 從應用程式範本實例化庫應用程式。 |
| 微軟.目錄/稽核紀錄/所有屬性/讀取 | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.目錄/雲配置/所有屬性/所有任務 | 讀取和配置 Azure AD 雲端配置服務的所有屬性。 |
| microsoft.directory/聯合身份驗證/所有屬性/所有任務 | 在 Azure AD 中管理活動目錄聯合服務 (ADFS) 或第三方聯合提供程式的所有方面。 |
| 微軟.目錄/組織/dirSync/更新 | 更新 Azure Active Directory 中的 organization.dirSync 屬性。 |
| 微軟.目錄/密碼哈希同步/所有屬性/所有任務 | 管理 Azure AD 中密碼哈希同步 (PHS) 的所有方面。 |
| microsoft.directory/通過身份驗證/所有屬性/所有任務 | 管理 Azure AD 中直通身份驗證 (PTA) 的所有方面。 |
| 微軟.目錄/無縫SSO/所有屬性/所有任務 | 在 Azure AD 中管理無縫單一登錄 (SSO) 的所有方面。 |
| 微軟.目錄/服務主體/觀眾/更新 | 更新 Azure Active Directory 中的 servicePrincipals.audience 屬性。 |
| 微軟.目錄/服務主體/身份驗證/更新 | 更新 Azure Active Directory 中的 servicePrincipals.authentication 屬性。 |
| 微軟.目錄/服務主體/基本/更新 | 更新 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| 微軟.目錄/服務主體/創建 | 在 Azure Active Directory 中建立 servicePrincipals。 |
| 微軟.目錄/服務主體/憑據/更新 | 更新 Azure Active Directory 中的 servicePrincipals.credentials 屬性。 |
| 微軟.目錄/服務主體/刪除 | 刪除 Azure Active Directory 中的 servicePrincipals。 |
| 微軟.目錄/服務主體/擁有者/更新 | 更新 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.directory/服務主體/許可權/更新 | 更新 Azure Active Directory 中的 servicePrincipals.permissions 屬性。 |
| microsoft.directory/服務主體/策略/更新 | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft.directory/服務主體/同步作業/管理 | 管理 Azure AD 中同步作業的所有方面。 |
| microsoft.directory/服務主體/同步架構/管理 | 在 Azure AD 中管理同步架構的所有方面。 |
| microsoft.directory/服務主體/同步憑據/管理 | 在 Azure AD 中管理同步憑據的所有方面。 |
| 微軟.目錄/服務主體/標籤/更新 | 更新 Azure 活動目錄中的服務主體.標記屬性。 |
| 微軟.目錄/簽署報告/所有屬性/讀取 | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |


### <a name="intune-service-administrator-permissions"></a>優服服務管理員許可權

可管理 Intune 產品的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/連絡人/基本/更新 | 更新 Azure Active Directory 中 contacts 的基本屬性。 |
| 微軟.目錄/連絡人/建立 | 在 Azure Active Directory 中建立 contacts。 |
| 微軟.目錄/連絡人/刪除 | 刪除 Azure Active Directory 中的 contacts。 |
| 微軟.目錄/裝置/基本/更新 | 更新 Azure Active Directory 中 devices 的基本屬性。 |
| 微軟.目錄/裝置/位鎖復原金鑰/讀取 | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| 微軟.目錄/裝置/建立 | 在 Azure Active Directory 中建立 devices。 |
| 微軟.目錄/裝置/刪除 | 刪除 Azure Active Directory 中的 devices。 |
| 微軟.目錄/設備/註冊擁有者/更新 | 更新 Azure Active Directory 中的 devices.registeredOwners 屬性。 |
| 微軟.目錄/裝置/註冊使用者/更新 | 更新 Azure Active Directory 中的 devices.registeredUsers 屬性。 |
| 微軟.目錄/組/應用程式角色分配/更新 | 更新 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| 微軟.目錄/組/基本/更新 | 更新 Azure Active Directory 中 groups 的基本屬性。 |
| 微軟目錄/群組/建立 | 在 Azure Active Directory 中建立 groups。 |
| microsoft.目錄/組/建立擁有者 | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| 微軟.目錄/群組/刪除 | 刪除 Azure Active Directory 中的 groups。 |
| 微軟.目錄/組/隱藏成員/閱讀 | 讀取 Azure Active Directory 中的 groups.hiddenMembers 屬性。 |
| 微軟.目錄/組/成員/更新 | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| 微軟.目錄/組/擁有者/更新 | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| 微軟.目錄/組/恢復 | 還原 Azure Active Directory 中的 groups。 |
| 微軟.目錄/組/設定/更新 | 更新 Azure Active Directory 中的 groups.settings 屬性。 |
| 微軟.目錄/使用者/應用程式角色分配/更新 | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| 微軟.目錄/使用者/基本/更新 | 更新 Azure Active Directory 中 users 的基本屬性。 |
| 微軟.目錄/使用者/經理/更新 | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.intune/allEntities/allTasks | 管理 Intune 的所有層面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="kaizala-administrator-permissions"></a>凱扎拉管理員許可權

可以管理微軟凱扎拉的設置。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 閱讀 Office 365 管理中心。 |

### <a name="license-administrator-permissions"></a>授權管理員權限

可管理使用者和群組的產品授權。

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/使用者/配置授權 | 管理 Azure Active Directory 中的使用者授權。 |
| 微軟.目錄/使用者/使用位置/更新 | 更新 Azure Active Directory 中的 users.usageLocation 屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |

### <a name="lync-service-administrator-permissions"></a>Lync 服務管理員權限

可管理商務用 Skype 產品的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理商務用 Skype Online 的所有層面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.usageReports/allEntities/read    | 讀取 Office 365 使用量報告。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |


### <a name="message-center-privacy-reader-permissions"></a>資訊中心隱私閱讀器權限

可以讀取消息中心帖子、數據隱私消息、組、域和訂閱。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.messageCenter/securityMessages/read | 讀取 microsoft.office365.messageCenter 中的 securityMessages。 |

### <a name="message-center-reader-permissions"></a>訊息中心讀取器權限
只可在 Office 365 訊息中心讀取及更新其組織的訊息。 

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |

### <a name="network-administrator-permissions"></a>網路管理員權限
可以管理網路位置並查看 Microsoft 365 軟體即服務應用程式的企業網路設計見解。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| 微軟.office365.網路/性能/所有屬性/讀取 | 閱讀 M365 管理中心中的網路性能頁面。  |
| 微軟.office365.網络/位置/所有屬性/所有任務 | 讀取和配置每個位置的網路位置屬性。 |

### <a name="office-apps-administrator-permissions"></a>辦公室應用 管理員權限
可以管理 Office 應用的雲服務(包括策略和設置管理),並管理選擇、取消選擇和向最終使用者設備發佈"新增功能內容"的功能。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |
| 微軟.office365.使用者通信/所有身份/所有任務 | 閱讀和更新"新增郵件"可見性。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="partner-tier1-support-permissions"></a>合作夥伴第 1 層支援權限

請勿使用 - 不適用於一般用途。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/連絡人/基本/更新 | 更新 Azure Active Directory 中 contacts 的基本屬性。 |
| 微軟.目錄/連絡人/建立 | 在 Azure Active Directory 中建立 contacts。 |
| 微軟.目錄/連絡人/刪除 | 刪除 Azure Active Directory 中的 contacts。 |
| 微軟目錄/群組/建立 | 在 Azure Active Directory 中建立 groups。 |
| microsoft.目錄/組/建立擁有者 | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| 微軟.目錄/組/成員/更新 | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| 微軟.目錄/組/擁有者/更新 | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| 微軟.目錄/使用者/應用程式角色分配/更新 | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| 微軟.目錄/使用者/配置授權 | 管理 Azure Active Directory 中的使用者授權。 |
| 微軟.目錄/使用者/基本/更新 | 更新 Azure Active Directory 中 users 的基本屬性。 |
| 微軟.目錄/使用者/刪除 | 刪除 Azure Active Directory 中的 users。 |
| microsoft.directory/使用者/無效所有刷新權杖 | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| 微軟.目錄/使用者/經理/更新 | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| 微軟.目錄/使用者/密碼/更新 | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| 微軟.目錄/用戶/恢復 | 還原 Azure Active Directory 中已刪除的使用者。 |
| 微軟.目錄/使用者/使用者主名/更新 | 更新 Azure Active Directory 中的 users.userPrincipalName 屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="partner-tier2-support-permissions"></a>合作夥伴第 2 層支援權限

請勿使用 - 不適用於一般用途。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/連絡人/基本/更新 | 更新 Azure Active Directory 中 contacts 的基本屬性。 |
| 微軟.目錄/連絡人/建立 | 在 Azure Active Directory 中建立 contacts。 |
| 微軟.目錄/連絡人/刪除 | 刪除 Azure Active Directory 中的 contacts。 |
| 微軟.目錄/網域/所有任務 | 建立和刪除 domains，以及在 Azure Active Directory 中讀取和更新標準屬性。 |
| 微軟目錄/群組/建立 | 在 Azure Active Directory 中建立 groups。 |
| 微軟.目錄/群組/刪除 | 刪除 Azure Active Directory 中的 groups。 |
| 微軟.目錄/組/成員/更新 | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| 微軟.目錄/組/恢復 | 還原 Azure Active Directory 中的 groups。 |
| 微軟.目錄/組織/基本/更新 | 更新 Azure Active Directory 中 organization 的基本屬性。 |
| 微軟.目錄/使用者/應用程式角色分配/更新 | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| 微軟.目錄/使用者/配置授權 | 管理 Azure Active Directory 中的使用者授權。 |
| 微軟.目錄/使用者/基本/更新 | 更新 Azure Active Directory 中 users 的基本屬性。 |
| 微軟.目錄/使用者/刪除 | 刪除 Azure Active Directory 中的 users。 |
| microsoft.directory/使用者/無效所有刷新權杖 | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| 微軟.目錄/使用者/經理/更新 | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| 微軟.目錄/使用者/密碼/更新 | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| 微軟.目錄/用戶/恢復 | 還原 Azure Active Directory 中已刪除的使用者。 |
| 微軟.目錄/使用者/使用者主名/更新 | 更新 Azure Active Directory 中的 users.userPrincipalName 屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="password-administrator-permissions"></a>密碼管理員權限

可以為非管理員和密碼管理員重置密碼。

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/使用者/密碼/更新 | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="power-bi-service-administrator-permissions"></a>電源 BI 服務管理員權限

可管理 Power BI 產品的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>
| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.powerApps.powerBI/allEntities/allTasks | 管理 Power BI 的所有層面。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |


### <a name="power-platform-administrator-permissions"></a>電源平台管理員權限

可以創建和管理微軟動態365、PowerApps和微軟流的所有方面。 

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>
| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| 微軟.動態365/所有實體/所有任務 | 管理 Dynamics 365 的所有層面。 |
| 微軟.流/所有實體/所有任務 | 管理 Microsoft 流的所有方面。 |
| 微軟.電源應用程式/所有實體/所有任務 | 管理 PowerApps 的所有方面。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="printer-administrator-permissions"></a>印表機管理員權限

可以管理印表機和印表機連接器的所有方面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>
| **動作** | **說明** |
| --- | --- |
| microsoft.azure.列印/所有實體/所有屬性/所有任務 | 創建和刪除印表機和連接器,並讀取和更新 Microsoft 列印中的所有屬性。 |

### <a name="printer-technician-permissions"></a>印表機技術人員權限

可以註冊和取消註冊印表機並更新印表機狀態。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>
| **動作** | **說明** |
| --- | --- |
| microsoft.azure.列印/連接器/所有屬性/讀取 | 讀取 Microsoft 列印中連接器的所有屬性。 |
| microsoft.azure.列印/印表機/所有屬性/讀取 | 閱讀 Microsoft 列印版中印表機的所有屬性。 |
| microsoft.azure.列印/印表機/基本/更新 | 更新 Microsoft 列印版中印表機的基本屬性。 |
| 微軟.azure.列印/印表機/寄存器 | 在 Microsoft 列印中註冊印表機。 |
| 微軟.azure.列印/印表機/取消註冊 | 在 Microsoft 列印中取消註冊印表機。 |

### <a name="privileged-authentication-administrator-permissions"></a>特權身份驗證管理員權限

允許查看、設置和重置任何使用者的身份驗證方法資訊(管理員或非管理員)。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/使用者/無效所有刷新權杖 | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| 微軟.目錄/使用者/強身份驗證/更新 | 更新 MFA 認證資訊等增強式驗證屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| 微軟.目錄/使用者/密碼/更新 | 更新 Office 365 組織中所有用戶的密碼。 如需詳細資訊，請參閱線上文件。 |

### <a name="privileged-role-administrator-permissions"></a>權限角色管理員權限

可以管理 Azure AD 中的角色分配以及特權標識管理的所有方面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.aad.privilegedIdentityManagement 中的標準屬性。 |
| microsoft.directory/服務主體/應用程式角色分配給/所有任務 | 讀取和配置 Azure 活動目錄中的服務主體. appRole 分配到屬性。 |
| microsoft.目錄/服務主體/oAuth2許可權授予/所有任務 | 讀取和配置 Azure 活動目錄中的服務主體.oAuth2許可權授予屬性。 |
| microsoft.目錄/管理單位/所有屬性/所有任務 | 建立與管理管理單位(包括成員) |
| microsoft.目錄/角色分配/所有屬性/所有任務 | 創建和管理角色分配。 |
| 微軟.目錄/角色定義/所有屬性/所有任務 | 創建和管理角色定義。 |

### <a name="reports-reader-permissions"></a>報告讀取器權限

可讀取登入與稽核報告。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/稽核紀錄/所有屬性/讀取 | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| 微軟.目錄/簽署報告/所有屬性/讀取 | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |

### <a name="search-administrator-permissions"></a>搜尋管理員權限

可以創建和管理 Microsoft 搜索設置的所有方面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| 微軟.office365.搜索/所有身份/所有屬性/所有任務 | 創建和刪除所有資源,並讀取和更新 Microsoft.office365.search 中的所有屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="search-editor-permissions"></a>搜尋編輯器權限

可以創建和管理編輯內容,如書籤、問答、位置、平面圖。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| 微軟.office365.搜索/內容/所有屬性/所有任務 | 創建和刪除內容,並讀取和更新 Microsoft.office365.搜索中的所有屬性。 |

### <a name="security-administrator-permissions"></a>安全管理員權限

可以讀取安全資訊和報告,並在 Azure AD 和 Office 365 中管理配置。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/應用程式/策略/更新 | 更新 Azure Active Directory 中的 applications.policies 屬性。 |
| 微軟.目錄/稽核紀錄/所有屬性/讀取 | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| 微軟.目錄/裝置/位鎖復原金鑰/讀取 | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| 微軟.目錄/策略/基本/更新 | 更新 Azure Active Directory 中 policies 的基本屬性。 |
| 微軟.目錄/策略/建立 | 在 Azure Active Directory 中建立原則。 |
| 微軟.目錄/策略/刪除 | 刪除 Azure Active Directory 中的原則。 |
| 微軟.目錄/策略/擁有者/更新 | 更新 Azure Active Directory 中的 policies.owners 屬性。 |
| microsoft.目錄/策略/租戶預設/更新 | 更新 Azure Active Directory 中的 policies.tenantDefault 屬性。 |
| microsoft.directory/服務主體/策略/更新 | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| 微軟.目錄/簽署報告/所有屬性/讀取 | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.aad.identityProtection/allEntities/read | 讀取 microsoft.aad.identityProtection 中的所有資源。 |
| microsoft.aad.identityProtection/allEntities/update | 更新 microsoft.aad.identityProtection 中的所有資源。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 讀取 microsoft.aad.privilegedIdentityManagement 中的所有資源。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.protectionCenter/allEntities/read | 讀取 Office 365 防護中心的所有層面。 |
| microsoft.office365.protectionCenter/allEntities/update | 更新 microsoft.office365.protectionCenter 中的所有資源。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |

### <a name="security-operator-permissions"></a>安全管理員權限

創建和管理安全事件。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | 讀取和配置 Microsoft 雲端應用安全性。 |
| microsoft.aad.identityProtection/allEntities/read | 讀取 microsoft.aad.identityProtection 中的所有資源。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 讀取 microsoft.aad.privilegedIdentityManagement 中的所有資源。 |
| microsoft.azure.advancedThreatProtection/allEntities/read | 讀取和配置 Azure AD 高級威脅防護。 |
| microsoft.intune/allEntities/allTasks | 管理 Intune 的所有層面。 |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | 讀取和配置安全&合規性中心。 |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | 讀取和配置 Windows 防禦器進階威脅保護。 |

### <a name="security-reader-permissions"></a>安全讀取器權限

可讀取安全性資訊及 Azure AD 與 Office 365 中的報告。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/稽核紀錄/所有屬性/讀取 | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| 微軟.目錄/裝置/位鎖復原金鑰/讀取 | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft.目錄/策略/條件存取/基本/讀取 | 讀取 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| 微軟.目錄/簽署報告/所有屬性/讀取 | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.aad.identityProtection/allEntities/read | 讀取 microsoft.aad.identityProtection 中的所有資源。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 讀取 microsoft.aad.privilegedIdentityManagement 中的所有資源。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.protectionCenter/allEntities/read | 讀取 Office 365 防護中心的所有層面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |

### <a name="service-support-administrator-permissions"></a>服務支援管理員權限

可讀取服務健康情況資訊及管理支援票證。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="sharepoint-service-administrator-permissions"></a>共用點服務管理員權限

可管理 SharePoint 服務的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| 微軟.目錄/組/統一/應用程式角色分配/更新 | 更新 Azure Active Directory 中的 groups.unified 屬性。 |
| 微軟.目錄/組/統一/基本/更新 | 更新 Office 365 群組的基本屬性。 |
| 微軟.目錄/組/統一/創建 | 建立 Office 365 群組。 |
| 微軟.目錄/組/統一/刪除 | 刪除 Office 365 群組。 |
| 微軟.目錄/組/統一/成員/更新 | 更新 Office 365 群組的成員資格。 |
| 微軟.目錄/組/統一/擁有者/更新 | 更新 Office 365 群組的擁有權。 |
| 微軟.office365.網路/性能/所有屬性/讀取 | 閱讀 M365 管理中心中的網路性能頁面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.sharepoint/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.office365.sharepoint 中的標準屬性。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.usageReports/allEntities/read    | 讀取 Office 365 使用量報告。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="teams-communications-administrator-permissions"></a>團隊通訊管理員權限

能夠管理 Microsoft Teams 服務內的呼叫和會議功能。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |

### <a name="teams-communications-support-engineer-permissions"></a>團隊通訊支援工程師權限

能夠使用進階工具針對 Microsoft Teams 內的通訊問題進行疑難排解。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |

### <a name="teams-communications-support-specialist-permissions"></a>團隊通訊支援專家權限

能夠使用基本工具針對 Microsoft Teams 內的通訊問題進行疑難排解。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |

### <a name="teams-service-administrator-permissions"></a>小組服務管理員權限

能夠管理 Microsoft Teams 服務。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| 微軟.目錄/組/隱藏成員/閱讀 | 讀取 Azure Active Directory 中的 groups.hiddenMembers 屬性。 |
| 微軟.目錄/組/統一/應用程式角色分配/更新 | 更新 Azure Active Directory 中的 groups.unified 屬性。 |
| 微軟.目錄/組/統一/基本/更新 | 更新 Office 365 群組的基本屬性。 |
| 微軟.目錄/組/統一/創建 | 建立 Office 365 群組。 |
| 微軟.目錄/組/統一/刪除 | 刪除 Office 365 群組。 |
| 微軟.目錄/組/統一/成員/更新 | 更新 Office 365 群組的成員資格。 |
| 微軟.目錄/組/統一/擁有者/更新 | 更新 Office 365 群組的擁有權。 |
| 微軟.office365.網路/性能/所有屬性/讀取 | 閱讀 M365 管理中心中的網路性能頁面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="user-administrator-permissions"></a>使用者管理員權限
能夠管理使用者與群組的所有層面，包含為受限制的管理員重設密碼。

| **動作** | **說明** |
| --- | --- |
| 微軟.目錄/應用程式角色分配/創建 | 在 Azure Active Directory 中建立 appRoleAssignments。 |
| 微軟.目錄/應用程式角色分配/刪除 | 刪除 Azure Active Directory 中的 appRoleAssignments。 |
| 微軟.目錄/應用程式Role分配/更新 | 更新在 Azure Active Directory 中的 appRoleAssignments。 |
| 微軟.目錄/連絡人/基本/更新 | 更新 Azure Active Directory 中 contacts 的基本屬性。 |
| 微軟.目錄/連絡人/建立 | 在 Azure Active Directory 中建立 contacts。 |
| 微軟.目錄/連絡人/刪除 | 刪除 Azure Active Directory 中的 contacts。 |
| 微軟.目錄/組/應用程式角色分配/更新 | 更新 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| 微軟.目錄/組/基本/更新 | 更新 Azure Active Directory 中 groups 的基本屬性。 |
| 微軟目錄/群組/建立 | 在 Azure Active Directory 中建立 groups。 |
| microsoft.目錄/組/建立擁有者 | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| 微軟.目錄/群組/刪除 | 刪除 Azure Active Directory 中的 groups。 |
| 微軟.目錄/組/隱藏成員/閱讀 | 讀取 Azure Active Directory 中的 groups.hiddenMembers 屬性。 |
| 微軟.目錄/組/成員/更新 | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| 微軟.目錄/組/擁有者/更新 | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| 微軟.目錄/組/恢復 | 還原 Azure Active Directory 中的 groups。 |
| 微軟.目錄/組/設定/更新 | 更新 Azure Active Directory 中的 groups.settings 屬性。 |
| 微軟.目錄/使用者/應用程式角色分配/更新 | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| 微軟.目錄/使用者/配置授權 | 管理 Azure Active Directory 中的使用者授權。 |
| 微軟.目錄/使用者/基本/更新 | 更新 Azure Active Directory 中 users 的基本屬性。 |
| 微軟.目錄/使用者/建立 | 在 Azure Active Directory 中建立 users。 |
| 微軟.目錄/使用者/刪除 | 刪除 Azure Active Directory 中的 users。 |
| microsoft.directory/使用者/無效所有刷新權杖 | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| 微軟.目錄/使用者/經理/更新 | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| 微軟.目錄/使用者/密碼/更新 | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| 微軟.目錄/用戶/恢復 | 還原 Azure Active Directory 中已刪除的使用者。 |
| 微軟.目錄/使用者/使用者主名/更新 | 更新 Azure Active Directory 中的 users.userPrincipalName 屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

## <a name="role-template-ids"></a>角色樣本識別碼

角色範本 ID 主要由 Microsoft 圖形 API 或 PowerShell 使用者使用。

圖形顯示名稱 | Azure 門戶顯示名稱 | 目錄角色樣本Id
----------------- | ------------------------- | -------------------------
應用程式系統管理員 | 應用程式管理員 | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
應用程式開發人員 | 應用程式開發人員 | CF1C38E5-3621-4004-A7CB-879624DCED7C
驗證系統管理員 | 驗證管理員 | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure 開發人員管理員 | Azure 開發人員管理員 | e3973bdf-4987-49ae-837a-ba8e231c7286
Azure 資訊保護管理員 | Azure 資訊保護管理員 | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C 使用者流管理員 | B2C 使用者流管理員 | 6e591065-9bad-43ed-90f3-e9424366d2f0
B2C 使用者流屬性管理員 | B2C 使用者流屬性管理員 | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
B2C IEF 金鑰集管理員 | B2C IEF 金鑰集管理員 | aaf43236-0c0d-4d5f-883a-6955382ac081
B2C IEF 政策管理員 | B2C IEF 政策管理員 | 3edaf663-341e-4475-9f94-5c398ef6c070
計費管理員 | 計費管理員 | b0f54661-2d74-4c50-afa3-1ec803f12efe
雲端應用程式系統管理員 | 雲端應用程式系統管理員 | 158c047a-c907-4556-b7ef-446551a6b5f7
雲端裝置管理員 | 雲端裝置管理員 | 7698a772-787b-4ac8-901f-60d6b08affd2
公司系統管理員 | 全域管理員 | 62e90394-69f5-4237-9190-012177145e10
規範管理員 | 規範管理員 | 17315797-102d-40b4-93e0-432062卡卡18
相容性資料管理員 | 合規性資料管理員 | e6d1a23a-da11-4be4-9570-befc86d067a7
條件式存取系統管理員 | 條件式存取系統管理員 | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
CRM 服務管理員 | 動態 365 管理員 | 44367163-eba1-44c3-98af-f5787879f96a
客戶 LockBox 存取核准者 | 客戶加密箱存取核准者 | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
電腦分析系統管理員 | 電腦分析系統管理員 | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
裝置系統管理員 | 裝置管理員 | 9f06204d-73c1-4d4c-880a-6edb90606fd8
加入裝置 | 設備聯結 | 9c094953-4995-41c8-84c8-3ebb9b32c93f
裝置管理員 | 裝置管理員 | 2b499bcd-da44-4968-8aec-78e1674fa64d
裝置使用者 | 裝置使用者 | d405c6df-0af8-4e3b-95e4-4d06e542189e
目錄讀取器 | 目錄讀取器 | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
目錄同步處理帳戶 | 目錄同步帳戶 | d29b2b05-8046-44ba-8758-1e26182fcf32
目錄撰寫者 | 目錄寫入器 | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange 服務管理員 | Exchange 系統管理員 | 29232cdf-9323-42fd-ade2-1d097af3e4de
外部識別提供者管理員 | 外部識別提供者管理員 | be2f45a1-457d-42af-a067-6ec1fa63bc45
全域讀者 | 全球讀者 | f2ef992c-3afb-46b9-b7cf-a126ee74c451
組管理員 | 組管理員 | fdd7a751-b60b-444a-984c-02652fe8fa1c 
來賓邀請者 | 來賓邀請者 | 95e79109-95c0-4d8e-aee3-d01accf2d47b
服務台系統管理員 | 服務台管理員 | 729827e3-9c14-49f7-bb1b-9608f156bbb8
混合識別管理員 | 混合識別管理員 | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2
Intune 服務管理員 | Intune 系統管理員 | 3a2c62db-5318-420d-8d74-23affee5d9d5
凱紮拉管理員 | 凱紮拉管理員 | 74ef975b-6605-40af-a5d2-b9539d836353
授權管理員 | 授權管理員 | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync 服務管理員 | 商務用 Skype 的管理員 | 75941009-915a-4869-abe7-691bff18279e
訊息中心隱私閱讀器 | 訊息中心隱私讀取器 | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
訊息中心讀取者 | 訊息中心閱讀器 | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
網路管理員 | 網路管理員 | d37c8床-0711-4417-ba38-b4abe66ce4c2
辦公室應用管理員 | 辦公室應用管理員 | 2b745bdf-0803-4d80-aa65-822c4493daac
合作夥伴第 1 層支援 | 合作夥伴第 1 層支援 | 4ba39ca4-527c-499a-b93d-d9b492c50246
合作夥伴第 2 層支援 | 合作夥伴第 2 層支援 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
密碼管理員 | 密碼管理員 | 966707d0-3269-4727-9be2-8c3a10f19b9d
Power BI 服務管理員 | 電源 BI 管理員 | a9ea8996-122f-4c74-9520-8edcd192826c
電源平台管理員 | 電源平台管理員 | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
印表機管理員 | 印表機管理員 | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f
印表機技術員 | 印表機技師 | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477
特權識別管理員 | 特權識別管理員 | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
特殊權限角色管理員 | 特殊權限角色管理員 | e8611ab8-c189-46e8-94e1-60213ab1f814
報告讀者 | 報表讀者 | 4a5d8f65-41da-4de4-8968-e035b65339cf
搜尋管理員 | 搜尋管理員 | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
搜尋編輯器 | 搜尋編輯器 | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
安全性系統管理員 | 安全性系統管理員 | 194ae4cb-b126-40b2-bd5b-6091b380977d
安全性操作員 | 安全操作員 | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
安全性讀取者 | 安全性讀取者 | 5d6b6bb7-de71-4623-b4af-96380a352509
服務支援管理員 | 服務支援管理員 | f023fd81-a637-4b56-95fd-791ac0226033
SharePoint 服務管理員 | SharePoint 管理員 | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Microsoft Teams 通訊系統管理員 | Microsoft Teams 通訊系統管理員 | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Microsoft Teams 通訊支援工程師 | Microsoft Teams 通訊支援工程師 | f70938a0-fc10-4177-9e90-2178f8765737
Microsoft Teams 通訊支援專家 | Microsoft Teams 通訊支援專家 | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Microsoft Teams 服務管理員 | Microsoft Teams 服務管理員 | 69091246-20e8-4a56-aa4d-066075b2a7a8
User | User | a0b1b346-4d3e-4e8b-98f8-753987be4970
使用者帳戶管理員 | 使用者管理員 | fe930be7-5e62-47db-91af-98c3a49a38b1
加入工作場所裝置 | 工作場所裝置加入 | c34f683f-4d5a-4403-affd-6615e00e3a7f

## <a name="deprecated-roles"></a>已被取代的角色

以下是不應使用的角色。 它們已被取代，而且未來將從 Azure AD 中移除。

* AdHoc 授權管理員
* 加入裝置
* 裝置管理員
* 裝置使用者
* 傳送電子郵件給經過驗證的使用者建立者
* 信箱管理員
* 加入工作場所裝置

## <a name="roles-not-shown-in-the-portal"></a>門戶中未顯示的角色

不是 PowerShell 或 MS 圖形 API 傳回的每個角色在 Azure 門戶中都可見。 下表將組織這些差異。

API 名稱 | Azure 門戶名稱 | 注意
-------- | ------------------- | -------------
公司系統管理員 | 全域管理員 | [變更名稱以更清晰](directory-assign-admin-roles.md#role-template-ids)
CRM 服務管理員 | 動態 365 管理員 | [反映當前產品品牌](directory-assign-admin-roles.md#role-template-ids)
加入裝置 | 已被取代 | [已棄用的角色文件](directory-assign-admin-roles.md#deprecated-roles)
裝置管理員 | 已被取代 | [已棄用的角色文件](directory-assign-admin-roles.md#deprecated-roles)
裝置使用者 | 已被取代 | [已棄用的角色文件](directory-assign-admin-roles.md#deprecated-roles)
目錄同步處理帳戶 | 未顯示,因為它不應使用 | [目錄同步帳戶文件](directory-assign-admin-roles.md#directory-synchronization-accounts)
目錄撰寫者 | 未顯示,因為它不應使用 | [目錄寫入文件](directory-assign-admin-roles.md#directory-writers)
來賓使用者 | 未顯示,因為它不能使用  | NA
Lync 服務管理員 | 商務用 Skype 的管理員 | [反映當前產品品牌](directory-assign-admin-roles.md#role-template-ids)
合作夥伴第 1 層支援 | 未顯示,因為它不應使用 | [合作夥伴第 1 層支援文件](directory-assign-admin-roles.md#partner-tier1-support)
合作夥伴第 2 層支援 | 未顯示,因為它不應使用 | [合作夥伴第二層支援文件](directory-assign-admin-roles.md#partner-tier2-support)
印表機管理員 | 進行中 | 進行中
印表機技術員 | 進行中 | 進行中
受限來賓使用者 | 未顯示,因為它不能使用 | NA
User | 未顯示,因為它不能使用 | NA
加入工作場所裝置 | 已被取代 | [已棄用的角色文件](directory-assign-admin-roles.md#deprecated-roles)

## <a name="next-steps"></a>後續步驟

* 要瞭解有關如何將使用者分配為 Azure 訂閱的管理員,請參閱[使用 Azure 角色管理存取 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)
* 要瞭解有關如何在 Microsoft Azure 中控制資源訪問,請參閱[瞭解不同的角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* 如需有關 Azure Active Directory 與您的 Azure 訂用帳戶產生關聯之方式的詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
