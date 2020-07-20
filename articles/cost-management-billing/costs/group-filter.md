---
title: Azure 成本管理中的分組和篩選選項
description: 此文章說明如何使用 Azure 成本管理中的分組和篩選選項。
author: bandersmsft
ms.author: banders
ms.date: 06/08/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: f67cc019e8e7d4da4b0db1c0bbfdb6ee1cd495d7
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561864"
---
# <a name="group-and-filter-options-in-cost-analysis"></a>成本分析中的分組和篩選選項

成本分析有許多群組和篩選選項。 此文章可協助您了解使用它們的時機。

若要觀看有關群組和篩選選項的影片，請觀看[依維度和標籤報告的成本管理](https://www.youtube.com/watch?v=2Vx7V17zbmk)影片。 若要觀看其他影片，請造訪[成本管理 YouTube 頻道](https://www.youtube.com/c/AzureCostManagement)。

>[!VIDEO https://www.youtube.com/embed/2Vx7V17zbmk]

## <a name="group-and-filter-properties"></a>分組組和篩選屬性

下表列出成本分析中部分最常見的分組和篩選選項，以及應使用它們的時機。

| 屬性 | 使用時機 | 注意 |
| --- | --- | --- |
| **可用性區域** | 依照可用性區域細分 AWS 成本。 | 僅適用於 AWS 範圍和管理群組。 Azure 資料不包含可用性區域，因此將會顯示為 [不適用]。 |
| **計費週期** | 依照已經 (或即將) 開發票的月份細分 PAYG 成本。 | 使用 [計費期間] 來取得已開發票 PAYG 費用的正確表示法。 如果要向下篩選到自訂日期範圍，請在計費週期前後納入 2 個額外天數。 受限於確切的計費週期日期不會與發票相符。 將會顯示計費期間中所有發票的成本。 使用 [發票識別碼] 以向下篩選至特定發票。 僅適用於 PAYG 訂用帳戶，因為 EA 和 MCA 是依照日曆月份計費。 EA/MCA 帳戶可以在日期選擇器或每月資料粒度中使用行事曆月份，來達到相同目標。 |
| **收費類型** | 細分使用量、購買金額、退款和未使用的保留容量成本。 | 只有在使用實際成本且不是使用攤銷成本時，才能使用購買的保留容量和退款。 只有在查看攤銷成本時，才能使用未使用的保留容量成本。 |
| **部門** | 依照 EA 部門細分成本。 | 僅適用於 EA 和管理群組。 PAYG 訂用帳戶沒有部門，因此將會顯示為 [不適用] 或 [未指派]。 |
| **註冊帳戶** | 依照 EA 帳戶擁有者細分成本。 | 僅適用於 EA 計費帳戶、部門及管理群組。 PAYG 訂用帳戶沒有 EA 註冊帳戶，因此將會顯示為 [不適用] 或 [未指派]。 |
| **頻率** | 細分以使用量為基礎、一次性和週期性的成本。 | |
| **發票識別碼** | 依照發票月份細分成本。 | 未開立帳單的費用尚無發票識別碼，且 EA 費用不會包含發票詳細資料，並將會顯示為 [不適用]。  |
| **計量** | 依照使用量計量細分成本。 | 購買項目與 Marketplace 使用量都會顯示為 [不適用]。 請參考 [收費類型] 以識別購買項目，並參考 [發行者類型] 以識別 Marketplace 費用。 |
| **運算** | 依照作業細分 AWS 成本。 | 僅適用於 AWS 範圍和管理群組。 Azure 資料不包含作業，因此將會顯示為 [不適用] - 請改為使用 [計量]。 |
| **定價模式** | 依照隨選、保留或即期使用量細分成本。 | 購買項目會顯示為 [OnDemand]。 如果您看到 [不適用]，請依照 [保留] 分組，以判斷使用量是保留或隨選使用量，以及依照 [費用類型] 分組來識別購買項目。
| **提供者** | 依 AWS 和 Azure 細分成本。 | 僅適用於管理群組。 |
| **發行者類型** | 細分 AWS、Azure 和 Marketplace 成本。 |  |
| **保留容量** | 依保留容量細分成本。 | 任何未與保留相關聯的使用量或購買項目都會顯示為 [不適用]。 依照 [發行者類型] 分組，以識別其他 Azure、AWS 或 Marketplace 購買項目。 |
| **Resource** | 依資源細分成本。 | 購買項目會顯示為 [不適用]，因為其套用在 EA/PAYG 計費帳戶或 MCA 帳單設定檔層級並未與特定資源相關聯。 依照 [發行者類型] 分組，以識別其他 Azure、AWS 或 Marketplace 購買項目。 |
| **資源群組** | 依資源群組細分成本。 | 未與訂用帳戶相關聯的購買項目、租用戶資源、未部署到資源群組的訂用帳戶資源，以及傳統資源都沒有資源群組，因此將會顯示為 [其他]、[傳統服務]、[$system] 或 [不適用]。 |
| **資源類型** | 依資源類型細分成本。 | 購買項目和傳統服務沒有 Azure Resource Manager 資源類型，因此將會顯示為 [其他]、[傳統服務] 或 [不適用]。 |
| **資源位置** | 依照位置或區域細分成本。 | 購買項目和 Marketplace 使用量可能會顯示為 [未指派]、[未知]、[未對應] 或 [不適用]。 |
| **服務名稱**或**計量類別** | 依 Azure 服務細分成本。 | 購買項目與 Marketplace 使用量都會顯示為 [不適用] 或 [未指派]。 |
| **服務層**或**計量子類別** | 依 Azure 使用量計量子類別細分成本。 | 購買項目與 Marketplace 使用量都會顯示為 [不適用] 或 [未指派]。 |
| **訂用帳戶** | 依照 Azure 訂用帳戶和 AWS 連結帳戶來細分成本。 | 購買和租用戶資源可能會顯示為 [不適用]。 |
| **Tag** | 依特定標籤金鑰的標籤值細分成本。 | 標記不適用於購買項目、未與訂用帳戶相關聯的租用戶資源、未部署到資源群組的訂用帳戶資源，或傳統資源。 某些服務不會在使用量資料中包含標籤。 深入了解[每種資源類型的標籤支援](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support)。 |

如需術語的詳細資訊，請參閱[了解 Azure 使用量和費用檔案中所使用的術語](../understand/understand-usage.md)。

## <a name="next-steps"></a>後續步驟

- [開始分析成本](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis)。
