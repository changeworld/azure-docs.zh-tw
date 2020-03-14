---
title: 什麼是權利管理？ - Azure AD
description: 取得 Azure Active Directory 權利管理的總覽，以及如何使用它來管理內部和外部使用者對群組、應用程式和 SharePoint Online 網站的存取。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/10/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d1faf501aff8960a4b1961b34164be07b1d685d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261758"
---
# <a name="what-is-azure-ad-entitlement-management"></a>什麼是 Azure AD 權利管理？

Azure Active Directory （Azure AD）權利管理是一種身分[識別治理](identity-governance-overview.md)功能，可讓組織透過自動化存取要求工作流程、存取權指派、評論和到期時間，大規模地管理身分識別和存取生命週期。

組織中的員工需要存取各種群組、應用程式和網站，才能執行其工作。 管理此存取是一項挑戰，因為需求變更-新增了新的應用程式，或使用者需要額外的存取權限。  當您與外部組織共同作業時，此案例會變得更複雜-您可能不知道其他組織中的誰需要存取貴組織的資源，而且他們不知道您的組織使用哪些應用程式、群組或網站。

Azure AD 權利管理可協助您更有效率地管理內部使用者對群組、應用程式和 SharePoint Online 網站的存取權，也可以讓組織外部的使用者需要存取這些資源。

## <a name="why-use-entitlement-management"></a>為何要使用權利管理？

企業組織通常面臨的挑戰，就是管理員工對資源的存取，例如：

- 使用者可能不知道他們應該擁有哪些存取權，即使他們這麼做，他們可能難以找到適當的個人來核准他們的存取權
- 一旦使用者找到並接收資源的存取權，他們可能會因為商務目的而讓存取時間超過所需

這些問題對於需要來自另一個組織（例如來自供應鏈組織或其他商務合作夥伴的外部使用者）存取權的使用者而言是更複雜的。 例如：

- 任何人都不能知道其他組織目錄中的所有特定個人都能邀請他們
- 即使他們可以邀請這些使用者，組織中也不會有任何人記得以一致的方式管理所有使用者的存取權

Azure AD 權利管理有助於解決這些挑戰。  若要深入瞭解客戶如何使用 Azure AD 權利管理，您可以閱讀[Avanade 案例研究](https://aka.ms/AvanadeELMCase)和[Centrica 案例研究](https://aka.ms/CentricaELMCase)。  這段影片提供權利管理和其價值的總覽：

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>我可以使用權利管理來做什麼？

以下是一些權利管理的功能：

- 委派給非系統管理員建立存取套件的能力。 這些存取套件包含使用者可以要求的資源，而委派的存取套件管理員則可以定義原則，其中包含使用者可以要求的規則、必須核准其存取權，以及存取何時過期。
- 選取使用者可以要求存取權的已連線組織。  當不在您目錄中的使用者要求存取權，並已核准時，會自動邀請他們進入您的目錄並指派存取權。  當他們的存取權過期時，如果沒有其他存取套件指派，您目錄中的 B2B 帳戶就可以自動移除。

您可以從我們的教學課程開始，[以建立您的第一個存取套件](entitlement-management-access-package-first.md)。 您也可以閱讀[常見的案例](entitlement-management-scenarios.md)或觀賞影片，包括

- [如何在您的組織中部署 Azure AD 權利管理](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [如何監視及調整您使用 Azure AD 權利管理](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [如何在權利管理中委派](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>什麼是存取套件，以及我可以使用哪些資源來管理？

權利管理導入了 Azure AD*存取套件*的概念。 存取套件是所有資源的組合，其中包含使用者處理專案或執行其工作所需的存取權。 存取套件是用來管理內部員工的存取權，以及組織外部的使用者。

 以下是您可以使用 [權利管理] 來管理使用者存取權的資源類型：

- Azure AD 安全性群組的成員資格
- Office 365 群組和小組的成員資格
- 指派給 Azure AD 企業應用程式，包括 SaaS 應用程式和支援同盟/單一登入和/或布建的自訂整合應用程式
- SharePoint Online 網站的成員資格

您也可以控制依賴 Azure AD 安全性群組或 Office 365 群組之其他資源的存取權。  例如：

- 您可以使用存取套件中的 Azure AD 安全性群組，以及為該群組設定以[群組為基礎的授權](../users-groups-roles/licensing-groups-assign.md)，為使用者提供 Microsoft Office 365 授權
- 您可以使用存取套件中的 Azure AD 安全性群組，並建立該群組的[azure 角色指派](../../role-based-access-control/role-assignments-portal.md)，以授與使用者管理 azure 資源的存取權

## <a name="how-do-i-control-who-gets-access"></a>如何? 控制誰取得存取權？

使用存取套件時，系統管理員或委派存取套件管理員會列出資源（群組、應用程式和網站），以及使用者針對這些資源所需的角色。

存取套件也包含一或多個*原則*。 原則會定義要指派給存取封裝的規則或護欄。 每個原則都可以用來確保只有適當的使用者能夠要求存取權、有核准者要求，而且他們對這些資源的存取權有時間限制，而且如果未更新，則會到期。

![存取套件和原則](./media/entitlement-management-overview/elm-overview-access-package.png)

在每個原則中，「系統管理員」或「存取套件管理員」定義

- 已存在的使用者（通常是員工或已受邀的來賓），或外部使用者的夥伴組織，其符合要求存取權
- 核准程式和可以核准或拒絕存取的使用者
- 在指派到期之前，使用者的存取權指派在核准後的持續時間

下圖顯示 [權利管理] 中不同元素的範例。 它會顯示一個目錄，其中包含兩個範例存取封裝。

- **存取套件 1**包含單一群組做為資源。 存取是以原則定義，可讓目錄中的一組使用者要求存取權。
- **存取套件 2**包含群組、應用程式和 SharePoint Online 網站做為資源。 存取會以兩個不同的原則定義。 第一個原則可讓目錄中的一組使用者要求存取權。 第二個原則可讓外部目錄中的使用者要求存取權。

![權利管理總覽](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>何時應該使用存取套件？

存取套件不會取代其他存取指派的機制。  它們最適用于下列情況：

- 員工對於特定工作需要有時間限制的存取權。  例如，您可以使用以群組為基礎的授權和動態群組，以確保所有員工都擁有 Exchange Online 信箱，然後在員工需要額外存取權的情況下使用存取套件，例如，從另一部讀取部門資源國防部.
- 存取權必須由員工的經理或其他指定的個人核准。
- 部門想要為其資源管理自己的存取原則，而不需要 IT 介入。  
- 有兩個或多個組織在專案上共同作業，因此，一個組織中的多個使用者必須透過 Azure AD B2B 提供，才能存取另一個組織的資源。

## <a name="how-do-i-delegate-access"></a>如何? 委派存取權？

 存取套件會定義在名為*目錄*的容器中。  您可以為所有存取套件擁有單一目錄，也可以指定個人來建立和擁有自己的目錄。 系統管理員可以將資源新增至任何目錄，但是非系統管理員只能將其所擁有的資源新增至目錄。 目錄擁有者可以將其他使用者新增為目錄共同擁有者，或做為存取套件管理員。  這些案例會在[Azure AD 權利管理中的委派和角色](entitlement-management-delegate.md)一文中進一步說明。

## <a name="summary-of-terminology"></a>術語摘要

若要進一步瞭解權利管理及其檔，您可以參考下列條款清單。

| 詞彙 | 描述 |
| --- | --- |
| 存取套件 | 小組或專案所需且受原則規範的資源組合。 存取套件一律包含在目錄中。 在使用者需要要求存取權的案例中，您會建立新的存取封裝。  |
| 存取要求 | 存取存取套件中資源的要求。 要求通常會經過核准工作流程。  若已核准，要求的使用者會收到存取套件指派。 |
| 設定 | 將存取套件指派給使用者，可確保使用者擁有該存取套件的所有資源角色。  存取套件指派在到期前通常會有時間限制。 |
| catalog | 相關資源和存取封裝的容器。  目錄會用於委派，讓非系統管理員可以建立自己的存取封裝。 目錄擁有者可以將自己擁有的資源新增至目錄。 |
| 目錄建立者 | 已獲授權可建立新目錄的使用者集合。  當授權為目錄建立者的非系統管理員使用者建立新的類別目錄時，他們會自動成為該目錄的擁有者。 |
| 已連線的組織 | 與您有關聯性的外部 Azure AD 目錄或網域。 已連線組織中的使用者可以在原則中指定為允許要求存取。 |
| 原則 | 定義存取生命週期的一組規則，例如使用者取得存取權的方式、核准者，以及使用者可以透過指派存取的時間長度。 原則會連結至存取封裝。 例如，存取套件可能有兩個原則-一個供員工要求存取，而第二個用於外部使用者要求存取權。 |
| resource | 資產，例如 Office 群組、安全性群組、應用程式或 SharePoint Online 網站，其角色可授與使用者許可權。 |
| 資原始目錄 | 具有一或多個要共用之資源的目錄。 |
| 資源角色 | 與資源相關聯和所定義的許可權集合。 群組有兩個角色-成員和擁有者。 SharePoint 網站通常會有3個角色，但可能會有額外的自訂角色。 應用程式可以有自訂角色。 |


## <a name="license-requirements"></a>授權需求

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

特製化雲端（例如 Azure Government、Azure 德國和 Azure 中國世紀）目前無法供使用。

### <a name="how-many-licenses-must-you-have"></a>您必須擁有多少個授權？

請確定您的目錄至少有多個 Azure AD Premium P2 授權，因為您擁有將執行下列工作的員工：

- **可以**要求存取封裝的成員使用者。
- 要求存取封裝的成員和來賓使用者。
- 核准存取封裝要求的成員和來賓使用者。

下列工作**不**需要 Azure AD Premium P2 授權：

- 具有設定初始目錄、存取封裝和原則之全域管理員角色的使用者，並不需要任何授權，而是會將系統管理工作委派給其他使用者。
- 已委派系統管理工作的使用者（例如目錄建立者、目錄擁有者和存取封裝管理員）不需要任何授權。
- **可以**要求存取套件**但不要求**存取套件的來賓，不需要任何授權。

針對您為成員使用者（員工）購買的每個付費 Azure AD Premium P2 授權，您可以使用 Azure AD B2B 邀請最多5位來賓使用者。 這些來賓使用者也可以使用 Azure AD Premium P2 功能。 如需詳細資訊，請參閱[AZURE AD B2B 共同作業授權指引](../b2b/licensing-guidance.md)。

如需有關授權的詳細資訊，請參閱[使用 Azure Active Directory 入口網站指派或移除授權](../fundamentals/license-users-groups.md)。

### <a name="example-license-scenarios"></a>範例授權案例

以下是一些範例授權案例，可協助您判斷您必須擁有的授權數目。

| 狀況 | 計算 | 授權數目 |
| --- | --- | --- |
| Woodgrove Bank 的全域管理員會建立初始目錄，並將系統管理工作委派給其他6個使用者。 其中一個原則會指定**所有員工**（2000員工）可以要求一組特定的存取封裝。 150員工會要求存取封裝。 | **可**要求存取套件的2000員工 | 2,000 |
| Woodgrove Bank 的全域管理員會建立初始目錄，並將系統管理工作委派給其他6個使用者。 其中一個原則會指定**所有員工**（2000員工）可以要求一組特定的存取封裝。 另一個原則指定來自**合作夥伴 Contoso** （來賓）之使用者的某些使用者，可以要求相同的存取權套件進行核准。 Contoso 有30000個使用者。 150員工向 Contoso 要求存取套件和10500位使用者。 | 2000員工 + 500 來自 Contoso 的來賓使用者超過1:5 的比率（10500-（2000 * 5）） | 2,500 |

## <a name="next-steps"></a>後續步驟

- [教學課程：建立您的第一個存取套件](entitlement-management-access-package-first.md)
- [常見案例](entitlement-management-scenarios.md)
