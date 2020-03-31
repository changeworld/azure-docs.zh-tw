---
title: 在 Azure 資訊安全中心監視身分識別和存取 | Microsoft Docs
description: 了解如何在 Azure 資訊安全中心使用身分識別和存取功能，來監視使用者的存取活動和身分識別相關問題。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2020
ms.author: memildin
ms.openlocfilehash: 152168bbadd5815659bc5f70c91bd2a28f5e049d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481954"
---
# <a name="monitor-identity-and-access"></a>監視身分識別和存取

> [!TIP]
> 從 2020 年 3 月起，Azure 安全中心的身份和訪問建議包含在免費定價層上的所有訂閱中。 如果您在免費套餐上擁有訂閱，則其安全分數將受到影響，因為之前未對其身份和訪問安全性進行評估。 

當資訊安全中心發現潛在的安全性弱點時，它會建立可引導您完成所需控制之設定程序的建議，以加強並保護您的資源。

安全性周邊已從網路周邊發展到身分識別周邊。 安全性變得較不著重於防禦您的網路，而是較著重於防禦您的資料，以及管理您應用程式和使用者的安全性。 現今有越來越多資料和應用程式移至雲端，身分識別成為新的周邊。

通過監視標識活動，您可以在事件發生之前採取主動行動，也可以採取被動行動來阻止攻擊嘗試。 例如，安全中心可能會標記已棄用的帳戶（不再需要的帳戶，並且 Azure 活動目錄阻止它們登錄），以便刪除。 

在 Azure 安全中心的**標識和訪問**資源安全部分上可能看到的建議示例包括：

- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- 應針對您的訂用帳戶指定最多 3 位擁有者
- 具有讀取權限的外部帳戶應該從您的訂用帳戶中移除
- 已取代帳戶應該從您的訂用帳戶中移除

有關這些建議的詳細資訊以及您可能在此處看到的建議的完整清單，請參閱[標識和訪問建議](recommendations-reference.md#recs-identity)。

> [!NOTE]
> 如果訂用帳戶內含超過 600 個帳戶，資訊安全中心就無法針對訂用帳戶執行身分識別建議。 未運行的建議列在下面的"不可用評估"下。
資訊安全中心無法針對雲端解決方案提供者 (CSP) 合作夥伴的管理專員執行身分識別建議。
>


所有標識和訪問建議都在 **"建議"** 頁中的兩個安全控制項中可用：

- 管理存取權限和許可權 
- 啟用 MFA

![與標識和訪問相關的建議的兩個安全控制](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>啟用多重要素驗證 （MFA）

啟用 MFA 需要[Azure 活動目錄 （AD） 租戶許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。 

- 如果您有 AD 的高級版本，請使用[條件訪問](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)啟用 MFA。

- AD 免費版本的使用者可以在 Azure 活動目錄中啟用**安全預設值**，如[AD 文檔中](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)所述，但啟用 MFA 的安全中心建議仍將顯示。


## <a name="next-steps"></a>後續步驟
要瞭解有關適用于其他 Azure 資源類型的建議，請參閱以下文章：

- [保護 Azure 資訊安全中心內的機器和應用程式](security-center-virtual-machine-protection.md)
- [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)
- [保護 Azure 資訊安全中心內的 Azure SQL 服務和資料](security-center-sql-service-recommendations.md)