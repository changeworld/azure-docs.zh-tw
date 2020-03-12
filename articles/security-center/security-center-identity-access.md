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
ms.date: 03/06/2020
ms.author: memildin
ms.openlocfilehash: 183b81134b2fe72a539cc6460a05d828342aafbb
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086519"
---
# <a name="monitor-identity-and-access"></a>監視身分識別和存取

> [!TIP]
> 自2020年3月起，Azure 資訊安全中心的身分識別和存取建議會包含在免費定價層的所有訂用帳戶中。 如果您擁有免費層的訂用帳戶，其安全分數會受到影響，因為先前並未針對其身分識別和存取安全性進行評估。 

當資訊安全中心發現潛在的安全性弱點時，它會建立可引導您完成所需控制之設定程序的建議，以加強並保護您的資源。

安全性周邊已從網路周邊發展到身分識別周邊。 安全性變得較不著重於防禦您的網路，而是較著重於防禦您的資料，以及管理您應用程式和使用者的安全性。 現今有越來越多資料和應用程式移至雲端，身分識別成為新的周邊。

監視您的身分識別活動，您就能夠在事件發生前採取主動式動作，或採取回應式動作以停止攻擊。 您可能會在 Azure 資訊安全中心的身分**識別與存取**資源安全性區段中看到的建議範例包括：

- 應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA
- 應針對您的訂用帳戶指定最多 3 位擁有者
- 已取代帳戶應該從您的訂用帳戶中移除
- 具有讀取權限的外部帳戶應該從您的訂用帳戶中移除

如需您可能會在這裡看到之建議的完整清單，請參閱身分[識別和存取建議](recommendations-reference.md#recs-identity)。

> [!NOTE]
> 如果訂用帳戶內含超過 600 個帳戶，資訊安全中心就無法針對訂用帳戶執行身分識別建議。 未執行的建議會列在下方的 [無法使用的評量] 底下。
資訊安全中心無法針對雲端解決方案提供者 (CSP) 合作夥伴的管理專員執行身分識別建議。
>


所有的身分識別和存取建議都可在 [**建議**] 頁面中的兩個安全性控制項中取得：

- 管理存取權和許可權 
- 啟用 MFA

![這兩個安全性控制與身分識別和存取相關的建議](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>啟用多重要素驗證（MFA）

啟用 MFA 需要[Azure Active Directory （AD）租使用者許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。 

- 如果您有 premium 版本的 AD，請使用[條件式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)來啟用 MFA。

- AD 免費版的使用者可以在 Azure Active Directory 中啟用**安全性預設值**（如[AD 檔](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)中所述），但仍會顯示啟用 MFA 的資訊安全中心建議。


## <a name="next-steps"></a>後續步驟
若要深入瞭解適用于其他 Azure 資源類型的建議，請參閱下列文章：

- [保護 Azure 資訊安全中心內的機器和應用程式](security-center-virtual-machine-protection.md)
- [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)
- [保護 Azure 資訊安全中心內的 Azure SQL 服務和資料](security-center-sql-service-recommendations.md)