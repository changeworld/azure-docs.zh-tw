---
title: 預設使用者權限 - Azure Active Directory | Microsoft Docs
description: 深入了解 Azure Active Directory 中可用的不同使用者權限。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 227230f2d6f46fae27e2cec69d99390f5054c7db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80366256"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Azure Active Directory 中的預設使用者權限是什麼？
在 Azure Active Directory (Azure AD) 中，所有使用者都會獲得一組預設權限。 使用者的存取權包含使用者類型、其[角色指派](active-directory-users-assign-role-azure-portal.md)，以及個別物件的擁有權。 本文說明這些預設權限，並包含成員與來賓使用者預設值的比較。 只能在 Azure AD 中的使用者設定中變更預設使用者權限。

## <a name="member-and-guest-users"></a>成員和來賓使用者
使用者所收到的預設權限集取決於使用者是租用戶的原生成員 (成員使用者)，還是來自另一個目錄的 B2B 共同作業來賓 (來賓使用者)。 如需新增來賓使用者的詳細資訊，請參閱[什麼是 Azure AD B2B 共同作業？](../b2b/what-is-b2b.md)。
* 成員使用者可以註冊應用程式、管理自己的設定檔相片和行動電話號碼、變更自己的密碼，以及邀請 B2B 來賓。 此外，使用者還可讀取所有目錄資訊 (但有一些例外狀況)。 
* 來賓使用者具有受限的目錄權限。 例如，來賓使用者無法瀏覽其本身的設定檔資訊以外的租用戶資訊。 不過，來賓使用者可藉由提供使用者主體名稱或 objectId 來擷取另一位使用者的相關資訊。 不論 [來賓使用者權限受限]**** 設定為何，來賓使用者可以讀取所屬群組的屬性，包括群組成員資格。 來賓無法檢視任何其他租用戶物件的相關資訊。

預設會限制來賓的預設權限。 您可將來賓新增至系統管理員角色，將角色內含的完整讀取和寫入權限授予他們。 另外還有一項可用限制，就是來賓邀請其他來賓的能力。 將 [來賓可邀請]**** 設為 [否]****，可防止來賓邀請其他來賓。 請參閱[委派 B2B 共同作業的邀請](../b2b/delegate-invitations.md)以了解詳情。 若要將與成員使用者相同的預設權限授予來賓使用者，請將 [來賓使用者權限受限]**** 設定為 [否]****。 此設定預設會將所有成員使用者權限授予來賓使用者，以及允許將來賓新增至系統管理角色。

## <a name="compare-member-and-guest-default-permissions"></a>比較成員與來賓預設的權限

**領域** | **成員使用者權限** | **來賓使用者權限**
------------ | --------- | ----------
使用者和連絡人 | 讀取使用者和連絡人的所有公用屬性<br>邀請來賓<br>變更自己的密碼<br>管理自己的行動電話號碼<br>管理自己的相片<br>使自己的重新整理權杖失效 | 讀取自己的屬性<br>讀取顯示名稱、電子郵件、登入名稱、相片、使用者主體名稱，以及其他使用者和連絡人的使用者類型屬性<br>變更自己的密碼
群組 | 建立安全性群組<br>建立 Office 365 群組<br>讀取群組的所有屬性<br>讀取非隱藏的群組成員資格<br>讀取已加入群組的隱藏 Office 365 群組成員資格<br>管理使用者擁有之群組的屬性、擁有權及成員資格<br>將來賓新增至擁有的群組<br>管理動態成員資格設定<br>刪除擁有的群組<br>還原擁有的 Office 365 群組 | 讀取群組的所有屬性<br>讀取非隱藏的群組成員資格<br>讀取已加入群組的隱藏 Office 365 群組成員資格<br>管理擁有的群組<br>將來賓新增至擁有的群組 (如果允許)<br>刪除擁有的群組<br>還原擁有的 Office 365 群組<br>讀取所屬群組的屬性，包括成員資格。
應用程式 | 註冊 (建立) 新的應用程式<br>讀取已註冊和企業應用程式的屬性<br>管理擁有之應用程式的應用程式屬性、指派及認證<br>建立或刪除使用者的應用程式密碼<br>刪除擁有的應用程式<br>還原擁有的應用程式 | 讀取已註冊和企業應用程式的屬性<br>管理擁有之應用程式的應用程式屬性、指派及認證<br>刪除擁有的應用程式<br>還原擁有的應用程式
裝置 | 讀取裝置的所有屬性<br>管理擁有之裝置的所有屬性<br> | 沒有權限<br>刪除擁有的裝置<br>
目錄 | 讀取所有公司資訊<br>讀取所有網域<br>讀取所有夥伴合約 | 讀取顯示名稱和已驗證的網域
角色和範圍 | 讀取所有系統管理角色和成員資格<br>讀取系統管理單元的所有屬性和成員資格 | 沒有權限 
訂閱 | 讀取所有訂用帳戶<br>啟用服務方案成員 | 沒有權限
原則 | 讀取原則的所有屬性<br>管理擁有之原則的所有屬性 | 沒有權限

## <a name="to-restrict-the-default-permissions-for-member-users"></a>限制成員使用者的預設權限

您可利用下列方式限制成員使用者的預設權限。

權限 | 設定說明
---------- | ------------
使用者可以註冊應用程式 | 將此選項設定為 [否] 可防止使用者建立應用程式註冊。 藉由將其新增至應用程式開發人員角色，即可將其授與特定的個人。
允許使用者使用 LinkedIn 連接公司或學校帳戶 | 將此選項設定為 [否] 可防止使用者使用其 LinkedIn 帳戶連接其公司或學校帳戶。 如需詳細資訊，請參閱[LinkedIn 帳戶連接資料共用和同意](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent)。
建立安全性群組的能力 | 將此選項設定為 [否] 可防止使用者建立安全性群組。 全域管理員和使用者系統管理員仍然可以建立安全性群組。 請參閱[用於進行群組設定的 Azure Active Directory Cmdlet](../users-groups-roles/groups-settings-cmdlets.md) 以了解詳情。
建立 Office 365 群組的能力 | 將此選項設定為 [否] 可防止使用者建立 Office 365 群組。 將此選項設定為 [部分] 可允許一組特定使用者建立 Office 365 群組。 全域管理員和使用者系統管理員仍然可以建立 Office 365 群組。 請參閱[用於進行群組設定的 Azure Active Directory Cmdlet](../users-groups-roles/groups-settings-cmdlets.md) 以了解詳情。
限制 Azure AD 系統管理入口網站的存取 | 將此選項設定為 [否] 可讓非系統管理員使用 Azure AD 系統管理入口網站來讀取和管理 Azure AD 資源。 [是] 會限制所有非系統管理員存取系統管理入口網站中的任何 Azure AD 資料。 重要事項：此設定不會使用 PowerShell 或其他用戶端（例如 Visual Studio）來限制 Azure AD 資料的存取。 設定為 [是] 時，若要授與特定非系統管理員使用者使用 Azure AD 系統管理入口網站的能力，請指派任何系統管理角色，例如目錄讀取者角色。 此角色可讓您讀取基本目錄資訊，成員使用者預設會有此資訊（來賓和服務主體不會這麼做）。
讀取其他使用者的能力 | 此設定僅在 PowerShell 中可用。 將此旗標設定為 $false 可防止所有的非系統管理員讀取目錄中的使用者資訊。 此旗標不會防止讀取其他 Microsoft 服務（例如 Exchange Online）中的使用者資訊。 這種設定適用于特殊情況，不建議將此旗標設定為 $false。

## <a name="object-ownership"></a>物件擁有權

### <a name="application-registration-owner-permissions"></a>應用程式註冊擁有者權限
當使用者註冊應用程式時，系統會將他們自動新增為應用程式的擁有者。 身為擁有者的他們可以管理應用程式的中繼資料，例如應用程式要求的名稱和權限。 他們也可以管理應用程式的租用戶專屬設組態定，例如 SSO 組態和使用者指派。 擁有者也可以新增或移除其他擁有者。 不同於全域管理員，擁有者只能管理自己的應用程式。

### <a name="enterprise-application-owner-permissions"></a>企業應用程式擁有者許可權
當使用者新增新的企業應用程式時，系統會自動將他們新增為擁有者。 身為擁有者，他們可以管理應用程式的租使用者特定設定，例如 SSO 設定、布建和使用者指派。 擁有者也可以新增或移除其他擁有者。 不同于全域管理員，擁有者只能管理他們所擁有的應用程式。

### <a name="group-owner-permissions"></a>群組擁有者權限
當使用者建立群組時，他們會自動新增為該群組的擁有者。 身為擁有者的他們可以管理群組的屬性 (例如名稱)，以及管理群組成員資格。 擁有者也可以新增或移除其他擁有者。 不同于全域管理員和使用者系統管理員，擁有者只能管理他們所擁有的群組。 若要指派群組擁有者，請參閱[管理群組的擁有者](active-directory-accessmanagement-managing-group-owners.md)。

### <a name="ownership-permissions"></a>擁有權許可權
下表描述 Azure Active Directory 成員使用者在擁有的物件上擁有的特定許可權。 使用者只擁有他們所擁有之物件的這些許可權。

#### <a name="owned-application-registrations"></a>擁有的應用程式註冊
使用者可以在所擁有的應用程式註冊上執行下列動作。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/應用程式/物件/更新 | 在 Azure Active Directory 中更新 applications.audience 屬性。 |
| microsoft 目錄/應用程式/驗證/更新 | 在 Azure Active Directory 中更新 applications.authentication 屬性。 |
| microsoft 目錄/應用程式/基本/更新 | 更新 Azure Active Directory 中 Applications 的基本屬性。 |
| microsoft 目錄/應用程式/認證/更新 | 在 Azure Active Directory 中更新 applications.credentials 屬性。 |
| microsoft 目錄/應用程式/刪除 | 刪除 Azure Active Directory 中的應用程式。 |
| microsoft 目錄/應用程式/擁有者/更新 | 更新 Azure Active Directory 中的 applications.owners 屬性。 |
| microsoft 目錄/應用程式/許可權/更新 | 在 Azure Active Directory 中更新 applications.permissions 屬性。 |
| microsoft 目錄/應用程式/原則/更新 | 更新 Azure Active Directory 中的 applications.policies 屬性。 |
| microsoft 目錄/應用程式/還原 | 還原 Azure Active Directory 中的 applications。 |

#### <a name="owned-enterprise-applications"></a>擁有的企業應用程式
使用者可以在擁有的企業應用程式上執行下列動作。 企業應用程式是由服務主體、一或多個應用程式原則，有時與服務主體在相同租使用者中的應用程式物件所組成。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft 目錄/原則/基本/更新 | 更新 Azure Active Directory 中 policies 的基本屬性。 |
| microsoft 目錄/原則/刪除 | 刪除 Azure Active Directory 中的原則。 |
| microsoft 目錄/原則/擁有者/更新 | 更新 Azure Active Directory 中的 policies.owners 屬性。 |
| microsoft directory/servicePrincipals/Serviceprincipals.approleassignedto/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft 目錄/servicePrincipals/物件/更新 | 更新 Azure Active Directory 中的 servicePrincipals.audience 屬性。 |
| microsoft 目錄/servicePrincipals/驗證/更新 | 更新 Azure Active Directory 中的 servicePrincipals.authentication 屬性。 |
| microsoft 目錄/servicePrincipals/基本/更新 | 更新 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft 目錄/servicePrincipals/認證/更新 | 更新 Azure Active Directory 中的 servicePrincipals.credentials 屬性。 |
| microsoft 目錄/servicePrincipals/delete | 刪除 Azure Active Directory 中的 servicePrincipals。 |
| microsoft 目錄/servicePrincipals/擁有者/更新 | 更新 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft 目錄/servicePrincipals/許可權/更新 | 更新 Azure Active Directory 中的 servicePrincipals.permissions 屬性。 |
| microsoft 目錄/servicePrincipals/原則/更新 | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft 目錄/Signinreports 所包含/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |

#### <a name="owned-devices"></a>擁有的裝置
使用者可以在擁有的裝置上執行下列動作。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/裝置/Devices.bitlockerrecoverykeys/讀取 | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft 目錄/裝置/停用 | 停用 Azure Active Directory 中的 devices。 |

#### <a name="owned-groups"></a>擁有的群組
使用者可以在擁有的群組上執行下列動作。

| **動作** | **說明** |
| --- | --- |
| microsoft 目錄/群組/appRoleAssignments/更新 | 更新 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| microsoft 目錄/群組/基本/更新 | 更新 Azure Active Directory 中 groups 的基本屬性。 |
| microsoft 目錄/群組/刪除 | 刪除 Azure Active Directory 中的 groups。 |
| microsoft 目錄/群組/Groups.dynamicmembershiprule/更新 | 更新 Azure Active Directory 中的 groups.dynamicMembershipRule 屬性。 |
| microsoft 目錄/群組/成員/更新 | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft。目錄/群組/擁有者/更新 | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft. 目錄/群組/還原 | 還原 Azure Active Directory 中的 groups。 |
| microsoft 目錄/群組/設定/更新 | 更新 Azure Active Directory 中的 groups.settings 屬性。 |

## <a name="next-steps"></a>後續步驟

* 如需深入了解如何指派 Azure AD 管理員角色，請參閱[在 Azure Active Directory 中將使用者指派給系統管理員角色](active-directory-users-assign-role-azure-portal.md)
* 若要深入了解如何在 Microsoft Azure 中控制資源存取，請參閱 [了解 Azure 中的資源存取](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* 如需有關 Azure Active Directory 與您的 Azure 訂用帳戶產生關聯之方式的詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](active-directory-how-subscriptions-associated-directory.md)
* [管理使用者](add-users-azure-active-directory.md)
