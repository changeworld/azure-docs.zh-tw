---
title: 管理員角色描述和許可權-Azure AD |Microsoft Docs
description: 系統管理員角色可以新增使用者、指派系統管理角色、重設使用者密碼、管理使用者授權或管理網域。
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 02/28/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: d024382f816e98fb5cb83331dd417f0c41362bc4
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207042"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Azure Active Directory 中的系統管理員角色權限

使用 Azure Active Directory （Azure AD），您可以指定有限的系統管理員，以較低許可權的角色來管理身分識別工作。 指派系統管理員的目的，是為了新增或變更使用者、指派系統管理角色、重設使用者密碼、管理使用者授權，以及管理功能變數名稱等。 只能在 Azure AD 中的使用者設定中變更預設使用者權限。

## <a name="limit-the-use-of-global-administrator"></a>限制全域管理員的使用

指派給全域管理員角色的使用者可以讀取和修改您 Azure AD 組織中的每個系統管理設定。 根據預設，註冊 Azure 訂用帳戶的人員會獲指派 Azure AD 組織的全域管理員角色。 只有全域管理員和特殊許可權角色管理員可以委派系統管理員角色。 若要降低業務的風險，建議您將此角色指派給組織中最少的人員。

建議的最佳作法是將此角色指派給組織中的員工人數少於五個。 如果您的組織中有超過五個指派給全域管理員角色的系統管理員，以下是一些減少其使用方式的方法。

### <a name="find-the-role-you-need"></a>尋找您所需的角色

如果您在許多角色清單中找不到需要的角色，Azure AD 可以根據角色類別來顯示角色的子集。 查看適用于[Azure AD 角色和系統管理員](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)的新**類型**篩選，只顯示所選類型中的角色。

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>當您指派全域管理員角色時，現在角色已存在

角色或角色可能已新增至 Azure AD，以提供更細微的許可權，讓某些使用者在全域管理員時無法使用此選項。 經過一段時間，我們會推出其他角色，以完成隻有全域管理員角色才可執行檔工作。 您可以看到這些反映在下列[可用的角色](#available-roles)中。

## <a name="assign-or-remove-administrator-roles"></a>指派或移除系統管理員角色

若要了解如何將系統管理角色指派給 Azure Active Directory 中的使用者，請參閱[在 Azure Active Directory 中檢視和指派系統管理員角色](directory-manage-roles-portal.md)。

## <a name="available-roles"></a>可用的角色

可用的系統管理員角色如下：

### <a name="application-administrator"></a>[應用程式系統管理員](#application-administrator-permissions)

此角色中的使用者可以建立和管理企業應用程式、應用程式註冊和應用程式 Proxy 設定的所有層面。 請注意，在建立新的應用程式註冊或企業應用程式時，指派給此角色的使用者不會新增為擁有者。

應用程式系統管理員可以管理應用程式認證，讓他們模擬應用程式。 因此，指派給這個角色的使用者只能管理未指派給任何 Azure AD 角色或指派給下列系統管理員角色之應用程式的應用程式認證：
* 應用程式系統管理員
* 應用程式開發人員
* 雲端應用程式系統管理員
* 目錄讀取器

如果將應用程式指派給上述未提及的任何其他角色，則應用程式系統管理員將無法管理該應用程式的認證。 
 
此角色也會授與_同意_委派許可權和應用程式許可權的能力，但不含 Microsoft Graph API 的許可權例外。

> [!IMPORTANT]
> 這個例外狀況表示您仍然可以同意_其他_應用程式（例如，您已註冊的協力廠商應用程式或應用程式）的許可權，但不是對 Azure AD 本身的許可權。 您仍然可以在應用程式註冊過程中_要求_這些許可權 _，但授_與（亦即同意）這些許可權需要 Azure AD 系統管理員。這表示惡意使用者無法輕鬆提升其許可權，例如，建立並同意可寫入整個目錄的應用程式，以及透過該應用程式的許可權，將自己提升為全域管理員。

### <a name="application-developer"></a>[應用程式開發人員](#application-developer-permissions)

將「使用者可以註冊應用程式」設定設為「否」時，此角色中的使用者可以建立應用程式註冊。 當「使用者可以同意應用程式代表自己存取公司資料」設定設為 [否] 時，此角色也會授與許可權以代表自己的同意。 在建立新的應用程式註冊或企業應用程式時，指派給此角色的使用者會被新增為擁有者。

### <a name="authentication-administrator"></a>[驗證系統管理員](#authentication-administrator-permissions)

驗證管理員角色目前處於公開預覽狀態。 具有此角色的使用者可以設定或重設非密碼認證，並且可以更新所有使用者的密碼。 驗證系統管理員可以要求使用者針對現有的非密碼認證（例如，MFA 或 FIDO）重新註冊，並撤銷**裝置上的 [記住 mfa**]，這會在下一次登入非系統管理員或僅指派下列角色的使用者登入時提示 mfa：

* 驗證系統管理員
* 目錄讀取器
* 來賓邀請者
* 訊息中心讀取者
* 報告讀者

> [!IMPORTANT]
> 對於可存取機密或私人資訊或 Azure Active Directory 內外重要組態的人員，具備此角色的使用者可以變更認證。 變更使用者的認證表示可承擔該使用者身分識別和權限。 例如：
>
>- 應用程式註冊和企業應用程式擁有者，他們可以管理他們自己的應用程式認證。 這些應用程式在 Azure AD 中可能有特殊權限，而在其他地方未授與驗證系統管理員。 透過此路徑，驗證系統管理員可以假設應用程式擁有者的身分識別，然後藉由更新應用程式的認證，進一步假設特殊許可權應用程式的識別。
>- Azure 訂用帳戶擁有者，他們具有機密或私人資訊或者 Azure 中重要組態的存取權。
>- 安全性群組和 Office 365 群組擁有者，他們可以管理群組成員資格。 這個群組可以存取機密或私人資訊或者 Azure AD 和其他位置中的重要組態。
>- Azure AD 外部其他服務 (例如，Exchange Online、Office 安全性與合規性中心和人力資源系統) 中的系統管理員。
>- 非系統管理員，例如主管、法律顧問和人力資源員工，他們可以存取機密或私人資訊。

### <a name="azure-devops-administrator"></a>[Azure DevOps 系統管理員](#azure-devops-administrator-permissions)

具有此角色的使用者可以管理 Azure DevOps 原則，將新的 Azure DevOps 組織建立限制為一組可設定的使用者或群組。 此角色中的使用者可以透過任何支援公司 Azure AD 組織的 Azure DevOps 組織來管理此原則。

此角色中的使用者可以管理所有企業 Azure DevOps 的原則。

### <a name="azure-information-protection-administrator"></a>[Azure 資訊保護系統管理員](#azure-information-protection-administrator-permissions)

具有此角色的使用者在 Azure 資訊保護服務上擁有所有權限。 此角色允許設定「Azure 資訊保護」原則的標籤、管理保護範本，以及啟用保護。 此角色並未授與「Identity Protection 中心」、Privileged Identity Management、「監視 Office 365 服務健康情況」及「Office 365 安全與規範中心」中的任何權限。

### <a name="b2c-user-flow-administrator"></a>[B2C 使用者流程管理員](#b2c-user-flow-administrator-permissions)

具有此角色的使用者可以建立和管理 Azure 入口網站中的 B2C 消費者流程（也稱為「內建」原則）。 藉由建立或編輯使用者流程，這些使用者可以變更使用者體驗的 html/CSS/javascript 內容、變更每個使用者流程的 MFA 需求、變更權杖中的宣告，以及調整租使用者中所有原則的會話設定。 另一方面，此角色並不包括檢查使用者資料的能力，或對租使用者架構中包含的屬性進行變更。 Identity Experience Framework （也稱為「自訂」）原則的變更也不在此角色的範圍內。

### <a name="b2c-user-flow-attribute-administrator"></a>[B2C 使用者流程屬性管理員](#b2c-user-flow-attribute-administrator-permissions)

具有此角色的使用者可在租使用者中的所有使用者流程中，新增或刪除自訂屬性。 因此，具備此角色的使用者可以變更或新增元素至使用者架構，並影響所有使用者流程的行為，並間接導致使用者可能會要求哪些資料的變更，最後以宣告的形式傳送給應用程式。 此角色無法編輯使用者流程。

### <a name="b2c-ief-keyset-administrator"></a>[B2C IEF 索引鍵集管理員](#b2c-ief-keyset-administrator-permissions)

使用者可以建立和管理權杖加密、權杖簽章和宣告加密/解密的原則金鑰和密碼。 藉由將新金鑰新增至現有的金鑰容器，此有限的系統管理員可以視需要變換秘密，而不會影響現有的應用程式。 此使用者可以查看這些秘密的完整內容及其到期日，即使在建立之後也一樣。

> [!IMPORTANT]
> 這是敏感性角色。 在生產前和生產期間，應謹慎地仔細測試並指派金鑰集系統管理員角色。

### <a name="b2c-ief-policy-administrator"></a>[B2C IEF 原則管理員](#b2c-ief-policy-administrator-permissions)

此角色的使用者能夠在 Azure AD B2C 中建立、讀取、更新及刪除所有自訂原則，因此可以完全控制相關 Azure AD B2C 租使用者中的 Identity Experience Framework。 藉由編輯原則，此使用者可以與外部身分識別提供者建立直接同盟、變更目錄架構、變更所有使用者面向內容（HTML、CSS、JavaScript）、變更需求以完成驗證、建立新使用者、傳送使用者資料到外部系統，包括完整的遷移，以及編輯所有使用者資訊，包括密碼和電話號碼等敏感欄位。 相反地，此角色無法變更加密金鑰，或編輯用於租使用者中同盟的秘密。

> [!IMPORTANT]
> B2 IEF 原則系統管理員是高度敏感的角色，應針對生產環境中的租使用者以非常有限的基礎加以指派。 這些使用者的活動應該仔細地進行審核，特別是針對生產環境中的租使用者。

### <a name="billing-administrator"></a>[計費管理員](#billing-administrator-permissions)

進行採購、管理訂用帳戶、管理支援票證，以及監控服務健全狀況。

### <a name="cloud-application-administrator"></a>[雲端應用程式系統管理員](#cloud-application-administrator-permissions)

此角色中的使用者具有與應用程式系統管理員角色相同的權限，但不包括管理應用程式 Proxy 的能力。 此角色會授與能力來建立和管理企業應用程式和應用程式註冊的所有層面。 此角色也會授與同意委派許可權的能力，以及不包括 Microsoft Graph API 的應用程式許可權。 在建立新的應用程式註冊或企業應用程式時，不會將指派給此角色的使用者新增為擁有者。

雲端應用程式系統管理員可以管理應用程式認證，讓他們模擬應用程式。 因此，指派給這個角色的使用者只能管理未指派給任何 Azure AD 角色或指派給下列系統管理員角色之應用程式的應用程式認證：
* 應用程式開發人員
* 雲端應用程式系統管理員
* 目錄讀取器

如果將應用程式指派給上述未提及的任何其他角色，則雲端應用程式系統管理員無法管理該應用程式的認證。

### <a name="cloud-device-administrator"></a>[雲端裝置管理員](#cloud-device-administrator-permissions)

此角色的使用者可以啟用、停用和刪除 Azure AD 中的裝置，並在 Azure 入口網站中讀取 Windows 10 BitLocker 金鑰 (如果有的話)。 此角色不會授與可供管理裝置上任何其他屬性的權限。

### <a name="compliance-administrator"></a>[合規性管理員](#compliance-administrator-permissions)

具備此角色的使用者有權限管理 Microsoft 365 合規性中心、Microsoft 365 系統管理中心、Azure 和 Office 365 安全性與合規性中心中的合規性相關功能。 「代理人」也可以管理 Exchange 系統管理中心內的所有功能，以及 & 「商務用 Skype 系統管理中心」的小組，並建立 Azure 和 Microsoft 365 的支援票證。 如需詳細資訊，請參閱[關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

在 | 可以執行
----- | ----------
[Microsoft 365 合規性中心](https://protection.office.com) | 保護和管理您組織在所有 Microsoft 365 服務中的資料<br>管理合規性警示
[合規性管理員](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 追蹤、指派和確認您組織的法規合規性活動
[Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理資料治理<br>執行法律和資料的調查<br>管理資料主體要求<br><br>此角色具有與 Office 365 安全性 & 合規性中心角色型存取控制中的[相容性系統管理員 RoleGroup](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)相同的許可權。
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 檢視所有的 Intune 稽核資料
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 具有唯讀權限，並可管理警示<br>可建立和修改檔案原則，並允許檔案治理動作<br>可檢視 [資料管理] 下的所有內建報告

### <a name="compliance-data-administrator"></a>[合規性資料管理員](#compliance-data-administrator-permissions)

具有此角色的使用者具有在 Microsoft 365 合規性中心、Microsoft 365 系統管理中心和 Azure 中追蹤資料的許可權。 使用者也可以追蹤 Exchange 系統管理中心、合規性管理員和小組 & 商務用 Skype 系統管理中心的相容性資料，並建立 Azure 和 Microsoft 365 的支援票證。

在 | 可以執行
----- | ----------
[Microsoft 365 合規性中心](https://protection.office.com) | 監視跨 Microsoft 365 服務的合規性相關原則<br>管理合規性警示
[合規性管理員](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 追蹤、指派和確認您組織的法規合規性活動
[Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理資料治理<br>執行法律和資料的調查<br>管理資料主體要求<br><br>此角色具有與 Office 365 安全性 & 合規性中心角色型存取控制中的[相容性資料管理員 RoleGroup](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)相同的許可權。
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 檢視所有的 Intune 稽核資料
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 具有唯讀權限，並可管理警示<br>可建立和修改檔案原則，並允許檔案治理動作<br>可檢視 [資料管理] 下的所有內建報告

### <a name="conditional-access-administrator"></a>[條件式存取系統管理員](#conditional-access-administrator-permissions)

具有此角色的使用者能夠管理 Azure Active Directory 的條件式存取設定。
> [!NOTE]
> 若要在 Azure 中部署 Exchange ActiveSync 條件式存取原則，使用者也必須是全域管理員。

### <a name="customer-lockbox-access-approver"></a>[客戶加密箱存取核准者](#customer-lockbox-access-approver-permissions)

管理您組織中的[客戶加密箱要求](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests)。 他們會收到「客戶加密箱」要求的電子郵件通知，並且可以核准和拒絕來自 Microsoft 365 系統管理中心的要求。 他們也可以開啟或關閉「客戶加密箱」功能。 只有全域管理員可以重設指派給此角色之人員的密碼。

### <a name="desktop-analytics-administrator"></a>[電腦分析系統管理員](#desktop-analytics-administrator-permissions)


此角色中的使用者可以管理電腦分析和 Office 自訂 & 原則服務。 針對電腦分析，這包括能夠查看資產清查、建立部署計畫、查看部署和健康狀態。 若為 Office 自訂 & 原則服務，此角色可讓使用者管理 Office 原則。

### <a name="device-administrator"></a>[裝置系統管理員](#device-administrators-permissions)

此角色是只能指派為[裝置設定](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)中的其他本機系統管理員。 具有此角色的使用者，會在已加入 Azure Active Directory 的所有 Windows 10 裝置上，成為本機電腦系統管理員。 它們並沒有在 Azure Active Directory 中管理裝置物件的能力。

### <a name="directory-readers"></a>[目錄讀取器](#directory-readers-permissions)

此角色中的使用者可以讀取基本目錄資訊。 此角色應用於：
* 授與一組特定的來賓使用者讀取權限，而不是將其授與所有來賓使用者。
* 將一組特定的非系統管理員使用者存取權授與 Azure 入口網站在「僅限系統管理員存取 Azure AD 入口網站」設定為 [是] 時。
* 將目錄. Read 的存取權授與服務主體。 All 不是選項。

### <a name="directory-synchronization-accounts"></a>[目錄同步處理帳戶](#directory-synchronization-accounts-permissions)

請勿使用。 此角色會自動指派給 Azure AD Connect 服務，不適用於也不支援任何其他用途。

### <a name="directory-writers"></a>[目錄寫入器](#directory-writers-permissions)

這是舊版角色，用來指派給不支援[同意架構](../develop/quickstart-register-app.md)的應用程式。 不應將它指派給任何使用者。

### <a name="dynamics-365-administrator--crm-administrator"></a>[Dynamics 365 管理員/CRM 管理員](#crm-service-administrator-permissions)

此角色的使用者具有 Microsoft Dynamics 365 Online (如其存在) 的全域權限，並能管理支援票證及監視服務的健康情況。 如需詳細資訊，請參閱[使用服務管理員角色管理您的租用戶](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，會將此角色識別為「Dynamics 365 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com)中則是「Dynamics 365 管理員」。

### <a name="exchange-administrator"></a>[Exchange 系統管理員](#exchange-service-administrator-permissions)

此角色的使用者具有 Microsoft Exchange Online (如其存在) 的全域權限。 此外，也具備建立和管理所有「Office 365 群組」、管理支援票證，以及監視服務健康情況的能力。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，會將此角色識別為「Exchange 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com)中則是「Exchange 管理員」。 這是[exchange 系統管理中心](https://go.microsoft.com/fwlink/p/?LinkID=529144)內的「exchange Online 系統管理員」。

### <a name="external-identity-provider-administrator"></a>[外部識別提供者系統管理員](#external-identity-provider-administrator-permissions)

此系統管理員會管理 Azure Active Directory 租使用者與外部身分識別提供者之間的同盟。 使用此角色，使用者可以加入新的身分識別提供者，並設定所有可用的設定（例如，驗證路徑、服務識別碼、指派的金鑰容器）。 此使用者可以讓租使用者信任來自外部識別提供者的驗證。 對終端使用者體驗產生的影響取決於租使用者的類型：

* Azure Active Directory 員工和合作夥伴的租使用者：新增同盟（例如使用 Gmail）會立即影響尚未兌換的所有來賓邀請。 請參閱[將 Google 新增為 B2B 來賓使用者的身分識別提供者](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)。
* Azure Active Directory B2C 租使用者：新增同盟（例如，使用 Facebook 或與另一個 Azure AD 組織）不會立即影響使用者流程，直到將身分識別提供者新增為使用者流程中的選項（也稱為內建）原則）。 如需範例，請參閱設定[Microsoft 帳戶做為身分識別提供者](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app)。 若要變更使用者流程，需要「B2C 使用者流程管理員」的有限角色。

### <a name="global-administrator--company-administrator"></a>[全域管理員/公司系統管理員](#company-administrator-permissions)

具有此角色的使用者可以存取 Azure Active Directory 中所有的系統管理功能，以及使用 Azure Active Directory 身分識別的服務，例如 Microsoft 365 資訊安全中心、Microsoft 365 合規性中心、Exchange Online、SharePoint Online 和商務用 Skype Online。 註冊 Azure Active Directory 租用戶的人員會變成全域管理員。 您的公司可以有多位全域管理員。 全域系統管理員可以為任何使用者和所有其他系統管理員重設密碼。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，會將此角色識別為「公司系統管理員」。 它是 [Azure 入口網站](https://portal.azure.com)中的「全域管理員」。
>
>

### <a name="global-reader"></a>[全域讀者](#global-reader-permissions)

此角色中的使用者可以跨 Microsoft 365 服務讀取設定和系統管理資訊，但無法採取管理動作。 全域讀取器是全域管理員的唯讀對應。 指派全域讀取者，而不是全域管理員進行規劃、審核或調查。 將全域讀取器與其他有限的系統管理員角色（例如 Exchange 系統管理員）搭配使用，可讓您在沒有指派全域系統管理員角色的情況下，更輕鬆地完成工作。 全域讀取者可與 Microsoft 365 系統管理中心、Exchange 系統管理中心、小組系統管理中心、安全中心、合規性中心、Azure AD 系統管理中心和裝置管理系統管理中心搭配運作。

> [!NOTE]
> 全域讀取者角色現在有幾個限制：
>
>- SharePoint 系統管理中心-SharePoint 系統管理中心不支援全域讀取者角色。 在左窗格中，您不會在[Microsoft 365 系統管理中心](https://admin.microsoft.com/Adminportal/Home#/homepage)的 [系統管理中心] 下看到 [SharePoint]。
>- [Onedrive 系統管理中心](https://admin.onedrive.com/)-onedrive 系統管理中心不支援全域讀取者角色。
>- [Azure AD 入口網站](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)-全域讀取器無法讀取企業應用程式的布建模式。
>- [M365 系統管理中心](https://admin.microsoft.com/Adminportal/Home#/homepage)-全域讀取者無法讀取客戶加密箱要求。 您在 M365 系統管理中心的左窗格中，找不到 [**支援**] 下的 [**客戶加密箱要求**] 索引標籤。
>- [M365 安全性中心](https://security.microsoft.com/homepage)-全域讀取者無法讀取敏感度和保留標籤。 您在 M365 的 [安全性中心] 的左窗格中，找不到 [**敏感度卷**標]、[**保留標籤**] 和 [**標籤分析**]
>- [Office 安全性 & 合規性中心](https://sip.protection.office.com/homepage)-全域讀取器無法讀取 SCC audit 記錄檔、執行內容搜尋，或查看安全分數。
>- [小組系統管理中心](https://admin.teams.microsoft.com)-全域讀者無法讀取**小組生命週期**、**分析 & 報告**、 **IP 電話裝置管理**和**應用程式類別目錄**。
>- [特殊許可權[存取管理（PAM）](https://docs.microsoft.com/office365/securitycompliance/privileged-access-management-overview) ] 不支援 [全域讀取者] 角色。
>- [Azure 資訊保護](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)-全域讀取器僅支援[集中報告](https://docs.microsoft.com/azure/information-protection/reports-aip)，而當您的 Azure AD 組織不在[統一標籤平臺](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)上時。
>
> 這些功能目前正在開發中。
>

### <a name="groups-administrator"></a>[群組管理員](#groups-administrator-permissions)

此角色中的使用者可以建立/管理群組及其設定（例如命名和到期原則）。 請務必瞭解，將使用者指派給這個角色，讓他們能夠在除了 Outlook 以外的各種工作負載（例如小組、SharePoint、Yammer）上管理租使用者中的所有群組。 此外，使用者也可以在各種不同的系統管理入口網站（例如 Microsoft 系統管理中心、Azure 入口網站，以及小組和 SharePoint 系統管理中心之類的工作負載）中管理各種群組設定。

### <a name="guest-inviter"></a>[來賓邀請者](#guest-inviter-permissions)

當 [成員可邀請] 使用者設定為 [否] 時，此角色中的使用者可以管理 Azure Active Directory B2B 來賓使用者的邀請 在[關於 Azure AD B2B 共同作業](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)中查看 B2B 共同作業的詳細資訊。 這不包含任何其他權限。

### <a name="helpdesk-administrator"></a>[技術服務管理員](#helpdesk-administrator-permissions)

具備此角色的使用者可以變更密碼、讓重新整理權杖失效、管理服務要求，以及監視服務健康情況。 讓重新整理權杖失效會強制使用者重新登入。 技術服務管理員可以重設密碼，並使非系統管理員的其他使用者重新整理權杖失效，或只指派下列角色：

* 目錄讀取器
* 來賓邀請者
* 服務台系統管理員
* 訊息中心讀取者
* 報告讀者

> [!IMPORTANT]
> 具備此角色的使用者可以變更可存取機密或私人資訊或 Azure Active Directory 內外重要組態的人員密碼。 變更使用者的密碼表示可承擔該使用者身分識別和權限。 例如：
>
>- 應用程式註冊和企業應用程式擁有者，他們可以管理他們自己的應用程式認證。 這些應用程式在 Azure AD 中可能有特殊權限，而在其他地方未授與技術支援中心系統管理員。 技術支援中心系統管理員可以透過此路徑承擔應用程式擁有者的身分識別，然後藉由更新應用程式的認證，進一步承擔特殊權限應用程式的身分識別。
>- Azure 訂用帳戶擁有者，他們可以存取機密或私人資訊，或 Azure 中的重要設定。
>- 安全性群組和 Office 365 群組擁有者，他們可以管理群組成員資格。 這個群組可以存取機密或私人資訊或者 Azure AD 和其他位置中的重要組態。
>- Azure AD 外部其他服務 (例如，Exchange Online、Office 安全性與合規性中心和人力資源系統) 中的系統管理員。
>- 非系統管理員，例如主管、法律顧問和人力資源員工，他們可以存取機密或私人資訊。

將系統管理許可權委派給使用者子集，並將原則套用到使用者子集，可以使用[管理單位（現在處於公開預覽狀態）](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units)。

此角色先前在[Azure 入口網站](https://portal.azure.com/)中稱為「密碼管理員」。 Azure AD 中的「技術服務管理員」名稱現在會符合其在 Azure AD PowerShell 和 Microsoft Graph API 中的名稱。

### <a name="intune-administrator"></a>[Intune 系統管理員](#intune-service-administrator-permissions)

此角色的使用者具有 Microsoft Intune Online (如其存在) 的全域權限。 此外，此角色包含管理使用者和裝置的能力，可相關聯原則以及建立和管理群組。 以角色為[基礎的系統管理控制（RBAC）與 Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)的詳細資訊。

此角色可以建立和管理所有安全性群組。 不過，Intune 系統管理員沒有 Office 群組的系統管理許可權。 這表示系統管理員無法更新租使用者中所有 Office 群組的擁有者或成員資格。 不過，他/她可以管理他所建立的 Office 群組，這是他/她的使用者權限之一部分。 因此，他/她所建立的任何 Office 群組（而不是安全性群組），都應該根據其250的配額來計算。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，會將此角色識別為「Intune 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com)中則是「Intune 管理員」。

### <a name="kaizala-administrator"></a>[Kaizala 系統管理員](#kaizala-administrator-permissions)

具有此角色的使用者具有全域許可權，可管理 Microsoft Kaizala 中的設定（當服務存在時），以及管理支援票證和監控服務健康情況的能力。 此外，使用者也可以存取與採用 & 使用 Kaizala by 組織成員和使用 Kaizala 動作產生的商務報表相關的報表。

### <a name="license-administrator"></a>[授權管理員](#license-administrator-permissions)

此角色中的使用者可新增、移除和更新使用者和群組的授權指派 (使用群組型授權)，以及管理使用者的使用位置。 此角色不會授與購買或管理訂用帳戶、建立或管理群組，或在使用位置以外建立或管理使用者的能力。 這個角色沒有檢視、建立或管理支援票證的存取權。

### <a name="message-center-privacy-reader"></a>[訊息中心隱私權讀者](#message-center-privacy-reader-permissions)

此角色中的使用者可以監視訊息中心內的所有通知，包括資料隱私權訊息。 訊息中心隱私權讀者會收到電子郵件通知，包括資料隱私權的相關資訊，並可使用訊息中心喜好設定取消訂閱。 只有全域管理員和訊息中心隱私權讀取者可以讀取資料隱私權訊息。 此外，此角色包含可供您查看群組、網域和訂閱的功能。 此角色沒有任何許可權可查看、建立或管理服務要求。

### <a name="message-center-reader"></a>[訊息中心讀者](#message-center-reader-permissions)

此角色中的使用者可以在 [Office 365 訊息中心](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)內，為他們的組織監視所設服務 (例如 Exchange、Intune 和 Microsoft Teams) 的通知和諮詢健康情況更新。 訊息中心讀者每週會收到貼文的電子郵件摘要和更新，並且可以在 Office 365 中分享訊息中心的貼文。 在 Azure AD 中，指派至此角色的使用者只會有 Azure AD 服務的唯讀存取權，與使用者和群組一樣。 這個角色沒有檢視、建立或管理支援票證的存取權。

### <a name="office-apps-administrator"></a>[Office 應用程式系統管理員](#office-apps-administrator-permissions)

此角色中的使用者可以管理 Office 365 應用程式的雲端設定。 這包括管理雲端原則、自助式下載管理，以及可查看 Office 應用程式相關報表的功能。 此角色還會授與管理支援票證的能力，以及監視主要系統管理中心內的服務健康情況。 指派給此角色的使用者也可以管理 Office 應用程式中新功能的通訊。 

### <a name="partner-tier1-support"></a>[合作夥伴第1層支援](#partner-tier1-support-permissions)

請勿使用。 此角色已被取代，而且未來將從 Azure AD 中移除。 此角色僅供少數 Microsoft 轉售合作夥伴使用，不適用於一般用途。

### <a name="partner-tier2-support"></a>[合作夥伴第2層支援](#partner-tier2-support-permissions)

請勿使用。 此角色已被取代，而且未來將從 Azure AD 中移除。 此角色僅供少數 Microsoft 轉售合作夥伴使用，不適用於一般用途。

### <a name="password-administrator"></a>[密碼管理員](#password-administrator-permissions)

具有此角色的使用者管理密碼的能力有限。 此角色不會授與管理服務要求或監視服務健全狀況的能力。 密碼管理員可以重設非系統管理員或下列角色成員的其他使用者的密碼：

* 目錄讀取器
* 來賓邀請者
* 密碼管理員

### <a name="power-bi-administrator"></a>[Power BI 系統管理員](#power-bi-service-administrator-permissions)

此角色的使用者具有 Microsoft Power BI (如其存在) 的全域權限，並能管理支援票證及監視服務的健康情況。 如需詳細資訊，請參閱[了解 Power BI 管理員角色](https://docs.microsoft.com/power-bi/service-admin-role)。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，會將此角色識別為「Power BI 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com)中則是「Power BI 管理員」。

### <a name="power-platform-administrator"></a>[Power 平臺系統管理員](#power-platform-administrator-permissions)

此角色中的使用者可以建立及管理環境、PowerApps、流程、資料遺失防護原則的所有層面。 此外，具備此角色的使用者能夠管理支援票證及監視服務的健康情況。

### <a name="privileged-authentication-administrator"></a>[特殊許可權驗證管理員](#privileged-authentication-administrator-permissions)

具有此角色的使用者可以為所有使用者設定或重設非密碼認證，包括全域管理員，而且可以更新所有使用者的密碼。 特殊許可權驗證系統管理員可以強制使用者針對現有的非密碼認證（例如 MFA、FIDO）重新註冊，並撤銷「在裝置上記住 MFA」，在下次登入所有使用者時提示 MFA。

### <a name="privileged-role-administrator"></a>[特殊許可權角色管理員](#privileged-role-administrator-permissions)

具備此角色的使用者可以管理 Azure Active Directory 中，以及 Azure AD Privileged Identity Management 內的角色指派。 此外，此角色可讓您管理 Privileged Identity Management 和管理單位的所有層面。

> [!IMPORTANT]
> 此角色會授與管理所有 Azure AD 角色指派的能力，包括全域管理員角色。 此角色不包含 Azure AD 中的任何其他特殊權限能力，例如建立或更新使用者。 不過，指派給這個角色的使用者可以藉由指派額外的角色，來授與自己或其他人額外權限。

### <a name="reports-reader"></a>[報告讀取者](#reports-reader-permissions)

具有此角色的使用者可以在 Microsoft 365 系統管理中心和 Power BI 中的採用內容套件中，查看使用方式報告資料和報告儀表板。 此外，此角色還可讓使用者存取 Azure AD 中的登入報告與活動，以及 Microsoft Graph 報告 API 所傳回的資料。 獲指派「報告讀者」角色的使用者只能存取相關的使用情況和採用計量。 他們並不具備任何系統管理權限，因此無法進行設定或存取產品特定的系統管理中心 (例如 Exchange)。 這個角色沒有檢視、建立或管理支援票證的存取權。

### <a name="search-administrator"></a>[搜尋系統管理員](#search-administrator-permissions)

此角色中的使用者具有 Microsoft 365 系統管理中心內所有 Microsoft 搜尋管理功能的完整存取權。 搜尋系統管理員可以將 [搜尋管理員] 和 [搜尋編輯器] 角色委派給使用者，以及建立和管理內容，例如書簽、Q & As 和位置。 此外，這些使用者可以查看訊息中心、監視服務健全狀況，以及建立服務要求。

### <a name="search-editor"></a>[搜尋編輯器](#search-editor-permissions)

此角色中的使用者可以在 Microsoft 365 系統管理中心內建立、管理及刪除 Microsoft Search 的內容，包括書簽、Q & As 和位置。

### <a name="security-administrator"></a>[安全性系統管理員](#security-administrator-permissions)

具備此角色的使用者有權限管理 Microsoft 365 資訊安全中心、Azure Active Directory Identity Protection、Azure 資訊保護和 Office 365 安全性與合規性中心中的安全性相關功能。 關於 Office 365 權限的詳細資訊可在 [Office 365 安全性與法規遵循中心的權限](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)中取得。

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

### <a name="security-operator"></a>[安全性運算子](#security-operator-permissions)

具有此角色的使用者可以管理警示，並具有安全性相關功能的全域唯讀存取權，包括 Microsoft 365 資訊安全中心、Azure Active Directory、身分識別保護、Privileged Identity Management 和 Office 365 中的所有資訊安全性 & 合規性中心。 關於 Office 365 權限的詳細資訊可在 [Office 365 安全性與法規遵循中心的權限](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center)中取得。

在 | 可以執行
--- | ---
[Microsoft 365 資訊安全中心](https://protection.office.com) | 「安全性讀取者」角色的所有權限<br>查看、調查及回應安全性威脅警示
身分識別防護中心 | 「安全性讀取者」角色的所有權限<br>此外，還能夠執行除了重設密碼以外的所有身分識別防護中心作業
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | 「安全性讀取者」角色的所有權限
[Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 「安全性讀取者」角色的所有權限<br>查看、調查及回應安全性警示
Windows Defender ATP 和 EDR | 「安全性讀取者」角色的所有權限<br>查看、調查及回應安全性警示
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 「安全性讀取者」角色的所有權限
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 「安全性讀取者」角色的所有權限
[Office 365 服務健康情況](https://docs.microsoft.com/office365/enterprise/view-service-health) | 檢視 Office 365 服務的健康情況

### <a name="security-reader"></a>[安全性讀取者](#security-reader-permissions)

具備此角色的使用者具有安全性相關功能的全域唯讀存取權 (含 Microsoft 365 資訊安全中心、Azure Active Directory、Identity Protection、Privileged Identity Management 中的所有資訊)，並能讀取 Azure Active Directory 登入報告與稽核記錄，且具有 Office 365 安全性與合規性中心的全域唯讀存取權。 關於 Office 365 權限的詳細資訊可在 [Office 365 安全性與法規遵循中心的權限](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)中取得。

在 | 可以執行
--- | ---
[Microsoft 365 資訊安全中心](https://protection.office.com) | 檢視所有 Microsoft 365 服務的安全性相關原則<br>檢視安全性威脅和警示<br>檢視報表
身分識別防護中心 | 讀取安全性功能的所有安全性報告和設定資訊<br><ul><li>反垃圾郵件<li>加密<li>資料外洩防護<li>反惡意程式碼<li>進階威脅防護<li>防網路釣魚<li>郵件流程規則
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | 具有 Azure AD Privileged Identity Management 中呈現的所有資訊的唯讀存取權： Azure AD 角色指派和安全性評論的原則和報告。<br>**無法**註冊 Azure AD Privileged Identity Management 或對它進行任何變更。 在 Privileged Identity Management 入口網站或透過 PowerShell，此角色中的人員可以啟用其他角色（例如，全域管理員或特殊許可權角色管理員）（如果使用者有資格）。
[Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 檢視安全性原則<br>檢視及調查安全性威脅<br>檢視報表
Windows Defender ATP 和 EDR | 查看和調查警示。 當您在 Windows Defender ATP 中開啟角色型存取控制時，具有唯讀許可權（例如 Azure AD 安全性讀取者角色）的使用者會失去存取權，直到指派給 Windows Defender ATP 角色為止。
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | 檢視使用者、裝置、註冊、設定及應用程式資訊。 無法對 Intune 進行變更。
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 具有唯讀權限，並可管理警示
[Azure 資訊安全中心](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | 可檢視建議和警示、檢視安全性原則、檢視安全性狀態，但無法進行變更
[Office 365 服務健康情況](https://docs.microsoft.com/office365/enterprise/view-service-health) | 檢視 Office 365 服務的健康情況

### <a name="service-support-administrator"></a>[服務支援系統管理員](#service-support-administrator-permissions)

具有此角色的使用者可以開啟 Microsoft for Azure 和 Office 365 服務的支援要求，並在 [ [Azure 入口網站](https://portal.azure.com)] 和 [ [Microsoft 365 系統管理中心](https://admin.microsoft.com)] 中查看服務儀表板和訊息中心。 [有關系統管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)的詳細資訊。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，會將此角色識別為「服務支援系統管理員」。 這是[Azure 入口網站](https://portal.azure.com)、 [Microsoft 365 系統管理中心](https://admin.microsoft.com)和 Intune 入口網站中的「服務管理員」。

### <a name="sharepoint-administrator"></a>[SharePoint 系統管理員](#sharepoint-service-administrator-permissions)

具備此角色的使用者在有 Microsoft SharePoint Online 服務時，於該服務內具有全域權限，以及建立和管理所有 Office 365 群組、管理支援票證和監控服務健康情況的能力。 [有關系統管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)的詳細資訊。

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，會將此角色識別為「SharePoint 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com)中則是「SharePoint 管理員」。

### <a name="skype-for-business--lync-administrator"></a>[商務用 Skype/Lync 管理員](#lync-service-administrator-permissions)

在有 Microsoft 商務用 Skype 服務時，具備此角色的使用者在該服務內會具有全域權限，以及在 Azure Active Directory 中管理 Skype 特定的使用者屬性。 此外，此角色會授與管理支援票證及監視服務健康情況的能力，以及存取 Microsoft Teams 和商務用 Skype 系統管理中心的能力。 此帳戶也必須獲得 Microsoft Teams 授權，否則就無法執行 Microsoft Teams PowerShell Cmdlet。 如需詳細資訊，請參閱[關於商務用 Skype 系統管理員角色](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5)，如需 Microsoft Teams 的授權資訊，請參閱[商務用 Skype 和 Microsoft Teams 附加授權](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> 在 Microsoft Graph API 和 Azure AD PowerShell 中，會將此角色識別為「Lync 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com/)中則是「商務用 Skype 管理員」。

### <a name="teams-communications-administrator"></a>[小組通訊管理員](#teams-communications-administrator-permissions)

此角色的使用者可以管理 Microsoft Teams 在語音和電話語音相關工作負載的各個層面。 這包括電話號碼指派管理工具、語音和會議原則，以及呼叫分析工具組的完整存取權。

### <a name="teams-communications-support-engineer"></a>[小組通訊支援工程師](#teams-communications-support-engineer-permissions)

此角色的使用者可以使用 Microsoft Teams 和商務用 Skype 系統管理中心內的使用者呼叫疑難排解工具，針對 Microsoft Teams 和商務用 Skype 內的通訊問題進行疑難排解。 此角色的使用者可以檢視所有相關參與者的完整呼叫記錄資訊。 這個角色沒有檢視、建立或管理支援票證的存取權。

### <a name="teams-communications-support-specialist"></a>[小組通訊支援專家](#teams-communications-support-specialist-permissions)

此角色的使用者可以使用 Microsoft Teams 和商務用 Skype 系統管理中心內的使用者呼叫疑難排解工具，針對 Microsoft Teams 和商務用 Skype 內的通訊問題進行疑難排解。 此角色的使用者只能檢視其所查閱特定使用者的呼叫中所含有的使用者詳細資料。 這個角色沒有檢視、建立或管理支援票證的存取權。

### <a name="teams-service-administrator"></a>[小組服務管理員](#teams-service-administrator-permissions)

此角色的使用者可以透過 Microsoft Teams 和商務用 Skype 系統管理中心以及個別的 PowerShell 模組，管理 Microsoft Teams 工作負載的所有層面。 這包括所有與電話語音、傳訊、會議和小組本身相關的管理工具以及其他領域。 這個角色會額外獲得授與建立和管理所有 Office 365 群組、管理支援票證，以及監視服務健康情況的能力。

### <a name="user-administrator"></a>[使用者管理員](#user-administrator-permissions)

具有此角色的使用者可以建立使用者，以及管理具有一些限制的使用者所有層面（如下所示），並可更新密碼到期原則。 此外，具有此角色的使用者可以建立與管理所有群組。 此角色也包含建立和管理使用者檢視、管理支援票證，以及監視服務健康情況的能力。 使用者系統管理員沒有許可權，無法管理大部分管理員角色中使用者的某些使用者屬性。 具有此角色的使用者沒有可以可管理 MFA。 下表列出此限制的例外狀況角色。

| | |
| --- | --- |
|一般權限|<p>建立 [使用者和群組]</p><p>建立和管理使用者檢視</p><p>建立 Office 支援票證<p>更新密碼到期原則|
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

### <a name="application-administrator-permissions"></a>應用程式系統管理員許可權

能夠建立及管理應用程式註冊與企業應用程式的所有層面。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/應用程式/appProxyAuthentication/更新 | 更新 Azure Active Directory 中服務主體的應用程式 Proxy 驗證屬性。 |
| microsoft 目錄/應用程式/appProxyUrlSettings/更新 | 更新 Azure Active Directory 中的應用程式 proxy 內部和外部 URL。 |
| microsoft 目錄/應用程式/applicationProxy/讀取 | 讀取所有應用程式 Proxy 屬性。 |
| microsoft 目錄/應用程式/applicationProxy/更新 | 更新所有應用程式 Proxy 屬性。 |
| microsoft 目錄/應用程式/物件/更新 | 在 Azure Active Directory 中更新 applications.audience 屬性。 |
| microsoft 目錄/應用程式/驗證/更新 | 在 Azure Active Directory 中更新 applications.authentication 屬性。 |
| microsoft 目錄/應用程式/基本/更新 | 更新 Azure Active Directory 中 Applications 的基本屬性。 |
| microsoft 目錄/應用程式/建立 | 在 Azure Active Directory 中建立應用程式。 |
| microsoft 目錄/應用程式/認證/更新 | 在 Azure Active Directory 中更新 applications.credentials 屬性。 |
| microsoft 目錄/應用程式/刪除 | 刪除 Azure Active Directory 中的應用程式。 |
| microsoft 目錄/應用程式/擁有者/更新 | 更新 Azure Active Directory 中的 applications.owners 屬性。 |
| microsoft 目錄/應用程式/許可權/更新 | 在 Azure Active Directory 中更新 applications.permissions 屬性。 |
| microsoft 目錄/應用程式/原則/更新 | 更新 Azure Active Directory 中的 applications.policies 屬性。 |
| microsoft 目錄/appRoleAssignments/建立 | 在 Azure Active Directory 中建立 appRoleAssignments。 |
| microsoft 目錄/appRoleAssignments/read | 讀取 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft 目錄/appRoleAssignments/更新 | 更新在 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft 目錄/appRoleAssignments/delete | 刪除 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft 目錄/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft 目錄/connectorGroups/所有內容/讀取 | 讀取 Azure Active Directory 中的應用程式 proxy 連接器群組屬性。 |
| microsoft 目錄/connectorGroups/所有專案/更新 | 更新 Azure Active Directory 中的所有應用程式 proxy 連接器群組屬性。 |
| microsoft 目錄/connectorGroups/建立 | 在 Azure Active Directory 中建立應用程式 proxy 連接器群組。 |
| microsoft 目錄/connectorGroups/delete | 在 Azure Active Directory 中刪除應用程式 proxy 連接器群組。 |
| microsoft 目錄/連接器/所有內容/讀取 | 讀取 Azure Active Directory 中的所有應用程式 proxy 連接器屬性。 |
| microsoft 目錄/連接器/建立 | 在 Azure Active Directory 中建立應用程式 proxy 連接器。 |
| microsoft 目錄/原則/Policies.applicationconfiguration/基本/讀取 | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft 目錄/原則/Policies.applicationconfiguration/基本/更新 | 更新 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft 目錄/原則/Policies.applicationconfiguration/create | 在 Azure Active Directory 中建立原則。 |
| microsoft 目錄/原則/Policies.applicationconfiguration/delete | 刪除 Azure Active Directory 中的原則。 |
| microsoft 目錄/原則/Policies.applicationconfiguration/擁有者/讀取 | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft 目錄/原則/Policies.applicationconfiguration/擁有者/更新 | 更新 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft 目錄/原則/Policies.applicationconfiguration/policyAppliedTo/read | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft directory/servicePrincipals/Serviceprincipals.approleassignedto/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| microsoft 目錄/servicePrincipals/物件/更新 | 更新 Azure Active Directory 中的 servicePrincipals.audience 屬性。 |
| microsoft 目錄/servicePrincipals/驗證/更新 | 更新 Azure Active Directory 中的 servicePrincipals.authentication 屬性。 |
| microsoft 目錄/servicePrincipals/基本/更新 | 更新 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft 目錄/servicePrincipals/建立 | 在 Azure Active Directory 中建立 servicePrincipals。 |
| microsoft 目錄/servicePrincipals/認證/更新 | 更新 Azure Active Directory 中的 servicePrincipals.credentials 屬性。 |
| microsoft 目錄/servicePrincipals/delete | 刪除 Azure Active Directory 中的 servicePrincipals。 |
| microsoft 目錄/servicePrincipals/擁有者/更新 | 更新 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft 目錄/servicePrincipals/許可權/更新 | 更新 Azure Active Directory 中的 servicePrincipals.permissions 屬性。 |
| microsoft 目錄/servicePrincipals/原則/更新 | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft 目錄/Signinreports 所包含/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="application-developer-permissions"></a>應用程式開發人員許可權

可建立與 [使用者可註冊應用程式] 設定不相關的應用程式註冊。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/應用程式/createAsOwner | 在 Azure Active Directory 中建立應用程式。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft 目錄/appRoleAssignments/createAsOwner | 在 Azure Active Directory 中建立 appRoleAssignments。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft 目錄/oAuth2PermissionGrants/createAsOwner | 在 Azure Active Directory 中建立 oAuth2PermissionGrants。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft 目錄/servicePrincipals/createAsOwner | 在 Azure Active Directory 中建立 servicePrincipals。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |

### <a name="authentication-administrator-permissions"></a>驗證系統管理員許可權

可存取以檢視、設定及重設所有非管理員使用者的驗證方法資訊。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/使用者/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft 目錄/使用者/Users.strongauthentication/更新 | 更新 MFA 認證資訊等增強式驗證屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft 目錄/使用者/密碼/更新 | 更新 Office 365 組織中所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |

### <a name="azure-devops-administrator-permissions"></a>Azure DevOps 系統管理員許可權

可以管理 Azure DevOps 的組織原則和設定。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱上述[角色說明](#azure-devops-administrator)。
>
>

| **動作** | **說明** |
| --- | --- |
| devOps/allEntities/allTasks | 讀取和設定 Azure DevOps。 |

### <a name="azure-information-protection-administrator-permissions"></a>Azure 資訊保護系統管理員許可權

可以管理 Azure 資訊保護服務的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱上述[角色說明](#)。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 資訊保護的所有層面。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="b2c-user-flow-administrator-permissions"></a>B2C 使用者流程系統管理員許可權

建立和管理使用者流程的所有層面。

| **動作** | **說明** |
| --- | --- |
| microsoft. aad. b2c/userFlows/allTasks | 在 Azure Active Directory B2C 中讀取和設定使用者流程。 |

### <a name="b2c-user-flow-attribute-administrator-permissions"></a>B2C 使用者流程屬性系統管理員許可權

建立及管理所有使用者流程可用的屬性架構。

| **動作** | **說明** |
| --- | --- |
| microsoft. aad. b2c/userAttributes/allTasks | 在 Azure Active Directory B2C 中讀取和設定使用者屬性。 |

### <a name="b2c-ief-keyset-administrator-permissions"></a>B2C IEF 金鑰組系統管理員許可權

在 Identity Experience Framework 中管理同盟和加密的秘密。

| **動作** | **說明** |
| --- | --- |
| trustFramework/索引鍵集/allTasks | 在 Azure Active Directory B2C 中讀取和設定金鑰集。 |

### <a name="b2c-ief-policy-administrator-permissions"></a>B2C IEF 原則系統管理員許可權

在 Identity Experience Framework 中建立和管理信任架構原則。

| **動作** | **說明** |
| --- | --- |
| trustFramework/原則/allTasks | 在 Azure Active Directory B2C 中讀取和設定自訂原則。 |

### <a name="billing-administrator-permissions"></a>計費管理員許可權

能夠執行一般計費相關工作，例如更新付款資訊。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/組織/基本/更新 | 更新 Azure Active Directory 中 organization 的基本屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.commerce.billing/allEntities/allTasks | 管理 Office 365 帳單的所有層面。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="cloud-application-administrator-permissions"></a>雲端應用程式系統管理員許可權

能夠建立及管理應用程式註冊與企業應用程式的所有層面，但應用程式 Proxy 除外。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/應用程式/物件/更新 | 在 Azure Active Directory 中更新 applications.audience 屬性。 |
| microsoft 目錄/應用程式/驗證/更新 | 在 Azure Active Directory 中更新 applications.authentication 屬性。 |
| microsoft 目錄/應用程式/基本/更新 | 更新 Azure Active Directory 中 Applications 的基本屬性。 |
| microsoft 目錄/應用程式/建立 | 在 Azure Active Directory 中建立應用程式。 |
| microsoft 目錄/應用程式/認證/更新 | 在 Azure Active Directory 中更新 applications.credentials 屬性。 |
| microsoft 目錄/應用程式/刪除 | 刪除 Azure Active Directory 中的應用程式。 |
| microsoft 目錄/應用程式/擁有者/更新 | 更新 Azure Active Directory 中的 applications.owners 屬性。 |
| microsoft 目錄/應用程式/許可權/更新 | 在 Azure Active Directory 中更新 applications.permissions 屬性。 |
| microsoft 目錄/應用程式/原則/更新 | 更新 Azure Active Directory 中的 applications.policies 屬性。 |
| microsoft 目錄/appRoleAssignments/建立 | 在 Azure Active Directory 中建立 appRoleAssignments。 |
| microsoft 目錄/appRoleAssignments/更新 | 更新在 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft 目錄/appRoleAssignments/delete | 刪除 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft 目錄/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft 目錄/原則/Policies.applicationconfiguration/create | 在 Azure Active Directory 中建立原則。 |
| microsoft 目錄/原則/Policies.applicationconfiguration/基本/讀取 | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft 目錄/原則/Policies.applicationconfiguration/基本/更新 | 更新 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft 目錄/原則/Policies.applicationconfiguration/delete | 刪除 Azure Active Directory 中的原則。 |
| microsoft 目錄/原則/Policies.applicationconfiguration/擁有者/讀取 | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft 目錄/原則/Policies.applicationconfiguration/擁有者/更新 | 更新 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft 目錄/原則/Policies.applicationconfiguration/policyAppliedTo/read | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft directory/servicePrincipals/Serviceprincipals.approleassignedto/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| microsoft 目錄/servicePrincipals/物件/更新 | 更新 Azure Active Directory 中的 servicePrincipals.audience 屬性。 |
| microsoft 目錄/servicePrincipals/驗證/更新 | 更新 Azure Active Directory 中的 servicePrincipals.authentication 屬性。 |
| microsoft 目錄/servicePrincipals/基本/更新 | 更新 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft 目錄/servicePrincipals/建立 | 在 Azure Active Directory 中建立 servicePrincipals。 |
| microsoft 目錄/servicePrincipals/認證/更新 | 更新 Azure Active Directory 中的 servicePrincipals.credentials 屬性。 |
| microsoft 目錄/servicePrincipals/delete | 刪除 Azure Active Directory 中的 servicePrincipals。 |
| microsoft 目錄/servicePrincipals/擁有者/更新 | 更新 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft 目錄/servicePrincipals/許可權/更新 | 更新 Azure Active Directory 中的 servicePrincipals.permissions 屬性。 |
| microsoft 目錄/servicePrincipals/原則/更新 | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft 目錄/Signinreports 所包含/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="cloud-device-administrator-permissions"></a>雲端裝置系統管理員許可權

在 Azure AD 中管理裝置的完整存取。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft 目錄/裝置/Devices.bitlockerrecoverykeys/讀取 | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft 目錄/裝置/刪除 | 刪除 Azure Active Directory 中的 devices。 |
| microsoft 目錄/裝置/停用 | 停用 Azure Active Directory 中的 devices。 |
| microsoft 目錄/裝置/啟用 | 啟用 Azure Active Directory 中的裝置。 |
| microsoft 目錄/Signinreports 所包含/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |

### <a name="company-administrator-permissions"></a>公司系統管理員許可權

可管理使用 Azure AD 身分識別的 Azure AD 與 Microsoft 服務的所有層面。 這個角色也稱為全域管理員角色。 

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | 建立和刪除所有資源，同時讀取及更新 microsoft.aad.cloudAppSecurity 中的標準屬性。 |
| microsoft 目錄/administrativeUnits/allProperties/allTasks | 建立和刪除 administrativeUnits，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft 目錄/應用程式/allProperties/allTasks | 建立和刪除應用程式，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft 目錄/appRoleAssignments/allProperties/allTasks | 建立和刪除 appRoleAssignments，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft 目錄/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft 目錄/contacts/allProperties/allTasks | 建立和刪除合約，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft 目錄/合約/allProperties/allTasks | 建立和刪除合約，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft 目錄/devices/allProperties/allTasks | 建立和刪除裝置，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft 目錄/directoryRoles/allProperties/allTasks | 建立和刪除 directoryRoles，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft 目錄/directoryRoleTemplates/allProperties/allTasks | 建立和刪除 directoryRoleTemplates，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft 目錄/網域/allProperties/allTasks | 建立和刪除 domains，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft. directory/groups/allProperties/allTasks | 建立和刪除 groups，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft 目錄/groupSettings/allProperties/allTasks | 建立和刪除 groupSettings，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft 目錄/groupSettingTemplates/allProperties/allTasks | 建立和刪除 groupSettingTemplates，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft 目錄/loginTenantBranding/allProperties/allTasks | 建立和刪除 loginTenantBranding，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft 目錄/oAuth2PermissionGrants/allProperties/allTasks | 建立和刪除 oAuth2PermissionGrants，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft 目錄/組織/allProperties/allTasks | 建立和刪除 organization，同時讀取及更新 Azure Active Directory 中的所有屬性。 |
| microsoft 目錄/原則/allProperties/allTasks | 建立和刪除 policies，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft 目錄/roleAssignments/allProperties/allTasks | 建立與刪除 roleAssignments，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft 目錄/roleDefinitions/allProperties/allTasks | 建立與刪除 roleDefinitions，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft 目錄/scopedRoleMemberships/allProperties/allTasks | 建立和刪除 scopedRoleMemberships，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft 目錄/serviceAction/activateService | 可以在 Azure Active Directory 中執行 Activateservice 服務動作 |
| microsoft 目錄/serviceAction/disableDirectoryFeature | 可以在 Azure Active Directory 中執行 Disabledirectoryfeature 服務動作 |
| microsoft 目錄/serviceAction/enableDirectoryFeature | 可以在 Azure Active Directory 中執行 Enabledirectoryfeature 服務動作 |
| microsoft 目錄/serviceAction/getAvailableExtentionProperties | 可以在 Azure Active Directory 中執行 Getavailableextentionproperties 服務動作 |
| microsoft 目錄/servicePrincipals/allProperties/allTasks | 建立和刪除 servicePrincipals，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft 目錄/Signinreports 所包含/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft 目錄/subscribedSkus/allProperties/allTasks | 建立和刪除 subscribedSkus，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft directory/users/allProperties/allTasks | 建立和刪除 users，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| directorySync/allEntities/allTasks | 執行 Azure AD Connect 中的所有動作。 |
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

### <a name="compliance-administrator-permissions"></a>合規性系統管理員許可權

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

### <a name="compliance-data-administrator-permissions"></a>合規性資料管理員許可權

建立和管理合規性內容。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | 讀取和設定 Microsoft Cloud App Security。 |
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

### <a name="conditional-access-administrator-permissions"></a>條件式存取系統管理員許可權

可以管理條件式存取功能。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/原則/Policies.conditionalaccess/基本/讀取 | 讀取 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft 目錄/原則/Policies.conditionalaccess/基本/更新 | 更新 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft 目錄/原則/Policies.conditionalaccess/create | 在 Azure Active Directory 中建立原則。 |
| microsoft 目錄/原則/Policies.conditionalaccess/delete | 刪除 Azure Active Directory 中的原則。 |
| microsoft 目錄/原則/Policies.conditionalaccess/擁有者/讀取 | 讀取 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft 目錄/原則/Policies.conditionalaccess/擁有者/更新 | 更新 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft 目錄/原則/Policies.conditionalaccess/Policies.policiesappliedto/read | 讀取 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft 目錄/原則/Policies.conditionalaccess/Policies.tenantdefault/update | 更新 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |

### <a name="crm-service-administrator-permissions"></a>CRM 服務管理員許可權

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

### <a name="customer-lockbox-access-approver-permissions"></a>客戶加密箱存取核准者許可權

可核准 Microsoft 支援要求，以存取客戶組織的資料。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.lockbox/allEntities/allTasks | 管理 Office 365 客戶加密箱的所有層面 |

### <a name="desktop-analytics-administrator-permissions"></a>電腦分析系統管理員許可權

可以管理電腦分析和 Office 自訂 & 原則服務。 針對電腦分析，這包括能夠查看資產清查、建立部署計畫、查看部署和健康狀態。 若為 Office 自訂 & 原則服務，此角色可讓使用者管理 Office 原則。

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

### <a name="device-administrators-permissions"></a>裝置系統管理員許可權

指派給此角色的使用者會新增至已加入 Azure AD 的裝置上的本機系統管理員群組。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/groupSettings/基本/讀取 | 讀取 Azure Active Directory 中 groupSettings 的基本屬性。 |
| microsoft 目錄/groupSettingTemplates/基本/讀取 | 讀取 Azure Active Directory 中 groupSettingTemplates 的基本屬性。 |

### <a name="directory-readers-permissions"></a>目錄讀取者許可權
可讀取基本目錄資訊。 用來授與應用程式的存取權，不適用於使用者。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/administrativeUnits/基本/讀取 | 讀取 Azure Active Directory 中 administrativeUnits 的基本屬性。 |
| microsoft 目錄/administrativeUnits/成員/讀取 | 讀取 Azure Active Directory 中的 administrativeUnits.members 屬性。 |
| microsoft 目錄/應用程式/基本/讀取 | 讀取 Azure Active Directory 中 Applications 的基本屬性。 |
| microsoft 目錄/應用程式/擁有者/讀取 | 讀取 Azure Active Directory 中的 applications.owners 屬性。 |
| microsoft 目錄/應用程式/原則/讀取 | 讀取 Azure Active Directory 中的 applications.policies 屬性。 |
| microsoft 目錄/連絡人/基本/讀取 | 讀取 Azure Active Directory 中 contacts 的基本屬性。 |
| microsoft 目錄/連絡人/memberOf/read | 讀取 Azure Active Directory 中的 contacts.memberOf 屬性。 |
| microsoft 目錄/合約/基本/讀取 | 讀取 Azure Active Directory 中 contracts 的基本屬性。 |
| microsoft 目錄/裝置/基本/讀取 | 讀取 Azure Active Directory 中 devices 的基本屬性。 |
| microsoft 目錄/裝置/memberOf/read | 讀取 Azure Active Directory 中的 devices.memberOf 屬性。 |
| microsoft 目錄/裝置/Devices.registeredowners/讀取 | 讀取 Azure Active Directory 中的 devices.registeredOwners 屬性。 |
| microsoft 目錄/裝置/Devices.registeredusers/讀取 | 讀取 Azure Active Directory 中的 devices.registeredUsers 屬性。 |
| microsoft 目錄/directoryRoles/基本/讀取 | 讀取 Azure Active Directory 中 directoryRoles 的基本屬性。 |
| microsoft 目錄/directoryRoles/Directoryroles.eligiblemembers/read | 讀取 Azure Active Directory 中的 directoryRoles.eligibleMembers 屬性。 |
| microsoft 目錄/directoryRoles/成員/讀取 | 讀取 Azure Active Directory 中的 directoryRoles.members 屬性。 |
| microsoft 目錄/網域/基本/讀取 | 讀取 Azure Active Directory 中 domain 的基本屬性。 |
| microsoft 目錄/群組/appRoleAssignments/讀取 | 讀取 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| microsoft 目錄/群組/基本/讀取 | 讀取 Azure Active Directory 中 groups 的基本屬性。 |
| microsoft 目錄/群組/memberOf/read | 讀取 Azure Active Directory 中的 Read groups.memberOf 屬性。 |
| microsoft 目錄/群組/成員/讀取 | 讀取 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft。目錄/群組/擁有者/讀取 | 讀取 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft 目錄/群組/設定/讀取 | 讀取 Azure Active Directory 中的 groups.settings 屬性。 |
| microsoft 目錄/groupSettings/基本/讀取 | 讀取 Azure Active Directory 中 groupSettings 的基本屬性。 |
| microsoft 目錄/groupSettingTemplates/基本/讀取 | 讀取 Azure Active Directory 中 groupSettingTemplates 的基本屬性。 |
| microsoft 目錄/oAuth2PermissionGrants/基本/讀取 | 讀取 Azure Active Directory 中 oAuth2PermissionGrants 的基本屬性。 |
| microsoft 目錄/組織/基本/讀取 | 讀取 Azure Active Directory 中 organization 的基本屬性。 |
| microsoft 目錄/組織/trustedCAsForPasswordlessAuth/讀取 | 讀取 Azure Active Directory 中的 organization.trustedCAsForPasswordlessAuth 屬性。 |
| microsoft 目錄/roleAssignments/基本/讀取 | 讀取 Azure Active Directory 中 roleAssignments 的基本屬性。 |
| microsoft 目錄/roleDefinitions/基本/讀取 | 讀取 Azure Active Directory 中 roleDefinitions 的基本屬性。 |
| microsoft 目錄/servicePrincipals/Serviceprincipals.approleassignedto/read | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft 目錄/servicePrincipals/appRoleAssignments/read | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| microsoft 目錄/servicePrincipals/基本/讀取 | 讀取 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft 目錄/servicePrincipals/memberOf/read | 讀取 Azure Active Directory 中的 servicePrincipals.memberOf 屬性。 |
| microsoft 目錄/servicePrincipals/oAuth2PermissionGrants/基本/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 屬性。 |
| microsoft 目錄/servicePrincipals/ownedObjects/read | 讀取 Azure Active Directory 中的 servicePrincipals.ownedObjects 屬性。 |
| microsoft 目錄/servicePrincipals/擁有者/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft 目錄/servicePrincipals/原則/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft 目錄/subscribedSkus/基本/讀取 | 讀取 Azure Active Directory 中 subscribedSkus 的基本屬性。 |
| microsoft 目錄/使用者/appRoleAssignments/讀取 | 讀取 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft 目錄/使用者/基本/讀取 | 讀取 Azure Active Directory 中 users 的基本屬性。 |
| microsoft 目錄/使用者/directReports/讀取 | 讀取 Azure Active Directory 中的 users.directReports 屬性。 |
| microsoft 目錄/使用者/管理員/讀取 | 讀取 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft 目錄/使用者/memberOf/read | 讀取 Azure Active Directory 中的 users.memberOf 屬性。 |
| microsoft 目錄/使用者/oAuth2PermissionGrants/基本/讀取 | 讀取 Azure Active Directory 中的 users.oAuth2PermissionGrants 屬性。 |
| microsoft 目錄/使用者/Users.owneddevices/讀取 | 讀取 Azure Active Directory 中的 users.ownedDevices 屬性。 |
| microsoft 目錄/使用者/ownedObjects/讀取 | 讀取 Azure Active Directory 中的 users.ownedObjects 屬性。 |
| microsoft 目錄/使用者/registeredDevices/讀取 | 讀取 Azure Active Directory 中的 users.registeredDevices 屬性。 |

### <a name="directory-synchronization-accounts-permissions"></a>目錄同步作業帳戶許可權

僅供 Azure AD Connect 服務使用。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/組織/dirSync/更新 | 更新 Azure Active Directory 中的 organization.dirSync 屬性。 |
| microsoft 目錄/原則/建立 | 在 Azure Active Directory 中建立原則。 |
| microsoft 目錄/原則/刪除 | 刪除 Azure Active Directory 中的原則。 |
| microsoft 目錄/原則/基本/讀取 | 讀取 Azure Active Directory 中 policies 的基本屬性。 |
| microsoft 目錄/原則/基本/更新 | 更新 Azure Active Directory 中 policies 的基本屬性。 |
| microsoft 目錄/原則/擁有者/讀取 | 讀取 Azure Active Directory 中的 policies.owners 屬性。 |
| microsoft 目錄/原則/擁有者/更新 | 更新 Azure Active Directory 中的 policies.owners 屬性。 |
| microsoft 目錄/原則/Policies.policiesappliedto/read | 讀取 Azure Active Directory 中的 policies.policiesAppliedTo 屬性。 |
| microsoft 目錄/原則/Policies.tenantdefault/更新 | 更新 Azure Active Directory 中的 policies.tenantDefault 屬性。 |
| microsoft 目錄/servicePrincipals/Serviceprincipals.approleassignedto/read | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft directory/servicePrincipals/Serviceprincipals.approleassignedto/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft 目錄/servicePrincipals/appRoleAssignments/read | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| microsoft directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| microsoft 目錄/servicePrincipals/物件/更新 | 更新 Azure Active Directory 中的 servicePrincipals.audience 屬性。 |
| microsoft 目錄/servicePrincipals/驗證/更新 | 更新 Azure Active Directory 中的 servicePrincipals.authentication 屬性。 |
| microsoft 目錄/servicePrincipals/基本/讀取 | 讀取 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft 目錄/servicePrincipals/基本/更新 | 更新 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft 目錄/servicePrincipals/建立 | 在 Azure Active Directory 中建立 servicePrincipals。 |
| microsoft 目錄/servicePrincipals/認證/更新 | 更新 Azure Active Directory 中的 servicePrincipals.credentials 屬性。 |
| microsoft 目錄/servicePrincipals/memberOf/read | 讀取 Azure Active Directory 中的 servicePrincipals.memberOf 屬性。 |
| microsoft 目錄/servicePrincipals/oAuth2PermissionGrants/基本/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 屬性。 |
| microsoft 目錄/servicePrincipals/擁有者/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft 目錄/servicePrincipals/擁有者/更新 | 更新 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft 目錄/servicePrincipals/ownedObjects/read | 讀取 Azure Active Directory 中的 servicePrincipals.ownedObjects 屬性。 |
| microsoft 目錄/servicePrincipals/許可權/更新 | 更新 Azure Active Directory 中的 servicePrincipals.permissions 屬性。 |
| microsoft 目錄/servicePrincipals/原則/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft 目錄/servicePrincipals/原則/更新 | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| directorySync/allEntities/allTasks | 執行 Azure AD Connect 中的所有動作。 |

### <a name="directory-writers-permissions"></a>目錄寫入器許可權

可讀取和寫入基本目錄資訊。 用來授與應用程式的存取權，不適用於使用者。

| **動作** | **說明** |
| --- | --- |
| microsoft. 目錄/群組/建立 | 在 Azure Active Directory 中建立 groups。 |
| microsoft 目錄/群組/createAsOwner | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft 目錄/群組/appRoleAssignments/更新 | 更新 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| microsoft 目錄/群組/基本/更新 | 更新 Azure Active Directory 中 groups 的基本屬性。 |
| microsoft 目錄/群組/成員/更新 | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft。目錄/群組/擁有者/更新 | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft 目錄/群組/設定/更新 | 更新 Azure Active Directory 中的 groups.settings 屬性。 |
| microsoft 目錄/groupSettings/基本/更新 | 更新 Azure Active Directory 中 groupSettings 的基本屬性。 |
| microsoft 目錄/groupSettings/建立 | 在 Azure Active Directory 中建立 groupSettings。 |
| microsoft 目錄/groupSettings/delete | 在 Azure Active Directory 中刪除 groupSettings。 |
| microsoft 目錄/使用者/appRoleAssignments/更新 | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft 目錄/使用者/assignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft 目錄/使用者/基本/更新 | 更新 Azure Active Directory 中 users 的基本屬性。 |
| microsoft 目錄/使用者/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft 目錄/使用者/管理員/更新 | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft 目錄/使用者/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 屬性。 |

### <a name="exchange-service-administrator-permissions"></a>Exchange 服務管理員許可權

可管理 Exchange 產品的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/群組/整合/appRoleAssignments/更新 | 更新 Azure Active Directory 中的 groups.unified 屬性。 |
| microsoft 目錄/群組/統一/基本/更新 | 更新 Office 365 群組的基本屬性。 |
| microsoft。目錄/群組/整合/建立 | 建立 Office 365 群組。 |
| microsoft 目錄/群組/整合/刪除 | 刪除 Office 365 群組。 |
| microsoft 目錄/群組/整合/成員/更新 | 更新 Office 365 群組的成員資格。 |
| microsoft。目錄/群組/整合/擁有者/更新 | 更新 Office 365 群組的擁有權。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.exchange/allEntities/allTasks | 管理 Exchange Online 的所有層面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="external-identity-provider-administrator-permissions"></a>外部識別提供者系統管理員許可權

設定要在直接同盟中使用的識別提供者。

| **動作** | **說明** |
| --- | --- |
| microsoft. aad. b2c/identityProviders/allTasks | 在 Azure Active Directory B2C 中讀取和設定識別提供者。 |

### <a name="global-reader-permissions"></a>全域讀取者許可權
可以讀取全域管理員可以進行的所有作業，但無法編輯任何專案。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱上述[角色說明](#global-reader)。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft commerce. 帳單/allEntities/讀取   | 閱讀 Office 365 帳單的所有層面。 |
| microsoft 目錄/administrativeUnits/基本/讀取    | 讀取 Azure Active Directory 中 administrativeUnits 的基本屬性。 |
| microsoft 目錄/administrativeUnits/成員/讀取  | 讀取 Azure Active Directory 中的 administrativeUnits.members 屬性。 |
| microsoft 目錄/應用程式/基本/讀取   | 讀取 Azure Active Directory 中 Applications 的基本屬性。 |
| microsoft 目錄/應用程式/擁有者/讀取  | 讀取 Azure Active Directory 中的 applications.owners 屬性。 |
| microsoft 目錄/應用程式/原則/讀取    | 讀取 Azure Active Directory 中的 applications.policies 屬性。 |
| microsoft 目錄/連絡人/基本/讀取   | 讀取 Azure Active Directory 中 contacts 的基本屬性。 |
| microsoft 目錄/連絡人/memberOf/read    | 讀取 Azure Active Directory 中的 contacts.memberOf 屬性。 |
| microsoft 目錄/合約/基本/讀取  | 讀取 Azure Active Directory 中 contracts 的基本屬性。 |
| microsoft 目錄/裝置/基本/讀取    | 讀取 Azure Active Directory 中 devices 的基本屬性。 |
| microsoft 目錄/裝置/memberOf/read | 讀取 Azure Active Directory 中的 devices.memberOf 屬性。 |
| microsoft 目錄/裝置/Devices.registeredowners/讀取 | 讀取 Azure Active Directory 中的 devices.registeredOwners 屬性。 |
| microsoft 目錄/裝置/Devices.registeredusers/讀取  | 讀取 Azure Active Directory 中的 devices.registeredUsers 屬性。 |
| microsoft 目錄/directoryRoles/基本/讀取 | 讀取 Azure Active Directory 中 directoryRoles 的基本屬性。 |
| microsoft 目錄/directoryRoles/Directoryroles.eligiblemembers/read   | 讀取 Azure Active Directory 中的 directoryRoles.eligibleMembers 屬性。 |
| microsoft 目錄/directoryRoles/成員/讀取   | 讀取 Azure Active Directory 中的 directoryRoles.members 屬性。 |
| microsoft 目錄/網域/基本/讀取    | 讀取 Azure Active Directory 中 domain 的基本屬性。 |
| microsoft 目錄/群組/appRoleAssignments/讀取    | 讀取 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| microsoft 目錄/群組/基本/讀取 | 讀取 Azure Active Directory 中 groups 的基本屬性。 |
| microsoft 目錄/群組/Groups.hiddenmembers/讀取 | 讀取 Azure Active Directory 中的 groups.hiddenMembers 屬性。 |
| microsoft 目錄/群組/memberOf/read  | 讀取 Azure Active Directory 中的 Read groups.memberOf 屬性。 |
| microsoft 目錄/群組/成員/讀取   | 讀取 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft。目錄/群組/擁有者/讀取    | 讀取 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft 目錄/群組/設定/讀取  | 讀取 Azure Active Directory 中的 groups.settings 屬性。 |
| microsoft 目錄/groupSettings/基本/讀取  | 讀取 Azure Active Directory 中 groupSettings 的基本屬性。 |
| microsoft 目錄/groupSettingTemplates/基本/讀取  | 讀取 Azure Active Directory 中 groupSettingTemplates 的基本屬性。 |
| microsoft 目錄/oAuth2PermissionGrants/基本/讀取 | 讀取 Azure Active Directory 中 oAuth2PermissionGrants 的基本屬性。 |
| microsoft 目錄/組織/基本/讀取   | 讀取 Azure Active Directory 中 organization 的基本屬性。 |
| microsoft 目錄/組織/trustedCAsForPasswordlessAuth/讀取   | 讀取 Azure Active Directory 中的 organization.trustedCAsForPasswordlessAuth 屬性。 |
| microsoft 目錄/原則/標準/讀取    | 閱讀 Azure Active Directory 中的標準原則。 |
| microsoft 目錄/roleAssignments/基本/讀取    | 讀取 Azure Active Directory 中 roleAssignments 的基本屬性。 |
| microsoft 目錄/roleDefinitions/基本/讀取    | 讀取 Azure Active Directory 中 roleDefinitions 的基本屬性。 |
| microsoft 目錄/servicePrincipals/Serviceprincipals.approleassignedto/read  | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft 目錄/servicePrincipals/appRoleAssignments/read | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| microsoft 目錄/servicePrincipals/基本/讀取  | 讀取 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft 目錄/servicePrincipals/memberOf/read   | 讀取 Azure Active Directory 中的 servicePrincipals.memberOf 屬性。 |
| microsoft 目錄/servicePrincipals/oAuth2PermissionGrants/基本/讀取   | 讀取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 屬性。 |
| microsoft 目錄/servicePrincipals/ownedObjects/read   | 讀取 Azure Active Directory 中的 servicePrincipals.ownedObjects 屬性。 |
| microsoft 目錄/servicePrincipals/擁有者/讀取 | 讀取 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft 目錄/servicePrincipals/原則/讀取   | 讀取 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft 目錄/Signinreports 所包含/allProperties/read  | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft 目錄/subscribedSkus/基本/讀取 | 讀取 Azure Active Directory 中 subscribedSkus 的基本屬性。 |
| microsoft 目錄/使用者/appRoleAssignments/讀取 | 讀取 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft 目錄/使用者/基本/讀取  | 讀取 Azure Active Directory 中 users 的基本屬性。 |
| microsoft 目錄/使用者/directReports/讀取  | 讀取 Azure Active Directory 中的 users.directReports 屬性。 |
| microsoft 目錄/使用者/管理員/讀取    | 讀取 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft 目錄/使用者/memberOf/read   | 讀取 Azure Active Directory 中的 users.memberOf 屬性。 |
| microsoft 目錄/使用者/oAuth2PermissionGrants/基本/讀取   | 讀取 Azure Active Directory 中的 users.oAuth2PermissionGrants 屬性。 |
| microsoft 目錄/使用者/Users.owneddevices/讀取   | 讀取 Azure Active Directory 中的 users.ownedDevices 屬性。 |
| microsoft 目錄/使用者/ownedObjects/讀取   | 讀取 Azure Active Directory 中的 users.ownedObjects 屬性。 |
| microsoft 目錄/使用者/registeredDevices/讀取  | 讀取 Azure Active Directory 中的 users.registeredDevices 屬性。 |
| microsoft 目錄/使用者/Users.strongauthentication/讀取   | 讀取增強式驗證屬性，例如 MFA 認證資訊。 |
| office365。 exchange/allEntities/read | 閱讀 Exchange Online 的所有層面。 |
| microsoft.office365.messageCenter/messages/read   | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.messageCenter/securityMessages/read   | 讀取 microsoft.office365.messageCenter 中的 securityMessages。 |
| microsoft.office365.protectionCenter/allEntities/read | 讀取 Office 365 防護中心的所有層面。 |
| office365. Microsoft.office365.securitycompliancecenter/allEntities/read | 讀取 office365. Microsoft.office365.securitycompliancecenter 中的所有標準屬性。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |
| office365. Microsoft.office365.webportal/allEntities/standard/read   | 讀取 office365. Microsoft.office365.webportal 中所有資源的標準屬性。 |

### <a name="groups-administrator-permissions"></a>群組系統管理員許可權
可以管理群組和群組設定（例如命名和到期原則）的所有層面。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/群組/基本/讀取 | 讀取 Azure Active Directory 中 Groups 的標準屬性。  |
| microsoft 目錄/群組/基本/更新 | 更新 Azure Active Directory 中 groups 的基本屬性。 |
| microsoft. 目錄/群組/建立 | 在 Azure Active Directory 中建立 groups。 |
| microsoft 目錄/群組/createAsOwner | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft 目錄/群組/刪除 | 刪除 Azure Active Directory 中的 groups。 |
| microsoft 目錄/群組/Groups.hiddenmembers/讀取 | 讀取 Azure Active Directory 中的 groups.hiddenMembers 屬性。 |
| microsoft 目錄/群組/成員/更新 | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft。目錄/群組/擁有者/更新 | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft. 目錄/群組/還原 | 還原 Azure Active Directory 中的 groups。 |
| microsoft 目錄/群組/設定/更新 | 更新 Azure Active Directory 中的 groups.settings 屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |

### <a name="guest-inviter-permissions"></a>來賓邀請者許可權
能夠邀請不受 [成員能夠邀請來賓] 設定限制的來賓使用者。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/使用者/appRoleAssignments/讀取 | 讀取 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft 目錄/使用者/基本/讀取 | 讀取 Azure Active Directory 中 users 的基本屬性。 |
| microsoft 目錄/使用者/directReports/讀取 | 讀取 Azure Active Directory 中的 users.directReports 屬性。 |
| microsoft 目錄/使用者/inviteGuest | 邀請 Azure Active Directory 中的來賓使用者。 |
| microsoft 目錄/使用者/管理員/讀取 | 讀取 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft 目錄/使用者/memberOf/read | 讀取 Azure Active Directory 中的 users.memberOf 屬性。 |
| microsoft 目錄/使用者/oAuth2PermissionGrants/基本/讀取 | 讀取 Azure Active Directory 中的 users.oAuth2PermissionGrants 屬性。 |
| microsoft 目錄/使用者/Users.owneddevices/讀取 | 讀取 Azure Active Directory 中的 users.ownedDevices 屬性。 |
| microsoft 目錄/使用者/ownedObjects/讀取 | 讀取 Azure Active Directory 中的 users.ownedObjects 屬性。 |
| microsoft 目錄/使用者/registeredDevices/讀取 | 讀取 Azure Active Directory 中的 users.registeredDevices 屬性。 |

### <a name="helpdesk-administrator-permissions"></a>技術支援中心系統管理員許可權

能夠為非系統管理員與技術服務人員系統管理員重設密碼。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/裝置/Devices.bitlockerrecoverykeys/讀取 | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft 目錄/使用者/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft 目錄/使用者/密碼/更新 | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="intune-service-administrator-permissions"></a>Intune 服務管理員許可權

可管理 Intune 產品的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/連絡人/基本/更新 | 更新 Azure Active Directory 中 contacts 的基本屬性。 |
| microsoft 目錄/連絡人/建立 | 在 Azure Active Directory 中建立 contacts。 |
| microsoft 目錄/連絡人/刪除 | 刪除 Azure Active Directory 中的 contacts。 |
| microsoft 目錄/裝置/基本/更新 | 更新 Azure Active Directory 中 devices 的基本屬性。 |
| microsoft 目錄/裝置/Devices.bitlockerrecoverykeys/讀取 | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft 目錄/裝置/建立 | 在 Azure Active Directory 中建立 devices。 |
| microsoft 目錄/裝置/刪除 | 刪除 Azure Active Directory 中的 devices。 |
| microsoft 目錄/裝置/Devices.registeredowners/更新 | 更新 Azure Active Directory 中的 devices.registeredOwners 屬性。 |
| microsoft 目錄/裝置/Devices.registeredusers/更新 | 更新 Azure Active Directory 中的 devices.registeredUsers 屬性。 |
| microsoft 目錄/群組/appRoleAssignments/更新 | 更新 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| microsoft 目錄/群組/基本/更新 | 更新 Azure Active Directory 中 groups 的基本屬性。 |
| microsoft. 目錄/群組/建立 | 在 Azure Active Directory 中建立 groups。 |
| microsoft 目錄/群組/createAsOwner | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft 目錄/群組/刪除 | 刪除 Azure Active Directory 中的 groups。 |
| microsoft 目錄/群組/Groups.hiddenmembers/讀取 | 讀取 Azure Active Directory 中的 groups.hiddenMembers 屬性。 |
| microsoft 目錄/群組/成員/更新 | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft。目錄/群組/擁有者/更新 | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft. 目錄/群組/還原 | 還原 Azure Active Directory 中的 groups。 |
| microsoft 目錄/群組/設定/更新 | 更新 Azure Active Directory 中的 groups.settings 屬性。 |
| microsoft 目錄/使用者/appRoleAssignments/更新 | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft 目錄/使用者/基本/更新 | 更新 Azure Active Directory 中 users 的基本屬性。 |
| microsoft 目錄/使用者/管理員/更新 | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.intune/allEntities/allTasks | 管理 Intune 的所有層面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="kaizala-administrator-permissions"></a>Kaizala 系統管理員許可權

可以管理 Microsoft Kaizala 的設定。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 閱讀 Office 365 系統管理中心。 |

### <a name="license-administrator-permissions"></a>授權管理員許可權

可管理使用者和群組的產品授權。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/使用者/assignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft 目錄/使用者/usageLocation/更新 | 更新 Azure Active Directory 中的 users.usageLocation 屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |

### <a name="lync-service-administrator-permissions"></a>Lync 服務管理員許可權

可管理商務用 Skype 產品的所有層面。

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
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理商務用 Skype Online 的所有層面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="message-center-privacy-reader-permissions"></a>訊息中心隱私權讀者許可權

可以讀取訊息中心文章、資料隱私權訊息、群組、網域和訂閱。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.messageCenter/securityMessages/read | 讀取 microsoft.office365.messageCenter 中的 securityMessages。 |

### <a name="message-center-reader-permissions"></a>訊息中心讀取者許可權
只可在 Office 365 訊息中心讀取及更新其組織的訊息。 

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |

### <a name="office-apps-administrator-permissions"></a>Office 應用程式系統管理員許可權
可以管理 Office 應用程式的雲端服務，包括原則和設定管理，以及管理對終端使用者裝置選取、取消選取和發佈「新功能」功能內容的能力。

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
| office365. userCommunication/allEntities/allTasks | 閱讀及更新新消息的可見度。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="partner-tier1-support-permissions"></a>合作夥伴第1層支援許可權

請勿使用 - 不適用於一般用途。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/連絡人/基本/更新 | 更新 Azure Active Directory 中 contacts 的基本屬性。 |
| microsoft 目錄/連絡人/建立 | 在 Azure Active Directory 中建立 contacts。 |
| microsoft 目錄/連絡人/刪除 | 刪除 Azure Active Directory 中的 contacts。 |
| microsoft. 目錄/群組/建立 | 在 Azure Active Directory 中建立 groups。 |
| microsoft 目錄/群組/createAsOwner | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft 目錄/群組/成員/更新 | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft。目錄/群組/擁有者/更新 | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft 目錄/使用者/appRoleAssignments/更新 | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft 目錄/使用者/assignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft 目錄/使用者/基本/更新 | 更新 Azure Active Directory 中 users 的基本屬性。 |
| microsoft 目錄/使用者/刪除 | 刪除 Azure Active Directory 中的 users。 |
| microsoft 目錄/使用者/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft 目錄/使用者/管理員/更新 | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft 目錄/使用者/密碼/更新 | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft 目錄/使用者/還原 | 還原 Azure Active Directory 中已刪除的使用者。 |
| microsoft 目錄/使用者/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="partner-tier2-support-permissions"></a>合作夥伴第2層支援許可權

請勿使用 - 不適用於一般用途。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/連絡人/基本/更新 | 更新 Azure Active Directory 中 contacts 的基本屬性。 |
| microsoft 目錄/連絡人/建立 | 在 Azure Active Directory 中建立 contacts。 |
| microsoft 目錄/連絡人/刪除 | 刪除 Azure Active Directory 中的 contacts。 |
| microsoft 目錄/網域/allTasks | 建立和刪除 domains，以及在 Azure Active Directory 中讀取和更新標準屬性。 |
| microsoft. 目錄/群組/建立 | 在 Azure Active Directory 中建立 groups。 |
| microsoft 目錄/群組/刪除 | 刪除 Azure Active Directory 中的 groups。 |
| microsoft 目錄/群組/成員/更新 | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft. 目錄/群組/還原 | 還原 Azure Active Directory 中的 groups。 |
| microsoft 目錄/組織/基本/更新 | 更新 Azure Active Directory 中 organization 的基本屬性。 |
| microsoft 目錄/使用者/appRoleAssignments/更新 | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft 目錄/使用者/assignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft 目錄/使用者/基本/更新 | 更新 Azure Active Directory 中 users 的基本屬性。 |
| microsoft 目錄/使用者/刪除 | 刪除 Azure Active Directory 中的 users。 |
| microsoft 目錄/使用者/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft 目錄/使用者/管理員/更新 | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft 目錄/使用者/密碼/更新 | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft 目錄/使用者/還原 | 還原 Azure Active Directory 中已刪除的使用者。 |
| microsoft 目錄/使用者/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="password-administrator-permissions"></a>密碼管理員許可權

可以重設非系統管理員和密碼管理員的密碼。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/使用者/密碼/更新 | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="power-bi-service-administrator-permissions"></a>Power BI 服務系統管理員許可權

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


### <a name="power-platform-administrator-permissions"></a>Power Platform 系統管理員許可權

可以建立及管理 Microsoft Dynamics 365、PowerApps 和 Microsoft Flow 的所有層面。 

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>
| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| dynamics365/allEntities/allTasks | 管理 Dynamics 365 的所有層面。 |
| microsoft flow/allEntities/allTasks | 管理 Microsoft Flow 的所有層面。 |
| microsoft powerApps/allEntities/allTasks | 管理 PowerApps 的所有層面。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="privileged-authentication-administrator-permissions"></a>特殊許可權驗證管理員許可權

允許針對任何使用者（管理員或非系統管理員）來查看、設定及重設驗證方法資訊。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/使用者/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft 目錄/使用者/Users.strongauthentication/更新 | 更新 MFA 認證資訊等增強式驗證屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft 目錄/使用者/密碼/更新 | 更新 Office 365 組織中所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |

### <a name="privileged-role-administrator-permissions"></a>特殊許可權角色管理員許可權

可以管理 Azure AD 中的角色指派，以及 Privileged Identity Management 的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.aad.privilegedIdentityManagement 中的標準屬性。 |
| microsoft 目錄/servicePrincipals/Serviceprincipals.approleassignedto/allTasks | 讀取和設定 Azure Active Directory 中的 Serviceprincipals.approleassignedto 屬性。 |
| microsoft 目錄/servicePrincipals/oAuth2PermissionGrants/allTasks | 讀取和設定 Azure Active Directory 中的 oAuth2PermissionGrants 屬性。 |
| microsoft 目錄/administrativeUnits/allProperties/allTasks | 建立和管理管理單位（包括成員） |
| microsoft 目錄/roleAssignments/allProperties/allTasks | 建立和管理角色指派。 |
| microsoft 目錄/roleDefinitions/allProperties/allTasks | 建立和管理角色定義。 |

### <a name="reports-reader-permissions"></a>報告讀者許可權

可讀取登入與稽核報告。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft 目錄/Signinreports 所包含/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |

### <a name="search-administrator-permissions"></a>搜尋系統管理員許可權

可以建立及管理 Microsoft 搜尋設定的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| office365。 search/allEntities/allProperties/allTasks | 建立和刪除所有資源，以及讀取和更新 office365 中的所有屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="search-editor-permissions"></a>搜尋編輯器許可權

可以建立及管理編輯內容，例如書簽、Q 和 As、位置、floorplan。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| office365/content/allProperties/allTasks | 建立和刪除內容，以及讀取和更新 office365 中的所有屬性。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |

### <a name="security-administrator-permissions"></a>安全性系統管理員許可權

可以讀取安全性資訊與報表，以及管理 Azure AD 和 Office 365 中的設定。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/應用程式/原則/更新 | 更新 Azure Active Directory 中的 applications.policies 屬性。 |
| microsoft 目錄/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft 目錄/裝置/Devices.bitlockerrecoverykeys/讀取 | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft 目錄/原則/基本/更新 | 更新 Azure Active Directory 中 policies 的基本屬性。 |
| microsoft 目錄/原則/建立 | 在 Azure Active Directory 中建立原則。 |
| microsoft 目錄/原則/刪除 | 刪除 Azure Active Directory 中的原則。 |
| microsoft 目錄/原則/擁有者/更新 | 更新 Azure Active Directory 中的 policies.owners 屬性。 |
| microsoft 目錄/原則/Policies.tenantdefault/更新 | 更新 Azure Active Directory 中的 policies.tenantDefault 屬性。 |
| microsoft 目錄/servicePrincipals/原則/更新 | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft 目錄/Signinreports 所包含/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.aad.identityProtection/allEntities/read | 讀取 microsoft.aad.identityProtection 中的所有資源。 |
| microsoft.aad.identityProtection/allEntities/update | 更新 microsoft.aad.identityProtection 中的所有資源。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 讀取 microsoft.aad.privilegedIdentityManagement 中的所有資源。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.protectionCenter/allEntities/read | 讀取 Office 365 防護中心的所有層面。 |
| microsoft.office365.protectionCenter/allEntities/update | 更新 microsoft.office365.protectionCenter 中的所有資源。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |

### <a name="security-operator-permissions"></a>安全性操作員許可權

建立和管理安全性事件。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | 讀取和設定 Microsoft Cloud App Security。 |
| microsoft.aad.identityProtection/allEntities/read | 讀取 microsoft.aad.identityProtection 中的所有資源。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 讀取 microsoft.aad.privilegedIdentityManagement 中的所有資源。 |
| microsoft.azure.advancedThreatProtection/allEntities/read | 閱讀並設定 Azure AD Advanced 威脅防護。 |
| microsoft.intune/allEntities/allTasks | 管理 Intune 的所有層面。 |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | 閱讀並設定安全性 & 合規性中心。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | 讀取和設定 Windows Defender Advanced 威脅防護。 |

### <a name="security-reader-permissions"></a>安全性讀取者許可權

可讀取安全性資訊及 Azure AD 與 Office 365 中的報告。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft 目錄/裝置/Devices.bitlockerrecoverykeys/讀取 | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft 目錄/原則/Policies.conditionalaccess/基本/讀取 | 讀取 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft 目錄/Signinreports 所包含/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.aad.identityProtection/allEntities/read | 讀取 microsoft.aad.identityProtection 中的所有資源。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 讀取 microsoft.aad.privilegedIdentityManagement 中的所有資源。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.protectionCenter/allEntities/read | 讀取 Office 365 防護中心的所有層面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |

### <a name="service-support-administrator-permissions"></a>服務支援系統管理員許可權

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

### <a name="sharepoint-service-administrator-permissions"></a>SharePoint 服務管理員許可權

可管理 SharePoint 服務的所有層面。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/群組/整合/appRoleAssignments/更新 | 更新 Azure Active Directory 中的 groups.unified 屬性。 |
| microsoft 目錄/群組/統一/基本/更新 | 更新 Office 365 群組的基本屬性。 |
| microsoft。目錄/群組/整合/建立 | 建立 Office 365 群組。 |
| microsoft 目錄/群組/整合/刪除 | 刪除 Office 365 群組。 |
| microsoft 目錄/群組/整合/成員/更新 | 更新 Office 365 群組的成員資格。 |
| microsoft。目錄/群組/整合/擁有者/更新 | 更新 Office 365 群組的擁有權。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.sharepoint/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.office365.sharepoint 中的標準屬性。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="teams-communications-administrator-permissions"></a>小組通訊系統管理員許可權

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

### <a name="teams-communications-support-engineer-permissions"></a>小組通訊支援工程師的許可權

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

### <a name="teams-communications-support-specialist-permissions"></a>小組通訊支援專家許可權

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

### <a name="teams-service-administrator-permissions"></a>小組服務系統管理員許可權

能夠管理 Microsoft Teams 服務。

> [!NOTE]
> 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
>
>

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/群組/Groups.hiddenmembers/讀取 | 讀取 Azure Active Directory 中的 groups.hiddenMembers 屬性。 |
| microsoft 目錄/群組/整合/appRoleAssignments/更新 | 更新 Azure Active Directory 中的 groups.unified 屬性。 |
| microsoft 目錄/群組/統一/基本/更新 | 更新 Office 365 群組的基本屬性。 |
| microsoft。目錄/群組/整合/建立 | 建立 Office 365 群組。 |
| microsoft 目錄/群組/整合/刪除 | 刪除 Office 365 群組。 |
| microsoft 目錄/群組/整合/成員/更新 | 更新 Office 365 群組的成員資格。 |
| microsoft。目錄/群組/整合/擁有者/更新 | 更新 Office 365 群組的擁有權。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |

### <a name="user-administrator-permissions"></a>使用者系統管理員許可權
能夠管理使用者與群組的所有層面，包含為受限制的管理員重設密碼。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/appRoleAssignments/建立 | 在 Azure Active Directory 中建立 appRoleAssignments。 |
| microsoft 目錄/appRoleAssignments/delete | 刪除 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft 目錄/appRoleAssignments/更新 | 更新在 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft 目錄/連絡人/基本/更新 | 更新 Azure Active Directory 中 contacts 的基本屬性。 |
| microsoft 目錄/連絡人/建立 | 在 Azure Active Directory 中建立 contacts。 |
| microsoft 目錄/連絡人/刪除 | 刪除 Azure Active Directory 中的 contacts。 |
| microsoft 目錄/群組/appRoleAssignments/更新 | 更新 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| microsoft 目錄/群組/基本/更新 | 更新 Azure Active Directory 中 groups 的基本屬性。 |
| microsoft. 目錄/群組/建立 | 在 Azure Active Directory 中建立 groups。 |
| microsoft 目錄/群組/createAsOwner | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft 目錄/群組/刪除 | 刪除 Azure Active Directory 中的 groups。 |
| microsoft 目錄/群組/Groups.hiddenmembers/讀取 | 讀取 Azure Active Directory 中的 groups.hiddenMembers 屬性。 |
| microsoft 目錄/群組/成員/更新 | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft。目錄/群組/擁有者/更新 | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft. 目錄/群組/還原 | 還原 Azure Active Directory 中的 groups。 |
| microsoft 目錄/群組/設定/更新 | 更新 Azure Active Directory 中的 groups.settings 屬性。 |
| microsoft 目錄/使用者/appRoleAssignments/更新 | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft 目錄/使用者/assignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft 目錄/使用者/基本/更新 | 更新 Azure Active Directory 中 users 的基本屬性。 |
| microsoft 目錄/使用者/建立 | 在 Azure Active Directory 中建立 users。 |
| microsoft 目錄/使用者/刪除 | 刪除 Azure Active Directory 中的 users。 |
| microsoft 目錄/使用者/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft 目錄/使用者/管理員/更新 | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft 目錄/使用者/密碼/更新 | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft 目錄/使用者/還原 | 還原 Azure Active Directory 中已刪除的使用者。 |
| microsoft 目錄/使用者/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

## <a name="role-template-ids"></a>角色範本識別碼

角色範本識別碼主要是由 Microsoft Graph API 或 PowerShell 使用者使用。

圖表 displayName | Azure 入口網站顯示名稱 | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
應用程式系統管理員 | 應用程式管理員 | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
應用程式開發人員 | 應用程式開發人員 | CF1C38E5-3621-4004-A7CB-879624DCED7C
驗證系統管理員 | 驗證管理員 | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure DevOps 系統管理員 | Azure DevOps 系統管理員 | e3973bdf-4987-49ae-837a-ba8e231c7286
Azure 資訊保護系統管理員 | Azure 資訊保護系統管理員 | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C 使用者流程管理員 | B2C 使用者流程管理員 | 6e591065-9bad-43ed-90f3-e9424366d2f0
B2C 使用者流程屬性管理員 | B2C 使用者流程屬性管理員 | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
B2C IEF 索引鍵集管理員 | B2C IEF 索引鍵集管理員 | aaf43236-0c0d-4d5f-883a-6955382ac081
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
裝置系統管理員 | 裝置系統管理員 | 9f06204d-73c1-4d4c-880a-6edb90606fd8
加入裝置 | 裝置加入 | 9c094953-4995-41c8-84c8-3ebb9b32c93f
裝置管理員 | 裝置管理員 | 2b499bcd-da44-4968-8aec-78e1674fa64d
裝置使用者 | 裝置使用者 | d405c6df-0af8-4e3b-95e4-4d06e542189e
目錄讀取器 | 目錄讀取器 | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
目錄同步處理帳戶 | 目錄同步處理帳戶 | d29b2b05-8046-44ba-8758-1e26182fcf32
目錄撰寫者 | 目錄寫入器 | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange 服務管理員 | Exchange 系統管理員 | 29232cdf-9323-42fd-ade2-1d097af3e4de
外部識別提供者系統管理員 | 外部識別提供者系統管理員 | be2f45a1-457d-42af-a067-6ec1fa63bc45
全域讀者 | 全域讀者 | f2ef992c-3afb-46b9-b7cf-a126ee74c451
群組管理員 | 群組管理員 | fdd7a751-b60b-444a-984c-02652fe8fa1c 
來賓邀請者 | 來賓邀請者 | 95e79109-95c0-4d8e-aee3-d01accf2d47b
服務台系統管理員 | 服務台管理員 | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Intune 服務管理員 | Intune 管理員 | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala 系統管理員 | Kaizala 系統管理員 | 74ef975b-6605-40af-a5d2-b9539d836353
授權管理員 | 授權管理員 | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync 服務管理員 | 商務用 Skype 的管理員 | 75941009-915a-4869-abe7-691bff18279e
訊息中心隱私權讀者 | 訊息中心隱私權讀者 | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
訊息中心讀取者 | 訊息中心讀者 | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Office 應用程式系統管理員 | Office 應用程式系統管理員 | 2b745bdf-0803-4d80-aa65-822c4493daac
合作夥伴第 1 層支援 | 合作夥伴第 1 層支援 | 4ba39ca4-527c-499a-b93d-d9b492c50246
合作夥伴第 2 層支援 | 合作夥伴第 2 層支援 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
密碼管理員 | 密碼管理員 | 966707d0-3269-4727-9be2-8c3a10f19b9d
Power BI 服務管理員 | Power BI 系統管理員 | a9ea8996-122f-4c74-9520-8edcd192826c
Power 平臺系統管理員 | Power 平臺系統管理員 | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
特殊許可權驗證管理員 | 特殊許可權驗證管理員 | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
特殊權限角色管理員 | 特殊權限角色管理員 | e8611ab8-c189-46e8-94e1-60213ab1f814
報告讀者 | 報表讀者 | 4a5d8f65-41da-4de4-8968-e035b65339cf
搜尋系統管理員 | 搜尋系統管理員 | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
搜尋編輯器 | 搜尋編輯器 | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
安全性系統管理員 | 安全性系統管理員 | 194ae4cb-b126-40b2-bd5b-6091b380977d
安全性運算子 | 安全性運算子 | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
安全性讀取者 | 安全性讀取者 | 5d6b6bb7-de71-4623-b4af-96380a352509
服務支援管理員 | 服務管理員 | f023fd81-a637-4b56-95fd-791ac0226033
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

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何將使用者指派為 Azure 訂用帳戶的系統管理員，請參閱[使用 RBAC 和 Azure 入口網站管理存取權](../../role-based-access-control/role-assignments-portal.md)
* 若要深入了解如何在 Microsoft Azure 中控制資源存取，請參閱 [了解 Azure 中的資源存取](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* 如需有關 Azure Active Directory 與您的 Azure 訂用帳戶產生關聯之方式的詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
