---
title: 了解 Azure 中的企業系統管理角色 | Microsoft Docs
description: 了解 Azure 中的企業系統管理員角色。
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: banders
ms.openlocfilehash: 9fab4d8ba0cf2e6f684a1b9de177084f8ce31604
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462185"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>了解 Azure 中的 Azure Enterprise 合約系統管理角色

為了協助您管理組織的使用方式及費用，具有 Enterprise 合約 (EA) 的 Azure 客戶可以指派五個不同的系統管理角色：

- 企業系統管理員
- 企業系統管理員 (唯讀)<sup>1</sup>
- 部門系統管理員
- 部門系統管理員 (唯讀)
- 帳戶擁有者

<sup>1</sup> EA 合約的帳單連絡人屬於此角色。

這些角色專門用來管理 Azure Enterprise 合約，而 Azure 必須控制資源存取權的內建角色除外。 如需詳細資訊，請參閱 [Azure 資源的內建角色](../../role-based-access-control/built-in-roles.md)。

下列各節說明每個角色的限制和功能。

## <a name="user-limit-for-admin-roles"></a>系統管理員角色的使用者限制

|角色| 使用者限制|
|---|---|
|企業系統管理員|無限制|
|企業系統管理員 (唯讀)|無限制|
|部門系統管理員|無限制|
|部門系統管理員 (唯讀)|無限制|
|帳戶擁有者|每個帳戶 1 位<sup>2</sup>|

<sup>2</sup> 每個帳戶都需要唯一的 Microsoft 帳戶，或是公司或學校帳戶。

## <a name="organization-structure-and-permissions-by-role"></a>組織結構及依權限的角色

|工作| 企業系統管理員|企業系統管理員 (唯讀)|部門系統管理員|部門系統管理員 (唯讀)|帳戶擁有者|
|---|---|---|---|---|---|
|檢視企業系統管理員|✔|✔|✘|✘|✘|
|新增或移除企業系統管理員|✔|✘|✘|✘|✘|
|檢視通知連絡人<sup>3</sup> |✔|✔|✘|✘|✘|
|新增或移除通知連絡人<sup>3</sup> |✔|✘|✘|✘|✘|
|建立和管理部門 |✔|✘|✘|✘|✘|
|檢視部門系統管理員|✔|✔|✔|✔|✘|
|新增或移除部門系統管理員|✔|✘|✔|✘|✘|
|在註冊中檢視帳戶 |✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✘|
|將帳戶新增至註冊，然後變更帳戶擁有者|✔|✘|✔<sup>4</sup>|✘|✘|
|建立和管理訂用帳戶及訂用帳戶權限|✘|✘|✘|✘|✔|

- <sup>3</sup>通知連絡人會收到有關於 Azure Enterprise 合約的電子郵件通訊。
- <sup>4</sup> 工作僅限於您部門中的帳戶。


## <a name="usage-and-costs-access-by-role"></a>依角色存取的使用方式和成本

|工作| 企業系統管理員|企業系統管理員 (唯讀)|部門系統管理員|部門系統管理員 (唯讀) |帳戶擁有者|
|---|---|---|---|---|---|
|檢視包括承諾用量在內的信用額度結餘|✔|✔|✘|✘|✘|
|檢視部門費用配額|✔|✔|✘|✘|✘|
|設定部門費用配額|✔|✘|✘|✘|✘|
|檢視組織的 EA 價位表|✔|✔|✘|✘|✘|
|檢視使用方式和成本詳細資料|✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✔<sup>6</sup>|
|管理 Azure 入口網站的資源|✘|✘|✘|✘|✔|

- <sup>5</sup> 要求企業系統管理員在企業版入口網站中啟用 **DA 檢視費用**原則。 部門系統管理員接著可以查看部門的成本詳細資料。
- <sup>6</sup> 要求企業系統管理員在企業版入口網站中啟用 **AO 檢視費用**原則。 帳戶擁有者接著可以查看帳戶的成本詳細資料。


## <a name="pricing-in-azure-portal"></a>Azure 入口網站中的價格

根據您的系統管理角色，以及企業系統管理員設定檢視費用原則的方式而定，您可能會在 Azure 入口網站中看到不同的價格。 企業版入口網站中以下的這兩個原則會影響您在 Azure 入口網站中看到價格：

- DA 檢視費用
- AO 檢視費用

若要了解如何設定這些原則，請參閱[管理對 Azure 帳單資訊的存取](manage-billing-access.md)。

下表顯示 Enterprise 合約系統管理角色、檢視費用原則、Azure 入口網站中的角色型存取控制 (RBAC) 角色，以及您在 Azure 入口網站中看到之價格間的關聯性。 企業系統管理員所看到的使用方式詳細資料一律會以組織的 EA 價格為依據。 不過，部門系統管理員和帳戶擁有者會根據檢視費用原則及其 RBAC 角色而看到不同的價格檢視。 下表所列的部門系統管理員角色是指部門系統管理員和部門系統管理員 (唯讀) 角色。

|Enterprise 合約系統管理員角色|角色的檢視費用原則|RBAC 角色|價格檢視|
|---|---|---|---|
|帳戶擁有者或部門系統管理員|✔ 已啟用|擁有者|組織的 EA 價格|
|帳戶擁有者或部門系統管理員|✘ 已停用|擁有者|零售價格|
|帳戶擁有者或部門系統管理員|✔ 已啟用 |無|沒有價格|
|帳戶擁有者或部門系統管理員|✘ 已停用 |無|沒有價格|
|None|不適用 |擁有者|零售價格|

您會在企業版入口網站中設定企業系統管理員角色和檢視費用原則。 RBAC 角色可以在 Azure 入口網站中更新。 如需詳細資料，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>後續步驟

- [管理對 Azure 帳單資訊的存取](manage-billing-access.md)
- [使用 RBAC 和 Azure 入口網站來管理存取權](../../role-based-access-control/role-assignments-portal.md)
- [適用於 Azure 資源的內建角色](../../role-based-access-control/built-in-roles.md)
