---
title: 定價&計費模型
description: 定價和計費模型如何適用于 Azure 邏輯應用的概述
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: 795acd67a8d4a9f8b8b7d78799a92134f249cf8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270455"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure Logic Apps 的定價模式

[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)可説明您創建和運行可在雲中擴展的自動集成工作流。 本文介紹了 Azure 邏輯應用的計費和定價的工作原理。 有關定價率，請參閱[邏輯應用定價](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>取用量定價模式

對於在公共或"全域"Azure 邏輯應用服務中運行的新邏輯應用，只需為使用的內容付費。 這些邏輯應用程式會使用以取用量為基礎的方案和定價模型。 在邏輯應用中，每個步驟都是一個操作，Azure 邏輯應用會管理邏輯應用中運行的所有操作。

例如，操作包括：

* 觸發器，這是特殊操作。 所有邏輯應用都需要觸發器作為第一步。
* ["內置"或本機操作](../connectors/apis-list.md#built-in)（如 HTTP、對 Azure 函數和 API 管理的調用等）
* 對[託管連接器](../connectors/apis-list.md#managed-connectors)（如 Outlook 365、Dropbox 等）的調用
* 控制流步驟，如迴圈、條件陳述式等

[標準連接器](../connectors/apis-list.md#managed-connectors)按[標準連接器價格](https://azure.microsoft.com/pricing/details/logic-apps)收費。 一般可用的[企業連接器](../connectors/apis-list.md#managed-connectors)按[企業連接器價格](https://azure.microsoft.com/pricing/details/logic-apps)收費，而公共預覽企業連接器按[標準連接器價格](https://azure.microsoft.com/pricing/details/logic-apps)收費。

詳細瞭解計費如何為[觸發器](#triggers)和[操作工作](#actions)。

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>固定定價模式

[*整合服務環境*（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)提供了一種獨立的方式，用於創建和運行可以訪問 Azure 虛擬網路中資源的邏輯應用。 對於在 ISE 內運行的新邏輯應用，您每月為這些功能支付[固定價格](https://azure.microsoft.com/pricing/details/logic-apps)：

* [內置](../connectors/apis-list.md#built-in)觸發器和操作

  在 ISE 中，內置觸發器和操作顯示**核心**標籤，並在與邏輯應用相同的 ISE 中運行。

* [標準](../connectors/apis-list.md#managed-connectors)連接器[和企業](../connectors/apis-list.md#enterprise-connectors)連接器（根據需要連接企業數）

   顯示**ISE**標籤的標準和企業連接器與邏輯應用在相同的 ISE 中運行。 不顯示 ISE 標籤的連接器在全域邏輯應用服務中運行。 固定每月定價也適用于在全域服務中運行的連接器，當您將其與在 ISE 中運行的邏輯應用一起運行時。

* 基於[ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)的[集成帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)使用方式，無需額外費用：

  * **高級 SKU**：單個[標準層](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)集成帳戶

  * **開發人員 SKU**： 單個[免費套餐](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)集成帳戶

  每個 ISE SKU 限制為 5 個總集成帳戶。 對於額外的成本，您可以基於 ISE SKU 擁有更多的集成帳戶：

  * **高級 SKU**： 最多四個標準帳戶。 無免費或基本帳戶。

  * **開發人員 SKU**： 最多 4 個標準帳戶，或最多 5 個標準帳戶。 無基本帳戶。

  有關集成帳戶限制的詳細資訊，請參閱[邏輯應用限制和配置](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。 在本主題稍後部分，您可以瞭解有關[集成帳戶層及其定價模型](#integration-accounts)的詳細資訊。

如果選擇高級 ISE SKU，則基本單位具有固定容量。 如果需要更多輸送量，可以在創建期間或之後[添加更多比例單位](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)。 開發人員 ISE SKU 無法添加更多縮放單位。 在 ISE 中運行的邏輯應用不會產生資料保留成本。

有關定價率，請參閱[邏輯應用定價](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="connectors"></a>

## <a name="connectors"></a>連接器

Azure 邏輯應用連接器通過提供[觸發器](#triggers)、[操作](#actions)或兩者來説明邏輯應用訪問雲或內部的應用、服務和系統。 連接器分為標準連接器或企業連接器。 有關這些連接器的概述，請參閱[Azure 邏輯應用的連接器](../connectors/apis-list.md)。 如果邏輯應用中沒有預構建的連接器可用於 REST API，則可以創建[自訂連接器](https://docs.microsoft.com/connectors/custom-connectors)，這些連接器只是這些 REST API 的包裝。 自訂連接器計費為標準連接器。 以下各節提供有關觸發器和操作計費工作方式的詳細資訊。

<a name="triggers"></a>

## <a name="triggers"></a>觸發程序

觸發程序是在特定事件發生時建立邏輯應用程式執行個體的特殊動作。 觸發程序會以不同動作影響邏輯應用程式計量方式。 以下是 Azure 邏輯應用中存在的各種觸發器：

* **輪詢觸發器**：此觸發器會持續檢查終結點中滿足創建邏輯應用實例和啟動工作流條件的消息。 即使未建立任何邏輯應用程式執行個體，Logic Apps 也會將每個輪詢要求當作一個執行來計量。 您可以透過邏輯應用程式設計工具來設定觸發程序，以指定輪詢間隔。

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook 觸發器**：此觸發器等待用戶端向特定終結點發送請求。 每個傳送至 Webhook 端點的要求都會計算為一個動作執行。 例如，要求和 HTTP Webhook 觸發程序都是 Webhook 觸發程序。

* **定期觸發器**：此觸發器基於您在觸發器中設置的定期間隔創建邏輯應用實例。 例如，您可以設置每三天運行一次的定期觸發器，或者按照更複雜的計畫運行。

<a name="actions"></a>

## <a name="actions"></a>動作

Azure 邏輯應用將"內置"操作（如 HTTP）作為本機操作進行。 例如，內置操作包括 HTTP 調用、Azure 函數或 API 管理中的調用以及控制流步驟（如條件、迴圈和切換語句）。 每個操作都有自己的操作類型。 例如，調用[連接器](https://docs.microsoft.com/connectors)的操作具有"ApiConnection"類型。 這些連接器被歸類為標準或企業連接器，根據各自的[定價](https://azure.microsoft.com/pricing/details/logic-apps)進行計量。 *預覽*中的企業連接器作為標準連接器充電。

Azure 邏輯應用將所有成功和不成功的操作作為執行進行。 但是，邏輯應用不會測量這些操作：

* 因為條件不符而略過的動作
* 因為邏輯應用程式在完成前停止而不會執行的動作

對於在迴圈內運行的操作，Azure 邏輯應用計算迴圈中每個迴圈的每個操作。 例如，假設您有可處理清單的 "for each" 迴圈。 Logic Apps 會將清單項目數目乘以迴圈中的動作數目，以計量該迴圈中的動作，並新增可啟動迴圈的動作。 因此，10 項清單的計算是 （10 + 1） = 1，這將導致 11 個操作執行。

## <a name="disabled-logic-apps"></a>禁用的邏輯應用

禁用的邏輯應用不會收費，因為它們在禁用時無法創建新實例。 停用邏輯應用程式之後，其目前正在執行的執行個體可能需要一些時間才能完全停止。

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>整合帳戶

[固定定價模型](https://azure.microsoft.com/pricing/details/logic-apps)適用于[集成帳戶](logic-apps-enterprise-integration-create-integration-account.md)，您可以在其中探索、開發和測試 Azure 邏輯應用中的[B2B 和 EDI](logic-apps-enterprise-integration-b2b.md)和[XML 處理](logic-apps-enterprise-integration-xml.md)功能，無需額外付費。 每個 Azure 訂閱可以具有最多特定的[集成帳戶限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。 每個集成帳戶可以存儲多達特定[工件限制](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)，包括交易夥伴、協定、地圖、架構、程式集、證書、批次處理配置等。

Azure 邏輯應用提供免費、基本和標準集成帳戶。 邏輯應用服務等級協定 （SLA） 支援基本層和標準層，而免費層不受 SLA 支援，並且輸送量和使用方式有限制。 除免費層集成帳戶外，每個 Azure 區域中可以有多個集成帳戶。 有關定價率，請參閱[邏輯應用定價](https://azure.microsoft.com/pricing/details/logic-apps/)。

如果您有[*整合服務環境*（ISE），](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)[高級或開發人員](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)， 您的 ISE 可以有 5 個總集成帳戶。 要瞭解固定定價模型如何為 ISE 工作，請參閱本主題中的上一個[固定定價模型](#fixed-pricing)部分。 有關定價率，請參閱[邏輯應用定價](https://azure.microsoft.com/pricing/details/logic-apps)。

要選擇免費、基本或標準集成帳戶，請查看這些用例說明：

* **免費**：對於要嘗試探索性方案，而不是生產方案

* **基本**：對於您只希望處理消息或充當與較大業務實體有交易夥伴關係的小業務合作夥伴時

* **標準**：對於具有更複雜的 B2B 關係和必須管理的實體數量增加時

<a name="data-retention"></a>

## <a name="data-retention"></a>資料保留

除了在整合服務環境 （ISE） 中運行的邏輯應用外，存儲在邏輯應用執行歷程記錄中的所有輸入和輸出都根據邏輯應用[的運行保留期](logic-apps-limits-and-config.md#run-duration-retention-limits)計費。 在 ISE 中運行的邏輯應用不會產生資料保留成本。 有關定價率，請參閱[邏輯應用定價](https://azure.microsoft.com/pricing/details/logic-apps)。

為了説明監視邏輯應用的存儲消耗，您可以：

* 查看邏輯應用每月使用的 GB 存儲單元數。
* 在邏輯應用的執行歷程記錄中查看特定操作的輸入和輸出的大小。

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>查看邏輯應用存儲消耗

1. 在 Azure 入口網站中，尋找並開啟邏輯應用程式。

1. 從邏輯應用的功能表中，在 **"監視"** 下，選擇**指標**。

1. 在右側窗格中，在"**指標"** 清單中的**圖表標題**下，選擇**存儲消耗量執行的計費使用方式**。

   此指標為您提供了每月按 GB 計費的存儲消耗單位數。

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>查看操作輸入和輸出大小

1. 在 Azure 入口網站中，尋找並開啟邏輯應用程式。

1. 在邏輯應用的功能表上，選擇 **"概述**"。

1. 在右側窗格中，在 **"執行歷程記錄"** 下，選擇具有要檢查的輸入和輸出的運行。

1. 在**邏輯應用運行**下，選擇 **"運行詳細資訊**"。

1. 在 **"邏輯"應用運行詳細資訊**窗格中，在動作表中列出每個操作的狀態和持續時間，選擇要查看的操作。

1. 在 **"邏輯應用操作"** 窗格中，查找該操作的輸入和輸出的大小分別顯示在 **"輸入"連結**和 **"輸出"連結**下。

## <a name="next-steps"></a>後續步驟

* [瞭解有關 Azure 邏輯應用的更多資訊](logic-apps-overview.md)
* [建立第一個邏輯應用程式](quickstart-create-first-logic-app-workflow.md)
