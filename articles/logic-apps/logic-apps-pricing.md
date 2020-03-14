---
title: 定價 & 計費模型
description: 有關定價和計費模型如何適用于 Azure Logic Apps 的總覽
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: 795acd67a8d4a9f8b8b7d78799a92134f249cf8d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270455"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure Logic Apps 的定價模式

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)可協助您建立和執行可在雲端中調整的自動化整合工作流程。 本文說明 Azure Logic Apps 的計費和定價。 如需定價費率，請參閱[Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>取用量定價模式

對於在公用或「全域」 Azure Logic Apps 服務中執行的新邏輯應用程式，您只需支付使用的部分。 這些邏輯應用程式會使用以取用量為基礎的方案和定價模型。 在您的邏輯應用程式中，每個步驟都是一個動作，並 Azure Logic Apps 計量邏輯應用程式中執行的所有動作。

例如，動作包括：

* 觸發程式，這是特殊動作。 所有邏輯應用程式都需要觸發程式做為第一個步驟。
* 「[內建」或原生動作](../connectors/apis-list.md#built-in)（例如 HTTP）、AZURE FUNCTIONS 和 API 管理的呼叫等等
* 呼叫[受管理的連接器](../connectors/apis-list.md#managed-connectors)，例如 Outlook 365、Dropbox 等
* 控制流程步驟，例如迴圈、條件陳述式等等

[標準](../connectors/apis-list.md#managed-connectors)連接器會以[標準連接器價格](https://azure.microsoft.com/pricing/details/logic-apps)收費。 正式運作的[企業連接器](../connectors/apis-list.md#managed-connectors)會以[Enterprise connector 價格](https://azure.microsoft.com/pricing/details/logic-apps)計費，而公開預覽企業連接器則會以[標準連接器價格](https://azure.microsoft.com/pricing/details/logic-apps)收費。

深入瞭解[觸發](#triggers)程式和[動作](#actions)的計費方式。

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>固定定價模式

[*整合服務環境*（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)提供隔離的方式，讓您建立和執行可存取 Azure 虛擬網路中資源的邏輯應用程式。 對於在 ISE 內執行的新邏輯應用程式，您需支付這些功能的[固定每月價格](https://azure.microsoft.com/pricing/details/logic-apps)：

* [內建觸發程式](../connectors/apis-list.md#built-in)和動作

  在 ISE 中，內建的觸發程式和動作會顯示**核心**標籤，並在與您的邏輯應用程式相同的 ISE 中執行。

* [標準](../connectors/apis-list.md#managed-connectors)連接器和[企業](../connectors/apis-list.md#enterprise-connectors)連接器（所需的企業連線數目）

   顯示**ise**標籤的標準和企業連接器，會在與邏輯應用程式相同的 ise 中執行。 未顯示 ISE 標籤的連接器會在全域 Logic Apps 服務中執行。 當您搭配在 ISE 中執行的邏輯應用程式使用時，每月價格也適用于全域服務中執行的連接器。

* [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)的使用量不需額外費用，視您的[ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)而定：

  * **PREMIUM SKU**：單一[標準層](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)整合帳戶

  * **開發人員 SKU**：單一[免費層](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)整合帳戶

  每個 ISE SKU 的總整合帳戶限制為5個。 如需額外費用，您可以根據 ISE SKU 擁有更多整合帳戶：

  * **PREMIUM SKU**：最多四個標準帳戶。 沒有免費或基本帳戶。

  * **開發人員 SKU**：最多4個標準帳戶，或最多5個標準帳戶。 沒有基本帳戶。

  如需整合帳戶限制的詳細資訊，請參閱[Logic Apps 限制和](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)設定。 您可以在本主題稍後深入瞭解[整合帳戶層及其計價模式](#integration-accounts)。

如果您選擇 Premium ISE SKU，基礎單位會有固定容量。 如果您需要更多的輸送量，您可以在建立期間或之後[新增更多縮放單位](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)。 開發人員 ISE SKU 沒有新增更多縮放單位的功能。 在 ISE 中執行的邏輯應用程式不會產生資料保留成本。

如需定價費率，請參閱[Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="connectors"></a>

## <a name="connectors"></a>連接器

Azure Logic Apps 連接器藉由提供[觸發](#triggers)程式、[動作](#actions)或兩者，協助邏輯應用程式存取雲端或內部部署中的應用程式、服務和系統。 連接器會分類為 [標準] 或 [企業]。 如需這些連接器的總覽，請參閱[Azure Logic Apps 的連接器](../connectors/apis-list.md)。 如果您想要在邏輯應用程式中使用的 REST Api 沒有預先建立的連接器可用，您可以建立[自訂連接器](https://docs.microsoft.com/connectors/custom-connectors)，這只是那些 rest api 的包裝函式。 自訂連接器會以標準連接器計費。 下列各節提供有關觸發程式和動作如何計費的詳細資訊。

<a name="triggers"></a>

## <a name="triggers"></a>觸發程序

觸發程序是在特定事件發生時建立邏輯應用程式執行個體的特殊動作。 觸發程序會以不同動作影響邏輯應用程式計量方式。 以下是 Azure Logic Apps 中存在的各種觸發程式：

* **輪詢觸發**程式：此觸發程式會持續檢查端點，尋找符合建立邏輯應用程式實例和啟動工作流程之準則的訊息。 即使未建立任何邏輯應用程式執行個體，Logic Apps 也會將每個輪詢要求當作一個執行來計量。 您可以透過邏輯應用程式設計工具來設定觸發程序，以指定輪詢間隔。

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook 觸發**程式：此觸發程式會等候用戶端將要求傳送至特定端點。 每個傳送至 Webhook 端點的要求都會計算為一個動作執行。 例如，要求和 HTTP Webhook 觸發程序都是 Webhook 觸發程序。

* **週期觸發**程式：此觸發程式會根據您在觸發程式中設定的週期間隔，建立邏輯應用程式實例。 例如，您可以設定每隔三天執行一次的週期觸發程式，或按較複雜的排程。

<a name="actions"></a>

## <a name="actions"></a>動作

Azure Logic Apps 計量「內建」動作（例如 HTTP）做為原生動作。 例如，內建動作包括 HTTP 呼叫、來自 Azure Functions 或 API 管理的呼叫，以及控制流程步驟（例如條件、迴圈和 switch 語句）。 每個動作都有自己的動作類型。 例如，呼叫[連接器](https://docs.microsoft.com/connectors)的動作會有 "ApiConnection" 類型。 這些連接器會分類為標準或企業連接器，並根據其各自的[定價](https://azure.microsoft.com/pricing/details/logic-apps)計量。 *預覽*中的企業連接器是以標準連接器收費。

Azure Logic Apps 將所有成功和失敗的動作當做執行計量。 不過，Logic Apps 不會計量這些動作：

* 因為條件不符而略過的動作
* 因為邏輯應用程式在完成前停止而不會執行的動作

對於在迴圈內執行的動作，Azure Logic Apps 會計算迴圈中每個迴圈的每個動作。 例如，假設您有可處理清單的 "for each" 迴圈。 Logic Apps 會將清單項目數目乘以迴圈中的動作數目，以計量該迴圈中的動作，並新增可啟動迴圈的動作。 因此，10個專案清單的計算是（10 * 1） + 1，這會產生11個動作執行。

## <a name="disabled-logic-apps"></a>已停用的邏輯應用程式

停用的邏輯應用程式不會產生費用，因為它們在停用時不會建立新的實例。 停用邏輯應用程式之後，其目前正在執行的執行個體可能需要一些時間才能完全停止。

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>整合帳戶

[固定定價模式](https://azure.microsoft.com/pricing/details/logic-apps)適用于[整合帳戶](logic-apps-enterprise-integration-create-integration-account.md)，您可以在其中探索、開發及測試 Azure Logic Apps 中的[B2B 和 EDI](logic-apps-enterprise-integration-b2b.md)和[XML 處理](logic-apps-enterprise-integration-xml.md)功能，而不需額外費用。 每個 Azure 訂用帳戶最多可以有個[特定的整合帳戶限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。 每個整合帳戶最多可以儲存特定[的成品限制](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)，包括交易夥伴、合約、地圖、架構、元件、憑證、批次設定等。

Azure Logic Apps 提供免費、基本和標準整合帳戶。 「基本」和「標準」層是由 Logic Apps 服務等級協定（SLA）所支援，而「免費」層則不受 SLA 支援，而且具有輸送量和使用量的限制。 除了免費層整合帳戶以外，您可以在每個 Azure 區域中有一個以上的整合帳戶。 如需定價費率，請參閱[Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps/)。

如果您有[Premium 或 Developer](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)的[*整合服務環境*（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)，您的 ISE 可以有5個整合帳戶總數。 若要瞭解適用于 ISE 的固定定價模式，請參閱本主題中的先前的[固定定價模式](#fixed-pricing)一節。 如需定價費率，請參閱[Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps)。

若要在免費、基本或標準整合帳戶之間進行選擇，請參閱下列使用案例描述：

* **免費**：當您想要嘗試探索案例，而不是生產案例時

* **基本**：當您只想要處理訊息，或做為與較大商務實體有交易夥伴關係的小型企業夥伴時

* **標準**：當您有更複雜的 B2B 關聯性，而且必須管理的實體數目增加時

<a name="data-retention"></a>

## <a name="data-retention"></a>資料保留

除了在整合服務環境（ISE）中執行的邏輯應用程式以外，儲存在邏輯應用程式執行歷程記錄中的所有輸入和輸出都會根據邏輯應用程式的[執行保留期限](logic-apps-limits-and-config.md#run-duration-retention-limits)來計費。 在 ISE 中執行的邏輯應用程式不會產生資料保留成本。 如需定價費率，請參閱[Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps)。

為了協助您監視邏輯應用程式的儲存體耗用量，您可以：

* 查看邏輯應用程式每月使用的儲存單位數（GB）。
* 在邏輯應用程式的執行歷程記錄中，查看特定動作的輸入和輸出大小。

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>查看邏輯應用程式儲存體耗用量

1. 在 Azure 入口網站中，尋找並開啟邏輯應用程式。

1. 從邏輯應用程式的功能表中，選取 [**監視**] 底下的 [**計量**]。

1. 在右**窗格的 [** **圖表標題**] 底下，從 [計量] 清單中選取 [**儲存體耗用量執行] 的 [計費使用量**]。

   此計量會提供您每月的儲存耗用量單位數（GB）。

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>視圖動作輸入和輸出大小

1. 在 Azure 入口網站中，尋找並開啟邏輯應用程式。

1. 在邏輯應用程式的功能表上，選取 **[總覽**]。

1. 在右窗格的 [**執行歷程記錄**] 底下，選取具有您想要檢查之輸入和輸出的執行。

1. 在 [**邏輯應用程式執行**] 下，選擇 [**執行詳細資料**]。

1. 在**邏輯應用程式的 [執行詳細資料**] 窗格中，于 [動作] 資料表中列出每個動作的 [狀態] 和 [持續時間]，選取您想要查看的動作。

1. 在**邏輯應用程式**的 [動作] 窗格中，尋找該動作之輸入和輸出的大小，分別出現在 [**輸入連結**] 和 [**輸出] 連結**之下。

## <a name="next-steps"></a>後續步驟

* [深入瞭解 Azure Logic Apps](logic-apps-overview.md)
* [建立第一個邏輯應用程式](quickstart-create-first-logic-app-workflow.md)
