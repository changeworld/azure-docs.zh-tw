---
title: Azure Marketplace
description: 描述 EA 客戶可以如何使用 Azure Marketplace
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 91079310d4d566a65c6e8bc7f913ba92d04dece1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689927"
---
# <a name="azure-marketplace"></a>Azure Marketplace

本文說明 EA 客戶及合作夥伴如何能夠檢視市集費用，並啟用 Azure Marketplace 購買。

## <a name="azure-marketplace-for-ea-customers"></a>適用於 EA 客戶的 Azure Marketplace

直接客戶可以在 Azure 企業版入口網站上看見 Azure Marketplace 費用。 Azure Marketplace 的購買和使用都會在 Azure 預付款以外按季或按月於期末計費。

間接客戶可以在 Azure 企業版入口網站的 [管理訂用帳戶] 頁面上找到其 Azure Marketplace 訂用帳戶，但價格將會隱藏。 客戶應該連絡其授權解決方案提供者 (LSP) 以了解相關的 Azure Marketplace 費用資訊。

新的每月或每年週期性 Azure Marketplace 購買，將在購買 Azure Marketplace 項目的期間內以完整金額計費。 這些項目會在原始購買下一個期間的同一天自動續約。

現有的每月週期性費用會繼續在每個日曆月份的第一天續約。 年度費用則會在購買屆滿一年的當天續約。

Azure Marketplace 上提供的某些第三方轉銷商服務會使用您 Enterprise 合約 (EA) 的 Azure 預付款餘額。 這些服務原先在 EA Azure 預付款範圍外計費，並另外開立發票。 Azure Marketplace 中這些服務的 EA Azure 預付款有助於簡化客戶購買及款項管理。 如需目前使用 Azure 預付款的完整服務清單，請參閱 [Azure 網站上的 2018 年 3 月 6 日更新](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/)。

### <a name="partners"></a>合作夥伴

LSP 可以從 Azure 企業版入口網站中的價位表頁面下載 Azure Marketplace 價目表。 選取右上角的 [Marketplace 價目表] 連結。 Azure Marketplace 價目表會顯示所有可用的服務及其價格。

若要下載價目表：

1. 在 Azure 企業版入口網站中，移至 [報表] > [價位表]。
1. 在右上角，找出您使用者名稱底下的 Azure Marketplace 價目表連結。
1. 以滑鼠右鍵按一下該連結，然後選取 [另存目標]。
1. 在 [儲存] 視窗上，將文件的標題變更為 `AzureMarketplacePricelist.zip`，這會將檔案從 .xlsx 變更為 .zip 檔案。
1. 下載完成後，您將取得一個 zip 檔案，內含特定國家/地區的價目表。
1. LSP 應參考個別的國家/地區檔案，以了解特定國家/地區的定價。 LSP 可以使用 [通知] 索引標籤來了解新增或淘汰的 SKU。
1. 價格不常發生變更。 LSP 會在 30 天前收到價格調升和外匯 (FX) 變更的電子郵件通知。
1. LSP 的每個註冊每季會收到每個 ISV 的一張發票。

### <a name="enabling-azure-marketplace-purchases"></a>啟用 Azure Marketplace 購買

企業系統管理員能夠為註冊下的所有 Azure 訂用帳戶「停用」或「啟用」Azure Marketplace 購買。 如果企業系統管理員停用購買，而某些 Azure 訂用帳戶已有 Azure Marketplace 訂用帳戶，這些 Azure Marketplace 訂用帳戶將不會取消或受到影響。

雖然客戶可在 Azure 企業版入口網站中將其直接 Azure 訂用帳戶與註冊產生關聯，藉以將訂用帳戶轉換至 Azure EA，但此動作並不會自動轉換子系訂用帳戶。

若要啟用 Azure Marketplace 購買：

1. 以企業系統管理員身分登入 Azure 企業版入口網站。
1. 移至 [管理]。
1. 在 [註冊詳細資料] 底下，選取 **Azure Marketplace** 明細項目旁的鉛筆圖示。
1. 視需要切換 [啟用/停用] 或 [僅限免費 BYOL SKU]。
1. 選取 [儲存]。

> [!NOTE]
> [BYOL (自備授權)] 和 [僅限免費] 選項會限制只有 BYOL 和免費 SKU 可購買和取得 Azure Marketplace SKU。

### <a name="services-billed-hourly-for-azure-ea"></a>Azure EA 的服務按小時計費

相較於 MOSP 中的每月費率，Enterprise 合約下的下列服務會按小時計費：

- 應用程式傳遞網路
- Web 應用程式防火牆

### <a name="azure-remoteapp"></a>Azure RemoteApp

如果您有 Enterprise 合約，您會根據 Enterprise 合約的價格等級支付 Azure RemoteApp 費用。 不會產生額外的費用。 標準價格包含最初的 40 小時。 無限制價格會涵蓋最初的 80 小時。 超過 80 小時後，遠端應用程式即停止發出使用量。

## <a name="azure-marketplace-faq"></a>Azure Marketplace 常見問題集

本節將說明您的 Azure 預付款可如何套用到 Azure Marketplace 中一些第三方轉銷商服務。

### <a name="what-changed-with-azure-marketplace-services-and-azure-ea-prepayment"></a>Azure Marketplace 服務和 Azure EA 預付款有哪些變更？

自 2018 年 3 月 1 日起，部分第三方轉銷商服務可取用 Azure EA 預付款。 除了 Azure 保留的 VM 執行個體 (RI) 以外，服務先前都會以 Azure EA 預付款以外的費率計費，並且個別開立發票。

我們已擴充 Azure 預付款的適用性，以納入一些最常購買且由第三方發佈的 Azure Marketplace 服務。 Azure Marketplace 中這些服務的 Azure EA 預付款有助於簡化您的購買及款項管理。

### <a name="why-did-we-make-this-change"></a>為什麼我們要進行此變更？

一直以來，客戶都希望能有其他方式可充分運用其預付的 Azure 預付款。 客戶經常要求這項變更，而且影響了大多數 Azure Marketplace 客戶。

### <a name="how-do-you-benefit"></a>您如何受益？

您可以獲得更簡單的計費體驗，並能夠更有效地使用 Azure EA 預付款。 因為這些服務已包含在您的 Azure 預付款中，所以您的 Azure EA 預付款會變得更有價值。

### <a name="what-azure-marketplace-services-use-azure-ea-prepayment-and-how-do-i-know"></a>哪些 Azure Marketplace 服務會使用 Azure EA 預付款？我該如何得知？

當您購買可使用 Azure 預付款的服務時，Azure Marketplace 會提供免責聲明。 支援的項目有 Red Hat、SUSE、Autodesk 和 Oracle 發行的一些服務。 目前，由其他廠商發佈且名稱類似的服務並不會從 Azure 預付款中扣款。 此常見問題的結尾處提供了完整清單。

### <a name="what-if-my-azure-ea-prepayment-runs-out"></a>如果我的 Azure EA 預付款已用盡該怎麼辦？

如果您用完所有的 Azure 預付款且已產生超額部分時，這些服務的相關費用將連同任何其他取用服務顯示在下一張超額發票中。 在 2018 年 3 月 1 日進行變更之前，這些費用會與其他 Azure Marketplace 服務一起開立發票。

### <a name="why-dont-all-azure-marketplaces-consume-azure-ea-prepayment"></a>為什麼不是所有 Azure Marketplace 項目都能使用 Azure EA 預付款？

我們一直都在設法提供 Azure EA 預付款方面的最佳客戶體驗。 這項變更將使許多客戶受惠，並解決在 Azure Marketplace 中的總支出比例偏高的問題。 未來可能會新增其他服務。

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>這對間接註冊和合作夥伴有何影響？

我們的間接註冊客戶或合作夥伴將不受影響。 這些服務將與其他取用服務一樣，受到相同合作夥伴標記功能的控管。 唯一的變更是，費用會顯示在不同的發票上，以及使用客戶的 Azure EA 預付款來支付費用。

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-azure-ea-prepayment"></a>是否有使用 Azure EA 預付款的 Azure Marketplace 服務清單？

特定 Azure Marketplace 供應項目可以使用 Azure 預付款資金。 如需參與此方案的完整產品清單，請參閱[使用 Azure 預付款的第三方服務](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment)。


## <a name="next-steps"></a>後續步驟

- 取得[定價](ea-pricing-overview.md)的詳細資訊。