---
title: 建立額外的 Azure 訂用帳戶
description: 了解如何在 Azure 入口網站中新增 Azure 訂用帳戶。
services: billing
documentationcenter: ''
author: amberb
manager: amberb
editor: ''
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: banders
ms.openlocfilehash: 33fa553e9148a1ac838e6bf53fc83cf7eadd7499
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "75992578"
---
# <a name="create-an-additional-azure-subscription"></a>建立額外的 Azure 訂用帳戶

您可以在 Azure 入口網站中為您的 [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)、[Microsoft 客戶合約](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)或 [Microsoft 合作夥伴合約](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement)計費帳戶建立額外的訂用帳戶。 您可能需要額外的訂用帳戶以避免達到訂用帳戶限制、基於安全性目的建立個別環境，或基於合規性因素隔離資料。

如果您有 Microsoft 線上服務方案 (MOSP) 計費帳戶，您可以在 [Azure 註冊入口網站](https://account.azure.com/signup?offer=ms-azr-0003p)中建立額外的訂用帳戶。

若要深入了解計費帳戶及識別您的計費帳戶類型，請參閱[在 Azure 入口網站中檢視計費帳戶](view-all-accounts.md)。

## <a name="permission-required-to-create-azure-subscriptions"></a>建立 Azure 訂用帳戶所需的權限

您需要下列權限才能建立訂用帳戶：

|計費帳戶  |權限  |
|---------|---------|
|Enterprise 合約 (EA) |  Enterprise 合約註冊的帳戶擁有者角色。 如需詳細資訊，請參閱[了解 Azure 中的 Azure Enterprise 合約系統管理角色](understand-ea-roles.md)。    |
|Microsoft 客戶合約 (MCA) |  發票區段、帳單設定檔或計費帳戶的擁有者或參與者角色。 或發票區段的 Azure 訂用帳戶建立者角色。  如需詳細資訊，請參閱[訂用帳戶計費角色和工作](understand-mca-roles.md#subscription-billing-roles-and-tasks)。    |
|Microsoft 合作夥伴合約 (MPA) |   CSP 合作夥伴組織中的全域管理員和管理員代理人角色。 若要深入了解，請參閱[合作夥伴中心 - 指派使用者角色和權限](https://docs.microsoft.com/partner-center/permissions-overview)。  使用者必須登入合作夥伴租用戶，才能建立 Azure 訂用帳戶。   |

## <a name="create-a-subscription-in-the-azure-portal"></a>在 Azure 入口網站中建立訂用帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 尋找**訂用帳戶**。

   ![顯示在入口網站中搜尋訂用帳戶的螢幕擷取畫面](./media/create-subscription/billing-search-subscription-portal.png)

1. 選取 [新增]  。

   ![在 [訂用帳戶] 檢視中顯示 [新增] 按鈕的螢幕擷取畫面](./media/create-subscription/subscription-add.png)

1. 如果您有多個計費帳戶的存取權，請選取您想建立訂用帳戶的計費帳戶。

1. 填妥表單，然後按一下 [建立]  。 下列表格針對每種計費帳戶類型列出表單上的欄位。

**Enterprise 合約**

|欄位  |定義  |
|---------|---------|
|名稱     | 可協助您輕鬆識別 Azure 入口網站中訂用帳戶的顯示名稱。  |
|供應項目     | 如果您打算將此訂用帳戶用於開發或測試工作負載，請選取 [EA 開發/測試]，否則使用 [Microsoft Azure 方案]。 您的註冊帳戶必須啟用 DevTest 供應項目，才能建立 EA 開發/測試訂用帳戶。|

**Microsoft 客戶合約**

|欄位  |定義  |
|---------|---------|
|帳單設定檔     | 訂用帳戶的費用將會計入您所選的帳單設定檔。 如果您只能存取一個帳單設定檔，選取項目將會呈現為灰色。     |
|發票區段     | 訂用帳戶的費用將會顯示在帳單設定檔發票的這個發票區段。 如果您只能存取一個發票區段，選取項目將會呈現為灰色。  |
|計畫     | 如果您打算將此訂用帳戶用於開發或測試工作負載，請選取 [適用於 DevTest 的 Microsoft Azure 方案]，否則請使用 [Microsoft Azure 方案]。 如果帳單設定檔只啟用一個方案，則選取項目會呈現為灰色。  |
|名稱     | 可協助您輕鬆識別 Azure 入口網站中訂用帳戶的顯示名稱。  |

**Microsoft 合作夥伴合約**

|欄位  |定義  |
|---------|---------|
|客戶    | 系統會為您選取的客戶建立訂用帳戶。 如果您只有一個客戶，則選取項目會呈現為灰色。  |
|Reseller    | 將提供服務給客戶的轉銷商。 這是選擇性欄位，僅適用於 CSP 兩層式模型中的間接提供者。 |
|名稱     | 可協助您輕鬆識別 Azure 入口網站中訂用帳戶的顯示名稱。  |

## <a name="create-an-additional-azure-subscription-programmatically"></a>以程式設計方式建立額外的 Azure 訂用帳戶

您也可以利用程式設計方式來建立額外的訂用帳戶。 如需詳細資訊，請參閱[以程式設計方式建立 Azure 訂用帳戶](../../azure-resource-manager/management/programmatically-create-subscription.md)。

## <a name="next-steps"></a>後續步驟

- [新增或變更 Azure 訂用帳戶管理員](add-change-subscription-administrator.md)
- [將資源移動到新的資源群組或訂用帳戶](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [建立用於資源組織及管理的管理群組](../../governance/management-groups/create.md)
- [取消您的 Azure 訂用帳戶](cancel-azure-subscription.md)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。
