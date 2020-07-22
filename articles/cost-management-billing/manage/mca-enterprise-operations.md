---
title: Microsoft 客戶合約中的 EA 工作 - Azure
description: 了解如何在您新的計費帳戶中完成 Enterprise 合約工作。
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: banders
ms.openlocfilehash: 57f77e5cb39da18414ef4287e9306733f403e005
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86523565"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>在您 Microsoft 客戶合約的計費帳戶中完成 Enterprise 合約工作

如果您的組織已簽署 Microsoft 客戶合約以更新 Enterprise 合約註冊，則會為該合約建立新的計費帳戶。 新帳戶中的帳單採用與 Enterprise 合約不同的方式進行組織。 本文說明如何使用新的計費帳戶來執行您在 Enterprise 合約中執行的工作。

## <a name="billing-organization-in-the-new-account"></a>新帳戶中的帳單組織

下圖說明如何在您新的計費帳戶中組織帳單。

![ea-mca-post-transition-hierarchy 的影像](./media/mca-enterprise-operations/mca-post-transition-hierarchy.png)

| Enterprise 合約   | Microsoft 客戶合約    |
|------------------------|--------------------------------------------------------|
| 申請            | 您可以使用帳單設定檔來管理組織的帳單，如同使用您的 Enterprise 合約註冊。 企業系統管理員會成為帳單設定檔的擁有者。 若要深入了解帳單設定檔，請參閱[了解帳單設定檔](../understand/mca-overview.md#billing-profiles)。
| department            | 您可以使用發票區段來彙整成本，如同使用 Enterprise 合約註冊中的部門。 部門會成為發票區段，而部門系統管理員會成為個別發票區段的擁有者。 若要深入了解發票區段，請參閱[了解發票區段](../understand/mca-overview.md#invoice-sections)。 |
| 帳戶               | 新的計費帳戶不支援在您 Enterprise 合約中建立的帳戶。 帳戶的訂用帳戶屬於其部門的個別發票區段。 帳戶擁有者可建立及管理其發票區段的訂用帳戶。 |

## <a name="changes-for-enterprise-administrators"></a>企業系統管理員的變更

已更新為 Microsoft 客戶合約的 Enterprise 合約上的企業系統管理員，將會有下列變更。

- 系統會為您的註冊建立帳單設定檔。 您將使用帳單設定檔來管理組織的帳單，如同使用您的 Enterprise 合約註冊。 若要深入了解帳單設定檔，請參閱[了解帳單設定檔](../understand/mca-overview.md#billing-profiles)。
- 系統會為您 Enterprise 合約註冊中的每個部門建立一個發票區段。 您將使用發票區段來管理您的部門。 您可以建立新的發票區段來設定其他部門。 若要深入了解發票區段，請參閱[了解發票區段](../understand/mca-overview.md#invoice-sections)。
- 您將使用發票區段上的 Azure 訂用帳戶建立者角色，為其他人授與建立 Azure 訂用帳戶的權限，如同在 Enterprise 合約註冊中建立的帳戶。
- 您將使用 [Azure 入口網站](https://portal.azure.com)來管理組織的帳單，而不是 Azure EA 入口網站。

您會在新的計費帳戶上獲得下列角色：

**帳單設定檔擁有者** - 您會在簽署合約時所建立的帳單設定檔上獲派帳單設定檔擁有者角色。 此角色可讓您管理組織的帳單。 您可以檢視費用和發票、彙整發票上的成本、管理付款方式，以及對組織的帳單進行存取控制。

**發票區段擁有者** - 您會在針對 Enterprise 合約註冊中的部門而建立的所有發票區段上，獲派發票區段擁有者角色。 此角色可讓您控制哪些人可以建立 Azure 訂用帳戶以及購買其他產品。

### <a name="view-charges-and-credits-balance-for-your-organization"></a>檢視組織的費用和點數餘額

您可以使用帳單設定檔來追蹤組織的費用和 Azure 點數餘額，如同使用您的 Enterprise 合約註冊。

若要了解如何檢視帳單設定檔的點數餘額，請參閱[追蹤帳單設定檔的 Azure 點數餘額](mca-check-azure-credits-balance.md)。

若要了解如何檢視帳單設定檔的費用，請參閱[了解 Microsoft 客戶合約發票上的費用](../understand/review-customer-agreement-bill.md)。

### <a name="view-charges-for-a-department"></a>檢視部門的費用

系統會為您在 Enterprise 合約中的每個部門建立一個發票區段。 您可以在 Azure 入口網站中檢視發票區段的費用。 如需詳細資訊，請參閱[依發票區段檢視交易](../understand/review-customer-agreement-bill.md#view-transactions-by-invoice-sections)。

### <a name="view-charges-for-an-account"></a>檢視帳戶的費用

新的計費帳戶不支援在您的 Enterprise 合約註冊中建立的帳戶。 帳戶的訂用帳戶屬於其部門的個別發票區段。 帳戶擁有者可建立及管理其發票區段的訂用帳戶。

若要針對屬於某個帳戶的訂用帳戶檢視彙總成本，您必須為每個訂用帳戶設定一個成本中心。 接著，您可以使用 Azure 使用量和費用 CSV 檔案，依成本中心篩選訂用帳戶。

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>下載使用量和費用 CSV、價位表和稅務文件

系統會為您計費帳戶中的每個帳單設定檔產生每月發票。 針對每張發票，您可以下載 Azure 使用量和費用 CSV 檔案、價位表和稅務文件 (如果適用)。 您也可以下載 Azure 使用量和 CSV 檔案以了解當月費用。

若要了解如何下載 Azure 使用量和費用 CSV 檔案，請參閱[下載 Microsoft 客戶合約的使用量](../understand/download-azure-daily-usage.md)。

若要了解如何下載價位表，請參閱[下載 Microsoft 客戶合約的價格](ea-pricing.md)。

若要了解如何下載稅務文件，請參閱[檢視 Microsoft 客戶合約的稅務文件](../understand/mca-download-tax-document.md#view-and-download-tax-documents)。

### <a name="add-an-additional-enterprise-administrator"></a>新增其他企業系統管理員

為使用者授與帳單設定檔的存取權，讓他們能夠檢視及管理組織的帳單。 您可以使用 Azure 入口網站中的 [存取控制 (IAM)] 頁面來授與存取權。  若要深入了解帳單設定檔角色，請參閱[帳單設定檔角色和工作](understand-mca-roles.md#billing-profile-roles-and-tasks)。

若要了解如何提供帳單設定檔的存取權，請參閱[在 Azure 入口網站中管理計費角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

### <a name="create-a-new-department"></a>建立新的部門

您可以建立發票區段以根據您的需求彙整成本，如同使用 Enterprise 合約註冊中的部門。 您可以在 Azure 入口網站中建立新的發票區段。 若要深入了解，請參閱[在您的發票上建立區段以彙整成本](mca-section-invoice.md)。

### <a name="create-a-new-account"></a>建立新帳戶

您可以在發票區段上為使用者指派 Azure 訂用帳戶建立者角色，為他們授與建立 Azure 訂用帳戶的權限，如同在 Enterprise 合約註冊中建立的帳戶。 如需指派腳色的詳細資訊，請參閱[在 Azure 入口網站中管理計費角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

## <a name="changes-for-department-administrators"></a>部門系統管理員的變更

已更新為 Microsoft 客戶合約的 Enterprise 合約上的部門系統管理員，將會有下列變更。

- 系統會為您 Enterprise 合約註冊中的每個部門建立一個發票區段。 您將使用發票區段來管理您的部門。 若要深入了解發票區段，請參閱[了解發票區段](../understand/mca-overview.md#invoice-sections)。
- 您將使用發票區段上的 Azure 訂用帳戶建立者角色，為其他人授與建立 Azure 訂用帳戶的權限，如同在 Enterprise 合約註冊中建立的帳戶。
- 您將使用 Azure 入口網站來管理組織的帳單，而不是 Azure EA 入口網站。

您會在新的計費帳戶上獲得下列角色：

**發票區段擁有者** - 您會在針對 Enterprise 合約中的部門而建立的發票區段上，獲派發票區段擁有者角色。 此角色可讓您檢視和追蹤費用，並控制哪些人可以建立 Azure 訂用帳戶以及為發票區段購買其他產品。

### <a name="view-charges-for-your-department"></a>檢視部門的費用

您可以在 Azure 入口網站的 [[成本管理 + 帳單]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview) 頁面中，檢視針對您的部門而建立之發票區段的費用。

### <a name="add-an-additional-department-administrator"></a>新增其他部門系統管理員

系統會為您在 Enterprise 合約中的每個部門建立一個發票區段。 您可以使用 Azure 入口網站中的 [存取控制 (IAM)] 頁面，為其他人授與檢視和管理發票區段的存取權。 若要深入了解發票區段角色，請參閱[發票區段角色和工作](understand-mca-roles.md#invoice-section-roles-and-tasks)。

若要了解如何提供發票區段的存取權，請參閱[在 Azure 入口網站中管理計費角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

### <a name="create-a-new-account-in-your-department"></a>在您的部門中建立新的帳戶

在針對您的部門而建立的發票區段上，為使用者指派 Azure 訂用帳戶建立者角色。 如需指派腳色的詳細資訊，請參閱[在 Azure 入口網站中管理計費角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="view-charges-for-accounts-in-your-departments"></a>檢視部門中的帳戶費用

新的計費帳戶不支援在您的 Enterprise 合約註冊中建立的帳戶。 帳戶的訂用帳戶屬於其部門的個別發票區段。 帳戶擁有者可建立及管理其發票區段的訂用帳戶。

若要針對屬於您的部門的訂用帳戶檢視彙總成本，您必須為每個訂用帳戶設定一個成本中心。 接著，您可以使用 Azure 使用量和費用檔案，依成本中心篩選訂用帳戶。

## <a name="changes-for-account-owners"></a>帳戶擁有者的變更

Enterprise 合約上的帳戶擁有者會取得在新的計費帳戶上建立 Azure 訂用帳戶的權限。 您現有的 Azure 訂用帳戶屬於為您的部門建立的發票區段。 如果您的帳戶不屬於任何部門，您的訂用帳戶將屬於名為「預設發票區段」的發票區段。

若要建立額外的 Azure 訂用帳戶，您會在新的計費帳戶上獲得下列角色。

**Azure 訂用帳戶建立者** - 您會在針對 Enterprise 合約中的部門而建立的發票區段上，獲派 Azure 訂用帳戶建立者角色。 如果您的帳戶不屬於任何部門，則會在名為「預設發票區段」的區段上取得 Azure 訂用帳戶建立者角色。 此角色可讓您建立發票區段的 Azure 訂用帳戶。

### <a name="create-an-azure-subscription"></a>建立 Azure 訂用帳戶

您可以在 Azure 入口網站中為您的發票區段建立 Azure 訂用帳戶。 如需詳細資訊，請參閱[為 Microsoft 客戶合約建立額外的 Azure 訂用帳戶](create-subscription.md)

### <a name="view-charges-for-your-account"></a>檢視帳戶的費用

若要對屬於某個帳戶的訂用帳戶檢視費用，請移至 Azure 入口網站中的[訂用帳戶頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。 [訂用帳戶] 頁面會顯示您所有訂用帳戶的費用。

### <a name="view-charges-for-a-subscription"></a>檢視訂用帳戶的費用

您可以在[訂用帳戶頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)或 Azure 成本分析上檢視訂用帳戶的費用。 如需 Azure 成本分析的詳細資訊，請參閱[使用成本分析探索及分析成本](../costs/quick-acm-cost-analysis.md)。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

- [了解 Microsoft 客戶合約的計費帳戶](../understand/mca-overview.md)
- [了解您的發票](../understand/review-individual-bill.md)
- [了解您的帳單](../understand/understand-invoice.md)
- [向其他使用者接管 Azure 訂用帳戶的帳單擁有權](mca-request-billing-ownership.md)
