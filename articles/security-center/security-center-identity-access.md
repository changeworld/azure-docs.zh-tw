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
ms.openlocfilehash: 313f10c77da499f4c405134028b975900d2d2a9e
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590229"
---
# <a name="monitor-identity-and-access"></a>監視身分識別和存取

> [!TIP]
> 自 2020 年 3 月起，免費定價層的所有訂用帳戶都包含 Azure 資訊安全中心的身分識別和存取建議。 如果您有免費定價層的訂用帳戶，因為先前並未評估這些訂用帳戶的身分識別和存取安全性，其安全分數將受到影響。 

當資訊安全中心發現潛在的安全性弱點時，它會建立可引導您完成所需控制之設定程序的建議，以加強並保護您的資源。

安全性周邊已從網路周邊發展到身分識別周邊。 安全性變得較不著重於防禦您的網路，而是較著重於防禦您的資料，以及管理您應用程式和使用者的安全性。 現今有越來越多資料和應用程式移至雲端，身分識別成為新的周邊。

您可以監視身分識別活動，以便在事件發生前採取主動式動作，或採取被動式動作來阻止攻擊嘗試。 例如，資訊安全中心可能將已取代的帳戶 (不再需要且禁止由 Azure Active Directory 登入的帳戶) 標示為移除。 

在 Azure 資訊安全中心的 [身分識別和存取] 資源安全性區段中，您可能看到的建議包括下列範例：

- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- 應針對您的訂用帳戶指定最多 3 位擁有者
- 具有讀取權限的外部帳戶應該從您的訂用帳戶中移除
- 已取代帳戶應該從您的訂用帳戶中移除

如需這些建議的詳細資訊，以及您在這裡可能看到的完整建議清單，請參閱[身分識別和存取建議](recommendations-reference.md#recs-identity)。

> [!NOTE]
> 如果訂用帳戶內含超過 600 個帳戶，資訊安全中心就無法針對訂用帳戶執行身分識別建議。 未執行的建議會列在下方的「無法使用的評量」下。
資訊安全中心無法針對雲端解決方案提供者 (CSP) 合作夥伴的管理專員執行身分識別建議。
>


所有身分識別和存取建議劃分為 [建議] 頁面中的兩個安全性控制：

- 管理存取權和權限 
- 啟用 MFA

![劃分身分識別和存取相關建議的兩個安全性控制](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>啟用多重要素驗證 (MFA)

需要有 [Azure Active Directory (AD) 租用戶權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)才能啟用 MFA。 

- 如果您有進階版的 AD，請使用[條件式存取](../active-directory/conditional-access/concept-conditional-access-policy-common.md)來啟用 MFA。

- 如果您使用的是 AD free edition，請在 Azure Active Directory 中啟用 **安全性預設值** ，如 [AD 檔](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)中所述。


## <a name="next-steps"></a>後續步驟
若要深入了解適用於其他 Azure 資源類型的建議，請參閱下列文章︰

- [保護 Azure 資訊安全中心內的機器和應用程式](security-center-virtual-machine-protection.md)
- [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)
- [保護 Azure 資訊安全中心內的 Azure SQL 服務和資料](security-center-sql-service-recommendations.md)