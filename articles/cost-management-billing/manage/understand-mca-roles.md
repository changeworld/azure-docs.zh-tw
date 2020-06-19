---
title: Microsoft 客戶合約的計費角色 - Azure
description: 了解 Azure 中可對 Microsoft 客戶合約的計費帳戶進行處理的計費角色。
author: amberbhargava
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 1003e26fa670a804328d5e34def7c9d78eced9c0
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2020
ms.locfileid: "84702596"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>了解 Azure 中的 Microsoft 客戶合約管理角色

若要管理 Microsoft 客戶合約的計費帳戶，請使用以下幾節所說明的角色。 這些角色是額外的角色，獨立於資源存取權須受到 Azure 控制的內建角色以外。 如需詳細資訊，請參閱 [Azure 資源的內建角色](../../role-based-access-control/built-in-roles.md)。

本文適用於 Microsoft 客戶合約的計費帳戶。 請確認您是否有 Microsoft 客戶合約的存取權。

## <a name="billing-role-definitions"></a>計費角色定義

下表說明您用來管理計費帳戶、帳單設定檔和發票區段的計費角色。

|角色|描述|
|---|---|
|計費帳戶擁有者|管理計費帳戶的所有項目|
|計費帳戶參與者|管理計費帳戶上除了權限以外的所有項目|
|計費帳戶讀者|對計費帳戶中所有項目的唯讀檢視|
|帳單設定檔擁有者|管理帳單設定檔的所有項目|
|帳單設定檔參與者|管理帳單設定檔上除了權限以外的所有項目|
|帳單設定檔讀者|對帳單設定檔中所有項目的唯讀檢視|
|發票管理員|檢視和支付帳單設定檔的發票|
|發票區段擁有者|管理發票區段上的所有項目|
|發票區段參與者|管理發票區段上除了權限以外的所有項目|
|發票區段讀者|對發票區段上所有項目的唯讀檢視|
|Azure 訂用帳戶建立者|建立 Azure 訂用帳戶|

## <a name="billing-account-roles-and-tasks"></a>計費帳戶角色和工作

計費帳戶可讓您管理組織的帳單。 您可以使用計費帳戶來組織成本、監視費用和發票，以及控制組織的帳單存取權。 如需詳細資訊，請參閱[了解計費帳戶](../understand/mca-overview.md#your-billing-account)。

下表顯示在計費帳戶的內容中完成工作所需的角色。

### <a name="manage-billing-account-permissions-and-properties"></a>管理計費帳戶權限和屬性

|Task|計費帳戶擁有者|計費帳戶參與者|計費帳戶讀者|
|---|---|---|---|
|檢視計費帳戶的現有權限|✔|✔|✔|
|授與他人檢視和管理計費帳戶的權限|✔|✘|✘|
|檢視計費帳戶屬性，例如公司名稱、地址等等|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>管理計費帳戶的帳單設定檔

|Task|計費帳戶擁有者|計費帳戶參與者|計費帳戶讀者|
|---|---|---|---|
|檢視帳戶中的所有帳單設定檔|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>管理計費帳戶的發票

|Task|計費帳戶擁有者|計費帳戶參與者|計費帳戶讀者|
|---|---|---|---|
|檢視帳戶中的所有發票|✔|✔|✔|
|下載帳戶中的發票、Azure 使用量和費用檔案、價位表及稅務文件|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>管理計費帳戶的發票區段

|Task|計費帳戶擁有者|計費帳戶參與者|計費帳戶讀者|
|---|---|---|---|
|檢視帳戶中的所有發票區段|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>管理計費帳戶的交易

|Task|計費帳戶擁有者|計費帳戶參與者|計費帳戶讀者|
|---|---|---|---|
|檢視帳戶的所有帳單交易|✔|✔|✔|
|查看為帳戶購買的所有產品|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>管理計費帳戶的訂用帳戶

|Task|計費帳戶擁有者|計費帳戶參與者|計費帳戶讀者|
|---|---|---|---|
|檢視計費帳戶中的所有 Azure 訂用帳戶|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>帳單設定檔角色和工作

帳單設定檔可讓您管理發票和付款方式。 針對 Azure 訂用帳戶和使用帳單設定檔購買的其他產品，會產生每月發票。 您可以使用付款方式來支付發票費用。 如需詳細資訊，請參閱[了解帳單設定檔](../understand/mca-overview.md#billing-profiles)。

下表顯示在帳單設定檔的內容中完成工作所需的角色。

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>管理帳單設定檔的權限、屬性和原則

|Task|帳單設定檔擁有者|帳單設定檔參與者|帳單設定檔讀者|發票管理員|計費帳戶擁有者|計費帳戶參與者|計費帳戶讀者
|---|---|---|---|---|---|---|---|
|檢視帳單設定檔的現有權限|✔|✔|✔|✔|✔|✔|✔|
|授與他人檢視和管理帳單設定檔的權限|✔|✘|✘|✘|✘|✘|✘|
|檢視帳單設定檔屬性，例如採購單號碼、電子郵件發票喜好設定等等|✔|✔|✔|✔|✔|✔|✔|
|更新帳單設定檔屬性 |✔|✔|✘|✘|✘|✘|✘|
|檢視在帳單設定檔上套用的原則，例如啟用 Azure 保留購買、啟用 Azure Marketplace 購買等等|✔|✔|✔|✔|✔|✔|✔|
|在帳單設定檔上套用原則 |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>管理帳單設定檔的發票

|Task|帳單設定檔擁有者|帳單設定檔參與者|帳單設定檔讀者|發票管理員|計費帳戶擁有者|計費帳戶參與者|計費帳戶讀者
|---|---|---|---|---|---|---|---|
|檢視帳單設定檔的所有發票|✔|✔|✔|✔|✔|✔|✔|
|下載帳單設定檔的發票、Azure 使用量和費用檔案、價位表及稅務文件|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>管理帳單設定檔的發票區段

|Task|帳單設定檔擁有者|帳單設定檔參與者|帳單設定檔讀者|發票管理員|計費帳戶擁有者|計費帳戶參與者|計費帳戶讀者
|---|---|---|---|---|---|---|---|
|檢視帳單設定檔的所有發票區段|✔|✔|✔|✔|✔|✔|✔|
|為帳單設定檔建立新的發票區段|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>管理帳單設定檔的交易

|Task|帳單設定檔擁有者|帳單設定檔參與者|帳單設定檔讀者|發票管理員|計費帳戶擁有者|計費帳戶參與者|計費帳戶讀者
|---|---|---|---|---|---|---|---|
|檢視帳單設定檔的所有帳單交易|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>管理帳單設定檔的付款方式

|Task|帳單設定檔擁有者|帳單設定檔參與者|帳單設定檔讀者|發票管理員|計費帳戶擁有者|計費帳戶參與者|計費帳戶讀者
|---|---|---|---|---|---|---|---|
|檢視帳單設定檔的付款方式|✔|✔|✔|✔|✔|✔|✔|
|追蹤帳單設定檔的 Azure 點數餘額|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>管理帳單設定檔的訂用帳戶

|Task|帳單設定檔擁有者|帳單設定檔參與者|帳單設定檔讀者|發票管理員|計費帳戶擁有者|計費帳戶參與者|計費帳戶讀者
|---|---|---|---|---|---|---|---|
|檢視帳單設定檔的所有 Azure 訂用帳戶|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>發票區段角色和工作

發票區段可讓您彙整發票上的成本。 您可以建立一個區段，以依據部門、開發環境或組織的需求來彙整成本。 授與他人為區段建立 Azure 訂用帳戶的權限。 訂用帳戶的任何使用量費用和購買隨後都會顯示在發票的區段上。 如需詳細資訊，請參閱[了解發票區段](../understand/mca-overview.md#invoice-sections)。

下表顯示在發票區段的內容中完成工作所需的角色。

### <a name="manage-invoice-section-permissions-and-properties"></a>管理發票區段權限和屬性

|工作|發票區段擁有者|發票區段參與者|發票區段讀者|Azure 訂用帳戶建立者|計費帳戶擁有者|計費帳戶參與者|計費帳戶讀者 | |
|---|---|---|---|---|---|---|---|---|
|檢視發票區段上的所有權限|✔|✔|✔|✔|✔|✔|✔| |
|授與他人檢視和管理發票區段的權限|✔|✘|✘|✘|✘|✘|✘| |
|檢視發票區段屬性|✔|✔|✔|✔|✔|✔|✔| |
|更新發票區段屬性|✔|✔|✘|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>管理發票區段的產品

|工作|發票區段擁有者|發票區段參與者|發票區段讀者|Azure 訂用帳戶建立者|計費帳戶擁有者|計費帳戶參與者|計費帳戶讀者
|---|---|---|---|---|---|---|---|
|檢視在發票區段中購買的所有產品|✔|✔|✔|✘|✔|✔|✔|
|管理發票區段的產品計費，例如取消、關閉自動續約等等|✔|✔|✘|✘|✘|✘|✘|
|變更產品的發票區段|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>管理發票區段的訂用帳戶

|工作|發票區段擁有者|發票區段參與者|發票區段讀者|Azure 訂用帳戶建立者|計費帳戶擁有者|計費帳戶參與者|計費帳戶讀者
|---|---|---|---|---|---|---|---|
|檢視發票區段的所有 Azure 訂用帳戶|✔|✔|✔|✘|✔|✔|✔|
|變更訂用帳戶的發票區段|✔|✔|✘|✘|✘|✘|✘|
|向其他計費帳戶中的使用者要求訂用帳戶的帳單擁有權|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>訂用帳戶計費角色和工作

下表顯示在訂用帳戶的內容中完成工作所需的角色。

|工作|發票區段擁有者|發票區段參與者|發票區段讀者|Azure 訂用帳戶建立者|
|---|---|---|---|---|
|建立 Azure 訂用帳戶|✔|✔|✘|✔|
|更新訂用帳戶的成本中心|✔|✔|✘|✘|
|變更訂用帳戶的發票區段|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>在 Azure 入口網站中管理計費角色

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]  。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/understand-mca-roles/billing-search-cost-management-billing.png)

3. 在您想要授與存取權的範圍內 (例如計費帳戶、帳單設定檔或發票區段)，選取 [存取控制 (IAM)]  。

4. [存取控制 (IAM)] 頁面會列出指派給該範圍內每個角色的使用者和群組。

   ![顯示計費帳戶管理員清單的螢幕擷取畫面](./media/understand-mca-roles/billing-list-admins.png)

5. 若要為使用者授與存取權，請從頁面頂端選取 [新增]  。 在 [角色] 下拉式清單中，選取角色。 輸入您要為其授與存取權之使用者的電子郵件地址。 選取 [儲存]  以指派角色。

   ![顯示將管理員新增至計費帳戶的螢幕擷取畫面](./media/understand-mca-roles/billing-add-admin.png)

6. 若要移除使用者的存取權，請選取要移除的角色指派所屬的使用者。 選取 [移除]。

   ![顯示從計費帳戶中移除管理員的螢幕擷取畫面](./media/understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員
如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

若要了解您的計費帳戶，請參閱下列文章：

- [開始使用 Microsoft 客戶合約的計費帳戶](../understand/mca-overview.md)
- [為您 Microsoft 客戶合約的計費帳戶建立 Azure 訂用帳戶](create-subscription.md)
