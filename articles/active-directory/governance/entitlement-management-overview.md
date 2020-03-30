---
title: 什麼是權利管理？ - Azure AD
description: 獲取 Azure 活動目錄授權管理的概述，以及如何使用它來管理對內部和外部使用者的組、應用程式和 SharePoint Online 網站的訪問。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3deb87fec8241ad6126314f3f6ce5fb9600ad1fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128553"
---
# <a name="what-is-azure-ad-entitlement-management"></a>什麼是 Azure AD 權利管理？

Azure 活動目錄 （Azure AD） 授權管理是一種[標識治理](identity-governance-overview.md)功能，通過自動訪問請求工作流、訪問分配、審核和過期，使組織能夠大規模管理標識和訪問生命週期。

組織中的員工需要訪問各種組、應用程式和網站才能執行其工作。 隨著需求的變化，管理此訪問具有挑戰性 - 添加新應用程式或使用者需要額外的存取權限。  當您與外部組織協作時，此方案變得更加複雜 - 您可能不知道其他組織中誰需要訪問組織的資源，並且他們不知道您的組織正在使用哪些應用程式、組或網站。

Azure AD 授權管理可説明您更有效地管理對內部使用者以及組織外部需要訪問這些資源的使用者的組、應用程式和 SharePoint Online 網站的訪問。

## <a name="why-use-entitlement-management"></a>為什麼要使用授權管理？

企業組織在管理員工訪問資源時經常面臨挑戰，例如：

- 使用者可能不知道他們應擁有哪些存取權限，即使他們擁有存取權限，也可能難以找到合適的個人來批准其存取權限
- 一旦使用者找到並接收對資源的訪問，他們可能會保留存取時間超過業務目的所需的存取時間

對於需要從其他組織進行訪問的使用者（例如來自供應鏈組織或其他業務合作夥伴的外部使用者）而言，這些問題會加劇。 例如：

- 任何人都不得知道其他組織目錄中的所有特定個人能夠邀請他們
- 即使他們能夠邀請這些使用者，該組織中任何人都不會記得始終如一地管理使用者的所有存取權限

Azure AD 授權管理可説明應對這些挑戰。  要詳細瞭解客戶如何使用 Azure AD 授權管理，請閱讀[埃維諾案例研究](https://aka.ms/AvanadeELMCase)和[Centrica 案例研究](https://aka.ms/CentricaELMCase)。  本視頻概述了授權管理及其價值：

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>我可以使用授權管理做什麼？

以下是授權管理的一些功能：

- 將創建訪問包的能力委派給非管理員。 這些訪問包包含使用者可以請求的資源，委派的訪問包管理員可以使用使用者可以請求的規則定義策略，使用者必須批准其訪問，以及訪問何時過期。
- 選擇使用者可以請求訪問的已連接組織。  當尚未在目錄中的使用者請求訪問並獲得批准時，將自動邀請他們進入您的目錄並分配存取權限。  當他們的存取權限過期時，如果他們沒有其他訪問包分配，則可以自動刪除目錄中的 B2B 帳戶。

你可以開始我們的[教程，創建你的第一個訪問包](entitlement-management-access-package-first.md)。 您還可以閱讀[常見方案](entitlement-management-scenarios.md)，或觀看視頻，包括

- [如何在組織中部署 Azure AD 授權管理](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [如何監視和擴展 Azure AD 授權管理的使用](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [如何在授權管理中委派](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>什麼是訪問包，可以使用這些資源管理哪些資源？

授權管理向 Azure AD 引入了*訪問包*的概念。 訪問包是使用者處理專案或執行任務所需的所有資源的包。 訪問包用於管理內部員工以及組織外部使用者的存取權限。

 以下是您可以使用授權管理管理使用者存取權限的資源類型：

- Azure AD 安全性群組的成員身份
- 辦公室 365 個組和團隊的成員身份
- 分配給 Azure AD 企業應用程式，包括支援聯合/單一登入和/或預配的 SaaS 應用程式和自訂集成應用程式
- 共用點線上網站的成員身份

您還可以控制對依賴于 Azure AD 安全性群組或 Office 365 組的其他資源的訪問。  例如：

- 您可以通過在訪問包中使用 Azure AD 安全性群組並為該組配置[基於組的許可](../users-groups-roles/licensing-groups-assign.md)，為使用者授予 Microsoft Office 365 的許可證
- 通過在訪問包中使用 Azure AD 安全性群組並為該組創建[Azure 角色指派](../../role-based-access-control/role-assignments-portal.md)，可以授予使用者管理 Azure 資源存取權限

## <a name="how-do-i-control-who-gets-access"></a>如何控制誰獲得存取權限？

使用訪問包，管理員或委派的訪問包管理器列出資源（組、應用和網站）以及使用者需要這些資源的角色。

訪問包還包括一個或多個*策略*。 策略定義用於分配訪問包的規則或護欄。 每個策略都可用於確保只有適當的使用者才能請求訪問，有核准者為其請求，並且他們對這些資源的訪問是有時間限制的，如果不續訂，他們將過期。

![訪問包和策略](./media/entitlement-management-overview/elm-overview-access-package.png)

在每個策略中，管理員或訪問包管理器定義

- 已存在的使用者（通常是員工或已邀請的來賓）或有資格請求存取權限的外部使用者的合作夥伴組織
- 審批流程和可以批准或拒絕存取權限的使用者
- 使用者的訪問分配的持續時間，一旦獲得批准，在分配到期之前

下圖顯示了授權管理中不同元素的示例。 它顯示一個目錄，其中有兩個示例訪問包。

- **訪問包 1**包括單個組作為資源。 訪問使用策略定義，該策略使目錄中的一組使用者能夠請求訪問。
- **訪問包 2**包括組、應用程式和 SharePoint 連線網站作為資源。 訪問使用兩個不同的策略定義。 第一個策略使目錄中的一組使用者能夠請求訪問。 第二個策略使外部目錄中的使用者能夠請求訪問。

![授權管理概述](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>何時應該使用訪問包？

訪問包不會替換用於訪問分配的其他機制。  它們最適合以下情況：

- 員工需要對特定任務進行限時訪問。  例如，您可以使用基於組的許可和動態組來確保所有員工都有 Exchange Online 郵箱，然後針對員工需要其他存取權限的情況（例如從另一個郵件中讀取部門資源）使用訪問包部門。
- 存取權限需要獲得員工經理或其他指定人員的批准。
- 各部門希望在沒有 IT 參與的情況下管理自己的資源訪問策略。  
- 兩個或多個組織正在協作一個專案，因此，需要通過 Azure AD B2B 引入一個組織的多個使用者來訪問另一個組織的資源。

## <a name="how-do-i-delegate-access"></a>如何委派存取權限？

 存取套件會定義在名為*目錄*的容器中。  您可以為所有訪問包提供單個目錄，也可以指定個人創建和擁有自己的目錄。 管理員可以將資源添加到任何目錄，但非管理員只能向目錄中添加他們擁有的資源。 目錄擁有者可以將其他使用者添加為目錄共同擁有者，也可以添加訪問包管理器。  這些方案在[Azure AD 授權管理中的文章委派和角色中](entitlement-management-delegate.md)進一步描述。

## <a name="summary-of-terminology"></a>術語摘要

為了更好地理解授權管理及其文檔，您可以參考以下術語清單。

| 詞彙 | 描述 |
| --- | --- |
| 訪問包 | 團隊或專案需要並受策略管理的一組資源。 訪問包始終包含在目錄中。 您將為使用者需要請求訪問的方案創建新的訪問包。  |
| 訪問請求 | 訪問訪問包中資源的請求。 請求通常通過審批工作流。  如果獲得批准，請求使用者將收到訪問包分配。 |
| 指派 | 向使用者分配訪問包可確保使用者具有該訪問包的所有資源角色。  訪問包分配通常有過期前的時間限制。 |
| catalog | 相關資源和訪問包的容器。  目錄用於委派，以便非管理員可以創建自己的訪問包。 目錄擁有者可以將他們擁有的資源添加到目錄中。 |
| 目錄建立者 | 授權創建新目錄的使用者的集合。  當被授權為目錄建立者的非管理員使用者創建新目錄時，他們將自動成為該目錄的擁有者。 |
| 互聯組織 | 與具有關系的外部 Azure AD 目錄或域。 在策略中可以指定來自已連接組織的使用者請求訪問。 |
| 原則 | 定義訪問生命週期的一組規則，例如使用者如何獲取存取權限、誰可以批准以及使用者通過分配訪問的時間。 策略連結到訪問包。 例如，訪問包可以有兩個策略 - 一個策略供員工請求訪問，另一個策略用於外部使用者請求訪問。 |
| resource | 具有使用者可以授予許可權的資產，如 Office 組、安全性群組、應用程式或 SharePoint Online 網站。 |
| 資原始目錄 | 具有一個或多個要共用資源的目錄。 |
| 資源角色 | 與資源關聯並由資源定義的許可權的集合。 組有兩個角色 - 成員和擁有者。 SharePoint 網站通常有 3 個角色，但可能具有其他自訂角色。 應用程式可以具有自訂角色。 |


## <a name="license-requirements"></a>授權需求

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

專用雲（如 Azure 政府、Azure 德國和 Azure 中國 21Vianet）當前不可用。

### <a name="how-many-licenses-must-you-have"></a>您必須擁有多少許可證？

確保目錄至少具有與員工執行以下任務相同的 Azure AD 高級 P2 許可證：

- **可以**請求訪問包的成員使用者。
- 請求訪問包的成員和來賓使用者。
- 批准訪問包請求的成員和來賓使用者。

以下任務**不需要**Azure AD 高級 P2 許可證：

- 具有全域管理員角色的使用者無需許可證，這些使用者設置了初始目錄、訪問包和策略，並將管理工作委派給其他使用者。
- 已委派管理工作的使用者（如目錄建立者、目錄擁有者和訪問包管理器）不需要許可證。
- **可以**請求訪問包**但不請求訪問**包的客人無需許可證。

對於為會員使用者（員工）購買的每個付費 Azure AD 高級 P2 許可證，可以使用 Azure AD B2B 邀請最多 5 個來賓使用者。 這些來賓使用者也可以使用 Azure AD Premium P2 功能。 有關詳細資訊，請參閱[Azure AD B2B 協作許可指南](../b2b/licensing-guidance.md)。

有關許可證的詳細資訊，請參閱使用[Azure 活動目錄門戶分配或刪除許可證](../fundamentals/license-users-groups.md)。

### <a name="example-license-scenarios"></a>許可證方案示例

下面是一些示例許可證方案，可説明您確定必須擁有的許可數。

| 狀況 | 計算 | 許可證數 |
| --- | --- | --- |
| 伍德格羅夫銀行的全球管理員創建初始目錄，並將管理工作委派給 6 個其他使用者。 其中一個策略指定**所有員工**（2，000 名員工）可以請求一組特定的訪問包。 150 名員工請求訪問包。 | 2，000 名員工**可以**請求訪問包 | 2,000 |
| 伍德格羅夫銀行的全球管理員創建初始目錄，並將管理工作委派給 6 個其他使用者。 其中一個策略指定**所有員工**（2，000 名員工）可以請求一組特定的訪問包。 另一個策略指定來自**合作夥伴 Contoso（** 來賓）的使用者的某些使用者可以請求相同的訪問包，但須經批准。 Contoso 有 30，000 個使用者。 150 名員工請求訪問包，Contoso 的 10，500 名使用者請求訪問。 | 2，000 名員工 + 來自 Contoso 的 500 名來賓使用者，比例超過 1：5（10，500 - （2，000 + 5）） | 2,500 |

## <a name="next-steps"></a>後續步驟

- [教程：創建第一個訪問包](entitlement-management-access-package-first.md)
- [常見方案](entitlement-management-scenarios.md)
