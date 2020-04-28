---
title: 什麼是群組型授權 - Azure Active Directory | Microsoft Docs
description: 了解 Azure Active Directory 群組型授權，包括其運作方式及最佳做法。
services: active-directory
keywords: Azure AD 授權
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eec896e6cccaf58c83820161c54c4f10cfadadd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77561556"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>什麼是 Azure Active Directory 中的群組型授權？

Microsoft 付費的雲端服務 (例如 Office 365、Enterprise Mobility + Security、Dynamics 365 及其他類似的產品) 需要授權。 這些授權指派給每位需要存取這些服務的使用者。 若要管理授權，系統管理員需要使用其中一個管理入口網站 (Office 或 Azure) 和 PowerShell Cmdlet。 Azure Active Directory (Azure AD) 是支援所有 Microsoft 雲端服務身分識別管理的基礎結構。 Azure AD 會儲存使用者授權指派狀態的相關資訊。

到目前為止，只能在個別使用者等級指派授權，因此難以進行大規模的管理。 例如，若要根據組織變更 (例如使用者加入或離開組織或部門) 來新增或移除使用者授權，系統管理員通常必須撰寫複雜的 PowerShell 指令碼。 此指令碼會對雲端服務進行個別的呼叫。

為了克服這些挑戰，Azure AD 現在包含群組型授權。 您可以將一或多個產品授權指派給群組。 Azure AD 會確保將授權指派給該群組的所有成員。 任何加入群組的新成員會獲派適當的授權。 當他們離開群組時，就會移除這些授權。 此授權管理方式不需透過 PowerShell 來自動管理授權，即可依據每一使用者反映組織和部門結構中的變更。

## <a name="licensing-requirements"></a>授權需求
您必須具有下列其中一個授權才能使用群組型授權：

- Azure AD Premium P1 和更新版本的付費或試用訂用帳戶

- 付費或試用版的 Office 365 企業版 E3 或 Office 365 A3 或 Office 365 GCC G3 或 office 365 E3 for GCCH 或 Office 365 E3 for DOD 和更新版本

### <a name="required-number-of-licenses"></a>所需的授權數目
針對指派授權的任何群組，您也必須為每個唯一成員取得授權。 雖然您不必為群組中的每個成員指派授權，但您必須至少要有足夠的授權，才能包含所有成員。 例如，如果您的租用戶中有 1,000 個唯一成員屬於授權群組，則必須至少擁有 1,000 個授權，才能符合授權合約。

## <a name="features"></a>特性

以下是群組型授權的主要特色：

- 可將授權指派給 Azure AD 中的任何安全性群組。 安全性群組可使用 Azure AD Connect 從內部部署環境進行同步處理。 您也可以直接在 Azure AD 中建立安全性群組 (也稱為僅限雲端的群組)，或透過 Azure AD 動態群組功能自動建立安全性群組。

- 將產品授權指派給群組時，系統管理員可以停用該產品中的一或多個服務方案。 通常是在組織尚未準備好開始使用產品中的服務時，才會這樣指派。 比方說，系統管理員可能將 Office 365 指派給某個部門，但暫時停用 Yammer 服務。

- 支援所有需要使用者層級授權的 Microsoft 雲端服務。 這項支援包括所有 Office 365 產品、Enterprise Mobility + Security 和 Dynamics 365。

- 以群組為基礎的授權目前僅透過[Azure 入口網站](https://portal.azure.com)提供。 如果您主要是使用其他管理入口網站進行使用者和群組管理，例如[Microsoft 365 系統管理中心](https://admin.microsoft.com)，您可以繼續執行此動作。 但是，若要在群組等級管理授權，您應該使用 Azure 入口網站。

- Azure AD 會自動管理因群組成員資格變更而產生的授權修改。 一般而言，在成員資格變更後的幾分鐘內，授權修改就會生效。

- 一個使用者可以屬於多個已指定授權原則的群組。 使用者也可以有一些直接從任何群組之外指派的授權。 產生的使用者狀態是所有已指派之產品與服務授權的組合。 如果使用者從多個來源獲派相同授權，該授權只會耗用一次。

- 在某些情況下，授權無法指派給使用者。 比方說，租用戶可能沒有足夠可用的授權，或可能同時指派衝突的服務。 對於 Azure AD 無法完整處理群組授權的使用者，系統管理員可以存取這些使用者的相關資訊。 然後，他們可以根據該資訊採取更正動作。

## <a name="your-feedback-is-welcome"></a>歡迎您提供寶貴的意見！

如果您有意見或功能要求，請使用 [Azure AD 管理員論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510)告訴我們。

## <a name="next-steps"></a>後續步驟

若要深入了解透過群組型授權來管理授權的其他案例，請參閱：

* [將授權指派給 Azure Active Directory 中的群組](../users-groups-roles/licensing-groups-assign.md)
* [識別及解決 Azure Active Directory 中群組的授權問題](../users-groups-roles/licensing-groups-resolve-problems.md)
* [如何將個別授權使用者移轉至 Azure Active Directory 中以群組為基礎的授權](../users-groups-roles/licensing-groups-migrate-users.md)
* [如何使用 Azure Active Directory 中的群組型授權在產品授權之間移轉使用者](../users-groups-roles/licensing-groups-change-licenses.md)
* [Azure Active Directory 群組型授權其他案例 (英文)](../users-groups-roles/licensing-group-advanced.md)
* [Azure Active Directory 群組型授權的 PowerShell 範例](../users-groups-roles/licensing-ps-examples.md)
