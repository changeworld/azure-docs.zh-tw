---
title: 什麼是權利管理？ - Azure AD
description: 概略了解 Azure Active Directory 權利管理，以及如何用權利管理來管理內部和外部使用者對群組、應用程式和 SharePoint Online 網站的存取。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 11/23/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.custom: contperf-fy21q1
ms.openlocfilehash: c9815355b26a9c14c02110e4bb5fff4f998d2105
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032027"
---
# <a name="what-is-azure-ad-entitlement-management"></a>什麼是 Azure AD 權利管理？

Azure Active Directory (Azure AD) 權利管理是一種[身分識別控管](identity-governance-overview.md)功能，可讓組織透過將存取要求工作流程、存取權指派、檢閱和到期自動化，來大規模管理身分識別和存取生命週期。

組織中的員工需要存取各種群組、應用程式和網站以便執行其工作。 管理這類存取並不容易，因為需求會變化；例如，有新增的應用程式，或使用者需要額外的存取權限。  當您與外部組織共同作業時，情況會變得更複雜；您可能不知道其他組織中有誰需要存取您所在組織的資源，對方也不知道您所在組織使用了哪些應用程式、群組或網站。

Azure AD 權利管理可協助您更有效率地管理內部使用者對群組、應用程式和 SharePoint Online 網站的存取，此外，也能對組織外需要存取這些資源的使用者做到這一點。

## <a name="why-use-entitlement-management"></a>為何要使用權利管理？

企業組織通常會在管理員工對資源的存取時面臨挑戰，例如：

- 使用者可能不知道他們應該擁有哪些存取權，即使他們知道，也可能難以找到適當人員來核准其存取權
- 一旦使用者找到並獲得資源的存取權，其可能會不斷存取，而超過正常業務行為所需的時間

對於需要從另一個組織存取的使用者 (例如來自供應鏈組織或其他商務夥伴的外部使用者) 而言，這些問題相當複雜。 例如：

- 沒有人會認識其他組織的目錄中能夠向自己發出邀請的所有特定人員
- 即使他們可以邀請這些使用者，該組織中也不會有任何人會記得以一致的方式管理所有使用者的存取權

Azure AD 權利管理有助於解決這些挑戰。  若要深入了解客戶一直以來使用 Azure AD 權利管理的情形，您可以閱讀 [Avanade 案例研究](https://customers.microsoft.com/story/avanade-professional-services-azure-canada)和 [Centrica 案例研究](https://customers.microsoft.com/story/757467-centrica-energy-azure)。  下面這段影片會概述權利管理及其價值：

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>權利管理有何功用？

以下是權利管理的一些功能：

- 向非管理員委派建立存取套件的能力。 這些存取套件包含使用者可以要求的資源，受到委派的存取套件管理員可以定義原則，其中包含哪些使用者可以提出要求、誰必須核准其存取權，以及存取權何時到期的規則。
- 選取其使用者可以要求存取權的已連線組織。  當還未在您目錄中的使用者要求存取權並獲得核准時，系統會自動邀請他們進入您的目錄並為其指派存取權。  其存取權到期時，如果未獲得任何其他存取套件指派，其在您目錄中的 B2B 帳戶就會自動遭到移除。

>[!NOTE]
>如果您已準備好試用權利管理，則可以從我們的[建立您的第一個存取套件教學課程](entitlement-management-access-package-first.md)來入門。

您也可以閱讀[常見案例](entitlement-management-scenarios.md)或觀看影片，包括

- [如何在您的組織中部署 Azure AD 權利管理](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [如何監視及擴縮您對 Azure AD 權利管理的使用](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [如何在權利管理中委派](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>什麼是存取套件，我可以用存取套件來管理哪些資源？

權利管理在 Azure AD 中引進了 *存取套件* 的概念。 使用者在處理專案或執行其工作時需要各種存取權，而存取套件集結了具有這些存取權的所有資源。 存取套件可用來控管內部員工以及組織外部使用者的存取權。

 以下是您可以使用權利管理來管理使用者存取權的資源類型：

- Azure AD 安全性群組的成員資格
- Microsoft 365 群組和小組的成員資格
- 對象為 Azure AD 企業應用程式的指派，包括 SaaS 應用程式和支援同盟/單一登入和/或佈建的自訂整合應用程式
- SharePoint Online 網站的成員資格

您也可以針對依賴 Azure AD 安全性群組或 Microsoft 365 群組的其他資源控制其存取權。  例如：

- 您可以使用存取套件中的 Azure AD 安全性群組，以及為該群組設定[群組型授權](../enterprise-users/licensing-groups-assign.md)，而為使用者提供 Microsoft 365 的授權。
- 您可以使用存取套件中的 Azure AD 安全性群組，以及為該群組建立 [Azure 角色指派](../../role-based-access-control/role-assignments-portal.md)，而為使用者提供用來管理 Azure 資源的存取權。
- 您可以使用可以在存取套件中指派給 Azure AD 角色的群組，並且[指派 Azure AD 角色給該群組](../roles/groups-assign-role.md)，給予使用者存取權以管理 Azure AD 角色。

## <a name="how-do-i-control-who-gets-access"></a>如何控制誰能夠獲得存取權？

透過存取套件，系統管理員或受委派的存取套件管理員可列出資源 (群組、應用程式和網站)，以及使用者要使用這些資源所需的角色。

存取套件也包含一或多個 *原則*。 原則會針對指派來定義用來存取套件的規則或護欄。 每個原則都可以用來確保只有適當的使用者能夠要求存取權、有核准者可處理其要求，以及其對這些資源的存取權有時間限制，如果未更新就會到期。

![存取套件和原則](./media/entitlement-management-overview/elm-overview-access-package.png)

在每個原則內，系統管理員或存取套件管理員會定義

- 有資格要求存取權的既存使用者 (通常是員工或已受邀的來賓) 或外部使用者所屬夥伴組織
- 核准程序和可以核准或拒絕存取的使用者
- 使用者的存取權指派在獲得核准後，於指派到期前可持續的時間

下圖顯示權利管理中不同元素的範例。 其會顯示一個目錄，其中包含兩個範例存取套件。

- **存取套件 1** 包含單一群組來作為資源。 會透過可讓目錄中的一組使用者要求存取權的原則來定義存取。
- **存取套件 2** 包含群組、應用程式和 SharePoint Online 網站來作為資源。 會透過兩個不同的原則來定義存取。 第一個原則可讓目錄中的一組使用者要求存取權。 第二個原則可讓外部目錄中的使用者要求存取權。

![權利管理概觀](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>何時該使用存取套件？

存取套件不會取代其他存取指派機制。  其最適合用於下列情況：

- 員工需要有限時間的存取權來執行特定工作。  例如，您可以使用群組型授權和動態群組，以確保所有員工都擁有 Exchange Online 信箱，然後在員工需要額外存取權的情況下使用存取套件，例如為了從另一個部門讀取部門資源。
- 需要員工經理或其他指定人員核准的存取。
- 部門想要針對其資源管理自己的存取原則，但不要 IT 介入。  
- 兩個以上的組織共同合作進行一項專案，因此，一個組織中的多個使用者必須透過 Azure AD B2B 來進入以便存取另一個組織的資源。

## <a name="how-do-i-delegate-access"></a>如何委派存取權？

 存取套件會定義在名為 *目錄* 的容器中。  您可以將單一目錄用於所有存取套件，也可以指定個人來建立和擁有自己的目錄。 系統管理員可以將資源新增至任何目錄，但是非系統管理員只能將其擁有的資源新增至目錄。 目錄擁有者可以將其他使用者新增為目錄的共同擁有者，也可以新增為存取套件管理員。  [Azure AD 權利管理中的委派和角色](entitlement-management-delegate.md)一文會進一步說明這些案例。

## <a name="summary-of-terminology"></a>術語摘要

若要進一步了解權利管理及其文件，請回頭參考下列詞彙清單。

| 詞彙 | 描述 |
| --- | --- |
| 存取套件 | 小組或專案所需且受原則控管的資源組合。 存取套件一律包含在目錄中。 在使用者需要要求存取權的情況下，您必須建立新的存取套件。  |
| 存取要求 | 想要在存取套件中存取資源的要求。 要求一般會經歷核准工作流程。  若獲得核准，提出要求的使用者便會收到存取套件指派。 |
| 指派 | 將存取套件指派給使用者可確保使用者擁有該存取套件的所有資源角色。  存取套件指派一般會有有限的到期時間。 |
| catalog | 相關資源和存取套件的容器。  目錄可用於委派，讓非系統管理員可以建立自己的存取套件。 目錄擁有者可以將自己擁有的資源新增至目錄。 |
| 目錄建立者 | 已獲授權而可建立新目錄的使用者集合。  當獲授權而成為目錄建立者的非系統管理員使用者建立新的目錄時，其會自動成為該目錄的擁有者。 |
| 已連線的組織 | 與您有關聯性的外部 Azure AD 目錄或網域。 已連線組織中的使用者可以在原則中指定為允許要求存取權。 |
| 原則 | 定義存取生命週期的一組規則，例如使用者如何取得存取權、誰可以核准，以及使用者可以透過指派存取多久。 原則會連結至存取套件。 例如，存取套件可能有兩個原則 - 一個供員工要求存取權，第二個則供外部使用者要求存取權。 |
| resource | 資產，例如 Office 群組、安全性群組、應用程式或 SharePoint Online 網站，其角色的權限可授與給使用者。 |
| 資源目錄 | 具有一或多個可共用資源的目錄。 |
| 資源角色 | 與資源相關聯並由資源定義的權限集合。 群組有兩個角色 - 成員和擁有者。 SharePoint 網站一般會有 3 個角色，但可能會有其他自訂角色。 應用程式可以有自訂角色。 |


## <a name="license-requirements"></a>授權需求

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

特製化雲端 (例如 Azure 德國和 Azure China 21Vianet) 目前無法供您使用。

### <a name="how-many-licenses-must-you-have"></a>您必須擁有多少授權？

請確定您的目錄所擁有的 Azure AD Premium P2 授權，至少要和您擁有的下列項目數目一樣多：

- **可以** 要求存取套件的成員使用者。
- <u>要求</u>存取套件的成員使用者。
- <u>核准存取套件要求</u>的成員使用者。
- <u>檢閱存取套件指派</u>的成員使用者。 
- <u>直接指派</u>給存取套件的成員使用者。

對於來賓使用者，授權需求將取決於您所使用的[授權模型](../external-identities/external-identities-pricing.md)而定。 不過，系統會將下列來賓使用者的活動會視為 Azure AD Premium P2 使用量：
- <u>要求</u>存取套件的來賓使用者。 
- <u>核准存取套件要求</u>的來賓使用者。
- <u>檢閱存取套件指派</u>的來賓使用者。
- <u>直接指派</u>給存取套件的來賓使用者。 

下列工作 **不** 需要 Azure AD Premium P2 授權：

- 使用者若具有全域管理員角色，並設定初始目錄、存取套件和原則，以及將系統管理工作委派給其他使用者，就不需要任何授權。
- 使用者若已獲得委派的系統管理工作 (例如目錄建立者、目錄擁有者和存取套件管理員)，就不需要任何授權。
- 來賓若 **可以** 要求存取套件，但 **未** 要求存取套件，就不需要任何授權。

如需授權的詳細資訊，請參閱[使用 Azure Active Directory 入口網站指派或移除授權](../fundamentals/license-users-groups.md)。

### <a name="example-license-scenarios"></a>範例授權案例

以下是一些範例授權案例，可協助您判斷您必須擁有的授權數目。

| 狀況 | 計算 | 授權數目 |
| --- | --- | --- |
| Woodgrove Bank 的全域管理員建立初始目錄，並將系統管理工作委派給另外 6 位使用者。 其中一個原則指定 **所有員工** (2,000 名員工) 都可以要求一組特定的存取套件。 150 名員工要求存取套件。 | 2,000 名員工 **可以** 要求存取套件 | 2,000 |
| Woodgrove Bank 的全域管理員建立初始目錄，並將系統管理工作委派給另外 6 位使用者。 其中一個原則指定 **所有員工** (2,000 名員工) 都可以要求一組特定的存取套件。 另一個原則指定來自 **合作夥伴 Contoso 使用者** (來賓) 的某些使用者可以要求相同的存取套件，但需要獲得核准。 Contoso 有 30,000 名使用者。 150 名員工要求存取套件，來自 Contoso 的 10,500 名使用者要求存取權。 | 2,000 名員工 + 500 名來自 Contoso 的來賓使用者，超過 1:5 的比率 (10,500 - (2,000 * 5)) | 2,500 |

## <a name="next-steps"></a>後續步驟

- [教學課程：建立您的第一個存取套件](entitlement-management-access-package-first.md)
- [常見案例](entitlement-management-scenarios.md)
