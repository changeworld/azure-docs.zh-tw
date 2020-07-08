---
title: 可用的自訂系統管理員角色許可權-Azure AD |Microsoft Docs
description: 委派身分識別管理的自訂系統管理員角色許可權。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c11723efe3fac236fce49c1f92fa338d4e58b59
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84732101"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Azure Active Directory 中的應用程式註冊子類型和許可權

本文包含 Azure Active Directory （Azure AD）中自訂角色定義目前可用的應用程式註冊許可權。

## <a name="permissions-for-managing-single-directory-applications"></a>管理單一目錄應用程式的許可權

選擇自訂角色的許可權時，您可以選擇僅授與管理單一目錄應用程式的存取權。 單一目錄應用程式僅適用于已註冊應用程式之 Azure AD 組織中的使用者。 單一目錄應用程式的定義，是將**支援的帳戶類型**設定為 [僅限此組織目錄中的帳戶]。 在圖形 API 中，單一目錄應用程式的 signInAudience 屬性設定為 "AzureADMyOrg"。

若要授與僅管理單一目錄應用程式的存取權，請使用下列許可權搭配**myOrganization**的子類型。 例如，microsoft 目錄/myOrganization/basic/update。

如需一般詞彙子類型、許可權和屬性集平均值的說明，請參閱[自訂角色總覽](roles-custom-overview.md)。 下列是應用程式註冊特有的資訊。

### <a name="create-and-delete"></a>建立和刪除

有兩種許可權可以授與建立應用程式註冊的能力，每個都有不同的行為：

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft.directory/applications/createAsOwner

指派此許可權會導致建立者新增為所建立應用程式註冊的第一個擁有者，而所建立的應用程式註冊將計入建立者的250建立物件配額中。

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft.directory/applications/create

指派此許可權會導致建立者不會被新增為所建立應用程式註冊的第一個擁有者，而建立的應用程式註冊將不會算在建立者的250建立物件配額中。 請謹慎使用此權限，因為在達到目錄層級配額之前，受託人將可不受限地建立應用程式註冊。 如果同時指派這兩種權限，將優先使用此權限。

如果同時指派這兩個許可權，將會優先使用/create 許可權。 雖然/createAsOwner 許可權不會自動新增 creator 做為第一個擁有者，但在使用圖形 Api 或 PowerShell Cmdlet 時，可以在建立應用程式註冊期間指定擁有者。

建立許可權授與**新註冊**命令的存取權。

[這些許可權會授與新註冊入口網站命令的存取權](./media/roles-create-custom/new-custom-role.png)

有兩個許可權可授與刪除應用程式註冊的能力：

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

授與刪除應用程式註冊的能力，而不考慮子類型。也就是單一租使用者和多租使用者應用程式。

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft 目錄/應用程式. myOrganization/delete

授與刪除應用程式註冊的能力，限制只有您的組織或單一租使用者應用程式（myOrganization 子類型）中的帳戶才能存取。

![這些許可權會授與 [刪除應用程式註冊] 命令的存取權](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> 指派包含建立許可權的角色時，必須在目錄範圍中建立角色指派。 在資源範圍指派的 create 許可權不會授與建立應用程式註冊的能力。

### <a name="read"></a>讀取

根據預設，組織中的所有成員使用者都可以讀取應用程式註冊資訊。 不過，來賓使用者和應用程式服務主體則不能。 如果您打算將角色指派給來賓使用者或應用程式，您必須包含適當的 [讀取] 許可權。

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft 目錄/應用程式/allProperties/讀取

能夠讀取屬性以外的單一租使用者和多租使用者應用程式的所有屬性，而無法在任何情況下讀取，例如認證。

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>myOrganization/allProperties/read

授與的許可權與 microsoft 目錄/應用程式/allProperties/讀取相同，但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.directory/applications/owners/read

授與讀取單一租使用者和多租使用者應用程式上擁有者屬性的能力。 授與 [應用程式註冊擁有者] 頁面上所有欄位的存取權：

![此許可權可授與應用程式註冊擁有者頁面的存取權](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>microsoft 目錄/應用程式/標準/讀取

授與讀取標準應用程式註冊屬性的存取權。 這包括跨應用程式註冊頁面的屬性。

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>myOrganization/standard/read

授與 microsoft 的相同許可權。目錄/應用程式/標準/讀取，但僅適用于單一租使用者應用程式。

### <a name="update"></a>更新

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft 目錄/應用程式/allProperties/更新

能夠更新單一目錄和多目錄應用程式上的所有屬性。

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>myOrganization/allProperties/update

授與與 microsoft 目錄/應用程式/allProperties/更新相同的許可權，但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

能夠在單一目錄和多目錄應用程式上更新支援的帳戶類型（signInAudience）屬性。

![此許可權會在驗證頁面上授與應用程式註冊支援的帳戶類型屬性的存取權](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft 目錄/應用程式 myOrganization/物件/更新

授與 microsoft 目錄/應用程式/物件/更新的相同許可權，但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

能夠在單一租使用者和多租使用者應用程式上更新回復 URL、登出 URL、隱含流程和發行者網域屬性。 除了支援的帳戶類型以外，授與應用程式註冊驗證頁面上所有欄位的存取權：

![授與應用程式註冊驗證的存取權，但不支援帳戶類型](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft 目錄/應用程式. myOrganization/驗證/更新

授與的許可權與 microsoft 目錄/應用程式/驗證/更新相同，但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

能夠在單一租使用者和多租使用者應用程式上更新名稱、標誌、首頁 URL、服務條款 URL 及隱私權聲明 URL 屬性。 授與應用程式注冊商標頁面上所有欄位的存取權：

![此許可權可授與應用程式注冊商標頁面的存取權](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft 目錄/應用程式 myOrganization/基本/更新

授與的許可權與 microsoft 目錄/應用程式/基本/更新相同，但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

能夠在單一租使用者和多租使用者應用程式上更新憑證和用戶端秘密屬性。 授與應用程式註冊憑證 & 秘密頁面上所有欄位的存取權：

![此許可權會授與應用程式註冊憑證 & 秘密頁面的存取權](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>myOrganization/認證/更新

會授與 microsoft 目錄/應用程式/認證/更新相同的許可權，但僅適用于單一目錄應用程式。

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

能夠在單一租使用者和多租使用者上更新擁有者屬性。 授與 [應用程式註冊擁有者] 頁面上所有欄位的存取權：

![此許可權可授與應用程式註冊擁有者頁面的存取權](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>myOrganization/擁有者/更新

授與的許可權與 microsoft 目錄/應用程式/擁有者/更新相同，但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

能夠更新委派的許可權、應用程式許可權、授權的用戶端應用程式、所需的許可權，以及授與單一租使用者和多租使用者應用程式的同意屬性。 並未授與執行同意的能力。 授與應用程式註冊 API 許可權上所有欄位的存取權，並公開 API 頁面：

![此許可權可授與應用程式註冊 API 許可權頁面的存取權](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![此許可權會授與應用程式註冊公開 API 頁面的存取權](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>myOrganization/許可權/更新

授與 microsoft 目錄/應用程式/許可權/更新的相同許可權，但僅適用于單一租使用者應用程式。

## <a name="required-license-plan"></a>必要授權方案

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>後續步驟

- 使用[Azure 入口網站、Azure AD PowerShell 和圖形 API](roles-create-custom.md)來建立自訂角色
- [檢視自訂角色的指派](roles-view-assignments.md)
