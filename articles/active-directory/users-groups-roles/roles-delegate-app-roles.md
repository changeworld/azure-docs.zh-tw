---
title: 委派應用程式管理管理 perms-Azure AD |Microsoft Docs
description: 在 Azure Active Directory 中授與應用程式存取管理的許可權
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 251bc1c2277f9e43543f95c49d0b730a5a41c3d9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253035"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>在 Azure Active Directory 中委派應用程式註冊許可權

本文說明如何使用 Azure Active Directory （Azure AD）中的自訂角色所授與的許可權，來處理您的應用程式管理需求。 在 Azure AD 中，您可以透過下列方式委派應用程式建立和管理許可權：

- [限制誰可以建立應用程式](#restrict-who-can-create-applications)，以及管理他們所建立的應用程式。 根據預設，在 Azure AD 中，所有使用者都可以註冊應用程式註冊，並管理他們所建立之應用程式的所有層面。 這可限制為僅允許選取的人員擁有該許可權。
- [將一或多個擁有者指派給應用程式](#assign-application-owners)。 這是一種簡單的方式，讓某人能夠管理特定應用程式的 Azure AD 設定的所有層面。
- [指派內建的系統管理角色](#assign-built-in-application-admin-roles)，以授與存取權來管理所有應用程式的 Azure AD 中的設定。 這是建議的方法，讓 IT 專家能夠管理廣泛的應用程式設定許可權，而不需授與存取權來管理與應用程式設定無關之 Azure AD 的其他部分。
- [建立自訂角色](#create-and-assign-a-custom-role-preview)，以定義非常特定的許可權，並將其指派給某個使用者的單一應用程式範圍，以限制擁有者身分，或在目錄範圍（所有應用程式）上為受限的系統管理員。

請務必考慮使用上述其中一種方法來授與存取權，原因有兩個。 首先，委派執行系統管理工作的能力，可減少全域系統管理員的負擔。 其次，使用有限的許可權可改善您的安全性狀態，並降低未經授權存取的可能性。 有關於委派問題和一般指導方針的討論，請見[在 Azure Active Directory 中委派管理](roles-concept-delegation.md)。

## <a name="restrict-who-can-create-applications"></a>限制可以建立應用程式的人員

根據預設，在 Azure AD 中，所有使用者都可以註冊應用程式註冊，並管理他們所建立之應用程式的所有層面。 每個人也都能同意應用程式代表自己存取公司資料。 您可以選擇將全域交換器設定為 [否]，並將選取的使用者新增至應用程式開發人員角色，以選擇性地授與這些許可權。

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>若要停用建立應用程式註冊或同意應用程式的預設功能

1. 使用適用于您 Azure AD 組織中全域管理員角色的帳戶，登入您的 Azure AD 組織。
1. 設定下列其中一項或兩項：

    - 在 [您組織的 [使用者設定] 頁面](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)上，將 [**使用者可以註冊應用程式**] 設定設為 [否]。 這會停用使用者建立應用程式註冊的預設功能。
    - 在 [ [企業應用程式的使用者設定](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)] 上，將 [**使用者可同意應用程式代表自己存取公司資料**] 設定設為 [否]。 這會停用使用者同意應用程式代表自己存取公司資料的預設功能。

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>授與個別許可權，以在預設功能停用時建立和同意應用程式

指派應用程式開發人員角色，以在 [**使用者可以註冊應用程式**] 設定設為 [否] 時，授與建立應用程式註冊的能力。 此角色也會在**使用者可同意應用程式代表自己存取公司資料**時，授與許可權以代表自己的同意設定為 [否]。 作為系統行為，當使用者建立新的應用程式註冊時，會自動將它們新增為第一個擁有者。 擁有權許可權可讓使用者管理他們所擁有之應用程式註冊或企業應用程式的所有層面。

## <a name="assign-application-owners"></a>指派應用程式擁有者

指派擁有者是一種簡單的方式，可讓您針對特定應用程式註冊或企業應用程式，授與管理 Azure AD 設定之所有層面的能力。 作為系統行為，當使用者建立新的應用程式註冊時，會自動將其新增為第一個擁有者。 擁有權許可權可讓使用者管理他們所擁有之應用程式註冊或企業應用程式的所有層面。 可以移除原始擁有者，也可以新增其他擁有者。

### <a name="enterprise-application-owners"></a>企業應用程式擁有者

身為擁有者，使用者可以管理企業應用程式的組織特定設定，例如單一登入設定、布建和使用者指派。 擁有者也可以新增或移除其他擁有者。 與全域管理員不同的是，擁有者只能管理他們所擁有的企業應用程式。

在某些情況下，從應用程式庫建立的企業應用程式包括企業應用程式和應用程式註冊。 當此條件為 true 時，將擁有者新增至企業應用程式會自動將擁有者新增至對應的應用程式註冊，做為擁有者。

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>將擁有者指派給企業應用程式

1. 使用適用于組織應用程式系統管理員或雲端應用程式管理員的帳戶，登入[您的 Azure AD 組織](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 。
1. 在組織 的 [ [應用程式註冊] 頁面](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)上，選取應用程式以開啟應用程式的 [總覽] 頁面。
1. 選取 [擁有者] 以查看應用程式的擁有者清單。
1. 選取 [新增]，以選取一或多個要新增至應用程式的擁有者。

> [!IMPORTANT]
> 使用者和服務主體可以是應用程式註冊的擁有者。 只有使用者可以是企業應用程式的擁有者。 群組無法指派為任何一個的擁有者。
>
> 擁有者可以將認證新增至應用程式，並使用這些認證來模擬應用程式的身分識別。 應用程式可能會有比擁有者更多的許可權，因此會提升擁有者以使用者或服務主體存取權的許可權。 應用程式擁有者可能會在模擬應用程式時建立或更新使用者或其他物件，端視應用程式的許可權而定。

## <a name="assign-built-in-application-admin-roles"></a>指派內建應用程式系統管理員角色

Azure AD 具有一組內建的系統管理員角色，可授與存取權來管理所有應用程式 Azure AD 中的設定。 建議使用這些角色來授與 IT 專家存取權，以管理廣泛的應用程式設定許可權，而不授予存取權來管理與應用程式設定無關之 Azure AD 的其他部分。

- 應用程式系統管理員：此角色中的使用者可以建立及管理企業應用程式、應用程式註冊和應用程式 proxy 設定的所有層面。 此角色也會授與同意委派許可權的能力，以及不含 Microsoft Graph 的應用程式許可權。 在建立新的應用程式註冊或企業應用程式時，不會將指派給此角色的使用者新增為擁有者。
- 雲端應用程式系統管理員：此角色中的使用者具有與應用程式系統管理員角色相同的許可權，但不包括管理應用程式 proxy 的能力。 在建立新的應用程式註冊或企業應用程式時，不會將指派給此角色的使用者新增為擁有者。

如需詳細資訊及查看這些角色的說明，請參閱 [可用的角色](directory-assign-admin-roles.md#available-roles)。

遵循將[角色指派給具有 Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)操作指南的使用者中的指示，來指派應用程式系統管理員或雲端應用程式系統管理員角色。

> [!IMPORTANT]
> 應用程式系統管理員和雲端應用程式系統管理員可以將認證新增至應用程式，並使用這些認證來模擬應用程式的身分識別。 應用程式可能具有許可權，而這是系統管理員角色許可權的權限提高。 根據應用程式的許可權，此角色中的系統管理員可能會在模擬應用程式時建立或更新使用者或其他物件。
> 而這些角色都不會授與管理條件式存取設定的能力。

## <a name="create-and-assign-a-custom-role-preview"></a>建立並指派自訂角色（預覽）

建立自訂角色和指派自訂角色是不同的步驟：

- [建立自訂*角色定義*](roles-create-custom.md) ，並[從預設清單新增其許可權](roles-custom-available-permissions.md)。 這些是內建角色中所使用的相同許可權。
- [建立*角色指派*](roles-assign-powershell.md)來指派自訂角色。

這種區隔可讓您建立單一角色定義，然後在不同的*範圍*指派多次。 您可以在整個組織範圍中指派自訂角色，如果單一 Azure AD 物件，則可以在範圍中指派。 物件範圍的範例是單一應用程式註冊。 使用不同的範圍，可以將相同的角色定義指派給組織中的所有應用程式註冊 Sally，然後只 Naveen Contoso 費用報表應用程式註冊。

建立和使用自訂角色來委派應用程式管理的秘訣：
- 自訂角色只會在 Azure AD 入口網站的最新應用程式註冊 blade 中授與存取權。 它們不會在繼承應用程式註冊 blade 中授與存取權。
- 當 [限制對 Azure AD 系統管理入口網站的存取] 使用者設定設為 [是] 時，自訂角色不會授與 Azure AD 入口網站的存取權。
- 使用者只有在應用程式註冊頁面上的 [所有應用程式] 索引標籤中才會顯示使用角色指派的存取權應用程式註冊。 它們不會顯示在 [擁有的應用程式] 索引標籤中。

如需有關自訂角色之基本概念的詳細資訊，請參閱[自訂角色總覽](roles-custom-overview.md)，以及如何[建立自訂角色](roles-create-custom.md)和如何[指派角色](roles-assign-powershell.md)。

## <a name="next-steps"></a>後續步驟

- [應用程式註冊子類型和許可權](roles-custom-available-permissions.md)
- [Azure AD 系統管理員角色參考](directory-assign-admin-roles.md)
