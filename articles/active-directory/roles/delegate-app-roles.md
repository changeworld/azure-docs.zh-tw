---
title: 委派應用程式管理系統管理員許可權-Azure AD |Microsoft Docs
description: 在 Azure Active Directory 中授與應用程式存取管理的許可權
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e36a29048d97798c2e1621fbdc957bde51b5a383
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740596"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>在 Azure Active Directory 中委派應用程式註冊許可權

本文說明如何使用 Azure Active Directory (Azure AD) 中自訂角色所授與的許可權，來滿足您的應用程式管理需求。 在 Azure AD 中，您可以透過下列方式委派應用程式建立和管理許可權：

- [限制誰可以建立應用程式](#restrict-who-can-create-applications) 和管理他們所建立的應用程式。 根據預設，在 Azure AD 中，所有使用者都可以註冊應用程式註冊，以及管理其所建立之應用程式的所有層面。 這可以限制為只允許所選的人員該許可權。
- [將一或多個擁有者指派給應用程式](#assign-application-owners)。 這是一種簡單的方式，可授與某人管理特定應用程式之 Azure AD 設定的所有層面的能力。
- [指派內建的系統管理角色](#assign-built-in-application-admin-roles) ，以授與存取權來管理所有應用程式 Azure AD 中的設定。 這是建議的方法，將管理廣泛的應用程式設定許可權，而不授與存取權來管理與應用程式設定無關 Azure AD 部分的存取權。
- 建立定義非常特定許可權的[自訂角色](#create-and-assign-a-custom-role-preview)，並將其指派給某個人，以作為受限擁有者的單一應用程式範圍，或在目錄範圍 (所有應用程式) 為受限的系統管理員。

基於兩個原因，請務必考慮使用上述其中一種方法來授與存取權。 首先，委派執行系統管理工作的能力可降低全域系統管理員的負擔。 其次，使用有限許可權可改善您的安全性狀態，並降低未經授權存取的可能性。 有關於委派問題和一般指導方針的討論，請見[在 Azure Active Directory 中委派管理](concept-delegation.md)。

## <a name="restrict-who-can-create-applications"></a>限制誰可以建立應用程式

根據預設，在 Azure AD 中，所有使用者都可以註冊應用程式註冊，以及管理其所建立之應用程式的所有層面。 每個人也都能夠同意代表他們存取公司資料的應用程式。 您可以選擇選擇性地授與這些許可權，方法是將全域參數設定為 [否]，並將選取的使用者新增至應用程式開發人員角色。

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>停用建立應用程式註冊或同意應用程式的預設功能

1. 使用符合您 Azure AD 組織內全域管理員角色資格的帳戶登入 Azure AD 組織。
1. 設定下列其中一項或兩項：

    - 在 [您組織的 [使用者設定] 頁面](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)上，將 [ **使用者可以註冊應用程式** ] 設定設為 [否]。 這將會停用使用者建立應用程式註冊的預設功能。
    - 在 [ [企業應用程式](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)] 的 [使用者設定] 上，將 [ **使用者可同意應用程式代表其存取公司資料** ] 設定設為 [否]。 這將會停用使用者同意應用程式代表其存取公司資料的預設功能。

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>在停用預設功能時，授與個別許可權以建立和同意應用程式

指派應用程式開發人員角色，以授與在 **使用者可註冊應用程式** 設定設為 [否] 時建立應用程式註冊的能力。 當 **使用者同意應用程式代表其存取公司資料** 時，此角色也會授與自行同意的許可權設定為 [否]。 系統行為是，當使用者建立新的應用程式註冊時，系統會自動將他們新增為第一個擁有者。 擁有權許可權可讓使用者管理其所擁有之應用程式註冊或企業應用程式的所有層面。

## <a name="assign-application-owners"></a>指派應用程式擁有者

指派擁有者是一種簡單的方式，可為特定應用程式註冊或企業應用程式授與管理 Azure AD 設定之所有層面的能力。 系統行為是，當使用者建立新的應用程式註冊時，系統會自動將他們新增為第一個擁有者。 擁有權許可權可讓使用者管理其所擁有之應用程式註冊或企業應用程式的所有層面。 您可以移除原始擁有者，也可以新增其他擁有者。

### <a name="enterprise-application-owners"></a>企業應用程式擁有者

作為擁有者，使用者可以管理企業應用程式的組織特定設定，例如單一登入設定、布建和使用者指派。 擁有者也可以新增或移除其他擁有者。 不同于全域管理員，擁有者只能管理他們擁有的企業應用程式。

在某些情況下，從應用程式庫建立的企業應用程式包含企業應用程式和應用程式註冊。 若為 true，則將擁有者新增至企業應用程式時，會自動將擁有者新增至對應的應用程式註冊作為擁有者。

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>將擁有者指派給企業應用程式

1. 使用適合組織應用程式系統管理員或雲端應用程式管理員的帳戶，登入 [您的 Azure AD 組織](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 。
1. 在組織的 [ [應用程式註冊] 頁面](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) 上，選取應用程式以開啟應用程式的 [總覽] 頁面。
1. 選取 [擁有者] 以查看應用程式的擁有者清單。
1. 選取 [新增]，以選取一或多個要新增至應用程式的擁有者。

> [!IMPORTANT]
> 使用者和服務主體可以是應用程式註冊的擁有者。 只有使用者可以是企業應用程式的擁有者。 無法將群組指派為其中一個的擁有者。
>
> 擁有者可以將認證新增至應用程式，並使用這些認證來模擬應用程式的身分識別。 應用程式可能會有比擁有者更多的許可權，因此在擁有者可以存取做為使用者或服務主體的情況下，會成為權限提高許可權。 應用程式擁有者可能會在模擬應用程式時建立或更新使用者或其他物件，視應用程式的許可權而定。

## <a name="assign-built-in-application-admin-roles"></a>指派內建的應用程式管理員角色

Azure AD 有一組內建的系統管理員角色，可授與存取權來管理所有應用程式 Azure AD 中的設定。 建議您將這些角色授與 IT 專家存取權，以管理廣泛的應用程式設定許可權，而不需授與存取權來管理與應用程式設定無關的其他 Azure AD 部分。

- 應用程式系統管理員：此角色中的使用者可以建立和管理企業應用程式、應用程式註冊和應用程式 Proxy 設定的所有層面。 此角色也會授與同意委派許可權的能力，以及不含 Microsoft Graph 的應用程式許可權。 建立新的應用程式註冊或企業應用程式時，獲指派此角色的使用者不會新增為擁有者。
- 雲端應用程式系統管理員：此角色中的使用者具有與應用程式系統管理員角色相同的權限，但不包括管理應用程式 Proxy 的能力。 建立新的應用程式註冊或企業應用程式時，獲指派此角色的使用者不會新增為擁有者。

如需詳細資訊和查看這些角色的描述，請參閱 [可用的角色](permissions-reference.md#available-roles)。

遵循將 [角色指派給使用者 Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md) 操作指南中的指示，指派應用程式系統管理員或雲端應用程式系統管理員角色。

> [!IMPORTANT]
> 應用程式系統管理員和雲端應用程式系統管理員可以將認證新增至應用程式，並使用這些認證來模擬應用程式的身分識別。 應用程式的許可權可能會比系統管理員角色的許可權更特權。 根據應用程式的許可權，此角色的系統管理員可能會在模擬應用程式時建立或更新使用者或其他物件。
> 而這些角色都不會授與管理條件式存取設定的能力。

## <a name="create-and-assign-a-custom-role-preview"></a>建立並指派自訂角色 (預覽版) 

建立自訂角色和指派自訂角色是個別的步驟：

- [建立自訂 *角色定義*](custom-create.md) ，並 [從預設清單新增許可權](custom-available-permissions.md)。 這些是內建角色中所使用的相同許可權。
- [建立 *角色指派*](custom-assign-powershell.md) 以指派自訂角色。

這種分隔可讓您建立單一角色定義，然後在不同的 *範圍* 內多次指派。 您可以在全組織範圍指派自訂角色，也可以在單一 Azure AD 物件的範圍內指派自訂角色。 物件範圍的範例為單一應用程式註冊。 使用不同的範圍，可以將相同的角色定義指派給組織中所有應用程式註冊的 Sally，然後只 Naveen Contoso Expense Reports 應用程式註冊。

建立和使用自訂角色來委派應用程式管理時的秘訣：
- 自訂角色只會在 Azure AD 入口網站的最新應用程式註冊 blade 中授與存取權。 他們不會在繼承應用程式註冊 blade 中授與存取權。
- 當「限制存取 Azure AD 管理入口網站」使用者設定設為 [是] 時，自訂角色並不會授與 Azure AD 入口網站的存取權。
- 應用程式註冊使用者只能在 [應用程式註冊] 頁面上的 [所有應用程式] 索引標籤中顯示使用角色指派的存取權。 這些應用程式不會顯示在 [擁有的應用程式] 索引標籤中。

如需自訂角色基本概念的詳細資訊，請參閱 [自訂角色總覽](custom-overview.md)，以及如何 [建立自訂角色](custom-create.md) 以及如何 [指派角色](custom-assign-powershell.md)。

## <a name="next-steps"></a>後續步驟

- [應用程式註冊子類型和許可權](custom-available-permissions.md)
- [Azure AD 系統管理員角色參考](permissions-reference.md)
