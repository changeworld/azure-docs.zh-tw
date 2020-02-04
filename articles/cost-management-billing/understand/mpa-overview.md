---
title: 開始使用您的 Microsoft 合作夥伴合約計費帳戶 - Azure CSP
description: 了解您的 Microsoft 合作夥伴合約計費帳戶 (CSP)
author: bandersmsft
manager: amberbhargava
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2019
ms.author: banders
ms.openlocfilehash: 13ac51fc4cefa4d66cf9d8310dce80704454bd93
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76122146"
---
# <a name="get-started-with-your-microsoft-partner-agreement-billing-account"></a>開始使用您的 Microsoft 合作夥伴合約計費帳戶

當您註冊使用 Azure 時，就會建立計費帳戶。 您可使用計費帳戶來管理發票、付款及追蹤成本。 您可擁有多個計費帳戶的存取權。 例如，您可能已針對您的個人專案註冊 Azure。 但也可以透過貴組織的 Enterprise 合約、Microsoft 客戶合約或 Microsoft 合作夥伴合約取得 Azure 的存取權。 針對上述每個案例，您會有不同的計費帳戶。

本文適用於 Microsoft 合作夥伴合約的計費帳戶。 針對雲端解決方案提供者 (CSP) 建立這些帳戶，以便在新的商務體驗中管理其客戶的帳單。 新的體驗僅適用於下列合作夥伴：至少有一個客戶已接受 Microsoft 客戶合約，且具有 Azure 方案。 [檢查您是否有 Microsoft 合作夥伴合約的存取權](#check-access-to-a-microsoft-partner-agreement)。 [Azure 方案](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)可讓客戶享有 Microsoft 客戶合約權益，以隨用隨付費率存取 Azure 服務。

## <a name="your-billing-account"></a>您的計費帳戶

Microsoft 合作夥伴合約的計費帳戶包含您計費所用之每種貨幣的帳單設定檔。 帳單設定檔可讓您管理發票的貨幣。 當您建立與客戶的關聯性時，視其貨幣而定，Azure 訂用帳戶和其他購買都會計入各自的帳單設定檔。

下圖顯示計費帳戶、帳單設定檔、客戶與轉銷商之間的關聯性。

![顯示 Microsoft 合作夥伴合約計費階層的圖表](./media/mpa-overview/mpa-hierarchy.svg)

貴組織中具有 [全域管理員]  和 [管理員代理人]  角色的使用者可以管理計費帳戶、帳單設定檔和客戶。 若要深入了解，請參閱[合作夥伴中心 - 指派使用者角色和權限](https://docs.microsoft.com/partner-center/permissions-overview)。

## <a name="billing-profiles"></a>帳單設定檔

帳單設定檔可讓您管理某種貨幣的發票。 您帳戶中的每個帳單設定檔都會在月初產生每月發票。 發票包含所有 Azure 訂用帳戶及上個月其他購買的費用 (以帳單設定檔的貨幣計費)。

您可以在 Azure 入口網站中檢視發票和下載相關文件，例如使用量檔案和價位表。 如需詳細資訊，請參閱[下載 Microsoft 合作夥伴合約的發票](download-azure-invoice.md)。

> [!IMPORTANT]
>
> 帳單設定檔的發票包含以下客戶的費用：具有 Azure 方案及 SaaS、Azure Marketplace 和保留購買的客戶，以及尚未接受 Microsoft 客戶合約且沒有 Azure 方案的客戶。

## <a name="customers"></a>客戶

您可以在 Azure 入口網站中，檢視及管理已接受 Microsoft 客戶合約並具有 Azure 方案的客戶。 您可以檢視費用和交易，以及為這些客戶建立和管理 Azure 訂用帳戶。

### <a name="enable-policy-to-give-visibility-into-cost"></a>啟用原則以提供成本的可見度

套用原則，以控制客戶組織中的使用者是否可以針對其 Azure 使用量，以隨用隨付費率檢視和分析成本。 根據預設，此原則會關閉且使用者無法檢視成本。 啟用後，具有訂用帳戶適當 [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) 存取權的使用者即可檢視及分析該訂用帳戶的成本。

若要開啟原則：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 針對 [成本管理 + 帳單]  進行搜尋。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/mpa-overview/search-cmb.png)

1. 從左側選取 [客戶]  ，然後從清單中選取客戶。

   ![顯示選取客戶的螢幕擷取畫面](./media/mpa-overview/mpa-customers.png)

1. 從左側選取 [原則]  。

   ![顯示原則的螢幕擷取畫面](./media/mpa-overview/mpa-change-policy.png)

1. 選取 [是]  。

## <a name="resellers"></a>經銷商

CSP [兩層式模型](https://docs.microsoft.com/partner-center)中的間接提供者在 Azure 入口網站中為客戶建立訂用帳戶時，可以選取轉銷商。 建立後，他們可以檢視依轉銷商篩選的訂用帳戶清單，並在 Azure 成本分析中依轉售商分析客戶的成本。

## <a name="check-access-to-a-microsoft-partner-agreement"></a>檢查 Microsoft 合作夥伴合約的存取權
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

若要了解您的計費帳戶，請參閱下列文章：

- [為 Microsoft 合作夥伴合約建立額外的 Azure 訂用帳戶](../manage/create-subscription.md)
- 使用 [Azure 計費 API](https://docs.microsoft.com/rest/api/billing/) 來整合帳單資料與您自己的報告系統
- [合作夥伴的 Azure 成本管理快速入門指南](https://go.microsoft.com/fwlink/?linkid=2106482)
