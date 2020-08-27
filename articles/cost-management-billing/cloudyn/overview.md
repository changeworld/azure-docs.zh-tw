---
title: Azure 中 Cloudyn 的概觀
description: Cloudyn 是多雲端成本管理解決方案，可協助您使用 Azure 和其他雲端資源。
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 3acc13ca535808f14cb01d50e38f6bd4d12902fc
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684434"
---
# <a name="what-is-the-cloudyn-service"></a>什麼是 Cloudyn 服務？

Cloudyn 是 Microsoft 的子公司之一，可讓您針對 Azure 資源及包含 AWS 和 Google 在內的其他雲端服務提供者，追蹤雲端使用量和費用。 淺顯易懂的儀表板報告有助於成本配置和回報 (showback)/計費 (chargeback)。 Cloudyn 可以識別未充分使用的資源，以便您管理及調整，藉此最佳化雲端消費。

若要觀看簡介影片，請觀看 [Azure Cloudyn 簡介](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo/)。
 
「Azure 成本管理」提供與 Cloudyn 類似的功能。 「Azure 成本管理」是原生的 Azure 成本管理解決方案。 它可協助您分析成本、建立及管理預算、匯出資料，以及檢閱最佳化建議並依據這些建議採取動作來節省成本。 如需詳細資訊，請參閱 [Azure 成本管理](../cost-management-billing-overview.md)。
 
[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

觀看 [Azure 成本管理和 Cloudyn 影片](https://www.youtube.com/watch?v=15DzKPMBRxM)取得相關建議，了解如何依據您的業務需求，決定應使用 Azure 成本管理或 Cloudyn。
 
>[!VIDEO https://www.youtube.com/embed/15DzKPMBRxM]

## <a name="monitor-usage-and-spending"></a>監視使用量和消費

由於組織需支付隨時間變化所耗用的資源，因此監視使用量和消費對於雲端基礎結構極為重要。 當使用量超過合約閾值時，可能會有非預期的超額成本。 有幾個重要因素會使得臨機監視較為困難。 首先，根據平均使用量來預測成本會假設在特定計費期間內有一致的耗用量。 其次，當成本接近或超過預算時，必須主動通知您來調整消費。 最後，雲端服務提供者可能不會提供成本預測對閾值或時段之間的比較報告。

報告可協助您監視消費，以分析及追蹤雲端使用量、成本和趨勢。 您可以使用「隨時間變化」的報告，以偵測出不同於正常趨勢的異常狀況。 最佳化報告中可看出雲端部署中低效率的項目。 您也可以在成本分析報告中發現低效率的項目。

## <a name="manage-costs"></a>管理成本

當您在分析隨時間變化的使用量和成本以辨識趨勢時，歷史資料將有助於管理成本。 之後，就可以使用趨勢來預測未來的消費。 Cloudyn 也包含有用的成本預測報告。

成本配置可根據標記的原則來分析成本，藉此來管理成本。 您可以在自訂的帳戶、資源和實體上使用標籤，以調整成本配置。 「類別管理員」可組織您的標籤，有助於提供額外的控管。 之後，您可將成本配置用於回報/計費，以顯示資源使用量和相關成本，進而影響耗用行為或對租用戶客戶收費。

存取控制可確保使用者和小組只能存取所需的成本管理資料，以協助管理成本。 實體結構、使用者管理和排程的報告可搭配使用收件者清單以指派存取權。

警示可以在出現異常消費或超支時自動通知您，以協助管理成本。 警示也可以在出現異常消費和超支風險時，自動通知利害關係人。 多種不同報告皆支援以預算和成本閾值為基礎的警示。 不過，CSP 夥伴帳戶或訂用帳戶目前不支援警示。

## <a name="improve-efficiency"></a>改善效率

您可以透過 Cloudyn，以決定最佳的 VM 使用量與識別閒置的 VM 或移除閒置的 VM 並卸載磁碟。 您可以使用「大小調整最佳化」和「低效率」報告中的資訊，以建立縮減或移除閒置 VM 的計劃。 不過，CSP 夥伴帳戶或訂用帳戶目前不支援最佳化報告。

若您佈建了 AWS Reserved Instances，就可以使用「最佳化」報告來檢視購買建議、修改未使用的保留項目及規劃佈建，進而改善保留的執行個體使用量。


## <a name="next-steps"></a>後續步驟

您現在已熟悉 Cloudyn，下一步是註冊雲端環境並開始探索您的資料。

- [註冊 CSP 合作夥伴計劃並檢視成本資料](quick-register-csp.md)
