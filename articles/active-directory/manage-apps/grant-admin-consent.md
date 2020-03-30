---
title: 授予租戶範圍的管理同意應用程式 - Azure AD
description: 瞭解如何向應用程式授予租戶範圍的同意，以便在登錄應用程式時不會提示最終使用者獲得同意。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480914"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>授予租戶範圍管理員對應用程式的同意

瞭解如何通過向應用程式授予租戶範圍管理員同意來簡化使用者體驗。 本文提供了實現此目的的不同方法。 這些方法適用於 Azure Active Directory (Azure AD) 租用戶中的所有使用者。

如需同意應用程式的詳細資訊，請參閱 [Azure Active Directory 同意架構](../develop/consent-framework.md)。

## <a name="prerequisites"></a>Prerequisites

授予租戶範圍的管理員同意要求您以[全域管理員](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)、[應用程式管理員](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)或[雲應用程式管理員](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)的身份登錄。

> [!IMPORTANT]
> 當應用程式被授予租戶範圍的管理同意時，所有使用者將能夠登錄到應用，除非已配置為需要使用者分配。 要限制哪些使用者可以登錄到應用程式，需要使用者分配，然後將使用者或組分配給應用程式。 如需詳細資訊，請參閱[指派使用者和群組的方法](methods-for-assigning-users-and-groups.md)。

> [!WARNING]
> 向應用程式授予租戶範圍的管理員同意將授予應用和應用的發行者對組織資料的存取權限。 在授予同意之前，請仔細查看應用程式請求的許可權。

## <a name="grant-admin-consent-from-the-azure-portal"></a>從 Azure 門戶授予管理員同意

### <a name="grant-admin-consent-in-enterprise-apps"></a>在企業應用中授予管理員同意

如果應用程式已在租戶中預配，則可以通過*企業應用程式*授予租戶範圍的管理同意。 例如，如果至少有一個使用者已同意該應用程式，則可以在租戶中預配應用。 有關詳細資訊，請參閱[如何以及為什麼將應用程式添加到 Azure 活動目錄](../develop/active-directory-how-applications-are-added.md)。

要授予租戶範圍管理員對**企業應用程式中**列出的應用的同意：

1. 以[全域管理員](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)、[應用程式管理員](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)或[雲應用程式管理員](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)的身份登錄到[Azure 門戶](https://portal.azure.com)。
2. 選擇**Azure 活動目錄**，然後**選擇企業應用程式**。
3. 選擇要授予租戶範圍管理員同意的應用程式。
4. 選擇**許可權**，然後按一下 **"授予管理員同意**"。
5. 仔細檢查應用程式所需的許可權。
6. 如果您同意應用程式所需的許可權，請授予同意。 如果沒有，請按一下 **"取消"** 或關閉視窗。

### <a name="grant-admin-consent-in-app-registrations"></a>在應用註冊中授予管理員同意

對於組織已開發的應用程式，或直接在 Azure AD 租戶中註冊的應用程式，還可以從 Azure 門戶中的應用**註冊**中授予租戶範圍的管理員同意。

授予**應用註冊**租戶範圍管理員同意：

1. 以[全域管理員](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)、[應用程式管理員](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)或[雲應用程式管理員](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)的身份登錄到[Azure 門戶](https://portal.azure.com)。
2. 選擇**Azure 活動目錄**，然後**應用註冊**。
3. 選擇要授予租戶範圍管理員同意的應用程式。
4. 選擇**API 許可權**，然後按一下 **"授予管理員同意**"。
5. 仔細檢查應用程式所需的許可權。
6. 如果您同意應用程式所需的許可權，請授予同意。 如果沒有，請按一下 **"取消"** 或關閉視窗。

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>構造用於授予租戶範圍管理員同意的 URL

使用上述任一方法授予租戶範圍的管理員同意時，將從 Azure 門戶打開一個視窗，提示租戶範圍的管理員同意。 如果您知道應用程式的用戶端 ID（也稱為應用程式 ID），則可以構建相同的 URL 以授予租戶範圍的管理員同意。

租戶範圍的管理同意 URL 遵循以下格式：

    https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}

其中：

* `{client-id}`是應用程式的用戶端 ID（也稱為應用 ID）。
* `{tenant-id}`是組織的租戶 ID 或任何經過驗證的功能變數名稱。

與往常一樣，在授予同意之前，請仔細查看應用程式請求的許可權。

## <a name="next-steps"></a>後續步驟

[配置最終使用者如何同意應用程式](configure-user-consent.md)

[建構管理員同意工作流](configure-admin-consent-workflow.md)

[Microsoft 身份平臺中的許可權和同意](../develop/active-directory-v2-scopes.md)

[堆疊溢位上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
