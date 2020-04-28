---
title: 將全租使用者系統管理員同意授與應用程式-Azure AD
description: 瞭解如何將整個租使用者同意授與應用程式，以便在登入應用程式時不會提示使用者同意。
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: c515fef4997720435c64bd5f3ae7b18f8921fc5d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75480914"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>對應用程式授與全租使用者系統管理員同意

瞭解如何將租使用者系統管理員同意授與應用程式，以簡化使用者體驗。 本文提供不同的方式來達成此目的。 這些方法適用於 Azure Active Directory (Azure AD) 租用戶中的所有使用者。

如需同意應用程式的詳細資訊，請參閱 [Azure Active Directory 同意架構](../develop/consent-framework.md)。

## <a name="prerequisites"></a>Prerequisites

授與整個租使用者的系統管理員同意，需要您以[全域管理員](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)、[應用程式系統](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)管理員或[雲端應用程式系統管理員](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)的身分登入。

> [!IMPORTANT]
> 當應用程式被授與租使用者系統管理員同意後，所有使用者都將能夠登入應用程式，除非它已設定為需要使用者指派。 若要限制哪些使用者可以登入應用程式，需要使用者指派，然後將使用者或群組指派給應用程式。 如需詳細資訊，請參閱[指派使用者和群組的方法](methods-for-assigning-users-and-groups.md)。

> [!WARNING]
> 將租使用者的系統管理員同意授與應用程式，會將您組織資料的存取權授與應用程式和應用程式的發行者。 在授與同意之前，請仔細檢查應用程式要求的許可權。

## <a name="grant-admin-consent-from-the-azure-portal"></a>授與系統管理員同意 Azure 入口網站

### <a name="grant-admin-consent-in-enterprise-apps"></a>在企業應用程式中授與管理員同意

如果已在您的租使用者中布建應用程式，您可以透過*企業應用程式*授與整個租使用者的系統管理員同意。 例如，如果至少有一個使用者已同意應用程式，則可以在您的租使用者中布建應用程式。 如需詳細資訊，請參閱[如何和為何將應用程式新增至 Azure Active Directory](../develop/active-directory-how-applications-are-added.md)。

若要將全租使用者系統管理員同意授與**企業應用程式**中所列的應用程式：

1. 以[全域管理員](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)、[應用程式系統](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)管理員或[雲端應用程式系統管理員](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)的身分登入[Azure 入口網站](https://portal.azure.com)。
2. 選取 [ **Azure Active Directory** ] [**企業應用程式**]。
3. 選取您要授與整個租使用者系統管理員同意的應用程式。
4. 選取 [**許可權**]，然後按一下 **[授與系統管理員同意**]。
5. 請仔細檢查應用程式所需的許可權。
6. 如果您同意應用程式所需的許可權，請授與同意。 如果沒有，請按一下 [**取消**] 或關閉視窗。

### <a name="grant-admin-consent-in-app-registrations"></a>在應用程式註冊中授與系統管理員同意

對於貴組織已開發或直接在您的 Azure AD 租使用者中註冊的應用程式，您也可以從 Azure 入口網站中的**應用程式註冊**授與租使用者系統管理員的同意。

若要授與整個租使用者的系統管理員同意**應用程式註冊**：

1. 以[全域管理員](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)、[應用程式系統](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)管理員或[雲端應用程式系統管理員](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)的身分登入[Azure 入口網站](https://portal.azure.com)。
2. 選取**Azure Active Directory**然後**應用程式註冊**。
3. 選取您要授與整個租使用者系統管理員同意的應用程式。
4. 選取 [ **API 許可權**]，然後按一下 **[授與系統管理員同意**]。
5. 請仔細檢查應用程式所需的許可權。
6. 如果您同意應用程式所需的許可權，請授與同意。 如果沒有，請按一下 [**取消**] 或關閉視窗。

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>建立用來授與全租使用者系統管理員同意的 URL

使用上述任一方法授與整個租使用者系統管理員同意時，會從 [Azure 入口網站] 開啟一個視窗，提示您提供全租使用者系統管理員同意。 如果您知道應用程式的用戶端識別碼（也稱為應用程式識別碼），您可以建立相同的 URL 來授與整個租使用者的系統管理員同意。

整個租使用者的系統管理員同意 URL 會遵循下列格式：

    https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}

其中：

* `{client-id}`這是應用程式的用戶端識別碼（也稱為應用程式識別碼）。
* `{tenant-id}`是您組織的租使用者識別碼或任何已驗證的功能變數名稱。

一如往常，請仔細檢查應用程式要求的許可權，然後再授與同意。

## <a name="next-steps"></a>後續步驟

[設定終端使用者同意應用程式的方式](configure-user-consent.md)

[設定管理員同意工作流程](configure-admin-consent-workflow.md)

[Microsoft 身分識別平臺中的許可權和同意](../develop/active-directory-v2-scopes.md)

[StackOverflow 上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
