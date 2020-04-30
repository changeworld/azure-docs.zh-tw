---
title: Azure Active Directory 中的自訂系統管理員角色 |Microsoft Docs
description: 預覽用於委派身分識別管理的自訂 Azure AD 角色。 管理 Azure 入口網站、PowerShell 或圖形 API 中的 Azure 角色。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae244d93d679199aaa0bd08891cd34d4ca3a2ddc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085105"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Azure Active Directory 中的自訂系統管理員角色（預覽）

本文說明如何使用角色型存取控制和資源範圍，瞭解 Azure Active Directory （Azure AD）中 Azure AD 自訂角色。 自訂 Azure AD 角色會呈現[內建角色](directory-assign-admin-roles.md)的基礎許可權，讓您可以建立和組織自己的自訂角色。 這種方法可讓您在需要時，以比內建角色更細微的方式來授與存取權。 第一版的 Azure AD 自訂角色包含建立角色以指派管理應用程式註冊許可權的能力。 經過一段時間，將會新增組織資源（例如企業應用程式、使用者和裝置）的其他許可權。  

此外，除了更傳統的組織範圍指派以外，Azure AD 自訂角色還支援以每個資源為基礎的指派。 這種方法可讓您授與存取權來管理某些資源（例如，一個應用程式註冊），而不需提供所有資源的存取權（所有應用程式註冊）。

Azure AD 以角色為基礎的存取控制是 Azure AD 的公開預覽功能，適用于任何付費 Azure AD 授權方案。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="understand-azure-ad-role-based-access-control"></a>瞭解 Azure AD 以角色為基礎的存取控制

使用自訂 Azure AD 角色來授與許可權是一個包含兩個步驟的程式，其中牽涉到建立自訂角色定義，然後使用角色指派來指派它。 自訂角色定義是您從預設清單新增的許可權集合。 這些許可權是內建角色中所使用的相同許可權。  

建立角色定義之後，您可以藉由建立角色指派，將它指派給使用者。 角色指派會授與使用者在指定範圍之角色定義中的許可權。 這個兩個步驟的程式可讓您建立單一角色定義，並在不同範圍指派多次。 「範圍」（scope）會定義角色成員可存取的一組 Azure AD 資源。 最常見的範圍是全公司（全組織）範圍。 您可以在整個組織範圍內指派自訂角色，這表示角色成員具有組織中所有資源的角色許可權。 您也可以在物件範圍指派自訂角色。 物件範圍的範例就是單一應用程式。 同一個角色可以指派給組織中所有應用程式的一個使用者，然後指派給另一個僅具有 Contoso Expense Reports 應用程式範圍的使用者。  

Azure AD 內建和自訂角色會在類似于[Azure 角色型存取控制](../../role-based-access-control/overview.md)的概念上運作。 [這兩個以角色為基礎的存取控制系統之間的差異](../../role-based-access-control/rbac-and-directory-admin-roles.md)在於，azure RBAC 會使用 Azure 資源管理來控制對 Azure 資源（例如虛擬機器或儲存體）的存取，並 Azure AD 自訂角色使用圖形 API 來控制 Azure AD 資源的存取權。 這兩個系統都利用角色定義和角色指派的概念。

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Azure AD 如何判斷使用者是否有權存取資源

以下是 Azure AD 用來判斷您是否有管理資源存取權的高階步驟。 使用此資訊對存取問題進行疑難排解。

1. 使用者（或服務主體）會取得 Microsoft Graph 或 Azure AD Graph 端點的權杖。

1. 使用者使用發行的權杖，透過 Microsoft Graph 或 Azure AD 圖表，進行 API 呼叫，以 Azure Active Directory （Azure AD）。

1. 視情況而定，Azure AD 會採取下列其中一項動作：

    - 根據使用者存取權杖中的[wids](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)宣告，評估使用者的角色成員資格。
    - 抓取對使用者套用的所有角色指派，不論是直接或透過群組成員資格，到執行動作的資源。

1. Azure AD 決定 API 呼叫中的動作是否包含在使用者對此資源所擁有的角色中。
1. 如果使用者在要求範圍內不具含有該動作的角色，則不會授與存取權。 否則會授與存取權。

### <a name="role-assignments"></a>角色指派

角色指派是一個物件，可將角色定義附加至特定範圍的使用者，以授與 Azure AD 資源存取權。 建立角色指派可授與存取權，而移除角色指派則可撤銷存取權。 在其核心中，角色指派是由三個元素所組成：

- 使用者（在 Azure Active Directory 中具有使用者設定檔的個人）
- 角色定義
- 資源範圍

您可以使用 Azure 入口網站、Azure AD PowerShell 或圖形 API 來[建立角色指派](roles-create-custom.md)。 您也可以[查看自訂角色的指派](roles-view-assignments.md#view-the-assignments-of-a-role)。

下圖顯示角色指派的範例。 在此範例中，已將 Contoso Widget Builder 應用程式註冊範圍內的應用程式註冊管理員自訂角色指派給 Chris 環保。 指派只會授與 Chris 此特定應用程式註冊的應用程式註冊系統管理員角色的許可權。

![角色指派是如何強制執行許可權，而且有三個部分](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>安全性主體

安全性主體代表要獲指派 Azure AD 資源存取權的使用者。 *使用者*是 Azure Active Directory 中具有使用者設定檔的個人。

### <a name="role"></a>[角色]

角色定義（或角色）是許可權的集合。 角色定義會列出可在 Azure AD 資源上執行的作業，例如建立、讀取、更新和刪除。 Azure AD 中有兩種角色類型：

- 由 Microsoft 建立且無法變更的內建角色。
- 您的組織所建立及管理的自訂角色。

### <a name="scope"></a>影響範圍

範圍是允許的動作對特定 Azure AD 資源的限制，做為角色指派的一部分。 當您指派角色時，您可以指定範圍來限制系統管理員對特定資源的存取權。 例如，如果您想要為開發人員授與自訂角色，但只是為了管理特定的應用程式註冊，您可以在角色指派中包含特定的應用程式註冊作為範圍。

  > [!Note]
  > 您可以在目錄範圍和資源範圍中指派自訂角色。 這些使用者尚未在系統管理單位範圍中指派。
  > 可以在目錄範圍中指派內建角色，而在某些情況下，可以在管理單位範圍中指派。 它們尚未指派在 Azure AD 資源範圍。

## <a name="required-license-plan"></a>必要的授權方案

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>後續步驟

- 使用[Azure 入口網站、Azure AD PowerShell 和圖形 API](roles-create-custom.md)來建立自訂角色指派
- [查看自訂角色的指派](roles-view-assignments.md#view-assignments-of-single-application-scope)
