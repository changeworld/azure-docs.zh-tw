---
title: 分析非預期的 Azure 費用
description: 了解如何分析 Azure 訂用帳戶的非預期費用。
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: f6df8c6ff0db74798b71d57feae91e5245f2a6c2
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028713"
---
# <a name="analyze-unexpected-charges"></a>分析非預期的費用

您針對貴組織建立的雲端資源基礎結構可能很複雜。 許多 Azure 資源類型可能有不同類型的費用。 Azure 資源可能由貴組織中的不同小組所擁有，且可能會有不同的計費模型類型適用於各種資源。 若要進一步了解費用，請使用下列各節中的一個或多個策略開始進行分析。

## <a name="review-invoice-for-resource-responsible-for-charge"></a>檢閱資源 (負責收費) 的發票

您購買 Azure 服務的方式可協助您判斷您在識別與費用相關聯的資源時，可供您使用的方法和工具。 若要判斷您適用哪一種方法，請先[判斷您的 Azure 供應項目類型](../costs/understand-cost-mgt-data.md#determine-your-offer-type)。 然後，在[支援的 Azure 供應項目](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers)清單中識別您的客戶類別。

下列文章提供詳細的步驟，說明如何根據您的客戶類型來檢閱您的帳單。 在每篇文章中都有相關指示，說明如何下載 CSV 檔案，其中包含指定計費週期的使用量和成本詳細資料。

- [隨用隨付帳單檢閱流程](review-individual-bill.md#charges)
- [Enterprise 合約帳單檢閱流程](review-enterprise-agreement-bill.md)
- [Microsoft 客戶合約檢閱流程](review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Microsoft 合作夥伴合約檢閱流程](review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

您的 Azure 帳單會根據每個 _計量_ 來匯總當月費用。 計量是用來追蹤一段時間的資源使用量，並用來計算您的帳單。 當您建立單一 Azure 資源 (例如虛擬機器) 時，會針對該資源建立一或多個計量執行個體。

根據您所要分析帳單上顯示的 _MeterName_ 篩選使用量 CSV 檔案，以查看適用於計量表的所有明細項目。 明細項目的 _InstanceID_ 會對應到產生費用的實際 Azure 資源。

當您識別出有問題的資源時，可以在 Azure 成本管理中使用成本分析，進一步分析資源的相關成本。 若要深入了解如何使用成本分析，請參閱[開始分析成本](../costs/quick-acm-cost-analysis.md)。

## <a name="review-invoiced-charges-in-cost-analysis"></a>檢閱成本分析中的發票費用

若要在 Azure 入口網站中檢視發票詳細資料，請瀏覽至與您正在分析之發票相關聯範圍的「成本分析」。 選取 [發票詳細資料] 檢視。 發票詳細資料會顯示發票上顯示的費用。

[![顯示發票詳細資料的範例](./media/analyze-unexpected-charges/invoice-details.png)](./media/analyze-unexpected-charges/invoice-details.png#lightbox)

若要檢視發票詳細資料，您可以識別具有非預期成本的服務，並在成本分析中判斷哪些資源會直接與資源相關聯。 例如，如果想分析虛擬機器服務的費用，請瀏覽至 **累計成本** 檢視。 然後，將細微性設定為 **每日**，並篩選 **服務名稱：虛擬機器** 的費用，並依 **資源** 將費用分組。

[![顯示虛擬機器累計成本的範例](./media/analyze-unexpected-charges/virtual-machines.png)](./media/analyze-unexpected-charges/virtual-machines.png#lightbox)

## <a name="identify-spikes-in-cost-over-time"></a>識別一段時間的成本尖峰

有時候，您可能不知道哪些最近的成本導致計費費用上的變更。 若要了解變更的內容，您可以使用成本分析來[查看一段時間內每日或每月的成本明細](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month)。 建立視圖之後，請依 [服務]  或 [資源]  將您的費用群組以識別變更。 您也可以將您的視圖變更為 [線條]  圖表，以更好的視覺化方式呈現資料。

![在成本分析中顯示一段時間的成本範例](./media/analyze-unexpected-charges/costs-over-time.png)

## <a name="determine-resource-pricing-and-billing-model"></a>判斷資源定價及其計費模型

單一資源可能會在多個 Azure 產品和服務之間產生費用。 如需深入了解每個 Azure 服務的定價，請參閱 [Azure 各項產品的定價](https://azure.microsoft.com/pricing/#product-pricing)頁面。 例如，在 Azure 中建立的單一虛擬機器 (VM) 可能會建立下列計量來追蹤其使用量。 每個都可能有不同的定價。

- 計算時數
- IP 位址時數
- 資料轉入
- 資料轉出
- 標準受控磁碟
- 標準受控磁碟作業
- 標準 IO 磁碟
- 標準 IO - 區塊 Blob 讀取
- 標準 IO - 區塊 Blob 寫入
- 標準 IO - 區塊 Blob 刪除

建立 VM 後，每個計量便會開始發出使用量記錄。 Azure 計量系統中會追蹤使用量和計量的價格。 您可以在使用量 CSV 檔案中查看用來計算費用的計量。

## <a name="find-people-responsible-for-the-resource-and-engage"></a>尋找負責資源的人員並與他們互動

通常，負責指定資源的小組會知道已對資源所做的變更。 與這些小組互動很有用，因為您可以識別可能出現費用的原因。 例如，主控小組最近可能已建立資源、更新其 SKU (因而變更資源費率)，或因為程式碼變更而增加資源的負載。 繼續閱讀下列各節，以了解判斷哪些人員擁有資源的更多技巧。

### <a name="analyze-the-audit-logs-for-the-resource"></a>分析資源的稽核記錄

如果您擁有檢視資源的許可權，您應該能夠存取其稽核記錄。 請檢閱記錄，以尋找負責資源最新變更的使用者。 若要深入了解，請參閱[檢視及擷取 Azure 活動記錄事件](../../azure-monitor/platform/activity-log.md#view-the-activity-log)。

### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>分析使用者對資源父範圍的許可權

具有訂用帳戶或資源群組寫入存取權的人員，通常會有建立資源的相關資訊。 他們應該能夠說明資源的用途，或將您指引至知道的人員。 若要識別擁有訂用帳戶範圍權限的人員，請參閱[檢查使用者的 Azure 資源存取權](../../role-based-access-control/check-access.md)。 您可以使用類似流程來處理資源群組。

## <a name="get-help-to-identify-charges"></a>取得識別費用的協助

如果您已使用上述策略，但仍無法了解為何會收到費用，或如果您需要帳單問題的其他協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- 深入了解如何[使用 Azure 成本管理來最佳化您的雲端投資](../costs/cost-mgt-best-practices.md)。