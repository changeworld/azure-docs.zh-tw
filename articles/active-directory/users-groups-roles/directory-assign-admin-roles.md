---
title: Azure AD 角色描述和許可權-Azure Active Directory |Microsoft Docs
description: 系統管理員角色可以新增使用者、指派系統管理角色、重設使用者密碼、管理使用者授權或管理網域。
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 06/15/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8b94d195ea1f31d228505f01c2a77a299e63c0a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518091"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Azure Active Directory 中的系統管理員角色權限

使用 Azure Active Directory (Azure AD) 時，您可以指定受限的管理員，以較低權限的角色來管理身分識別工作。 您可針對下列目的指派管理員：新增或變更使用者、指派系統管理角色、重設使用者密碼、管理使用者授權，以及管理網域名稱等。 只能在 Azure AD 中的使用者設定中變更[預設使用者權限](../fundamentals/users-default-permissions.md)。

## <a name="limit-use-of-global-administrator"></a>限制全域管理員的使用

獲指派全域管理員角色的使用者可以讀取及修改 Azure AD 組織中的每個系統管理設定。 根據預設，註冊 Azure 訂用帳戶的人員會獲指派 Azure AD 組織的全域管理員角色。 只有全域管理員和特殊權限角色管理員才能委派系統管理員角色。 為了降低業務風險，建議您將此角色指派給貴組織中的極少可能人員。

建議的最佳做法是將此角色指派給組織中的少於五個人員。 如果您在組織中有超過五個系統管理員獲指派全域管理員角色，以下是一些減少其使用的方法。

### <a name="find-the-role-you-need"></a>尋找您所需的角色

如果您在有許多角色的清單中找不到所需的角色，Azure AD 可根據角色類別來顯示角色的子集。 查看我們針對 [Azure AD 角色和管理員](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)提供的新 [類型] 篩選，只顯示所選類型中的角色。

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>當您獲指派全域管理員角色時不存在但現在存在的角色

Azure AD 可能已新增一或多個角色，以提供更細微的權限，但這並不是將某些使用者提升為全域管理員時的選項。 經過一段時間，我們會推出其他角色，以完成之前只有全域管理員角色才能進行的工作。 您可以看到這些角色反映在下列[可用的角色](#available-roles)中。

## <a name="assign-or-remove-administrator-roles"></a>指派或移除系統管理員角色

若要了解如何將系統管理角色指派給 Azure Active Directory 中的使用者，請參閱[在 Azure Active Directory 中檢視和指派系統管理員角色](directory-manage-roles-portal.md)。

> [!Note]
> 如果您有 Azure AD premium P2 授權，而且您已經是 Privileged Identity Management （PIM）使用者，則所有角色管理工作都是在許可權身分識別管理中執行，而不是在 Azure AD 中執行。
>
> ![在 PIM 中為已經使用 PIM 並具有 Premium P2 授權的使用者，Azure AD 管理的角色](./media/directory-manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="available-roles"></a>可用的角色

可用的系統管理員角色如下：

### <a name="application-administrator"></a>[應用程式系統管理員](#application-administrator-permissions)

此角色中的使用者可以建立和管理企業應用程式、應用程式註冊和應用程式 Proxy 設定的所有層面。 請注意，建立新的應用程式註冊或企業應用程式時，獲指派此角色的使用者不會新增為擁有者。

應用程式管理員可以管理應用程式認證，讓他們得以模擬應用程式。 因此，獲指派此角色的使用者只能針對未指派給任何 Azure AD 角色或已指派給下列管理員角色的應用程式，管理其應用程式認證：

* 應用程式系統管理員
* 應用程式開發人員
* 雲端應用程式系統管理員
* 目錄讀取器

如果將應用程式指派給上述未提及的任何其他角色，則應用程式管理員便無法管理該應用程式的認證。

此角色也能夠「同意」委派的權限以及應用程式權限 (Microsoft Graph API 的權限例外)。

> [!IMPORTANT]
> 此例外表示您仍然同意「其他」應用程式 (例如，非 Microsoft 應用程式或您已註冊的應用程式) 的權限，但不同意 Azure AD 本身的權限。 在應用程式註冊過程中，您仍然可以「要求」這些權限，但「授與」 (亦即同意) 這些權限則需要 Azure AD 管理員身分。這表示惡意使用者無法輕易提升其權限 (例如，藉由建立並同意可寫入至整個目錄的應用程式)，以及透過該應用程式的權限，將自己提升成為全域管理員。

### <a name="application-developer"></a>[應用程式開發人員](#application-developer-permissions)

將「使用者可以註冊應用程式」設定設為「否」時，此角色中的使用者可以建立應用程式註冊。 將「使用者可同意應用程式代表自己存取公司資料」設定設為「否」時，此角色也會授與代表某人同意的權限。 建立新的應用程式註冊或企業應用程式時，獲指派此角色的使用者會新增為擁有者。

### <a name="authentication-administrator"></a>[驗證管理員](#authentication-administrator-permissions)

具有此角色的使用者可以為某些使用者設定或重設非密碼認證，而且可以更新所有使用者的密碼。 驗證管理員可以要求非系統管理員或被指派某些角色的使用者，針對現有的非密碼認證 (例如 MFA，FIDO) 重新註冊，也可以撤銷**在裝置上記住 MFA** (其會在下次登入時提示 MFA)。 這些動作僅適用於非系統管理員或被指派一或多個下列角色的使用者：

* 驗證系統管理員
* 目錄讀取器
* 來賓邀請者
* 訊息中心讀取者
* 報告讀者

[特殊權限驗證管理員](#privileged-authentication-administrator)角色具有可對所有使用者強制進行重新註冊和多重要素驗證的權限。

> [!IMPORTANT]
> 對於可存取機密或私人資訊或 Azure Active Directory 內外重要組態的人員，具備此角色的使用者可以變更認證。 變更使用者的認證表示可承擔該使用者身分識別和權限。 例如：
>
>- 應用程式註冊和企業應用程式擁有者，他們可以管理他們自己的應用程式認證。 這些應用程式在 Azure AD 中可能有特殊權限，而在其他地方未授與驗證系統管理員。 驗證管理員可以透過此路徑承擔應用程式擁有者的身分識別，然後藉由更新應用程式的認證，進一步承擔特殊權限應用程式的身分識別。
>- Azure 訂用帳戶擁有者，他們具有機密或私人資訊或者 Azure 中重要組態的存取權。
>- 安全性群組和 Office 365 群組擁有者，他們可以管理群組成員資格。 這個群組可以存取機密或私人資訊或者 Azure AD 和其他位置中的重要組態。
>- Azure AD 外部其他服務 (例如，Exchange Online、Office 安全性與合規性中心和人力資源系統) 中的系統管理員。
>- 非系統管理員，例如主管、法律顧問和人力資源員工，他們可以存取機密或私人資訊。

### <a name="azure-devops-administrator"></a>[Azure DevOps 管理員](#azure-devops-administrator-permissions)

具有此角色的使用者可以管理 Azure DevOps 原則，將新的 Azure DevOps 組織建立限制於一組可設定的使用者或群組。 具備此角色的使用者可透過任何由公司 Azure AD 組織支援的 Azure DevOps 組織來管理此原則。

具備此角色的使用者可以管理所有企業 Azure DevOps 原則。

### <a name="azure-information-protection-administrator"></a>[Azure 資訊保護管理員](#azure-information-protection-administrator-permissions)

具有此角色的使用者在 Azure 資訊保護服務上擁有所有權限。 此角色允許設定「Azure 資訊保護」原則的標籤、管理保護範本，以及啟用保護。 此角色並未授與「Identity Protection 中心」、Privileged Identity Management、「監視 Office 365 服務健康情況」及「Office 365 安全與規範中心」中的任何權限。

### <a name="b2c-ief-keyset-administrator"></a>[B2C IEF 金鑰集管理員](#b2c-ief-keyset-administrator-permissions)

使用者可以建立及管理用於權杖加密、權杖簽章和宣告加密/解密的原則金鑰和秘密。 將新金鑰新增至現有金鑰容器，此受限的管理員可以視需要變換秘密，而不會影響現有的應用程式。 即使在秘密建立之後，此使用者仍可查看這些秘密的完整內容及其到期日。

> [!IMPORTANT]
> 這是敏感性角色。 在生產前和生產期間，應謹慎地稽核和指派金鑰集管理員角色。

### <a name="b2c-ief-policy-administrator"></a>[B2C IEF 原則管理員](#b2c-ief-policy-administrator-permissions)

具備此角色的使用者能夠在 Azure AD B2C 中建立、讀取、更新及刪除所有自訂原則，因此能完全掌控相關 Azure AD B2C 組織中的 Identity Experience Framework。 該使用者可藉由編輯原則來與外部識別提供者建立直接同盟、變更目錄結構描述、變更所有使用者面對的內容 (HTML、CSS、JavaScript)、變更完成驗證的需求、建立新使用者、將使用者資料傳送至外部系統 (包括完整移轉)，以及編輯所有使用者資訊 (包括敏感性欄位，像是密碼和電話號碼)。 相反地，此角色無法變更加密金鑰，或編輯組織中用於同盟的秘密。

> [!IMPORTANT]
> B2 IEF 原則管理員是高度敏感的角色，應針對生產中的組織非常有限地進行指派。 這些使用者的活動應予以嚴密稽核，尤其是針對生產中的組織。

### <a name="billing-administrator"></a>[計費管理員](#billing-administrator-permissions)

進行採購、管理訂用帳戶、管理支援票證，以及監控服務健全狀況。

### <a name="cloud-application-administrator"></a>[雲端應用程式系統管理員](#cloud-application-administrator-permissions)

此角色中的使用者具有與應用程式系統管理員角色相同的權限，但不包括管理應用程式 Proxy 的能力。 此角色會授與能力來建立和管理企業應用程式和應用程式註冊的所有層面。 此角色也能夠同意委派的權限以及 Microsoft Graph API 以外的應用程式權限。 建立新的應用程式註冊或企業應用程式時，獲指派此角色的使用者不會新增為擁有者。

雲端應用程式管理員可以管理應用程式認證，讓他們得以模擬應用程式。 因此，獲指派此角色的使用者只能針對未指派給任何 Azure AD 角色或已指派給下列管理員角色的應用程式，管理其應用程式認證：

* 應用程式開發人員
* 雲端應用程式系統管理員
* 目錄讀取器

如果將應用程式指派給上述未提及的任何其他角色，則雲端應用程式管理員便無法管理該應用程式的認證。

### <a name="cloud-device-administrator"></a>[雲端裝置管理員](#cloud-device-administrator-permissions)

此角色的使用者可以啟用、停用和刪除 Azure AD 中的裝置，並在 Azure 入口網站中讀取 Windows 10 BitLocker 金鑰 (如果有的話)。 此角色不會授與可供管理裝置上任何其他屬性的權限。

### <a name="compliance-administrator"></a>[合規性管理員](#compliance-administrator-permissions)

具備此角色的使用者有權限管理 Microsoft 365 合規性中心、Microsoft 365 系統管理中心、Azure 和 Office 365 安全性與合規性中心中的合規性相關功能。 受託人也可以管理 Exchange 系統管理中心、Teams 和商務用 Skype 系統管理中心內的所有功能，並建立適用於 Azure 和 Microsoft 365 的支援票證。 如需詳細資訊，請參閱[關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

在 | 可以執行
----- | ----------
[Microsoft 365 合規性中心](https://protection.office.com) | 保護和管理您組織在所有 Microsoft 365 服務中的資料<br>管理合規性警示
[合規性管理員](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 追蹤、指派和確認您組織的法規合規性活動
[Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理資料治理<br>執行法律和資料的調查<br>管理資料主體要求<br><br>此角色具有與 Office 365 安全性與合規性中心角色型存取控制中的[合規性管理員角色群組](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)相同的權限。
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 檢視所有的 Intune 稽核資料
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 具有唯讀權限，並可管理警示<br>可建立和修改檔案原則，並允許檔案治理動作<br>可檢視 [資料管理] 下的所有內建報告

### <a name="compliance-data-administrator"></a>[合規性資料管理員](#compliance-data-administrator-permissions)

具備此角色的使用者有權限追蹤 Microsoft 365 合規性中心、Microsoft 365 系統管理中心和 Azure 中的資料。 使用者也可以追蹤 Exchange 系統管理中心、合規性管理員、Teams 和商務用 Skype 系統管理中心內的合規性資料，並建立適用於 Azure 和 Microsoft 365 的支援票證。

在 | 可以執行
----- | ----------
[Microsoft 365 合規性中心](https://protection.office.com) | 監視所有 Microsoft 365 服務的合規性相關原則<br>管理合規性警示
[合規性管理員](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 追蹤、指派和確認您組織的法規合規性活動
[Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理資料治理<br>執行法律和資料的調查<br>管理資料主體要求<br><br>此角色具有與 Office 365 安全性與合規性中心角色型存取控制中的[合規性資料管理員角色群組](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)相同的權限。
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 檢視所有的 Intune 稽核資料
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 具有唯讀權限，並可管理警示<br>可建立和修改檔案原則，並允許檔案治理動作<br>可檢視 [資料管理] 下的所有內建報告

### <a name="conditional-access-administrator"></a>[條件式存取管理員](#conditional-access-administrator-permissions)

具備此角色的使用者能夠管理 Azure Active Directory 條件式存取設定。
> [!NOTE]
> 若要在 Azure 中部署 Exchange ActiveSync 條件式存取原則，使用者也必須是全域管理員。

### <a name="customer-lockbox-access-approver"></a>[客戶加密箱存取核准者](#customer-lockbox-access-approver-permissions)

管理您組織中的[客戶加密箱要求](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests)。 他們會收到「客戶加密箱」要求的電子郵件通知，並且可以核准和拒絕來自 Microsoft 365 系統管理中心的要求。 他們也可以開啟或關閉「客戶加密箱」功能。 只有全域管理員可以重設指派給此角色之人員的密碼。

### <a name="desktop-analytics-administrator"></a>[電腦分析管理員](#desktop-analytics-administrator-permissions)


具備此角色的使用者可以管理電腦分析及 Office 自訂和原則服務。 在電腦分析中，此角色能夠檢視資產清查、建立部署計畫、檢視部署和健康狀態。 對於 Office 自訂和原則服務，此角色可讓使用者管理 Office 原則。

### <a name="device-administrators"></a>[裝置系統管理員](#device-administrators-permissions)

此角色是只能指派為[裝置設定](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)中的其他本機系統管理員。 具有此角色的使用者，會在已加入 Azure Active Directory 的所有 Windows 10 裝置上，成為本機電腦系統管理員。 它們並沒有在 Azure Active Directory 中管理裝置物件的能力。

### <a name="directory-readers"></a>[目錄讀取者](#directory-readers-permissions)

具備此角色的使用者可讀取基本目錄資訊。 此角色應用於：

* 將讀取權限授予一組特定的來賓使用者，而不是將其授予所有來賓使用者。
* 當 [僅限系統管理員存取 Azure AD 入口網站] 設為 [是] 時，將 Azure 入口網站的存取權授予一組特定的非系統管理員使用者。
* 如果 Directory.Read.All 不是選項，將目錄的存取權授予服務主體。

### <a name="directory-synchronization-accounts"></a>[目錄同步處理帳戶](#directory-synchronization-accounts-permissions)

請勿使用。 此角色會自動指派給 Azure AD Connect 服務，不適用於也不支援任何其他用途。

### <a name="directory-writers"></a>[目錄寫入者](#directory-writers-permissions)

這是舊版角色，用來指派給不支援[同意架構](../develop/quickstart-register-app.md)的應用程式。 不應將它指派給任何使用者。

### <a name="dynamics-365-administrator--crm-administrator"></a>[Dynamics 365 管理員/CRM 管理員](#crm-service-administrator-permissions)

此角色的使用者具有 Microsoft Dynamics 365 Online (如其存在) 的全域權限，並能管理支援票證及監視服務的健康情況。 如需詳細資訊，請參閱[使用服務管理員角色管理您的 Azure AD 組織](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，會將此角色識別為「Dynamics 365 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com)中則是「Dynamics 365 管理員」。

### <a name="exchange-administrator"></a>[Exchange 管理員](#exchange-service-administrator-permissions)

此角色的使用者具有 Microsoft Exchange Online (如其存在) 的全域權限。 此外，也具備建立和管理所有「Office 365 群組」、管理支援票證，以及監視服務健康情況的能力。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，會將此角色識別為「Exchange 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com)中則是「Exchange 管理員」。 在 [Exchange 系統管理中心](https://go.microsoft.com/fwlink/p/?LinkID=529144)中則是「Exchange Online 管理員」。


### <a name="external-id-user-flow-administrator"></a>[外部識別碼使用者流程管理員](#external-id-user-flow-administrator-permissions)

具有此角色的使用者可以建立和管理 Azure 入口網站中的使用者流程（也稱為「內建」原則）。 這些使用者可以自訂 HTML/CSS/JavaScript 內容、變更 MFA 需求、選取權杖中的宣告、管理 API 連接器，以及為 Azure AD 組織中的所有使用者流程設定會話設定。 另一方面，此角色並不包括檢查使用者資料的能力，或對組織架構中包含的屬性進行變更。 Identity Experience Framework 原則的變更（也稱為自訂原則）也不在此角色的範圍內。

### <a name="external-id-user-flow-attribute-administrator"></a>[外部識別碼使用者流程屬性管理員](#external-id-user-flow-attribute-administrator-permissions)

具備此角色的使用者可新增或刪除 Azure AD 組織中所有使用者流程可用的自訂屬性。 因此，具備此角色的使用者可以變更項目，或將其新增至終端使用者結構描述，而影響所有使用者流程的行為，進而間接變更可能要求的終端使用者資料，最後以宣告的形式傳送給應用程式。此角色無法編輯使用者流程。 此角色無法編輯使用者流程。

### <a name="external-identity-provider-administrator"></a>[外部識別提供者管理員](#external-identity-provider-administrator-permissions)

此管理員可管理 Azure AD 組織與外部識別提供者之間的同盟。 具備此角色，使用者可以新增識別提供者及設定所有可用的設定 (例如，驗證路徑、服務識別碼、指派的金鑰容器)。 此使用者可讓 Azure AD 組織信任來自外部識別提供者的驗證。 對終端使用者體驗所產生的影響取決於組織類型：

* 員工和合作夥伴的 Azure AD 組織： 新增同盟 (例如與 Gmail 同盟) 會立即影響尚未兌換的所有來賓邀請。 請參閱[將 Google 新增為 B2B 來賓使用者的識別提供者](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)。
* Azure Active Directory B2C 組織：在識別提供者新增為使用者流程 (也稱為內建原則) 中的選項之前，新增同盟 (例如與 Facebook，或與另一個 Azure AD 組織) 並不會立即影響終端使用者流程。 如需範例，請參閱[將 Microsoft 帳戶設為識別提供者](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app)。 若要變更使用者流程，需要有「B2C 使用者流程管理員」的受限角色。

### <a name="global-administrator--company-administrator"></a>[全域管理員 / 公司管理員](#company-administrator-permissions)

具有此角色的使用者可以存取 Azure Active Directory 中所有的系統管理功能，以及使用 Azure Active Directory 身分識別的服務，例如 Microsoft 365 資訊安全中心、Microsoft 365 合規性中心、Exchange Online、SharePoint Online 和商務用 Skype Online。 此外，全域管理員可以[提升其存取權](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin)，以管理所有 Azure 訂用帳戶和管理群組。 這可讓全域管理員使用各自的 Azure AD 租使用者，取得所有 Azure 資源的完整存取權。 註冊 Azure AD 組織的人員會變成全域管理員。 您的公司可以有多位全域管理員。 全域系統管理員可以為任何使用者和所有其他系統管理員重設密碼。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，會將此角色識別為「公司管理員」。 它是 [Azure 入口網站](https://portal.azure.com)中的「全域管理員」。
>
>

### <a name="global-reader"></a>[全域讀取者](#global-reader-permissions)

擔任此角色的使用者可以讀取各種 Microsoft 365 服務的設定和系統管理資訊，但無法執行管理動作。 全域讀取者相當於全域管理員的唯讀代理人。 指派全域讀取者 (而不是全域管理員) 進行規劃、稽核或調查。 使用全域讀取者搭配其他受限的管理員角色 (例如 Exchange 管理員)，不需指派全域管理員角色，就能輕鬆完成工作。 全域讀取者適用於 Microsoft 365 系統管理中心、Exchange 系統管理中心、SharePoint 系統管理中心、Teams 系統管理中心、資訊安全中心、合規性中心、Azure AD 系統管理中心，以及裝置管理系統管理中心。

> [!NOTE]
> 全域讀取者角色現在有幾項限制：
>
>- [Onedrive 系統管理中心](https://admin.onedrive.com/)-onedrive 系統管理中心不支援全域讀取者角色
>- [M365 系統管理中心](https://admin.microsoft.com/Adminportal/Home#/homepage) - 全域讀取者無法讀取客戶加密箱要求。 在 M365 系統管理中心的左窗格中，您找不到 [支援] 底下的 [客戶加密箱要求] 索引標籤。
>- [Office 安全性與合規性中心](https://sip.protection.office.com/homepage) - 全域讀取者無法讀取 SCC 稽核記錄、進行內容搜尋，或查看安全分數。
>- [Teams 系統管理中心](https://admin.teams.microsoft.com) - 全域讀取者無法讀取 **Teams 生命週期**、**分析與報告**、**IP 電話裝置管理**和**應用程式目錄**。
>- [Privileged Access Management (PAM)](https://docs.microsoft.com/office365/securitycompliance/privileged-access-management-overview) 不支援全域讀取者角色。
>- [Azure 資訊保護](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) - 只有進行[集中報告](https://docs.microsoft.com/azure/information-protection/reports-aip)，以及當 Azure AD 組織不在[統一標籤平台](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)上時，才支援全域讀取者。
>
> 這些功能目前正在開發中。
>

### <a name="groups-administrator"></a>[群組管理員](#groups-administrator-permissions)

具備此角色的使用者可以建立/管理群組及其設定 (例如命名和到期原則)。 請務必瞭解，將使用者指派給這個角色，他就能夠在除了 Outlook 以外的各種工作負載 (例如 Teams、SharePoint、Yammer) 上管理組織中的所有群組。 此外，該使用者也能夠在各種系統管理入口網站 (例如 Microsoft 系統管理中心、Azure 入口網站，以及 Teams 和 SharePoint 系統管理中心之類的工作負載專屬入口網站) 中管理各種群組設定。

### <a name="guest-inviter"></a>[來賓邀請者](#guest-inviter-permissions)

當 [成員可邀請] 使用者設定為 [否] 時，此角色中的使用者可以管理 Azure Active Directory B2B 來賓使用者的邀請 在[關於 Azure AD B2B 共同作業](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)中查看 B2B 共同作業的詳細資訊。 這不包含任何其他權限。

### <a name="helpdesk-administrator"></a>[服務台管理員](#helpdesk-administrator-permissions)

具備此角色的使用者可以變更密碼、讓重新整理權杖失效、管理服務要求，以及監視服務健康情況。 讓重新整理權杖失效會強制使用者重新登入。 服務台管理員可以重設密碼，並且讓非系統管理員或被獲指派下列角色的其他使用者的重新整理權杖失效：

* 目錄讀取器
* 來賓邀請者
* 服務台系統管理員
* 訊息中心讀取者
* 報告讀者

> [!IMPORTANT]
> 具備此角色的使用者可以變更可存取機密或私人資訊或 Azure Active Directory 內外重要組態的人員密碼。 變更使用者的密碼表示可承擔該使用者身分識別和權限。 例如：
>
>- 應用程式註冊和企業應用程式擁有者，他們可以管理他們自己的應用程式認證。 這些應用程式在 Azure AD 中可能有特殊權限，而在其他地方未授與技術支援中心系統管理員。 技術支援中心系統管理員可以透過此路徑承擔應用程式擁有者的身分識別，然後藉由更新應用程式的認證，進一步承擔特殊權限應用程式的身分識別。
>- Azure 訂用帳戶擁有者，他們具有機密或私人資訊或者 Azure 中重要組態的存取權。
>- 安全性群組和 Office 365 群組擁有者，他們可以管理群組成員資格。 這個群組可以存取機密或私人資訊或者 Azure AD 和其他位置中的重要組態。
>- Azure AD 外部其他服務 (例如，Exchange Online、Office 安全性與合規性中心和人力資源系統) 中的系統管理員。
>- 非系統管理員，例如主管、法律顧問和人力資源員工，他們可以存取機密或私人資訊。

利用[管理單位 (現在處於公開預覽狀態)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units)，有可能將系統管理權限委派給使用者子集，以及將原則套用到使用者子集。

此角色先前在 [Azure 入口網站](https://portal.azure.com/)中稱為「密碼管理員」。 Azure AD 中的「服務台管理員」名稱現在符合其在 Azure AD PowerShell 和 Microsoft Graph API 中的名稱。

### <a name="hybrid-identity-administrator"></a>[混合式身分識別管理員](#hybrid-identity-administrator-permissions)

具備此角色的使用者可以啟用、設定及管理與在 Azure AD 中啟用混合式身分識別相關的服務和設定。 此角色能夠將 Azure AD 設定為三種支援的驗證方法之一 (密碼雜湊同步處理 (PHS)、傳遞驗證 (PTA) 或同盟 (AD FS 或第三方同盟提供者))，以及部署相關的內部部署基礎結構來啟用這些驗證方法。 內部部署基礎結構包含佈建和 PTA 代理程式。 此角色能夠啟用無縫單一登入 (SSO)，以在非 Windows 10 裝置或非 Windows Server 2016 電腦上啟用無縫驗證。 此外，此角色還能夠查看登入記錄，以及存取健康情況和分析，以供監視和疑難排解之用。 

### <a name="intune-administrator"></a>[Intune 管理員](#intune-service-administrator-permissions)

此角色的使用者具有 Microsoft Intune Online (如其存在) 的全域權限。 此外，此角色包含管理使用者和裝置的能力，可相關聯原則以及建立和管理群組。 如需詳細資訊，請參閱[將角色型系統管理控制用於 Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)。

此角色可以建立和管理所有安全性群組。 不過，Intune 管理員沒有 Office 群組的系統管理權限。 這表示此管理員無法更新組織中所有 Office 群組的擁有者或成員資格。 不過，他/她可以管理其所建立的 Office 群組，這屬於其終端使用者權限的一部分。 因此，他/她所建立的任何 Office 群組 (而不是安全性群組)，都應該根據其配額 250 個來計算。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，會將此角色識別為「Intune 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com)中則是「Intune 管理員」。

### <a name="kaizala-administrator"></a>[Kaizala 管理員](#kaizala-administrator-permissions)

具備此角色的使用者有全域權限可管理 Microsoft Kaizala (如其存在) 內的設定，還能夠管理支援票證及監視服務健康情況。 此外，該使用者也可存取與組織成員採用和使用 Kaizala 相關的報告，以及使用 Kaizala 動作所產生的商務報告。

### <a name="license-administrator"></a>[授權管理員](#license-administrator-permissions)

此角色中的使用者可新增、移除和更新使用者和群組的授權指派 (使用群組型授權)，以及管理使用者的使用位置。 此角色不會授與購買或管理訂用帳戶、建立或管理群組，或在使用位置以外建立或管理使用者的能力。 這個角色沒有檢視、建立或管理支援票證的存取權。

### <a name="message-center-privacy-reader"></a>[訊息中心隱私權讀取者](#message-center-privacy-reader-permissions)

具備此角色的使用者可以監視訊息中心內的所有通知，包括資料隱私權訊息。 訊息中心隱私權讀取者會收到電子郵件通知 (包括資料隱私權相關通知)，並可利用「訊息中心喜好設定」來取消訂閱。 只有全域管理員和訊息中心隱私權讀取者可以讀取資料隱私權訊息。 此外，此角色包含檢視群組、網域和訂閱的能力。 此角色沒有檢視、建立或管理服務要求的權限。

### <a name="message-center-reader"></a>[訊息中心讀取者](#message-center-reader-permissions)

此角色中的使用者可以在 [Office 365 訊息中心](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)內，為他們的組織監視所設服務 (例如 Exchange、Intune 和 Microsoft Teams) 的通知和諮詢健康情況更新。 訊息中心讀者每週會收到貼文的電子郵件摘要和更新，並且可以在 Office 365 中分享訊息中心的貼文。 在 Azure AD 中，指派至此角色的使用者只會有 Azure AD 服務的唯讀存取權，與使用者和群組一樣。 這個角色沒有檢視、建立或管理支援票證的存取權。

### <a name="modern-commerce-administrator"></a>[Modern Commerce 管理員](#modern-commerce-administrator-permissions)

請勿使用。 此角色會從 Commerce 自動指派，不適用於、也不支援任何其他用途。 請參閱下面的詳細資料。

Modern Commerce 管理員角色可讓特定使用者有權存取 Microsoft 365 系統管理中心，以及查看 [首頁]、[計費] 和 [支援] 的左側導覽項目。 這些區域中可用的內容是由指派給使用者的[商務專用角色](https://docs.microsoft.com/azure/cost-management-billing/manage/understand-mca-roles)所控制，以管理其為自己或貴組織所購買的產品。 這可能包括支付帳單之類的工作，或對計費帳戶和帳單設定檔的存取權。 

具有 Modern Commerce 管理員角色的使用者，在其他 Microsoft 採購系統中通常具有系統管理權限，但沒有用來存取系統管理中心的全域管理員或計費管理員角色。 

**何時會指派 Modern Commerce 管理員角色？**

* **Microsoft 365 系統管理中心的自助式採購** - 自助式採購讓使用者有機會藉由自行購買或註冊來試用新產品。 這些產品都是在系統管理中心進行管理。 進行自助式採購的使用者會被指派商務系統中的角色以及 Modern Commerce 管理員角色，以便在系統管理中心管理其採購項目。 系統管理員可透過 [PowerShell](https://docs.microsoft.com/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell?view=o365-worldwide) 來封鎖自助式採購 (針對 Power BI、Power Apps、Power Automate)。 如需詳細資訊，請參閱[自助式購買常見問題集](https://docs.microsoft.com/microsoft-365/commerce/subscriptions/self-service-purchase-faq?view=o365-worldwide)。  
* **從 Microsoft 商業市集採購** - 類似於自助式採購，當使用者從 Microsoft AppSource 或 Azure Marketplace 購買產品或服務時，如果他們沒有全域管理員或計費管理員角色，則會被指派 Modern Commerce 管理員角色。 在某些情況下，使用者可能會遭到封鎖而無法進行採購。 如需詳細資訊，請參閱 [Microsoft 商業市集](https://docs.microsoft.com/azure/marketplace/marketplace-faq-publisher-guide#what-could-block-a-customer-from-completing-a-purchase)。
* **Microsoft 的提案** – 提案是來自 Microsoft 的正式報價，可供貴組織購買 Microsoft 產品和服務。 如果接受提案的人員在 Azure AD 中沒有全域管理員或計費管理員角色，他們就會被指派商務專用角色來完成提案，以及被指派 Modern Commerce 管理員角色來存取系統管理中心。 當他們存取系統管理中心時，只能使用其商務專用角色所授權的功能。
* **商務專用角色** – 某些使用者會被指派商務專用角色。 如果使用者不是全域或計費管理員，他們會取得 Modern Commerce 管理員角色，以便存取系統管理中心。  

如果取消指派使用者的 Modern Commerce 管理員角色，他們就會喪失 Microsoft 365 系統管理中心的存取權。 如果他們正為自己或貴組織管理任何產品，將無法管理這些產品。 這可能包括指派授權、變更付款方式、支付帳單，或其他管理訂用帳戶的工作。

### <a name="network-administrator"></a>[網路管理員](#network-administrator-permissions)

具備此角色的使用者可以根據來自其使用者位置的網路遙測，檢閱 Microsoft 的網路周邊架構建議。 Office 365 的網路效能依賴嚴謹的企業客戶網路周邊架構，這通常是使用者位置專屬的架構。 此角色可讓您編輯探索到的使用者位置及設定這些位置的網路參數，以加速遙測測量和設計建議
### <a name="office-apps-administrator"></a>[Office 應用程式管理員](#office-apps-administrator-permissions)

具備此角色的使用者可以管理 Office 365 應用程式的雲端設定。 這包括管理雲端原則、自助式下載管理，以及檢視 Office 應用程式相關報告的功能。 此外，這個角色還能夠管理支援票證，以及監視主要系統管理中心內的服務健康情況。 指派給此角色的使用者也可以管理 Office 應用程式中新功能的通訊。 

### <a name="partner-tier1-support"></a>[合作夥伴第 1 層支援](#partner-tier1-support-permissions)

請勿使用。 此角色已被取代，而且未來將從 Azure AD 中移除。 此角色僅供少數 Microsoft 轉售合作夥伴使用，不適用於一般用途。

### <a name="partner-tier2-support"></a>[合作夥伴第 2 層支援](#partner-tier2-support-permissions)

請勿使用。 此角色已被取代，而且未來將從 Azure AD 中移除。 此角色僅供少數 Microsoft 轉售合作夥伴使用，不適用於一般用途。

### <a name="password-administrator"></a>[密碼管理員](#password-administrator-permissions)

具有此角色的使用者管理密碼的能力有限。 此角色無法管理服務要求或監視服務健康情況。 密碼管理員可以為非系統管理員的其他使用者或只為下列角色的成員重設其密碼：

* 目錄讀取器
* 來賓邀請者
* 密碼管理員

### <a name="power-bi-administrator"></a>[Power BI 管理員](#power-bi-service-administrator-permissions)

此角色的使用者具有 Microsoft Power BI (如其存在) 的全域權限，並能管理支援票證及監視服務的健康情況。 如需詳細資訊，請參閱[了解 Power BI 管理員角色](https://docs.microsoft.com/power-bi/service-admin-role)。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，會將此角色識別為「Power BI 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com)中則是「Power BI 管理員」。

### <a name="power-platform-administrator"></a>[Power Platform 管理員](#power-platform-administrator-permissions)

具備此角色的使用者可以建立及管理環境、PowerApps、流程、資料外洩防護原則的所有層面。 此外，具有此角色的使用者能夠管理支援票證及監視服務健康情況。

### <a name="printer-administrator"></a>[印表機管理員](#printer-administrator-permissions)

具備此角色的使用者可以註冊印表機，以及管理 Microsoft 通用列印解決方案中所有印表機設定的所有層面，包括通用列印連接器設定。 他們可以同意所有委派的列印權限要求。 印表機管理員也具有列印報告的權限。

### <a name="printer-technician"></a>[印表機技術人員](#printer-technician-permissions)

具備此角色的使用者可以在 Microsoft 通用列印解決方案中註冊印表機和管理印表機狀態。 他們也可以讀取所有連接器資訊。 印表機技術人員無法執行的重要工作，就是在印表機上設定使用者權限和共用印表機。

### <a name="privileged-authentication-administrator"></a>[特殊權限驗證管理員](#privileged-authentication-administrator-permissions)

具備此角色的使用者可以為所有使用者 (包括全域管理員) 設定或重設非密碼認證，而且可以更新所有使用者的密碼。 特殊權限驗證管理員可以強制使用者針對現有的非密碼認證 (例如 MFA 或 FIDO) 重新註冊，以及撤銷「在裝置上記住 MFA」(其會在所有使用者下次登入時提示 MFA)。 [驗證管理員](#authentication-administrator)角色可以只針對非系統管理員和指派給下列 Azure AD 角色的使用者，強制進行重新註冊和 MFA：

* 驗證系統管理員
* 目錄讀取器
* 來賓邀請者
* 訊息中心讀取者
* 報告讀者

### <a name="privileged-role-administrator"></a>[特殊權限角色管理員](#privileged-role-administrator-permissions)

具備此角色的使用者可以管理 Azure Active Directory 中，以及 Azure AD Privileged Identity Management 內的角色指派。 此外，這個角色允許管理 Privileged Identity Management 的各個層面和管理單位。

> [!IMPORTANT]
> 此角色能夠管理所有 Azure AD 角色 (包括全域管理員角色) 的指派。 此角色不包含 Azure AD 中的任何其他特殊權限能力，例如建立或更新使用者。 不過，指派給這個角色的使用者可以藉由指派額外的角色，來授與自己或其他人額外權限。

### <a name="reports-reader"></a>[報告讀取者](#reports-reader-permissions)

具備此角色的使用者可檢視 Microsoft 365 系統管理中心內的使用報告資料與報告儀表板，以及 PowerBI 中的採用內容套件。 此外，此角色還可讓使用者存取 Azure AD 中的登入報告與活動，以及 Microsoft Graph 報告 API 所傳回的資料。 獲指派「報告讀者」角色的使用者只能存取相關的使用情況和採用計量。 他們並不具備任何系統管理權限，因此無法進行設定或存取產品特定的系統管理中心 (例如 Exchange)。 這個角色沒有檢視、建立或管理支援票證的存取權。

### <a name="search-administrator"></a>[搜尋管理員](#search-administrator-permissions)

具備此角色的使用者具有 Microsoft 365 系統管理中心內所有 Microsoft 搜尋管理功能的完整存取權。 此外，這些使用者可以檢視訊息中心、監視服務健康情況，以及建立服務要求。

### <a name="search-editor"></a>[搜尋編輯者](#search-editor-permissions)

具備此角色的使用者可以建立、管理及刪除 Microsoft 365 系統管理中心中 Microsoft 搜尋的內容，包括書籤、問與答和位置。

### <a name="security-administrator"></a>[安全性管理員](#security-administrator-permissions)

具備此角色的使用者有權限管理 Microsoft 365 資訊安全中心、Azure Active Directory Identity Protection、Azure Active Directory 驗證、Azure 資訊保護和 Office 365 安全性與合規性中心的安全性相關功能。 關於 Office 365 權限的詳細資訊可在 [Office 365 安全性與法規遵循中心的權限](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)中取得。

在 | 可以執行
--- | ---
[Microsoft 365 資訊安全中心](https://protection.office.com) | 監視所有 Microsoft 365 服務的安全性相關原則<br>管理安全性威脅和警示<br>檢視報表
身分識別防護中心 | 「安全性讀取者」角色的所有權限<br>此外，還能夠執行除了重設密碼以外的所有身分識別防護中心作業
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | 「安全性讀取者」角色的所有權限<br>**無法**管理 Azure AD 角色指派或設定
[Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理安全性原則<br>檢視、調查及回應安全性威脅<br>檢視報表
Azure 進階威脅防護 | 監視及回應可疑的安全性活動
Windows Defender ATP 和 EDR | 指派角色<br>管理電腦群組<br>設定端點威脅偵測和自動補救<br>檢視、調查及回應警示
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 檢視使用者、裝置、註冊、設定及應用程式資訊<br>無法對 Intune 進行變更
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 新增管理員、新增原則和設定、上傳記錄及執行治理動作
[Azure 資訊安全中心](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | 可檢視安全性原則、檢視安全性狀態、編輯安全性原則、檢視警示和建議、關閉警示和建議
[Office 365 服務健康情況](https://docs.microsoft.com/office365/enterprise/view-service-health) | 檢視 Office 365 服務的健康情況
[智慧鎖定](../authentication/howto-password-smart-lockout.md) | 定義發生登入失敗事件時鎖定的閾值和持續時間。
[密碼保護](../authentication/concept-password-ban-bad.md) | 設定自訂禁用密碼清單或內部部署密碼保護。

### <a name="security-operator"></a>[安全性操作員](#security-operator-permissions)

具備此角色的使用者可以管理警示，且有安全性相關功能的全域唯讀存取權，包含 Microsoft 365 安全性中心、Azure Active Directory、Identity Protection、Privileged Identity Management 和 Office 365 安全性與合規性中心中的所有資訊。 關於 Office 365 權限的詳細資訊可在 [Office 365 安全性與法規遵循中心的權限](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center)中取得。

在 | 可以執行
--- | ---
[Microsoft 365 資訊安全中心](https://protection.office.com) | 「安全性讀取者」角色的所有權限<br>檢視、調查及回應安全性威脅警示
身分識別防護中心 | 「安全性讀取者」角色的所有權限<br>此外，還能夠執行除了重設密碼以外的所有身分識別防護中心作業
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | 「安全性讀取者」角色的所有權限
[Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 「安全性讀取者」角色的所有權限<br>檢視、調查及回應安全性警示
Windows Defender ATP 和 EDR | 「安全性讀取者」角色的所有權限<br>檢視、調查及回應安全性警示
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 「安全性讀取者」角色的所有權限
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 「安全性讀取者」角色的所有權限
[Office 365 服務健康情況](https://docs.microsoft.com/office365/enterprise/view-service-health) | 檢視 Office 365 服務的健康情況

### <a name="security-reader"></a>[安全性讀取者](#security-reader-permissions)

具備此角色的使用者具有安全性相關功能的全域唯讀存取權 (含 Microsoft 365 資訊安全中心、Azure Active Directory、Identity Protection、Privileged Identity Management 中的所有資訊)，並能讀取 Azure Active Directory 登入報告與稽核記錄，且具有 Office 365 安全性與合規性中心的全域唯讀存取權。 關於 Office 365 權限的詳細資訊可在 [Office 365 安全性與法規遵循中心的權限](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)中取得。

在 | 可以執行
--- | ---
[Microsoft 365 資訊安全中心](https://protection.office.com) | 檢視所有 Microsoft 365 服務的安全性相關原則<br>檢視安全性威脅和警示<br>檢視報表
身分識別防護中心 | 讀取安全性功能的所有安全性報告和設定資訊<br><ul><li>反垃圾郵件<li>加密<li>資料外洩防護<li>反惡意程式碼<li>進階威脅防護<li>防網路釣魚<li>郵件流程規則
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | 以唯讀方式存取 Azure AD Privileged Identity Management 中所顯示的一切資訊︰Azure AD 角色指派和安全性審查的原則與報告。<br>**無法** 註冊 Azure AD Privileged Identity Management 或對其進行任何變更。 具有此角色的人員可以在 Privileged Identity Management 入口網站中或透過 PowerShell，啟用其他角色 (例如「全域管理員」或「特殊權限角色管理員」)，前提是該使用者必須有資格擔任該角色。
[Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 檢視安全性原則<br>檢視及調查安全性威脅<br>檢視報表
Windows Defender ATP 和 EDR | 檢視和調查警示。 當您在 Windows Defender ATP 中開啟角色型存取控制時，具有唯讀權限 (例如 Azure AD 安全性讀取者角色) 的使用者會在指派給 Windows Defender ATP 角色之前喪失存取權。
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 檢視使用者、裝置、註冊、設定及應用程式資訊。 無法對 Intune 進行變更。
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 具有唯讀權限，並可管理警示
[Azure 資訊安全中心](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | 可檢視建議和警示、檢視安全性原則、檢視安全性狀態，但無法進行變更
[Office 365 服務健康情況](https://docs.microsoft.com/office365/enterprise/view-service-health) | 檢視 Office 365 服務的健康情況

### <a name="service-support-administrator"></a>[服務支援管理員](#service-support-administrator-permissions)

具備此角色的使用者可以開立 Microsoft Azure 與 Office 365 服務的支援要求，以及在 [Azure 入口網站](https://portal.azure.com)和 [Microsoft 365 系統管理中心](https://admin.microsoft.com)檢視服務儀表板與訊息中心。 如需詳細資訊，請參閱[關於管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

> [!NOTE]
> 先前，此角色在 [Azure 入口網站](https://portal.azure.com)和 [Microsoft 365 系統管理中心](https://admin.microsoft.com)內稱為「服務管理員」。 我們已將其重新命名為「服務支援管理員」，以符合 Microsoft Graph API、Azure AD Graph API 和 Azure AD PowerShell 中的現有名稱。

### <a name="sharepoint-administrator"></a>[SharePoint 管理員](#sharepoint-service-administrator-permissions)

具備此角色的使用者在有 Microsoft SharePoint Online 服務時，於該服務內具有全域權限，以及建立和管理所有 Office 365 群組、管理支援票證和監控服務健康情況的能力。 如需詳細資訊，請參閱[關於管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，會將此角色識別為「SharePoint 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com)中則是「SharePoint 管理員」。

> [!NOTE]
> 此角色也會將範圍許可權授與適用于 Microsoft Intune 的 Microsoft Graph API，讓您能夠管理和設定與 SharePoint 和 OneDrive 資源相關的原則。

### <a name="skype-for-business--lync-administrator"></a>[商務用 Skype / Lync 管理員](#lync-service-administrator-permissions)

在有 Microsoft 商務用 Skype 服務時，具備此角色的使用者在該服務內會具有全域權限，以及在 Azure Active Directory 中管理 Skype 特定的使用者屬性。 此外，此角色會授與管理支援票證及監視服務健康情況的能力，以及存取 Microsoft Teams 和商務用 Skype 系統管理中心的能力。 此帳戶也必須獲得 Microsoft Teams 授權，否則就無法執行 Microsoft Teams PowerShell Cmdlet。 如需詳細資訊，請參閱[關於商務用 Skype 系統管理員角色](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5)，如需 Microsoft Teams 的授權資訊，請參閱[商務用 Skype 和 Microsoft Teams 附加授權](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，會將此角色識別為「Lync 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com/)中則是「商務用 Skype 管理員」。

### <a name="teams-communications-administrator"></a>[Teams 通訊管理員](#teams-communications-administrator-permissions)

此角色的使用者可以管理 Microsoft Teams 在語音和電話語音相關工作負載的各個層面。 這包括電話號碼指派管理工具、語音和會議原則，以及呼叫分析工具組的完整存取權。

### <a name="teams-communications-support-engineer"></a>[Teams 通訊支援工程師](#teams-communications-support-engineer-permissions)

此角色的使用者可以使用 Microsoft Teams 和商務用 Skype 系統管理中心內的使用者呼叫疑難排解工具，針對 Microsoft Teams 和商務用 Skype 內的通訊問題進行疑難排解。 此角色的使用者可以檢視所有相關參與者的完整呼叫記錄資訊。 這個角色沒有檢視、建立或管理支援票證的存取權。

### <a name="teams-communications-support-specialist"></a>[Teams 通訊支援專家](#teams-communications-support-specialist-permissions)

此角色的使用者可以使用 Microsoft Teams 和商務用 Skype 系統管理中心內的使用者呼叫疑難排解工具，針對 Microsoft Teams 和商務用 Skype 內的通訊問題進行疑難排解。 此角色的使用者只能檢視其所查閱特定使用者的呼叫中所含有的使用者詳細資料。 這個角色沒有檢視、建立或管理支援票證的存取權。

### <a name="teams-service-administrator"></a>[Teams 服務管理員](#teams-service-administrator-permissions)

此角色的使用者可以透過 Microsoft Teams 和商務用 Skype 系統管理中心以及個別的 PowerShell 模組，管理 Microsoft Teams 工作負載的所有層面。 這包括所有與電話語音、傳訊、會議和小組本身相關的管理工具以及其他領域。 這個角色會額外獲得授與建立和管理所有 Office 365 群組、管理支援票證，以及監視服務健康情況的能力。

### <a name="user-administrator"></a>[使用者管理員](#user-administrator-permissions)

具有此角色的使用者可以建立使用者，以及管理使用者的所有層面，但有一些限制（請參閱表格），而且可以更新密碼到期原則。 此外，具有此角色的使用者可以建立與管理所有群組。 此角色也包含建立和管理使用者檢視、管理支援票證，以及監視服務健康情況的能力。 對於具有大部分系統管理員角色的使用者，使用者管理員沒有權限可管理其某些使用者屬性。 具有此角色的使用者沒有管理 MFA 的權限。 下表列出此限制的例外角色。

| **權限** | **可以執行** |
| --- | --- |
|一般權限|<p>建立 [使用者和群組]</p><p>建立和管理使用者檢視</p><p>建立 Office 支援票證<p>更新密碼到期原則|
| <p>所有使用者，包括所有管理員</p>|<p>管理授權</p><p>管理使用者主體名稱以外的所有使用者屬性</p>
| 只有非管理員或者下列任何有限管理員角色的使用者：<ul><li>目錄讀取器<li>來賓邀請者<li>服務台系統管理員<li>訊息中心讀取者<li>報告讀者<li>使用者管理員|<p>刪除及還原</p><p>停用和啟用</p><p>使重新整理權杖失效</p><p>管理包含使用者主體名稱的所有使用者屬性</p><p>重設密碼</p><p>更新 (FIDO) 裝置金鑰</p>|

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
| microsoft.directory/Application/appProxyAuthentication/update | 更新 Azure Active Directory 中服務主體的應用程式 Proxy 驗證屬性。 |
| microsoft.directory/Application/appProxyUrlSettings/update | 更新 Azure Active Directory 中的應用程式 Proxy 內部和外部 URL。 |
| microsoft.directory/applications/applicationProxy/read | 讀取所有應用程式 Proxy 屬性。 |
| microsoft.directory/applications/applicationProxy/update | 更新所有應用程式 Proxy 屬性。 |
| microsoft.directory/applications/audience/update | 在 Azure Active Directory 中更新 applications.audience 屬性。 |
| microsoft.directory/applications/authentication/update | 在 Azure Active Directory 中更新 applications.authentication 屬性。 |
| microsoft.directory/applications/basic/update | 更新 Azure Active Directory 中 Applications 的基本屬性。 |
| microsoft.directory/applications/create | 在 Azure Active Directory 中建立應用程式。 |
| microsoft.directory/applications/credentials/update | 在 Azure Active Directory 中更新 applications.credentials 屬性。 |
| microsoft.directory/applications/delete | 刪除 Azure Active Directory 中的應用程式。 |
| microsoft.directory/applications/owners/update | 更新 Azure Active Directory 中的 applications.owners 屬性。 |
| microsoft.directory/applications/permissions/update | 在 Azure Active Directory 中更新 applications.permissions 屬性。 |
| microsoft.directory/applications/policies/update | 更新 Azure Active Directory 中的 applications.policies 屬性。 |
| microsoft.directory/appRoleAssignments/create | 在 Azure Active Directory 中建立 appRoleAssignments。 |
| microsoft.directory/appRoleAssignments/read | 讀取 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.directory/appRoleAssignments/update | 更新在 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.directory/appRoleAssignments/delete | 刪除 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.directory/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.directory/connectorGroups/everything/read | 讀取 Azure Active Directory 中的應用程式 Proxy 連接器群組屬性。 |
| microsoft.directory/connectorGroups/everything/update | 更新 Azure Active Directory 中的所有應用程式 Proxy 連接器群組屬性。 |
| microsoft.directory/connectorGroups/create | 在 Azure Active Directory 中建立應用程式 Proxy 連接器群組。 |
| microsoft.directory/connectorGroups/delete | 刪除 Azure Active Directory 中的應用程式 Proxy 連接器群組。 |
| microsoft.directory/connectors/everything/read | 讀取 Azure Active Directory 中的所有應用程式 Proxy 連接器屬性。 |
| microsoft.directory/connectors/create | 在 Azure Active Directory 中建立應用程式 Proxy 連接器。 |
| microsoft.directory/policies/applicationConfiguration/basic/read | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.directory/policies/applicationConfiguration/basic/update | 更新 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.directory/policies/applicationConfiguration/create | 在 Azure Active Directory 中建立原則。 |
| microsoft.directory/policies/applicationConfiguration/delete | 刪除 Azure Active Directory 中的原則。 |
| microsoft.directory/policies/applicationConfiguration/owners/read | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.directory/policies/applicationConfiguration/owners/update | 更新 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| microsoft.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 屬性。 |
| microsoft.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 屬性。 |
| microsoft.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft.directory/servicePrincipals/create | 在 Azure Active Directory 中建立 servicePrincipals。 |
| microsoft.directory/servicePrincipals/credentials/update | 更新 Azure Active Directory 中的 servicePrincipals.credentials 屬性。 |
| microsoft.directory/servicePrincipals/delete | 刪除 Azure Active Directory 中的 servicePrincipals。 |
| microsoft.directory/servicePrincipals/owners/update | 更新 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.directory/servicePrincipals/permissions/update | 更新 Azure Active Directory 中的 servicePrincipals.permissions 屬性。 |
| microsoft.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft.directory/signInReports/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="application-developer-permissions"></a>應用程式開發人員權限

可建立與 [使用者可註冊應用程式] 設定不相關的應用程式註冊。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/applications/createAsOwner | 在 Azure Active Directory 中建立應用程式。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.directory/appRoleAssignments/createAsOwner | 在 Azure Active Directory 中建立 appRoleAssignments。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.directory/oAuth2PermissionGrants/createAsOwner | 在 Azure Active Directory 中建立 oAuth2PermissionGrants。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.directory/servicePrincipals/createAsOwner | 在 Azure Active Directory 中建立 servicePrincipals。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |

### <a name="authentication-administrator-permissions"></a>驗證管理員權限

可存取以檢視、設定及重設所有非管理員使用者的驗證方法資訊。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.directory/users/strongAuthentication/update | 更新 MFA 認證資訊等增強式驗證屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.directory/users/password/update | 更新 Office 365 組織中所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |

### <a name="azure-devops-administrator-permissions"></a>Azure DevOps 管理員權限

可管理 Azure DevOps 組織原則及設定。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述[角色說明](#azure-devops-administrator)。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.devOps/allEntities/allTasks | 讀取及設定 Azure DevOps。 |

### <a name="azure-information-protection-administrator-permissions"></a>Azure 資訊保護管理員權限

可管理 Azure 資訊保護服務的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述[角色說明](#)。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 資訊保護的所有層面。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="b2c-ief-keyset-administrator-permissions"></a>B2C IEF 金鑰集管理員權限

在 Identity Experience Framework 中管理同盟和加密的祕密。

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | 在 Azure Active Directory B2C 中讀取及設定金鑰集。 |

### <a name="b2c-ief-policy-administrator-permissions"></a>B2C IEF 原則管理員權限

在 Identity Experience Framework 中建立及管理信任架構原則。

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | 在 Azure Active Directory B2C 中讀取及設定自訂原則。 |

### <a name="billing-administrator-permissions"></a>計費管理員權限

能夠執行一般計費相關工作，例如更新付款資訊。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/organization/basic/update | 更新 Azure Active Directory 中 organization 的基本屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.commerce.billing/allEntities/allTasks | 管理計費的所有層面。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="cloud-application-administrator-permissions"></a>雲端應用程式管理員權限

能夠建立及管理應用程式註冊與企業應用程式的所有層面，但應用程式 Proxy 除外。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/applications/audience/update | 在 Azure Active Directory 中更新 applications.audience 屬性。 |
| microsoft.directory/applications/authentication/update | 在 Azure Active Directory 中更新 applications.authentication 屬性。 |
| microsoft.directory/applications/basic/update | 更新 Azure Active Directory 中 Applications 的基本屬性。 |
| microsoft.directory/applications/create | 在 Azure Active Directory 中建立應用程式。 |
| microsoft.directory/applications/credentials/update | 在 Azure Active Directory 中更新 applications.credentials 屬性。 |
| microsoft.directory/applications/delete | 刪除 Azure Active Directory 中的應用程式。 |
| microsoft.directory/applications/owners/update | 更新 Azure Active Directory 中的 applications.owners 屬性。 |
| microsoft.directory/applications/permissions/update | 在 Azure Active Directory 中更新 applications.permissions 屬性。 |
| microsoft.directory/applications/policies/update | 更新 Azure Active Directory 中的 applications.policies 屬性。 |
| microsoft.directory/appRoleAssignments/create | 在 Azure Active Directory 中建立 appRoleAssignments。 |
| microsoft.directory/appRoleAssignments/update | 更新在 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.directory/appRoleAssignments/delete | 刪除 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.directory/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.directory/policies/applicationConfiguration/create | 在 Azure Active Directory 中建立原則。 |
| microsoft.directory/policies/applicationConfiguration/basic/read | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.directory/policies/applicationConfiguration/basic/update | 更新 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.directory/policies/applicationConfiguration/delete | 刪除 Azure Active Directory 中的原則。 |
| microsoft.directory/policies/applicationConfiguration/owners/read | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.directory/policies/applicationConfiguration/owners/update | 更新 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| microsoft.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 屬性。 |
| microsoft.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 屬性。 |
| microsoft.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft.directory/servicePrincipals/create | 在 Azure Active Directory 中建立 servicePrincipals。 |
| microsoft.directory/servicePrincipals/credentials/update | 更新 Azure Active Directory 中的 servicePrincipals.credentials 屬性。 |
| microsoft.directory/servicePrincipals/delete | 刪除 Azure Active Directory 中的 servicePrincipals。 |
| microsoft.directory/servicePrincipals/owners/update | 更新 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.directory/servicePrincipals/permissions/update | 更新 Azure Active Directory 中的 servicePrincipals.permissions 屬性。 |
| microsoft.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft.directory/signInReports/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="cloud-device-administrator-permissions"></a>雲端裝置管理員權限

在 Azure AD 中管理裝置的完整存取。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft.directory/devices/delete | 刪除 Azure Active Directory 中的 devices。 |
| microsoft.directory/devices/disable | 停用 Azure Active Directory 中的 devices。 |
| microsoft.directory/devices/enable | 啟用 Azure Active Directory 中的裝置。 |
| microsoft.directory/signInReports/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |

### <a name="company-administrator-permissions"></a>公司管理員權限

可管理使用 Azure AD 身分識別的 Azure AD 與 Microsoft 服務的所有層面。 這個角色也稱為全域管理員角色。 

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | 建立和刪除所有資源，同時讀取及更新 microsoft.aad.cloudAppSecurity 中的標準屬性。 |
| microsoft.directory/administrativeUnits/allProperties/allTasks | 建立和刪除 administrativeUnits，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.directory/applications/allProperties/allTasks | 建立和刪除應用程式，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.directory/appRoleAssignments/allProperties/allTasks | 建立和刪除 appRoleAssignments，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.directory/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.directory/contacts/allProperties/allTasks | 建立和刪除合約，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.directory/contracts/allProperties/allTasks | 建立和刪除合約，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.directory/devices/allProperties/allTasks | 建立和刪除裝置，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.directory/directoryRoles/allProperties/allTasks | 建立和刪除 directoryRoles，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.directory/directoryRoleTemplates/allProperties/allTasks | 建立和刪除 directoryRoleTemplates，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.directory/domains/allProperties/allTasks | 建立和刪除 domains，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.directory/groups/allProperties/allTasks | 建立和刪除 groups，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.directory/groupSettings/allProperties/allTasks | 建立和刪除 groupSettings，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.directory/groupSettingTemplates/allProperties/allTasks | 建立和刪除 groupSettingTemplates，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.directory/loginTenantBranding/allProperties/allTasks | 建立和刪除 loginTenantBranding，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | 建立和刪除 oAuth2PermissionGrants，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.directory/organization/allProperties/allTasks | 建立和刪除 organization，同時讀取及更新 Azure Active Directory 中的所有屬性。 |
| microsoft.directory/policies/allProperties/allTasks | 建立和刪除 policies，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.directory/roleAssignments/allProperties/allTasks | 建立與刪除 roleAssignments，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.directory/roleDefinitions/allProperties/allTasks | 建立與刪除 roleDefinitions，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.directory/scopedRoleMemberships/allProperties/allTasks | 建立和刪除 scopedRoleMemberships，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.directory/serviceAction/activateService | 可以在 Azure Active Directory 中執行 Activateservice 服務動作 |
| microsoft.directory/serviceAction/disableDirectoryFeature | 可以在 Azure Active Directory 中執行 Disabledirectoryfeature 服務動作 |
| microsoft.directory/serviceAction/enableDirectoryFeature | 可以在 Azure Active Directory 中執行 Enabledirectoryfeature 服務動作 |
| microsoft.directory/serviceAction/getAvailableExtentionProperties | 可以在 Azure Active Directory 中執行 Getavailableextentionproperties 服務動作 |
| microsoft.directory/servicePrincipals/allProperties/allTasks | 建立和刪除 servicePrincipals，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.directory/signInReports/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.directory/subscribedSkus/allProperties/allTasks | 建立和刪除 subscribedSkus，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.directory/users/allProperties/allTasks | 建立和刪除 users，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.directorySync/allEntities/allTasks | 執行 Azure AD Connect 中的所有動作。 |
| microsoft.aad.identityProtection/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.aad.identityProtection 中的標準屬性。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 讀取 microsoft.aad.privilegedIdentityManagement 中的所有資源。 |
| microsoft.azure.advancedThreatProtection/allEntities/read | 讀取 microsoft.aad.advancedThreatProtection 中的所有資源。 |
| microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 資訊保護的所有層面。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.commerce.billing/allEntities/allTasks | 管理計費的所有層面。 |
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
| microsoft.office365.complianceManager/allEntities/allTasks | 管理 Office 365 合規性管理員的所有層面 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="compliance-data-administrator-permissions"></a>合規性資料管理員權限

建立及管理合規性內容。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| cloudAppSecurity/allEntities/allTasks | 讀取及設定 Microsoft Cloud App Security。 |
| microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 資訊保護的所有層面。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.complianceManager/allEntities/allTasks | 管理 Office 365 合規性管理員的所有層面 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="conditional-access-administrator-permissions"></a>條件式存取管理員權限

可管理條件式存取功能。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/policies/conditionalAccess/basic/read | 讀取 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft.directory/policies/conditionalAccess/basic/update | 更新 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft.directory/policies/conditionalAccess/create | 在 Azure Active Directory 中建立原則。 |
| microsoft.directory/policies/conditionalAccess/delete | 刪除 Azure Active Directory 中的原則。 |
| microsoft.directory/policies/conditionalAccess/owners/read | 讀取 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft.directory/policies/conditionalAccess/owners/update | 更新 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft.directory/policies/conditionalAccess/policiesAppliedTo/read | 讀取 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft.directory/policies/conditionalAccess/tenantDefault/update | 更新 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |

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

### <a name="customer-lockbox-access-approver-permissions"></a>客戶加密箱存取核准者權限

可核准 Microsoft 支援要求，以存取客戶組織的資料。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.lockbox/allEntities/allTasks | 管理 Office 365 客戶加密箱的所有層面 |

### <a name="desktop-analytics-administrator-permissions"></a>電腦分析管理員權限

可以管理電腦分析及 Office 自訂和原則服務。 在電腦分析中，此角色能夠檢視資產清查、建立部署計畫、檢視部署和健康狀態。 對於 Office 自訂和原則服務，此角色可讓使用者管理 Office 原則。

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

獲指派此角色的使用者會新增至已加入 Azure AD 的裝置上的本機系統管理員群組。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/groupSettings/basic/read | 讀取 Azure Active Directory 中 groupSettings 的基本屬性。 |
| microsoft.directory/groupSettingTemplates/basic/read | 讀取 Azure Active Directory 中 groupSettingTemplates 的基本屬性。 |

### <a name="directory-readers-permissions"></a>目錄讀取者權限
可讀取基本目錄資訊。 用來授與應用程式的存取權，不適用於使用者。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/administrativeUnits/basic/read | 讀取 Azure Active Directory 中 administrativeUnits 的基本屬性。 |
| microsoft.directory/administrativeUnits/members/read | 讀取 Azure Active Directory 中的 administrativeUnits.members 屬性。 |
| microsoft.directory/applications/basic/read | 讀取 Azure Active Directory 中 Applications 的基本屬性。 |
| microsoft.directory/applications/owners/read | 讀取 Azure Active Directory 中的 applications.owners 屬性。 |
| microsoft.directory/applications/policies/read | 讀取 Azure Active Directory 中的 applications.policies 屬性。 |
| microsoft.directory/contacts/basic/read | 讀取 Azure Active Directory 中 contacts 的基本屬性。 |
| microsoft.directory/contacts/memberOf/read | 讀取 Azure Active Directory 中的 contacts.memberOf 屬性。 |
| microsoft.directory/contracts/basic/read | 讀取 Azure Active Directory 中 contracts 的基本屬性。 |
| microsoft.directory/devices/basic/read | 讀取 Azure Active Directory 中 devices 的基本屬性。 |
| microsoft.directory/devices/memberOf/read | 讀取 Azure Active Directory 中的 devices.memberOf 屬性。 |
| microsoft.directory/devices/registeredOwners/read | 讀取 Azure Active Directory 中的 devices.registeredOwners 屬性。 |
| microsoft.directory/devices/registeredUsers/read | 讀取 Azure Active Directory 中的 devices.registeredUsers 屬性。 |
| microsoft.directory/directoryRoles/basic/read | 讀取 Azure Active Directory 中 directoryRoles 的基本屬性。 |
| microsoft.directory/directoryRoles/eligibleMembers/read | 讀取 Azure Active Directory 中的 directoryRoles.eligibleMembers 屬性。 |
| microsoft.directory/directoryRoles/members/read | 讀取 Azure Active Directory 中的 directoryRoles.members 屬性。 |
| microsoft.directory/domains/basic/read | 讀取 Azure Active Directory 中 domain 的基本屬性。 |
| microsoft.directory/groups/appRoleAssignments/read | 讀取 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| microsoft.directory/groups/basic/read | 讀取 Azure Active Directory 中 groups 的基本屬性。 |
| microsoft.directory/groups/memberOf/read | 讀取 Azure Active Directory 中的 Read groups.memberOf 屬性。 |
| microsoft.directory/groups/members/read | 讀取 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft.directory/groups/owners/read | 讀取 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft.directory/groups/settings/read | 讀取 Azure Active Directory 中的 groups.settings 屬性。 |
| microsoft.directory/groupSettings/basic/read | 讀取 Azure Active Directory 中 groupSettings 的基本屬性。 |
| microsoft.directory/groupSettingTemplates/basic/read | 讀取 Azure Active Directory 中 groupSettingTemplates 的基本屬性。 |
| microsoft.directory/oAuth2PermissionGrants/basic/read | 讀取 Azure Active Directory 中 oAuth2PermissionGrants 的基本屬性。 |
| microsoft.directory/organization/basic/read | 讀取 Azure Active Directory 中 organization 的基本屬性。 |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | 讀取 Azure Active Directory 中的 organization.trustedCAsForPasswordlessAuth 屬性。 |
| microsoft.directory/roleAssignments/basic/read | 讀取 Azure Active Directory 中 roleAssignments 的基本屬性。 |
| microsoft.directory/roleDefinitions/basic/read | 讀取 Azure Active Directory 中 roleDefinitions 的基本屬性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| microsoft.directory/servicePrincipals/basic/read | 讀取 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft.directory/servicePrincipals/memberOf/read | 讀取 Azure Active Directory 中的 servicePrincipals.memberOf 屬性。 |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | 讀取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 屬性。 |
| microsoft.directory/servicePrincipals/ownedObjects/read | 讀取 Azure Active Directory 中的 servicePrincipals.ownedObjects 屬性。 |
| microsoft.directory/servicePrincipals/owners/read | 讀取 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.directory/servicePrincipals/policies/read | 讀取 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft.directory/subscribedSkus/basic/read | 讀取 Azure Active Directory 中 subscribedSkus 的基本屬性。 |
| microsoft.directory/users/appRoleAssignments/read | 讀取 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft.directory/users/basic/read | 讀取 Azure Active Directory 中 users 的基本屬性。 |
| microsoft.directory/users/directReports/read | 讀取 Azure Active Directory 中的 users.directReports 屬性。 |
| microsoft.directory/users/manager/read | 讀取 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft.directory/users/memberOf/read | 讀取 Azure Active Directory 中的 users.memberOf 屬性。 |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | 讀取 Azure Active Directory 中的 users.oAuth2PermissionGrants 屬性。 |
| microsoft.directory/users/ownedDevices/read | 讀取 Azure Active Directory 中的 users.ownedDevices 屬性。 |
| microsoft.directory/users/ownedObjects/read | 讀取 Azure Active Directory 中的 users.ownedObjects 屬性。 |
| microsoft.directory/users/registeredDevices/read | 讀取 Azure Active Directory 中的 users.registeredDevices 屬性。 |

### <a name="directory-synchronization-accounts-permissions"></a>目錄同步處理帳戶權限

僅供 Azure AD Connect 服務使用。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/organization/dirSync/update | 更新 Azure Active Directory 中的 organization.dirSync 屬性。 |
| microsoft.directory/policies/create | 在 Azure Active Directory 中建立原則。 |
| microsoft.directory/policies/delete | 刪除 Azure Active Directory 中的原則。 |
| microsoft.directory/policies/basic/read | 讀取 Azure Active Directory 中 policies 的基本屬性。 |
| microsoft.directory/policies/basic/update | 更新 Azure Active Directory 中 policies 的基本屬性。 |
| microsoft.directory/policies/owners/read | 讀取 Azure Active Directory 中的 policies.owners 屬性。 |
| microsoft.directory/policies/owners/update | 更新 Azure Active Directory 中的 policies.owners 屬性。 |
| microsoft.directory/policies/policiesAppliedTo/read | 讀取 Azure Active Directory 中的 policies.policiesAppliedTo 屬性。 |
| microsoft.directory/policies/tenantDefault/update | 更新 Azure Active Directory 中的 policies.tenantDefault 屬性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| microsoft.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 屬性。 |
| microsoft.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 屬性。 |
| microsoft.directory/servicePrincipals/basic/read | 讀取 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft.directory/servicePrincipals/create | 在 Azure Active Directory 中建立 servicePrincipals。 |
| microsoft.directory/servicePrincipals/credentials/update | 更新 Azure Active Directory 中的 servicePrincipals.credentials 屬性。 |
| microsoft.directory/servicePrincipals/memberOf/read | 讀取 Azure Active Directory 中的 servicePrincipals.memberOf 屬性。 |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | 讀取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 屬性。 |
| microsoft.directory/servicePrincipals/owners/read | 讀取 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.directory/servicePrincipals/owners/update | 更新 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.directory/servicePrincipals/ownedObjects/read | 讀取 Azure Active Directory 中的 servicePrincipals.ownedObjects 屬性。 |
| microsoft.directory/servicePrincipals/permissions/update | 更新 Azure Active Directory 中的 servicePrincipals.permissions 屬性。 |
| microsoft.directory/servicePrincipals/policies/read | 讀取 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft.directorySync/allEntities/allTasks | 執行 Azure AD Connect 中的所有動作。 |

### <a name="directory-writers-permissions"></a>目錄寫入者權限

可讀取和寫入基本目錄資訊。 用來授與應用程式的存取權，不適用於使用者。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/groups/create | 在 Azure Active Directory 中建立 groups。 |
| microsoft.directory/groups/createAsOwner | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.directory/groups/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| microsoft.directory/groups/basic/update | 更新 Azure Active Directory 中 groups 的基本屬性。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 屬性。 |
| microsoft.directory/groupSettings/basic/update | 更新 Azure Active Directory 中 groupSettings 的基本屬性。 |
| microsoft.directory/groupSettings/create | 在 Azure Active Directory 中建立 groupSettings。 |
| microsoft.directory/groupSettings/delete | 在 Azure Active Directory 中刪除 groupSettings。 |
| microsoft.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft.directory/users/assignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft.directory/users/basic/update | 更新 Azure Active Directory 中 users 的基本屬性。 |
| microsoft.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft.directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 屬性。 |

### <a name="exchange-service-administrator-permissions"></a>Exchange 服務管理員權限

可管理 Exchange 產品的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.directory/groups/unified/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.unified 屬性。 |
| microsoft.directory/groups/unified/basic/update | 更新 Office 365 群組的基本屬性。 |
| microsoft.directory/groups/unified/create | 建立 Office 365 群組。 |
| microsoft.directory/groups/unified/delete | 刪除 Office 365 群組。 |
| microsoft.directory/groups/unified/members/update | 更新 Office 365 群組的成員資格。 |
| microsoft.directory/groups/unified/owners/update | 更新 Office 365 群組的擁有權。 |
| microsoft.office365.exchange/allEntities/allTasks | 管理 Exchange Online 的所有層面。 |
| microsoft.office365.network/performance/allProperties/read | 讀取 M365 系統管理中心中的網路效能頁面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="external-id-user-flow-administrator-permissions"></a>外部識別碼使用者流程管理員權限

建立及管理使用者流程的所有層面。

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | 在 Azure Active Directory B2C 中讀取及設定使用者流程。 |

### <a name="external-id-user-flow-attribute-administrator-permissions"></a>外部識別碼使用者流程屬性管理員權限

建立及管理所有使用者流程可用的屬性結構描述。

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | 在 Azure Active Directory B2C 中讀取及設定使用者屬性。 |

### <a name="external-identity-provider-administrator-permissions"></a>外部識別提供者管理員權限

設定要用於直接同盟的識別提供者。

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | 在 Azure Active Directory B2C 中讀取及設定識別提供者。 |

### <a name="global-reader-permissions"></a>全域讀取者權限
可讀取全域管理員能讀取的所有項目，但無法編輯任何項目。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述[角色說明](#global-reader)。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.commerce.billing/allEntities/read    | 讀取計費的所有層面。 |
| microsoft.directory/administrativeUnits/basic/read    | 讀取 Azure Active Directory 中 administrativeUnits 的基本屬性。 |
| microsoft.directory/administrativeUnits/members/read    | 讀取 Azure Active Directory 中的 administrativeUnits.members 屬性。 |
| microsoft.directory/applications/basic/read    | 讀取 Azure Active Directory 中 Applications 的基本屬性。 |
| microsoft.directory/applications/owners/read    | 讀取 Azure Active Directory 中的 applications.owners 屬性。 |
| microsoft.directory/applications/policies/read    | 讀取 Azure Active Directory 中的 applications.policies 屬性。 |
| microsoft.directory/contacts/basic/read    | 讀取 Azure Active Directory 中 contacts 的基本屬性。 |
| microsoft.directory/contacts/memberOf/read    | 讀取 Azure Active Directory 中的 contacts.memberOf 屬性。 |
| microsoft.directory/contracts/basic/read    | 讀取 Azure Active Directory 中 contracts 的基本屬性。 |
| microsoft.directory/devices/basic/read    | 讀取 Azure Active Directory 中 devices 的基本屬性。 |
| microsoft.directory/devices/memberOf/read    | 讀取 Azure Active Directory 中的 devices.memberOf 屬性。 |
| microsoft.directory/devices/registeredOwners/read    | 讀取 Azure Active Directory 中的 devices.registeredOwners 屬性。 |
| microsoft.directory/devices/registeredUsers/read    | 讀取 Azure Active Directory 中的 devices.registeredUsers 屬性。 |
| microsoft.directory/directoryRoles/basic/read    | 讀取 Azure Active Directory 中 directoryRoles 的基本屬性。 |
| microsoft.directory/directoryRoles/eligibleMembers/read    | 讀取 Azure Active Directory 中的 directoryRoles.eligibleMembers 屬性。 |
| microsoft.directory/directoryRoles/members/read    | 讀取 Azure Active Directory 中的 directoryRoles.members 屬性。 |
| microsoft.directory/domains/basic/read    | 讀取 Azure Active Directory 中 domain 的基本屬性。 |
| microsoft.directory/groups/appRoleAssignments/read    | 讀取 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| microsoft.directory/groups/basic/read    | 讀取 Azure Active Directory 中 groups 的基本屬性。 |
| microsoft.directory/groups/hiddenMembers/read    | 讀取 Azure Active Directory 中的 groups.hiddenMembers 屬性。 |
| microsoft.directory/groups/memberOf/read    | 讀取 Azure Active Directory 中的 Read groups.memberOf 屬性。 |
| microsoft.directory/groups/members/read    | 讀取 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft.directory/groups/owners/read    | 讀取 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft.directory/groups/settings/read    | 讀取 Azure Active Directory 中的 groups.settings 屬性。 |
| microsoft.directory/groupSettings/basic/read    | 讀取 Azure Active Directory 中 groupSettings 的基本屬性。 |
| microsoft.directory/groupSettingTemplates/basic/read    | 讀取 Azure Active Directory 中 groupSettingTemplates 的基本屬性。 |
| microsoft.directory/oAuth2PermissionGrants/basic/read    | 讀取 Azure Active Directory 中 oAuth2PermissionGrants 的基本屬性。 |
| microsoft.directory/organization/basic/read    | 讀取 Azure Active Directory 中 organization 的基本屬性。 |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read    | 讀取 Azure Active Directory 中的 organization.trustedCAsForPasswordlessAuth 屬性。 |
| microsoft.directory/policies/standard/read    | 讀取 Azure Active Directory 中的標準原則。 |
| microsoft.directory/roleAssignments/basic/read    | 讀取 Azure Active Directory 中 roleAssignments 的基本屬性。 |
| microsoft.directory/roleDefinitions/basic/read    | 讀取 Azure Active Directory 中 roleDefinitions 的基本屬性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read    | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft.directory/servicePrincipals/appRoleAssignments/read    | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| microsoft.directory/servicePrincipals/basic/read    | 讀取 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft.directory/servicePrincipals/memberOf/read    | 讀取 Azure Active Directory 中的 servicePrincipals.memberOf 屬性。 |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read    | 讀取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 屬性。 |
| microsoft.directory/servicePrincipals/ownedObjects/read    | 讀取 Azure Active Directory 中的 servicePrincipals.ownedObjects 屬性。 |
| microsoft.directory/servicePrincipals/owners/read    | 讀取 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.directory/servicePrincipals/policies/read    | 讀取 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft.directory/signInReports/allProperties/read    | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.directory/subscribedSkus/basic/read    | 讀取 Azure Active Directory 中 subscribedSkus 的基本屬性。 |
| microsoft.directory/users/appRoleAssignments/read    | 讀取 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft.directory/users/basic/read    | 讀取 Azure Active Directory 中 users 的基本屬性。 |
| microsoft.directory/users/directReports/read    | 讀取 Azure Active Directory 中的 users.directReports 屬性。 |
| microsoft.directory/users/manager/read    | 讀取 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft.directory/users/memberOf/read    | 讀取 Azure Active Directory 中的 users.memberOf 屬性。 |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read    | 讀取 Azure Active Directory 中的 users.oAuth2PermissionGrants 屬性。 |
| microsoft.directory/users/ownedDevices/read    | 讀取 Azure Active Directory 中的 users.ownedDevices 屬性。 |
| microsoft.directory/users/ownedObjects/read    | 讀取 Azure Active Directory 中的 users.ownedObjects 屬性。 |
| microsoft.directory/users/registeredDevices/read    | 讀取 Azure Active Directory 中的 users.registeredDevices 屬性。 |
| microsoft.directory/users/strongAuthentication/read    | 讀取 MFA 認證資訊等增強式驗證屬性。 |
| microsoft.office365.exchange/allEntities/read    | 讀取 Exchange Online 的所有層面。 |
| microsoft.office365.messageCenter/messages/read    | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.messageCenter/securityMessages/read    | 讀取 microsoft.office365.messageCenter 中的 securityMessages。 |
| microsoft.office365.network/performance/allProperties/read | 讀取 M365 系統管理中心中的網路效能頁面。 |
| microsoft.office365.protectionCenter/allEntities/read    | 讀取 Office 365 防護中心的所有層面。 |
| microsoft.office365.securityComplianceCenter/allEntities/read    | 讀取 microsoft.office365.securityComplianceCenter 中的所有標準屬性。 |
| microsoft.office365.usageReports/allEntities/read    | 讀取 Office 365 使用量報告。 |
| microsoft.office365.webPortal/allEntities/standard/read    | 讀取 microsoft.office365.webPortal 中所有資源的標準屬性。 |

### <a name="groups-administrator-permissions"></a>群組管理員權限
可管理群組與群組設定的所有層面，例如命名與過期原則。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/groups/basic/read | 讀取 Azure Active Directory 中 Groups 的標準屬性。  |
| microsoft.directory/groups/basic/update | 更新 Azure Active Directory 中 groups 的基本屬性。 |
| microsoft.directory/groups/create | 在 Azure Active Directory 中建立 groups。 |
| microsoft.directory/groups/createAsOwner | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.directory/groups/delete | 刪除 Azure Active Directory 中的 groups。 |
| microsoft.directory/groups/hiddenMembers/read | 讀取 Azure Active Directory 中的 groups.hiddenMembers 屬性。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft.directory/groups/restore | 還原 Azure Active Directory 中的 groups。 |
| microsoft.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="guest-inviter-permissions"></a>來賓邀請者權限
能夠邀請不受 [成員能夠邀請來賓] 設定限制的來賓使用者。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/users/appRoleAssignments/read | 讀取 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft.directory/users/basic/read | 讀取 Azure Active Directory 中 users 的基本屬性。 |
| microsoft.directory/users/directReports/read | 讀取 Azure Active Directory 中的 users.directReports 屬性。 |
| microsoft.directory/users/inviteGuest | 邀請 Azure Active Directory 中的來賓使用者。 |
| microsoft.directory/users/manager/read | 讀取 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft.directory/users/memberOf/read | 讀取 Azure Active Directory 中的 users.memberOf 屬性。 |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | 讀取 Azure Active Directory 中的 users.oAuth2PermissionGrants 屬性。 |
| microsoft.directory/users/ownedDevices/read | 讀取 Azure Active Directory 中的 users.ownedDevices 屬性。 |
| microsoft.directory/users/ownedObjects/read | 讀取 Azure Active Directory 中的 users.ownedObjects 屬性。 |
| microsoft.directory/users/registeredDevices/read | 讀取 Azure Active Directory 中的 users.registeredDevices 屬性。 |

### <a name="helpdesk-administrator-permissions"></a>服務台管理員權限

能夠為非系統管理員與技術服務人員系統管理員重設密碼。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.directory/users/password/update | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="hybrid-identity-administrator-permissions"></a>混合式身分識別管理員權限

啟用、部署、設定、管理及監視雲端佈建與驗證服務，並對這些服務進行疑難排解。 

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.directory/applications/audience/update  | 在 Azure Active Directory 中更新 applications.audience 屬性。 |
| microsoft.directory/applications/authentication/update | 在 Azure Active Directory 中更新 applications.authentication 屬性。  |
| microsoft.directory/applications/basic/update | 更新 Azure Active Directory 中 Applications 的基本屬性。 |
| microsoft.directory/applications/create | 在 Azure Active Directory 中建立應用程式。 |
| microsoft.directory/applications/credentials/update | 在 Azure Active Directory 中更新 applications.credentials 屬性。 |
| microsoft.directory/applications/delete | 刪除 Azure Active Directory 中的應用程式。 |
| microsoft.directory/applications/owners/update | 更新 Azure Active Directory 中的 applications.owners 屬性。 |
| microsoft.directory/applications/permissions/update | 在 Azure Active Directory 中更新 applications.permissions 屬性。 |
| microsoft.directory/applications/policies/update | 更新 Azure Active Directory 中的 applications.policies 屬性。 |
| microsoft.directory/applicationTemplates/instantiate | 從應用程式範本具現化資源庫應用程式。 |
| microsoft.directory/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.directory/cloudProvisioning/allProperties/allTasks | 讀取及設定 Azure AD 雲端佈建服務的所有屬性。 |
| microsoft.directory/federatedAuthentication/allProperties/allTasks | 在 Azure AD 中管理 Active Directory 同盟服務 (ADFS) 或第三方同盟提供者的所有層面。 |
| microsoft.directory/organization/dirSync/update | 更新 Azure Active Directory 中的 organization.dirSync 屬性。 |
| microsoft.directory/passwordHashSync/allProperties/allTasks | 在 Azure AD 中管理密碼雜湊同步 (PHS) 的所有層面。 |
| microsoft.directory/passThroughAuthentication/allProperties/allTasks | 在 Azure AD 中管理傳遞驗證 (PTA) 的所有層面。 |
| microsoft.directory/seamlessSSO/allProperties/allTasks | 在 Azure AD 中管理無縫單一登入 (SSO) 的所有層面。 |
| microsoft.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 屬性。 |
| microsoft.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 屬性。 |
| microsoft.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft.directory/servicePrincipals/create | 在 Azure Active Directory 中建立 servicePrincipals。 |
| microsoft.directory/servicePrincipals/credentials/update | 更新 Azure Active Directory 中的 servicePrincipals.credentials 屬性。 |
| microsoft.directory/servicePrincipals/delete | 刪除 Azure Active Directory 中的 servicePrincipals。 |
| microsoft.directory/servicePrincipals/owners/update | 更新 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.directory/servicePrincipals/permissions/update | 更新 Azure Active Directory 中的 servicePrincipals.permissions 屬性。 |
| microsoft.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft.directory/servicePrincipals/synchronizationJobs/manage | 在 Azure AD 中管理同步處理作業的所有層面。 |
| microsoft.directory/servicePrincipals/synchronizationSchema/manage | 在 Azure AD 中管理同步處理結構描述的所有層面。 |
| microsoft.directory/servicePrincipals/synchronizationCredentials/manage | 在 Azure AD 中管理同步處理認證的所有層面。 |
| microsoft.directory/servicePrincipals/tag/update | 更新 Azure Active Directory 中的 servicePrincipals.tag 屬性。 |
| microsoft.directory/signInReports/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |


### <a name="intune-service-administrator-permissions"></a>Intune 服務管理員權限

可管理 Intune 產品的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/contacts/basic/update | 更新 Azure Active Directory 中 contacts 的基本屬性。 |
| microsoft.directory/contacts/create | 在 Azure Active Directory 中建立 contacts。 |
| microsoft.directory/contacts/delete | 刪除 Azure Active Directory 中的 contacts。 |
| microsoft.directory/devices/basic/update | 更新 Azure Active Directory 中 devices 的基本屬性。 |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft.directory/devices/create | 在 Azure Active Directory 中建立 devices。 |
| microsoft.directory/devices/delete | 刪除 Azure Active Directory 中的 devices。 |
| microsoft.directory/devices/registeredOwners/update | 更新 Azure Active Directory 中的 devices.registeredOwners 屬性。 |
| microsoft.directory/devices/registeredUsers/update | 更新 Azure Active Directory 中的 devices.registeredUsers 屬性。 |
| microsoft.directory/groups/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| microsoft.directory/groups/basic/update | 更新 Azure Active Directory 中 groups 的基本屬性。 |
| microsoft.directory/groups/create | 在 Azure Active Directory 中建立 groups。 |
| microsoft.directory/groups/createAsOwner | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.directory/groups/delete | 刪除 Azure Active Directory 中的 groups。 |
| microsoft.directory/groups/hiddenMembers/read | 讀取 Azure Active Directory 中的 groups.hiddenMembers 屬性。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft.directory/groups/restore | 還原 Azure Active Directory 中的 groups。 |
| microsoft.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 屬性。 |
| microsoft.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft.directory/users/basic/update | 更新 Azure Active Directory 中 users 的基本屬性。 |
| microsoft.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.intune/allEntities/allTasks | 管理 Intune 的所有層面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="kaizala-administrator-permissions"></a>Kaizala 管理員權限

可以管理 Microsoft Kaizala 的設定。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 Office 365 系統管理中心。 |

### <a name="license-administrator-permissions"></a>授權管理員權限

可管理使用者和群組的產品授權。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/users/assignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft.directory/users/usageLocation/update | 更新 Azure Active Directory 中的 users.usageLocation 屬性。 |
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


### <a name="message-center-privacy-reader-permissions"></a>訊息中心隱私權讀取者權限

可以讀取訊息中心貼文、資料隱私權訊息、群組、網域及訂用帳戶。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.messageCenter/securityMessages/read | 讀取 microsoft.office365.messageCenter 中的 securityMessages。 |

### <a name="message-center-reader-permissions"></a>訊息中心讀取者權限
只可在 Office 365 訊息中心讀取及更新其組織的訊息。 

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |

### <a name="modern-commerce-administrator-permissions"></a>Modern Commerce 管理員權限
可以管理公司、部門或團隊的商業採購。 

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.commerce.billing/partners/read | 讀取 O365 計費的合作夥伴屬性。 |
| microsoft.commerce.volumeLicenseServiceCenter/allEntities/allTasks | 管理大量授權服務中心的所有層面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和觀看自己的 Office 365 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |


### <a name="network-administrator-permissions"></a>網路管理員權限
可管理網路位置，及預覽 Microsoft 365 軟體即服務應用程式的企業網路設計見解。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.network/performance/allProperties/read | 讀取 M365 系統管理中心中的網路效能頁面。  |
| microsoft.office365.network/locations/allProperties/allTasks | 讀取及設定每個位置的網路位置屬性。 |

### <a name="office-apps-administrator-permissions"></a>Office 應用程式管理員權限
可以管理 Office 應用程式的雲端服務，包括原則和設定管理，以及管理選取、取消選取「新功能」功能內容及將其發佈到終端使用者裝置的能力。

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
| microsoft.office365.userCommunication/allEntities/allTasks | 讀取及更新新功能訊息的顯示設定。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="partner-tier1-support-permissions"></a>合作夥伴第 1 層支援權限

請勿使用 - 不適用於一般用途。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/contacts/basic/update | 更新 Azure Active Directory 中 contacts 的基本屬性。 |
| microsoft.directory/contacts/create | 在 Azure Active Directory 中建立 contacts。 |
| microsoft.directory/contacts/delete | 刪除 Azure Active Directory 中的 contacts。 |
| microsoft.directory/groups/create | 在 Azure Active Directory 中建立 groups。 |
| microsoft.directory/groups/createAsOwner | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft.directory/users/assignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft.directory/users/basic/update | 更新 Azure Active Directory 中 users 的基本屬性。 |
| microsoft.directory/users/delete | 刪除 Azure Active Directory 中的 users。 |
| microsoft.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft.directory/users/password/update | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft.directory/users/restore | 還原 Azure Active Directory 中已刪除的使用者。 |
| microsoft.directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 屬性。 |
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
| microsoft.directory/contacts/basic/update | 更新 Azure Active Directory 中 contacts 的基本屬性。 |
| microsoft.directory/contacts/create | 在 Azure Active Directory 中建立 contacts。 |
| microsoft.directory/contacts/delete | 刪除 Azure Active Directory 中的 contacts。 |
| microsoft.directory/domains/allTasks | 建立和刪除 domains，以及在 Azure Active Directory 中讀取和更新標準屬性。 |
| microsoft.directory/groups/create | 在 Azure Active Directory 中建立 groups。 |
| microsoft.directory/groups/delete | 刪除 Azure Active Directory 中的 groups。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft.directory/groups/restore | 還原 Azure Active Directory 中的 groups。 |
| microsoft.directory/organization/basic/update | 更新 Azure Active Directory 中 organization 的基本屬性。 |
| microsoft.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft.directory/users/assignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft.directory/users/basic/update | 更新 Azure Active Directory 中 users 的基本屬性。 |
| microsoft.directory/users/delete | 刪除 Azure Active Directory 中的 users。 |
| microsoft.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft.directory/users/password/update | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft.directory/users/restore | 還原 Azure Active Directory 中已刪除的使用者。 |
| microsoft.directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="password-administrator-permissions"></a>密碼管理員權限

可以重設非管理員及密碼管理員的密碼。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/users/password/update | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="power-bi-service-administrator-permissions"></a>Power BI 服務管理員權限

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


### <a name="power-platform-administrator-permissions"></a>Power Platform 管理員權限

可建立及管理 Microsoft Dynamics 365、PowerApps 及 Microsoft Flow 的所有層面。 

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>
| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.dynamics365/allEntities/allTasks | 管理 Dynamics 365 的所有層面。 |
| microsoft.flow/allEntities/allTasks | 管理 Microsoft Flow 的所有層面。 |
| microsoft.powerApps/allEntities/allTasks | 管理 PowerApps 的所有層面。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="printer-administrator-permissions"></a>印表機管理員權限

可管理印表機和印表機連線程式的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>
| **動作** | **說明** |
| --- | --- |
| microsoft.azure.print/allEntities/allProperties/allTasks | 建立與刪除印表機和連線程式，及讀取與更新 Microsoft Print 中的所有屬性。 |

### <a name="printer-technician-permissions"></a>印表機技術人員權限

可註冊和取消註冊印表機，及更新印表機狀態。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>
| **動作** | **說明** |
| --- | --- |
| microsoft.azure.print/connectors/allProperties/read | 讀取 Microsoft Print 中連線程式的所有屬性。 |
| microsoft.azure.print/printers/allProperties/read | 讀取 Microsoft Print 中印表機的所有屬性。 |
| microsoft.azure.print/printers/basic/update | 更新 Microsoft Print 中印表機的基本屬性。 |
| microsoft.azure.print/printers/register | 在 Microsoft Print 中註冊印表機。 |
| microsoft.azure.print/printers/unregister | 在 Microsoft Print 中取消註冊印表機。 |

### <a name="privileged-authentication-administrator-permissions"></a>特殊權限驗證管理員權限

可檢視、設定及重設所有使用者 (管理員或非管理員) 的驗證方法資訊。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.directory/users/strongAuthentication/update | 更新 MFA 認證資訊等增強式驗證屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.directory/users/password/update | 更新 Office 365 組織中所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |

### <a name="privileged-role-administrator-permissions"></a>特殊權限角色管理員權限

能夠管理 Azure AD 中的角色指派，以及 Privileged Identity Management 的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.aad.privilegedIdentityManagement 中的標準屬性。 |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/allTasks | 讀取及設定 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | 讀取及設定 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 屬性。 |
| microsoft.directory/administrativeUnits/allProperties/allTasks | 建立及管理系統管理單位 (包括成員) |
| microsoft.directory/roleAssignments/allProperties/allTasks | 建立及管理角色指派。 |
| microsoft.directory/roleDefinitions/allProperties/allTasks | 建立及管理角色定義。 |

### <a name="reports-reader-permissions"></a>報告讀取者權限

可讀取登入與稽核報告。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.directory/signInReports/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |

### <a name="search-administrator-permissions"></a>搜尋管理員權限

可以建立及管理 Microsoft 搜尋設定的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.search/allEntities/allProperties/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.office365.search 中的所有屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="search-editor-permissions"></a>搜尋編輯者權限

可以建立及管理編輯內容，例如書籤、問與答、位置、樓面規劃。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.search/content/allProperties/allTasks | 在 microsoft.office365.search 中建立和刪除內容，以及讀取和更新所有屬性。 |

### <a name="security-administrator-permissions"></a>安全性管理員權限

能夠讀取安全性資訊與報表，以及管理 Azure AD 與 Office 365 中的設定。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/applications/policies/update | 更新 Azure Active Directory 中的 applications.policies 屬性。 |
| microsoft.directory/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft.directory/policies/basic/update | 更新 Azure Active Directory 中 policies 的基本屬性。 |
| microsoft.directory/policies/create | 在 Azure Active Directory 中建立原則。 |
| microsoft.directory/policies/delete | 刪除 Azure Active Directory 中的原則。 |
| microsoft.directory/policies/owners/update | 更新 Azure Active Directory 中的 policies.owners 屬性。 |
| microsoft.directory/policies/tenantDefault/update | 更新 Azure Active Directory 中的 policies.tenantDefault 屬性。 |
| microsoft.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft.directory/signInReports/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.aad.identityProtection/allEntities/read | 讀取 microsoft.aad.identityProtection 中的所有資源。 |
| microsoft.aad.identityProtection/allEntities/update | 更新 microsoft.aad.identityProtection 中的所有資源。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 讀取 microsoft.aad.privilegedIdentityManagement 中的所有資源。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.protectionCenter/allEntities/read | 讀取 Office 365 防護中心的所有層面。 |
| microsoft.office365.protectionCenter/allEntities/update | 更新 microsoft.office365.protectionCenter 中的所有資源。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |

### <a name="security-operator-permissions"></a>安全性操作員權限

建立及管理安全性事件。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | 讀取及設定 Microsoft Cloud App Security。 |
| microsoft.aad.identityProtection/allEntities/read | 讀取 microsoft.aad.identityProtection 中的所有資源。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 讀取 microsoft.aad.privilegedIdentityManagement 中的所有資源。 |
| microsoft.azure.advancedThreatProtection/allEntities/read | 讀取及設定 Azure AD 進階威脅防護。 |
| microsoft.intune/allEntities/allTasks | 管理 Intune 的所有層面。 |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | 讀取及設定安全性與合規性中心。 |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | 讀取及設定 Windows Defender 進階威脅防護。 |

### <a name="security-reader-permissions"></a>安全性讀取者權限

可讀取安全性資訊及 Azure AD 與 Office 365 中的報告。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft.directory/policies/conditionalAccess/basic/read | 讀取 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft.directory/signInReports/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
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

### <a name="sharepoint-service-administrator-permissions"></a>SharePoint 服務管理員權限

可管理 SharePoint 服務的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.directory/groups/unified/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.unified 屬性。 |
| microsoft.directory/groups/unified/basic/update | 更新 Office 365 群組的基本屬性。 |
| microsoft.directory/groups/unified/create | 建立 Office 365 群組。 |
| microsoft.directory/groups/unified/delete | 刪除 Office 365 群組。 |
| microsoft.directory/groups/unified/members/update | 更新 Office 365 群組的成員資格。 |
| microsoft.directory/groups/unified/owners/update | 更新 Office 365 群組的擁有權。 |
| microsoft.office365.network/performance/allProperties/read | 讀取 M365 系統管理中心中的網路效能頁面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.sharepoint/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.office365.sharepoint 中的標準屬性。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.usageReports/allEntities/read    | 讀取 Office 365 使用量報告。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="teams-communications-administrator-permissions"></a>Teams 通訊管理員權限

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

### <a name="teams-communications-support-engineer-permissions"></a>Teams 通訊支援工程師權限

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

### <a name="teams-communications-support-specialist-permissions"></a>Teams 通訊支援專家權限

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

### <a name="teams-service-administrator-permissions"></a>Teams 服務管理員權限

能夠管理 Microsoft Teams 服務。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.directory/groups/hiddenMembers/read | 讀取 Azure Active Directory 中的 groups.hiddenMembers 屬性。 |
| microsoft.directory/groups/unified/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.unified 屬性。 |
| microsoft.directory/groups/unified/basic/update | 更新 Office 365 群組的基本屬性。 |
| microsoft.directory/groups/unified/create | 建立 Office 365 群組。 |
| microsoft.directory/groups/unified/delete | 刪除 Office 365 群組。 |
| microsoft.directory/groups/unified/members/update | 更新 Office 365 群組的成員資格。 |
| microsoft.directory/groups/unified/owners/update | 更新 Office 365 群組的擁有權。 |
| microsoft.office365.network/performance/allProperties/read | 讀取 M365 系統管理中心中的網路效能頁面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="user-administrator-permissions"></a>使用者管理員權限
能夠管理使用者與群組的所有層面，包含為受限制的管理員重設密碼。

| **動作** | **說明** |
| --- | --- |
| microsoft.directory/appRoleAssignments/create | 在 Azure Active Directory 中建立 appRoleAssignments。 |
| microsoft.directory/appRoleAssignments/delete | 刪除 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.directory/appRoleAssignments/update | 更新在 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.directory/contacts/basic/update | 更新 Azure Active Directory 中 contacts 的基本屬性。 |
| microsoft.directory/contacts/create | 在 Azure Active Directory 中建立 contacts。 |
| microsoft.directory/contacts/delete | 刪除 Azure Active Directory 中的 contacts。 |
| microsoft.directory/groups/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| microsoft.directory/groups/basic/update | 更新 Azure Active Directory 中 groups 的基本屬性。 |
| microsoft.directory/groups/create | 在 Azure Active Directory 中建立 groups。 |
| microsoft.directory/groups/createAsOwner | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.directory/groups/delete | 刪除 Azure Active Directory 中的 groups。 |
| microsoft.directory/groups/hiddenMembers/read | 讀取 Azure Active Directory 中的 groups.hiddenMembers 屬性。 |
| microsoft.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft.directory/groups/restore | 還原 Azure Active Directory 中的 groups。 |
| microsoft.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 屬性。 |
| microsoft.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft.directory/users/assignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft.directory/users/basic/update | 更新 Azure Active Directory 中 users 的基本屬性。 |
| microsoft.directory/users/create | 在 Azure Active Directory 中建立 users。 |
| microsoft.directory/users/delete | 刪除 Azure Active Directory 中的 users。 |
| microsoft.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft.directory/users/password/update | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft.directory/users/restore | 還原 Azure Active Directory 中已刪除的使用者。 |
| microsoft.directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

## <a name="role-template-ids"></a>角色範本識別碼

角色範本識別碼主要是由 Microsoft Graph API 或 PowerShell 使用者使用。

Graph displayName | Azure 入口網站顯示名稱 | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
應用程式系統管理員 | 應用程式管理員 | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
應用程式開發人員 | 應用程式開發人員 | CF1C38E5-3621-4004-A7CB-879624DCED7C
驗證系統管理員 | 驗證管理員 | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure DevOps 管理員 | Azure DevOps 管理員 | e3973bdf-4987-49ae-837a-ba8e231c7286
Azure 資訊保護管理員 | Azure 資訊保護管理員 | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C IEF 金鑰集管理員 | B2C IEF 金鑰集管理員 | aaf43236-0c0d-4d5f-883a-6955382ac081
B2C IEF 原則管理員 | B2C IEF 原則管理員 | 3edaf663-341e-4475-9f94-5c398ef6c070
計費管理員 | 計費管理員 | b0f54661-2d74-4c50-afa3-1ec803f12efe
雲端應用程式系統管理員 | 雲端應用程式系統管理員 | 158c047a-c907-4556-b7ef-446551a6b5f7
雲端裝置管理員 | 雲端裝置管理員 | 7698a772-787b-4ac8-901f-60d6b08affd2
公司系統管理員 | 全域管理員 | 62e90394-69f5-4237-9190-012177145e10
規範管理員 | 規範管理員 | 17315797-102d-40b4-93e0-432062caca18
合規性資料管理員 | 合規性資料管理員 | e6d1a23a-da11-4be4-9570-befc86d067a7
條件式存取系統管理員 | 條件式存取系統管理員 | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
CRM 服務管理員 | Dynamics 365 管理員 | 44367163-eba1-44c3-98af-f5787879f96a
客戶 LockBox 存取核准者 | 客戶加密箱存取核准者 | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
電腦分析系統管理員 | 電腦分析系統管理員 | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
裝置系統管理員 | 裝置管理員 | 9f06204d-73c1-4d4c-880a-6edb90606fd8
加入裝置 | 已被取代 | 9c094953-4995-41c8-84c8-3ebb9b32c93f
裝置管理員 | 已被取代 | 2b499bcd-da44-4968-8aec-78e1674fa64d
裝置使用者 | 已被取代 | d405c6df-0af8-4e3b-95e4-4d06e542189e
目錄讀取器 | 目錄讀取器 | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
目錄同步處理帳戶 | 未顯示，因為不得使用 | d29b2b05-8046-44ba-8758-1e26182fcf32
目錄撰寫者 | 未顯示，因為不得使用 | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange 服務管理員 | Exchange 系統管理員 | 29232cdf-9323-42fd-ade2-1d097af3e4de
外部識別碼使用者流程管理員 | 外部識別碼使用者流程管理員 | 6e591065-9bad-43ed-90f3-e9424366d2f0
外部識別碼使用者流程屬性管理員 | 外部識別碼使用者流程屬性管理員 | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
外部識別提供者管理員 | 外部識別提供者管理員 | be2f45a1-457d-42af-a067-6ec1fa63bc45
全域讀取者 | 全域讀取者 | f2ef992c-3afb-46b9-b7cf-a126ee74c451
群組管理員 | 群組管理員 | fdd7a751-b60b-444a-984c-02652fe8fa1c 
來賓邀請者 | 來賓邀請者 | 95e79109-95c0-4d8e-aee3-d01accf2d47b
服務台系統管理員 | 服務台管理員 | 729827e3-9c14-49f7-bb1b-9608f156bbb8
混合式身分識別管理員 | 混合式身分識別管理員 | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2
Intune 服務管理員 | Intune 管理員 | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala 管理員 | Kaizala 管理員 | 74ef975b-6605-40af-a5d2-b9539d836353
授權管理員 | 授權管理員 | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync 服務管理員 | 商務用 Skype 的管理員 | 75941009-915a-4869-abe7-691bff18279e
訊息中心隱私權讀取者 | 訊息中心隱私權讀取者 | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
訊息中心讀取者 | 訊息中心讀取者 | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Modern Commerce 管理員 | Modern Commerce 管理員 | d24aef57-1500-4070-84db-2666f29cf966
網路管理員 | 網路管理員 | d37c8bed-0711-4417-ba38-b4abe66ce4c2
Office 應用程式管理員 | Office 應用程式管理員 | 2b745bdf-0803-4d80-aa65-822c4493daac
合作夥伴第 1 層支援 | 未顯示，因為不得使用 | 4ba39ca4-527c-499a-b93d-d9b492c50246
合作夥伴第 2 層支援 | 未顯示，因為不得使用 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
密碼管理員 | 密碼管理員 | 966707d0-3269-4727-9be2-8c3a10f19b9d
Power BI 服務管理員 | Power BI 管理員 | a9ea8996-122f-4c74-9520-8edcd192826c
Power Platform 管理員 | Power Platform 管理員 | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
印表機管理員 | 印表機管理員 | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f
印表機技術人員 | 印表機技術人員 | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477
特殊權限驗證管理員 | 特殊權限驗證管理員 | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
特殊權限角色管理員 | 特殊權限角色管理員 | e8611ab8-c189-46e8-94e1-60213ab1f814
報告讀者 | 報表讀者 | 4a5d8f65-41da-4de4-8968-e035b65339cf
搜尋管理員 | 搜尋管理員 | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
搜尋編輯者 | 搜尋編輯者 | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
安全性系統管理員 | 安全性系統管理員 | 194ae4cb-b126-40b2-bd5b-6091b380977d
安全性操作員 | 安全性操作員 | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
安全性讀取者 | 安全性讀取者 | 5d6b6bb7-de71-4623-b4af-96380a352509
服務支援管理員 | 服務支援管理員 | f023fd81-a637-4b56-95fd-791ac0226033
SharePoint 服務管理員 | SharePoint 管理員 | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Microsoft Teams 通訊系統管理員 | Microsoft Teams 通訊系統管理員 | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Microsoft Teams 通訊支援工程師 | Microsoft Teams 通訊支援工程師 | f70938a0-fc10-4177-9e90-2178f8765737
Microsoft Teams 通訊支援專家 | Microsoft Teams 通訊支援專家 | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Microsoft Teams 服務管理員 | Microsoft Teams 服務管理員 | 69091246-20e8-4a56-aa4d-066075b2a7a8
User | 未顯示，因為不能使用 | a0b1b346-4d3e-4e8b-98f8-753987be4970
使用者帳戶管理員 | 使用者管理員 | fe930be7-5e62-47db-91af-98c3a49a38b1
加入工作場所裝置 | 已被取代 | c34f683f-4d5a-4403-affd-6615e00e3a7f

## <a name="deprecated-roles"></a>已被取代的角色

以下是不應使用的角色。 它們已被取代，而且未來將從 Azure AD 中移除。

* AdHoc 授權管理員
* 加入裝置
* 裝置管理員
* 裝置使用者
* 傳送電子郵件給經過驗證的使用者建立者
* 信箱管理員
* 加入工作場所裝置

## <a name="roles-not-shown-in-the-portal"></a>入口網站中未顯示的角色

並非 PowerShell 或 MS Graph API 所傳回的每個角色都可以在 Azure 入口網站中看到。 下表將組織這些差異。

API 名稱 | Azure 入口網站名稱 | 注意
-------- | ------------------- | -------------
公司系統管理員 | 全域管理員 | [名稱已變更，變得更清楚明瞭](directory-assign-admin-roles.md#role-template-ids)
CRM 服務管理員 | Dynamics 365 管理員 | [反映目前的產品品牌](directory-assign-admin-roles.md#role-template-ids)
加入裝置 | 已被取代 | [已淘汰的角色文件](directory-assign-admin-roles.md#deprecated-roles)
裝置管理員 | 已被取代 | [已淘汰的角色文件](directory-assign-admin-roles.md#deprecated-roles)
裝置使用者 | 已被取代 | [已淘汰的角色文件](directory-assign-admin-roles.md#deprecated-roles)
目錄同步處理帳戶 | 未顯示，因為不得使用 | [目錄同步處理帳戶文件](directory-assign-admin-roles.md#directory-synchronization-accounts)
目錄撰寫者 | 未顯示，因為不得使用 | [目錄寫入者文件](directory-assign-admin-roles.md#directory-writers)
來賓使用者 | 未顯示，因為不能使用  | NA
Lync 服務管理員 | 商務用 Skype 的管理員 | [反映目前的產品品牌](directory-assign-admin-roles.md#role-template-ids)
合作夥伴第 1 層支援 | 未顯示，因為不得使用 | [合作夥伴第 1 層支援文件](directory-assign-admin-roles.md#partner-tier1-support)
合作夥伴第 2 層支援 | 未顯示，因為不得使用 | [合作夥伴第 2 層支援文件](directory-assign-admin-roles.md#partner-tier2-support)
受限制的來賓使用者 | 未顯示，因為不能使用 | NA
User | 未顯示，因為不能使用 | NA
加入工作場所裝置 | 已被取代 | [已淘汰的角色文件](directory-assign-admin-roles.md#deprecated-roles)

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何將使用者指派為 Azure 訂用帳戶的系統管理員，請參閱[使用 Azure 角色 (Azure RBAC) 管理存取權](../../role-based-access-control/role-assignments-portal.md)
* 若要深入了解如何在 Microsoft Azure 中控制資源存取，請參閱[了解不同角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* 如需訂用帳戶和 Azure AD 租使用者之間關聯性的詳細資訊，或如需相關指示或新增訂閱的指示，請參閱[將 Azure 訂用帳戶關聯或新增至您的 Azure Active Directory 租使用者](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
