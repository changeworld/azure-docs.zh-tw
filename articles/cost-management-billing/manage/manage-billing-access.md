---
title: 管理 Azure 帳單的存取權
description: 了解如何為您的小組成員提供對 Azure 計費資訊的存取權。
author: vikramdesai01
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: ccdd7b16c7ec292b8f4f4c155a6f9497c606a489
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371808"
---
# <a name="manage-access-to-billing-information-for-azure"></a>管理對 Azure 帳單資訊的存取

您可以在 Azure 入口網站中，為其他人提供您帳戶的計費資訊存取權。 計費角色的類型以及提供計費資訊存取權的指示，會因您的計費帳戶類型而異。 若要判斷您的計費帳戶類型，請參閱[檢查計費帳戶的類型](#check-the-type-of-your-billing-account)。

本文適用於具有 Microsoft Online Service 方案帳戶的客戶。 如果您是具有 Enterprise 合約的 Azure 客戶 (EA) 和企業系統管理員，您可以在企業版入口網站中，將權限授與部門系統管理員和帳戶擁有者。 如需詳細資訊，請參閱[了解 Azure 中的 Azure Enterprise 合約系統管理角色](understand-ea-roles.md)。 如果您是 Microsoft 客戶合約客戶，請參閱[了解 Azure 中的 Microsoft 客戶合約系統管理角色](understand-mca-roles.md)。

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Microsoft Online Service 方案帳戶的帳戶管理員

帳戶管理員是 Microsoft Online Service 方案計費帳戶的唯一擁有者。 系統會將角色指派給註冊 Azure 的人員。 帳戶管理員有權執行各種計費工作，例如建立訂用帳戶、檢視發票或變更訂用帳戶的計費。

## <a name="give-others-access-to-view-billing-information"></a>授與其他人檢視計費資訊的存取權

帳戶管理員可藉由在其帳戶中的某個訂用帳戶上指派下列其中一個角色，授與其他人 Azure 計費資訊的存取權。

- 服務管理員
- 共同管理員
- 擁有者
- 參與者
- 讀取者
- 帳單讀者

這些角色可以在 [Azure 入口網站](https://portal.azure.com/)中存取計費資訊。 獲指派這些角色的人員也可以使用[計費 API](usage-rate-card-overview.md)，以程式設計方式取得發票和使用量詳細資料。

若要指派角色，請參閱[使用 Azure 入口網站新增或移除 Azure 角色指派](../../role-based-access-control/role-assignments-portal.md)。

** 如果您是 EA 客戶，帳戶擁有者可以將上述角色指派給其小組的其他使用者。 但是為了讓這些使用者檢視計費資訊，企業系統管理員必須在 Enterprise 入口網站中啟用 AO 檢視費用。


### <a name="allow-users-to-download-invoices"></a><a name="opt-in"></a> 允許使用者下載發票

在帳戶管理員將適當的角色指派給其他使用者之後，他們必須開啟存取才能在 Azure 入口網站中下載發票。 2016 年 12 月以前的發票僅供帳戶管理員使用。

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。

1. 針對 [成本管理 + 帳單]  進行搜尋。

    ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. 從左側窗格中選取 [訂用帳戶]  。 視存取權而定，您可能需要選取計費範圍，然後選取 [訂用帳戶]  。

    ![顯示選取訂用帳戶的螢幕擷取畫面](./media/manage-billing-access/billing-select-subscriptions.png)

1. 選取 [發票]  ，然後選取 [存取發票]  。

    ![螢幕擷取畫面顯示如何委派存取發票](./media/manage-billing-access/aa-optin01.png)

1. 選取 [開啟]  ，然後儲存。

    ![螢幕擷取畫面顯示委派存取發票的開關](./media/manage-billing-access/aa-optinallow01.png)

「帳戶管理員」也可以設定透過電子郵件傳送發票。 若要深入了解，請參閱[透過電子郵件取得發票](download-azure-invoice-daily-usage-date.md)。

## <a name="give-read-only-access-to-billing"></a>授與帳單唯讀存取權

請將「帳單讀者」角色指派給需要以唯讀方式存取訂用帳戶帳單資訊，但不需要能夠管理或建立 Azure 服務的使用者。 此角色適用於組織內負責 Azure 訂用帳戶之財務和成本管理的使用者。

「帳單讀者」功能目前為預覽版，尚未支援非全球雲端。

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。

1. 針對 [成本管理 + 帳單]  進行搜尋。

    ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. 從左側窗格中選取 [訂用帳戶]  。 視存取權而定，您可能需要選取計費範圍，然後選取 [訂用帳戶]  。

    ![顯示選取訂用帳戶的螢幕擷取畫面](./media/manage-billing-access/billing-select-subscriptions.png)

1. 選取 [存取控制 (IAM)]  。
1. 從頁面頂端選取 [新增]  。

    ![顯示按一下新增角色指派的螢幕擷取畫面](./media/manage-billing-access/billing-click-add-role-assignment.png)

1. 在 [角色]  下拉式清單中，選擇 [帳單讀者]  。
1. 在 [選取]  文字方塊中，鍵入您要新增的使用者名稱或電子郵件。
1. 選取使用者。
1. 選取 [儲存]  。
    ![顯示按一下新增角色指派的螢幕擷取畫面](./media/manage-billing-access/billing-save-role-assignment.png)

1. 在幾分鐘之後，即會針對訂用帳戶將「帳單讀者」角色指派給使用者。

** 如果您是 EA 客戶，帳戶擁有者或部門系統管理員可以將「帳單讀者」角色指派給小組成員。 但若要讓帳單讀者檢視部門或帳戶的帳單資訊，企業系統管理員必須在企業版入口網站中啟用 [AO 檢視費用]  或 [DA 檢視費用]  原則。

## <a name="check-the-type-of-your-billing-account"></a>檢查您的計費帳戶類型
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>後續步驟

- 具備其他角色 (例如「擁有者」和「參與者」) 的使用者不僅可存取帳單資訊，還可存取 Azure 服務。 若要管理這些角色，請參閱[使用 Azure 入口網站新增或移除 Azure 角色指派](../../role-based-access-control/role-assignments-portal.md)。
- 如需角色的詳細資訊，請參閱 [Azure 內建角色](../../role-based-access-control/built-in-roles.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。
