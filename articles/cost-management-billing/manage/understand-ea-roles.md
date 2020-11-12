---
title: 了解 Azure 中的企業管理員角色
description: 了解 Azure 中的企業系統管理員角色。 您可以指派五個不同的系統管理角色。
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: e712b44f22a8080b14a2cc2532cadf2dd4738b76
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409192"
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

## <a name="azure-enterprise-portal-hierarchy"></a>Azure 企業版入口網站階層

Azure 企業版入口網站階層包含：

- **Azure 企業版入口網站** - 可協助您管理 Azure EA 服務成本的線上管理入口網站。 您可以：

  - 建立 Azure EA 階層，其中包含部門、帳戶和訂用帳戶。
  - 協調已取用服務的成本、下載使用量報告，以及查看價格清單。
  - 建立用於註冊的 API 金鑰。

- **部門** 可協助您將成本劃分成邏輯群組。 部門可讓您設定部門層級的預算或配額。

- **帳戶** 是 Azure 企業版入口網站中的組織單位。 您可以使用帳戶來管理訂用帳戶和存取報告。

- **訂用帳戶** 是 Azure 企業版入口網站中的最小單位。 這些訂用帳戶是由服務管理員所管理的 Azure 服務容器。

下圖說明簡單的 Azure EA 階層。

![簡單 Azure EA 階層的圖表](./media/understand-ea-roles/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>企業使用者角色

下列系統管理使用者角色是您企業註冊的一部分：

- 企業系統管理員
- 部門系統管理員
- 帳戶擁有者
- 服務管理員
- 通知連絡人

角色可在兩個不同的入口網站中完成工作。 您可以使用 [Azure 企業版入口網站](https://ea.azure.com)來管理計費和成本，以及使用 [Azure 入口網站](https://portal.azure.com)來管理 Azure 服務。

使用者角色會與使用者帳戶相關聯。 若要驗證使用者的真確性，每位使用者都必須擁有有效的公司、學校或 Microsoft 帳戶。 請確定每個帳戶都與主動監視的電子郵件地址相關聯。 帳戶通知會傳送至該電子郵件地址。

設定使用者時，您可以將多個帳戶指派給企業系統管理員角色。 不過，只有一個帳戶可以持有帳戶擁有者角色。 此外，您可以將企業系統管理員和帳戶擁有者角色指派給單一帳戶。

### <a name="enterprise-administrator"></a>企業系統管理員

具有此角色的使用者具備最高層級的存取權。 他們可以：

- 管理帳戶和帳戶擁有者。
- 管理其他企業系統管理員。
- 管理部門系統管理員。
- 管理通知連絡人。
- 檢視所有帳戶上的使用量。
- 檢視所有帳戶上未開立帳單的費用。

一個企業註冊中可以有多個企業系統管理員。 您可以將唯讀存取權授與企業系統管理員。 他們都會繼承部門系統管理員角色。

### <a name="department-administrator"></a>部門系統管理員

具有此角色的使用者可以：

- 建立和管理部門。
- 建立新的帳戶擁有者。
- 針對他們所管理的部門檢視使用量詳細資料。
- 檢視成本 (若已具備必要權限)。

每個企業註冊可以有多個部門系統管理員。

當您編輯或建立新的部門系統管理員時，您可以授與部門系統管理員唯讀存取權。 將 [唯讀] 選項設為 [是]。

### <a name="account-owner"></a>帳戶擁有者

具有此角色的使用者可以：

- 建立及管理訂用帳戶。
- 管理服務管理員。
- 檢視訂用帳戶的使用量。

每個帳戶都需要唯一的公司、學校或 Microsoft 帳戶。 如需有關 Azure 企業版入口網站系統管理角色的詳細資訊，請參閱[了解 Azure 中的 Azure Enterprise 合約系統管理角色](understand-ea-roles.md)。

### <a name="service-administrator"></a>服務管理員

服務管理員角色有權管理 Azure 入口網站中的服務，並可將使用者指派給共同管理員角色。

### <a name="notification-contact"></a>通知連絡人

通知連絡人可接收與註冊相關的使用量通知。

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

在管理 Azure EA 註冊上，企業系統管理員擁有最多權限。 設定 EA 合約時會建立最初的 Azure EA 系統管理員。 不過，您可以隨時新增或移除系統管理員。 新的系統管理員只會由現有的系統管理員新增。 如需新增其他企業系統管理員的詳細資訊，請參閱[建立另一個企業系統管理員](ea-portal-administration.md#create-another-enterprise-administrator)。如需有關帳單設定檔角色和工作的詳細資訊，請參閱[帳單設定檔角色和工作](understand-mca-roles.md#billing-profile-roles-and-tasks)。

## <a name="update-account-owner-state-from-pending-to-active"></a>將帳戶擁有者狀態從擱置更新為使用中

當新的帳戶擁有者 (AO) 第一次新增至 Azure EA 註冊時，其狀態會顯示為 [擱置]。 當新的帳戶擁有者收到用於啟用的歡迎電子郵件時，他們就可以登入來啟用其帳戶。 當他們啟用帳戶時，帳戶狀態會從 [擱置] 更新為 [作用中]。 帳戶擁有者必須閱讀「警告」訊息，然後選取 [繼續]。 系統可能會提示新使用者輸入其名字和姓氏，以建立商務帳戶。 若是如此，他們必須新增必要的資訊才能繼續，然後帳戶才會啟用。

## <a name="add-a-department-admin"></a>新增部門系統管理員

Azure EA 系統管理員建立部門之後，Azure 企業系統管理員就可以新增部門系統管理員，並將其與部門產生關聯。 部門系統管理員可以建立新的帳戶。 必須有新帳戶，才能建立 Azure EA 訂用帳戶。

如需新增部門管理員的詳細資訊，請參閱[建立 Azure EA 部門管理員](ea-portal-administration.md#add-a-department-administrator)。

## <a name="usage-and-costs-access-by-role"></a>依角色存取的使用方式和成本

|工作| 企業系統管理員|企業系統管理員 (唯讀)|部門系統管理員|部門系統管理員 (唯讀) |帳戶擁有者| Partner|
|---|---|---|---|---|---|---|
|檢視包括 Azure 預付款在內的信用額度餘額|✔|✔|✘|✘|✘|✔|
|檢視部門費用配額|✔|✔|✘|✘|✘|✔|
|設定部門費用配額|✔|✘|✘|✘|✘|✘|
|檢視組織的 EA 價位表|✔|✔|✘|✘|✘|✔|
|檢視使用方式和成本詳細資料|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|管理 Azure 入口網站的資源|✘|✘|✘|✘|✔|✘|

- <sup>6</sup> 要求企業系統管理員在企業版入口網站中啟用 **DA 檢視費用** 原則。 部門系統管理員接著可以查看部門的成本詳細資料。
- <sup>7</sup> 要求企業系統管理員在企業版入口網站中啟用 **AO 檢視費用** 原則。 帳戶擁有者接著可以查看帳戶的成本詳細資料。

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
