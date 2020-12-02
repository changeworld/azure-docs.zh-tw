---
title: 開始使用 Azure 企業版入口網站
description: 本文說明 Azure Enterprise 合約 (Azure EA) 客戶如何使用 Azure 企業版入口網站。
author: bandersmsft
ms.reviewer: baolcsva
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/28/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: 0f4f4d934361f1e59eceaaccca680f0cf155bb04
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348954"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>開始使用 Azure 企業版入口網站

本文協助直接和間接 Azure Enterprise 合約 (Azure EA) 客戶開始使用 [Azure 企業版入口網站](https://ea.azure.com)。 取得下列各項的基本資訊：

- Azure 企業版入口網站的結構。
- Azure 企業版入口網站中使用的角色。
- 訂用帳戶建立。
- Azure 企業版入口網站和 Azure 入口網站中的成本分析。

## <a name="get-started-with-ea-onboarding"></a>開始使用 EA 上線

如需 Azure EA 上線指南，請參閱 [Azure EA 上線指南 (PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide)。

觀看這段影片以觀賞完整的 Azure 企業版入口網站上線工作階段：

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="understanding-ea-user-roles-and-introduction-to-user-hierarchy"></a>了解 EA 使用者角色和使用者階層簡介

為了協助您管理組織的使用方式及費用，具有 Enterprise 合約 (EA) 的 Azure 客戶可以指派五個不同的系統管理角色：

- 企業系統管理員
- 企業系統管理員 (唯讀)
- 部門系統管理員
- 部門系統管理員 (唯讀)
- 帳戶擁有者

每個角色都有不同程度的使用者限制和權限。 如需詳細資訊，請參閱[組織結構及依權限的角色](./understand-ea-roles.md#organization-structure-and-permissions-by-role)。

## <a name="activate-your-enrollment-create-a-subscription-and-other-administrative-tasks"></a>啟用您的註冊、建立訂用帳戶，及執行其他管理工作

如需關於啟用註冊、建立部門或訂用帳戶、新增系統管理員和帳戶擁有者，以及執行其他管理工作的詳細資訊，請參閱 [Azure EA 入口網站管理](./ea-portal-administration.md)。

若要深入了解如何將企業版訂用帳戶轉移至隨用隨付訂用帳戶，請參閱 [Azure 企業版轉移](./ea-transfers.md)。

## <a name="view-usage-summary-and-download-reports"></a>檢視使用量摘要及下載報表

您可以管理 Azure EA 發票並對其採取行動。 您的發票即代表帳單，您應該仔細檢查其正確性。

若要檢視使用摘要、下載報表，以及管理註冊發票，請參閱 [Azure 企業版註冊發票](./ea-portal-enrollment-invoices.md)。

## <a name="now-that-youre-familiar-with-the-basics-here-are-some-additional-links-to-help-you-get-onboarded"></a>現在您已熟悉基本概念，以下是一些可協助您進行上線的其他連結

[Azure EA 定價](./ea-pricing-overview.md)提供如何計算使用量的詳細資料，並解說 Enterprise 合約中計算方式較為複雜的 Azure 服務費用。

如果您想要知道 VM 保留執行個體的 Azure 保留如何協助您透過企業版註冊節省經費，請參閱 [Azure EA VM 保留執行個體](./ea-portal-vm-reservations.md)。

如需關於應使用哪些 REST API 進行 Azure 企業版註冊的資訊，以及如何解決 REST API 常見問題的說明，請參閱 [Azure 企業版 REST API](./ea-portal-rest-apis.md)。

[Azure EA 合約和修訂文件](./ea-portal-agreements.md)會說明 Azure EA 合約和修訂文件對您存取、使用和支付 Azure 服務的方式有何影響。

[Azure Marketplace](./ea-azure-marketplace.md) 會說明 EA 客戶和合作夥伴如何檢視市集費用，以及啟用 Azure Marketplace 購買。

如需合作夥伴 EA 系統管理員在 Azure EA 入口網站中完成之一般工作的說明，請參閱[適用於合作夥伴的 Azure EA 入口網站管理](./ea-partner-portal-administration.md)。

## <a name="get-started-on-azure-ea---faq"></a>開始使用 Azure EA - 常見問題集

這一節提供在上線過程中，客戶所詢問的一般問題詳細資料。  

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>我不小心將現有的 Azure 帳戶與 Azure EA 註冊建立關聯。 因此，我失去了每月點數。 我可以拿回每月點數嗎？

如果您以具有與 Visual Studio 訂用帳戶相同認證的 Azure EA 帳戶擁有者身分登入，您可以執行下列其中一項動作來復原個別 Visual Studio 訂用帳戶的 Azure 權益：

- 移除或移動任何相關聯的 Azure 訂用帳戶之後，請從 Azure 企業版入口網站刪除您的帳戶擁有者。 然後，再次註冊個別 Visual Studio Azure 權益。
- 從 VLSC 中的系統管理網站刪除 Visual Studio 訂閱者，這次將訂用帳戶重新指派給具有不同認證的帳戶。 然後，再次註冊個別 Visual Studio Azure 權益。

### <a name="what-type-of-subscription-should-i-create"></a>我應該建立哪種類型的訂用帳戶？

Azure 企業版入口網站為企業客戶提供兩種類型的訂用帳戶：

- Microsoft Azure 企業版，其適合用於：
  - 所有生產使用量
  - 以基礎結構費用為基礎的最佳價格

  如需詳細資訊，[請連絡 Azure 業務代表](https://azure.microsoft.com/pricing/enterprise-agreement/)。

- Enterprise 開發/測試，其適合用於：
  - 所有團隊開發/測試工作負載
  - 中度至重度的個人開發/測試工作負載
  - 存取特殊 MSDN 映像和優惠服務費率

  如需詳細資訊，請參閱 [Enterprise 開發/測試供應項目](https://azure.microsoft.com/offers/ms-azr-0148p/)。

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>我的訂用帳戶名稱與供應項目名稱相同。 我應該將訂用帳戶名稱變更為對我組織有意義的名稱嗎？

當您建立訂用帳戶時，名稱會預設為您選擇的供應項目類型。 我們建議您將訂用帳戶名稱變更為可讓您輕鬆追蹤訂用帳戶的名稱。

若要變更名稱：

1. 登入 [https://account.windowsazure.com](https://account.windowsazure.com)。
1. 選取訂用帳戶清單。
1. 選取您要編輯的訂用帳戶。
1. 選取 [管理訂用帳戶] 圖示。
1. 編輯訂用帳戶詳細資料。

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>如何追蹤成本中心所產生的成本？

若要依成本中心追蹤成本，您必須在下列其中一個層級定義成本中心：

- department
- 帳戶
- 訂用帳戶

根據您的需求，您可以使用相同的成本中心來追蹤與特定成本中心相關聯的使用量和成本。

例如，為了追蹤牽涉到多個部門的特殊專案成本，您可以在訂用帳戶層級定義成本中心來追蹤使用量和成本。

您不能在服務層級定義成本中心。 如果您想要追蹤服務層級的使用量，可以使用服務層級提供的「標記」功能。

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>如何追蹤組織中不同部門的使用量和支出？

您可以視需要在 Azure EA 註冊之下建立許多部門。 若要正確地追蹤使用量，請確保您不會跨部門共用訂用帳戶。

建立部門和訂用帳戶之後，您可以在使用量報告中查看資料。 此資訊可協助您追蹤使用量，以及管理部門層級的成本與支出。

您也可以透過報告 API 來存取使用量資料。 如需詳細資訊和範例程式碼，請參閱 [Azure Enterprise REST API](./ea-portal-rest-apis.md)。

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>我可以設定消費配額，並在接近限制時收到警示嗎？

您可以設定部門層級的消費配額，而系統會在您的消費限制符合您所定義配額的 50%、75%、90% 和 100% 時自動通知您。

若要定義消費配額，請選取部門，然後選取編輯圖示。 編輯消費限制詳細資料之後，請選取 [儲存]。

### <a name="i-used-resource-groups-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>我使用了資源群組來實行 RBAC 和追蹤使用量。 如何才能檢視相關聯的使用量詳細資料？

如果您使用「資源群組」和「標記」，則會在服務層級追蹤此資訊，而且您可在詳細使用量下載 (CSV) 檔案中加以存取。 請參閱 Azure 企業版入口網站中的[下載使用量報告](https://ea.azure.com/report/downloadusage)。

您也可以透過 API 存取使用量。 如需詳細資訊和範例程式碼，請參閱 [Azure Enterprise REST API](./ea-portal-rest-apis.md)。

> [!NOTE]
> 您只能將標籤套用到支援 Azure Resource Manager 作業的資源。 如果您透過傳統部署模型 (例如透過傳統入口網站) 建立虛擬機器、虛擬網路或儲存體，就無法將標記套用至該資源。 您必須透過 Resource Manager 重新部署這些資源才能支援標記。 所有其他資源皆支援標記。

### <a name="can-i-perform-analyses-using-power-bi"></a>我可以使用 Power BI 執行分析嗎？

是。 使用適用於 Power BI 的 Microsoft Azure 企業版內容套件，您可以：

- 快速匯入和分析您企業註冊的 Azure 耗用量。
- 找出哪個部門、帳戶或訂用帳戶耗用了最多使用量。
- 了解貴組織最常使用的服務。
- 追蹤消費和使用量趨勢。

若要使用 Power BI：

1. 移至 Power BI 網站。
1. 使用有效的公司或學校帳戶登入。

   公司或學校帳戶可以是用於透過 Azure 企業版入口網站存取註冊的相同或不同帳戶。
1. 在服務的儀表板上，選擇 Microsoft Azure 企業版圖格，然後按一下 [連線]。
1. 在 [連線到 Azure 企業版] 畫面上，輸入：
    - Azure 環境 URL：[https://ea.azure.com](https://ea.azure.com)
    - 月數：介於 1 與 36 之間
    - 註冊號碼：您的註冊號碼
1. 選取 [下一步]  。
1. 在 [帳戶金鑰] 方塊中輸入 API 金鑰。

   您可以在 Azure 企業版入口網站中找到 API 金鑰。 在 [下載使用量] 索引標籤底下查看，然後選取 [API 存取金鑰]。 複製該金鑰，然後其貼到 Power BI 的 [帳戶金鑰] 方塊中。

視資料集的大小而定，可能需要 5 到 30 分鐘的時間，才能在 Power BI 中載入資料。

Power BI 報告適用於能夠檢視帳單資訊的 Azure EA 直接、合作夥伴和間接客戶。

## <a name="next-steps"></a>後續步驟

- Azure 企業版入口網站系統管理員應閱讀 [ Azure 企業版入口網站的系統管理](ea-portal-administration.md)，以了解常見的管理工作。
- 如果您需要 Azure 企業版入口網站問題的疑難排解協助，請參閱[針對 Azure 企業版入口網站的存取進行疑難排解](ea-portal-troubleshoot.md)。