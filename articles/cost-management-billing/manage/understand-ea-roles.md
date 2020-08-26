---
title: 了解 Azure 中的企業管理員角色
description: 了解 Azure 中的企業系統管理員角色。 您可以指派五個不同的系統管理角色。
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 787614afa1c71eee075a2e3efa81fa0cba17d1ba
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686457"
---
# <a name="managing-azure-enterprise-roles"></a>管理 Azure 企業角色

為了協助您管理組織的使用方式及費用，具有 Enterprise 合約 (EA) 的 Azure 客戶可以指派五個不同的系統管理角色：

- 企業系統管理員
- 企業系統管理員 (唯讀)<sup>1</sup>
- 部門系統管理員
- 部門系統管理員 (唯讀)
- 帳戶擁有者<sup>2</sup>

<sup>1</sup> EA 合約的帳單連絡人屬於此角色。

<sup>2</sup> 無法在 Azure EA 入口網站中新增或變更帳單連絡人，這些資訊會依合約層級上設定為帳單連絡人的使用者，新增至 EA 註冊。 若要變更帳單連絡人，必須透過合作夥伴/軟體顧問向區域營運中心 (ROC) 提出要求。

註冊佈建期間所設定的第一個註冊管理員，會決定帳單連絡人帳戶的驗證類型。 帳單連絡人以唯讀管理員的身分新增至 EA 入口網站時，系統會為其指定 Microsoft 帳戶驗證。 

例如，如果初始驗證類型設定為「混合」，則 EA 會新增為 Microsoft 帳戶，而帳單與連絡人將會擁有唯讀 EA 管理員權限。 如果 EA 管理員未核准現有帳單連絡人的 Microsoft 帳戶授權，EA 管理員可刪除有問題的使用者，並要求客戶將使用者重新新增為唯讀管理員，並且在 EA 入口網站的註冊層級設定其僅限公司或學校的帳戶。

這些角色專門用來管理 Azure Enterprise 合約，而 Azure 必須控制資源存取權的內建角色除外。 如需詳細資訊，請參閱 [Azure 內建角色](../../role-based-access-control/built-in-roles.md)。

下列各節說明每個角色的限制和功能。

## <a name="user-limit-for-admin-roles"></a>系統管理員角色的使用者限制

|角色| 使用者限制|
|---|---|
|企業系統管理員|無限制|
|企業系統管理員 (唯讀)|無限制|
|部門系統管理員|無限制|
|部門系統管理員 (唯讀)|無限制|
|帳戶擁有者|每個帳戶 1 位<sup>3</sup>|

<sup>3</sup> 每個帳戶都需要唯一的 Microsoft 帳戶，或是公司或學校帳戶。

## <a name="organization-structure-and-permissions-by-role"></a>組織結構及依權限的角色

|工作| 企業系統管理員|企業系統管理員 (唯讀)|部門系統管理員|部門系統管理員 (唯讀)|帳戶擁有者| Partner|
|---|---|---|---|---|---|---|
|檢視企業系統管理員|✔|✔|✘|✘|✘|✔|
|新增或移除企業系統管理員|✔|✘|✘|✘|✘|✘|
|檢視通知連絡人<sup>4</sup> |✔|✔|✘|✘|✘|✔|
|新增或移除通知連絡人<sup>4</sup> |✔|✘|✘|✘|✘|✘|
|建立和管理部門 |✔|✘|✘|✘|✘|✘|
|檢視部門系統管理員|✔|✔|✔|✔|✘|✔|
|新增或移除部門系統管理員|✔|✘|✔|✘|✘|✘|
|在註冊中檢視帳戶 |✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|將帳戶新增至註冊，然後變更帳戶擁有者|✔|✘|✔<sup>5</sup>|✘|✘|✘|
|建立和管理訂用帳戶及訂用帳戶權限|✘|✘|✘|✘|✔|✘|

- <sup>4</sup>通知連絡人會收到有關於 Azure Enterprise 合約的電子郵件通訊。
- <sup>5</sup> 工作僅限於您部門中的帳戶。

## <a name="add-a-new-enterprise-administrator"></a>新增企業系統管理員

在管理 Azure EA 註冊上，企業系統管理員擁有最多權限。 設定 EA 合約時會建立最初的 Azure EA 系統管理員。 不過，您可以隨時新增或移除系統管理員。 新的系統管理員只會由現有的系統管理員新增。 如需新增其他企業系統管理員的詳細資訊，請參閱[建立另一個企業系統管理員](ea-portal-get-started.md#create-another-enterprise-administrator)。如需有關帳單設定檔角色和工作的詳細資訊，請參閱[帳單設定檔角色和工作](understand-mca-roles.md#billing-profile-roles-and-tasks)。

## <a name="update-account-owner-state-from-pending-to-active"></a>將帳戶擁有者狀態從擱置更新為使用中

當新的帳戶擁有者 (AO) 第一次新增至 Azure EA 註冊時，其狀態會顯示為 [擱置]。 當新的帳戶擁有者收到用於啟用的歡迎電子郵件時，他們就可以登入來啟用其帳戶。 當他們啟用帳戶時，帳戶狀態會從 [擱置] 更新為 [作用中]。 帳戶擁有者必須閱讀「警告」訊息，然後選取 [繼續]。 系統可能會提示新使用者輸入其名字和姓氏，以建立商務帳戶。 若是如此，他們必須新增必要的資訊才能繼續，然後帳戶才會啟用。

## <a name="add-a-department-admin"></a>新增部門系統管理員

Azure EA 系統管理員建立部門之後，Azure 企業系統管理員就可以新增部門系統管理員，並將其與部門產生關聯。 部門系統管理員可以建立新的帳戶。 必須有新帳戶，才能建立 Azure EA 訂用帳戶。

如需新增部門管理員的詳細資訊，請參閱[建立 Azure EA 部門管理員](ea-portal-get-started.md#add-a-department-administrator)。

## <a name="usage-and-costs-access-by-role"></a>依角色存取的使用方式和成本

|工作| 企業系統管理員|企業系統管理員 (唯讀)|部門系統管理員|部門系統管理員 (唯讀) |帳戶擁有者| Partner|
|---|---|---|---|---|---|---|
|檢視包括 Azure 預付款在內的信用額度餘額|✔|✔|✘|✘|✘|✔|
|檢視部門費用配額|✔|✔|✘|✘|✘|✔|
|設定部門費用配額|✔|✘|✘|✘|✘|✘|
|檢視組織的 EA 價位表|✔|✔|✘|✘|✘|✔|
|檢視使用方式和成本詳細資料|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|管理 Azure 入口網站的資源|✘|✘|✘|✘|✔|✘|

- <sup>6</sup> 要求企業系統管理員在企業版入口網站中啟用 **DA 檢視費用**原則。 部門系統管理員接著可以查看部門的成本詳細資料。
- <sup>7</sup> 要求企業系統管理員在企業版入口網站中啟用 **AO 檢視費用**原則。 帳戶擁有者接著可以查看帳戶的成本詳細資料。

## <a name="see-pricing-for-different-user-roles"></a>請參閱不同使用者角色的定價

根據您的系統管理角色，以及企業系統管理員設定檢視費用原則的方式而定，您可能會在 Azure 入口網站中看到不同的價格。 企業版入口網站中以下的這兩個原則會影響您在 Azure 入口網站中看到價格：

- DA 檢視費用
- AO 檢視費用

若要了解如何設定這些原則，請參閱[管理對 Azure 帳單資訊的存取](manage-billing-access.md)。

下表顯示 Enterprise 合約管理員角色、檢視費用原則、Azure 入口網站中的 Azure 角色，以及您在 Azure 入口網站中看到之價格間的關聯性。 企業系統管理員所看到的使用方式詳細資料一律會以組織的 EA 價格為依據。 不過，部門系統管理員和帳戶擁有者會根據檢視費用原則及其 Azure 角色而看到不同的價格檢視。 下表所列的部門系統管理員角色是指部門系統管理員和部門系統管理員 (唯讀) 角色。

|Enterprise 合約系統管理員角色|角色的檢視費用原則|Azure 角色|價格檢視|
|---|---|---|---|
|帳戶擁有者或部門系統管理員|✔ 已啟用|擁有者|組織的 EA 價格|
|帳戶擁有者或部門系統管理員|✘ 已停用|擁有者|零售價格|
|帳戶擁有者或部門系統管理員|✔ 已啟用 |無|沒有價格|
|帳戶擁有者或部門系統管理員|✘ 已停用 |無|沒有價格|
|None|不適用 |擁有者|零售價格|

您會在企業版入口網站中設定企業系統管理員角色和檢視費用原則。 Azure 角色可以在 Azure 入口網站中更新。 如需詳細資料，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../../role-based-access-control/role-assignments-portal.md)。



## <a name="next-steps"></a>後續步驟

- [管理對 Azure 帳單資訊的存取](manage-billing-access.md)
- [使用 RBAC 和 Azure 入口網站來管理存取權](../../role-based-access-control/role-assignments-portal.md)
- [Azure 內建角色](../../role-based-access-control/built-in-roles.md)
