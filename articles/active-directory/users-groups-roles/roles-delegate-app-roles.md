---
title: 委託應用程式管理管理滲透 - Azure AD |微軟文檔
description: 授予 Azure 活動目錄中的應用程式訪問管理許可權
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253035"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>在 Azure 活動目錄中委派應用註冊許可權

本文介紹如何使用 Azure 活動目錄 （Azure AD） 中的自訂角色授予的許可權來滿足應用程式管理需求。 在 Azure AD 中，可以通過以下方式委派應用程式創建和管理許可權：

- [限制誰可以創建應用程式](#restrict-who-can-create-applications)並管理他們創建的應用程式。 預設情況下，在 Azure AD 中，所有使用者都可以註冊應用程式註冊並管理他們創建的應用程式的所有方面。 這可以限制為僅允許選定的人員獲得該許可權。
- [為應用程式分配一個或多個擁有者](#assign-application-owners)。 這是一種簡單的方法，可以授予某人管理特定應用程式 Azure AD 配置的所有方面的能力。
- [分配一個內置管理角色](#assign-built-in-application-admin-roles)，該角色授予管理 Azure AD 中所有應用程式佈建的存取權限。 這是授予 IT 專家管理廣泛應用程式佈建許可權的推薦方式，而無需授予管理與應用程式佈建無關的 Azure AD 其他部分的許可權。
- [創建自訂角色](#create-and-assign-a-custom-role-preview)，定義非常特定的許可權，並將其分配給某人，或者將其分配給單個應用程式的範圍，作為有限擁有者，或在目錄作用域（所有應用程式）中作為有限管理員。

考慮使用上述方法之一授予存取權限非常重要，原因有二。 首先，委派執行管理工作的能力可降低全域管理員開銷。 其次，使用有限的許可權可改善您的安全狀態，並降低未經授權的訪問的可能性。 有關於委派問題和一般指導方針的討論，請見[在 Azure Active Directory 中委派管理](roles-concept-delegation.md)。

## <a name="restrict-who-can-create-applications"></a>限制誰可以創建應用程式

預設情況下，在 Azure AD 中，所有使用者都可以註冊應用程式註冊並管理他們創建的應用程式的所有方面。 每個人都能夠同意代表他們訪問公司資料的應用。 通過將全域交換器設置為"否"並將所選使用者添加到應用程式開發人員角色，您可以選擇有選擇地授予這些許可權。

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>禁用創建應用程式註冊或同意應用程式的預設功能

1. 使用符合 Azure AD 組織中全域管理員角色的帳戶登錄到 Azure AD 組織。
1. 設置以下一個或兩個：

    - 在 [組織的"使用者設置"頁上](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)，將 **"使用者可以註冊應用程式**"設置為"否"。 這將禁用使用者創建應用程式註冊的預設功能。
    - 在 [企業應用程式的使用者設置](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)上，將**使用者可以同意應用程式代表其訪問公司資料**設置為"否"。 這將禁用使用者同意代表他們訪問公司資料的應用程式的預設能力。

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>在禁用預設功能時授予創建和同意應用程式的單獨許可權

分配應用程式開發人員角色，以授予在 **"使用者可以註冊應用程式**"設置為"否"時創建應用程式註冊的能力。 當使用者**可以同意代表其訪問公司資料的應用**設置為"否"時，此角色還會授予代表自己的同意許可權。 作為系統行為，當使用者創建新的應用程式註冊時，它們會自動添加為第一個擁有者。 擁有權許可權使使用者可以管理他們擁有的應用程式註冊或企業應用程式的所有方面。

## <a name="assign-application-owners"></a>分配應用程式擁有者

分配擁有者是授予管理特定應用程式註冊或企業應用程式 Azure AD 配置的所有方面的能力的一種簡單方法。 作為系統行為，當使用者創建新的應用程式註冊時，它們將自動添加為第一個擁有者。 擁有權許可權使使用者可以管理他們擁有的應用程式註冊或企業應用程式的所有方面。 可以刪除原始擁有者，並添加其他擁有者。

### <a name="enterprise-application-owners"></a>企業應用程式擁有者

作為擁有者，使用者可以管理企業應用程式的特定于組織的配置，例如單一登入配置、預配和使用者分配。 擁有者也可以新增或移除其他擁有者。 與全域管理員不同，擁有者只能管理他們擁有的企業應用程式。

在某些情況下，從應用程式庫創建的企業應用程式包括企業應用程式和應用程式註冊。 如果為 true，則將擁有者添加到企業應用程式中會自動將擁有者添加到相應的應用程式註冊中作為擁有者。

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>將擁有者分配給企業應用程式

1. 使用符合組織應用程式管理員或雲應用程式管理員資格的帳戶登錄到[Azure AD 組織](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 。
1. 在組織的 ["應用註冊"頁上](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) ，選擇一個應用以打開應用的"概述"頁。
1. 選取 [擁有者]**** 以查看應用程式的擁有者清單。
1. 選取 [新增]****，以選取一或多個要新增至應用程式的擁有者。

> [!IMPORTANT]
> 使用者和服務主體可以是應用程式註冊的擁有者。 只有使用者才能成為企業應用程式的擁有者。 不能將組指定為任的擁有者。
>
> 擁有者可以將憑據添加到應用程式，並使用這些憑據來類比應用程式的標識。 應用程式可能比擁有者擁有更多的許可權，因此，對擁有者作為使用者或服務主體有權訪問的許可權將提升。 應用程式擁有者在類比應用程式時可能會創建或更新使用者或其他物件，具體取決於應用程式的許可權。

## <a name="assign-built-in-application-admin-roles"></a>分配內置應用程式管理員角色

Azure AD 具有一組內置管理角色，用於授予所有應用程式在 Azure AD 中管理配置的許可權。 這些角色是授予 IT 專家管理廣泛應用程式佈建許可權的推薦方式，而無需授予管理與應用程式佈建無關的 Azure AD 其他部分的許可權。

- 應用程式系統管理員：此角色中的使用者可以建立和管理企業應用程式、應用程式註冊和應用程式 Proxy 設定的所有層面。 此角色還授予同意委派許可權的許可權的許可權以及應用程式許可權（不包括 Microsoft Graph）。 創建新應用程式註冊或企業應用程式時，分配給此角色的使用者不會添加為擁有者。
- 雲端應用程式系統管理員：此角色中的使用者具有與應用程式系統管理員角色相同的權限，但不包括管理應用程式 Proxy 的能力。 創建新應用程式註冊或企業應用程式時，分配給此角色的使用者不會添加為擁有者。

有關詳細資訊並查看這些角色的說明，請參閱 [可用角色](directory-assign-admin-roles.md#available-roles)。

按照"[將角色指派給具有 Azure 活動目錄操作指南"的使用者](../fundamentals/active-directory-users-assign-role-azure-portal.md)的說明進行操作，以分配應用程式管理員或雲應用程式管理員角色。

> [!IMPORTANT]
> 應用程式管理員和雲應用程式管理員可以將憑據添加到應用程式，並使用這些憑據來類比應用程式的標識。 應用程式可能具有的許可權，這些許可權是管理員角色許可權的提升。 此角色的管理員可能會在類比應用程式時創建或更新使用者或其他物件，具體取決於應用程式的許可權。
> 而這些角色都不會授與管理條件式存取設定的能力。

## <a name="create-and-assign-a-custom-role-preview"></a>創建和分配自訂角色（預覽）

創建自訂角色和分配自訂角色是單獨的步驟：

- [創建自訂*角色定義*](roles-create-custom.md)並從[預設清單中向其添加許可權](roles-custom-available-permissions.md)。 這些許可權與內置角色中使用的許可權相同。
- [創建*角色指派*](roles-assign-powershell.md)以分配自訂角色。

這種分離允許您創建單個角色定義，然後在不同的*作用域*中多次分配它。 可以在組織範圍分配自訂角色，也可以在作用域（如果單個 Azure AD 物件）中分配該角色。 物件作用域的一個示例是單個應用註冊。 使用不同的作用域，可以將相同的角色定義分配給 Sally 組織中的所有應用註冊，然後僅通過 Contoso 支出報表應用註冊分配給 Naveen。

創建和使用自訂角色進行委派應用程式管理的提示：
- 自訂角色僅在 Azure AD 門戶的最新版本註冊邊欄選項卡中授予存取權限。 它們不授予舊版應用註冊刀片中的存取權限。
- 當"限制對 Azure AD 監管中心的訪問"使用者設置設置為"是"時，自訂角色不會授予對 Azure AD 門戶的存取權限。
- 使用者可以使用角色指派的應用註冊僅在應用註冊頁上的"所有應用程式"選項卡中顯示。 它們不會顯示在"擁有的應用程式"選項卡中。

有關自訂角色基礎知識的詳細資訊，請參閱[自訂角色概述](roles-custom-overview.md)，以及如何[創建自訂角色](roles-create-custom.md)以及如何[分配角色](roles-assign-powershell.md)。

## <a name="next-steps"></a>後續步驟

- [應用程式註冊子類型和許可權](roles-custom-available-permissions.md)
- [Azure AD 系統管理員角色參考](directory-assign-admin-roles.md)
