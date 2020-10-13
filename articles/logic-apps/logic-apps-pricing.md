---
title: 定價 & 計費模型
description: 概述 Azure Logic Apps 的定價和計費模型的運作方式
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: a5511d7cd4b5bb0f3fe901a735535f8db9036ee7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87078157"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure Logic Apps 的定價模式

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 可協助您建立和執行可在雲端中調整的自動化整合工作流程。 本文說明 Azure Logic Apps 計費和定價的運作方式。 如需定價費率，請參閱 [Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>取用量定價模式

針對在公用「全域」、多租使用者 Azure Logic Apps 服務中執行的新邏輯應用程式，您只需支付使用的部分。 這些邏輯應用程式會使用以取用量為基礎的方案和定價模型。 在您的邏輯應用程式中，每個步驟都是一個動作，Azure Logic Apps 計量您的邏輯應用程式中執行的所有動作。

例如，動作包括：

* [觸發](#triggers)程式，這是特殊動作。 所有邏輯應用程式都需要觸發程式作為第一個步驟。

* 「[內建」或原生動作](../connectors/apis-list.md#built-in)（例如 HTTP）、AZURE FUNCTIONS 和 API 管理的呼叫等等

* 呼叫 [受管理的連接器](../connectors/apis-list.md#managed-connectors) ，例如 Outlook 365、Dropbox 等

* [控制工作流程動作](../connectors/apis-list.md#control-workflow) ，例如迴圈、條件陳述式等等

[標準連接器](../connectors/apis-list.md#managed-connectors) 會依 [標準連接器價格](https://azure.microsoft.com/pricing/details/logic-apps)收費。 正式運作的 [企業連接器](../connectors/apis-list.md#managed-connectors) 會以 [企業連接器價格](https://azure.microsoft.com/pricing/details/logic-apps)收費，而公用預覽企業連接器則是以 [標準連接器價格](https://azure.microsoft.com/pricing/details/logic-apps)計費。

深入瞭解計費在 [觸發](#triggers) 程式和 [動作](#actions) 層級的運作方式。 或者，如需限制的相關資訊，請參閱 [Azure Logic Apps 的限制和](logic-apps-limits-and-config.md)設定。

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>固定定價模式

[*整合服務環境* (ISE) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)可讓您建立和執行邏輯應用程式，以存取 Azure 虛擬網路中的資源。 在 ISE 中執行的邏輯應用程式不會產生資料保留成本。 當您建立 ISE，而只有在建立期間，您可以選擇 [ise 層級或「SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)」，其 [定價費率](https://azure.microsoft.com/pricing/details/logic-apps)不同：

* **Premium** ISE：此 SKU 的基礎單位有固定容量，但如果您需要更多的輸送量，您可以在 ISE 建立期間或之後 [新增更多縮放單位](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) 。 針對 ISE 限制，請參閱 [Azure Logic Apps 的限制和](logic-apps-limits-and-config.md#integration-service-environment-ise)設定。

* **開發人員** ISE：此 SKU 無法相應增加、沒有服務等級協定 (SLA) ，而且沒有已發佈的限制。 此 SKU 僅供實驗、開發和測試之用，而不是供生產環境或效能測試之用。

針對您在 ISE 中建立並執行的邏輯應用程式，您需支付 [固定價格](https://azure.microsoft.com/pricing/details/logic-apps) (與這些功能的每次使用) 費用：

* [內建觸發程式](../connectors/apis-list.md#built-in) 和動作

  在 ISE 中，內建的觸發程式和動作會顯示 **核心** 標籤，並在與邏輯應用程式相同的 ISE 中執行。

* [標準](../connectors/apis-list.md#managed-connectors) 連接器和 [企業](../connectors/apis-list.md#enterprise-connectors) 連接器，可讓您依需要擁有多個企業連線

   顯示 **ise** 標籤的標準和企業連接器會在與邏輯應用程式相同的 ise 中執行。 未顯示 ISE 標籤的連接器會在公用、「全域」、多租使用者 Logic Apps 服務中執行。 當您使用在 ISE 中執行的邏輯應用程式時，固定定價也適用于在多租使用者服務中執行的連接器。

* [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 的使用方式，不需額外費用，以 [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)為基礎：

  * **Premium** ISE SKU：單一 [標準層](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 整合帳戶

  * **開發人員** ISE SKU：單一 [免費層](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 整合帳戶

  每個 ISE SKU 的整合帳戶總計為5個。 針對額外成本，您可以根據 ISE SKU 擁有更多整合帳戶：

  * **Premium** ISE SKU：最多四個標準帳戶。 沒有免費或基本帳戶。

  * **開發人員** ISE SKU：最多4個標準帳戶，或最多5個標準帳戶。 沒有基本帳戶。

  如需有關整合帳戶限制的詳細資訊，請參閱 [Azure Logic Apps 的限制和](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)設定。 您可以稍後在本主題中深入瞭解 [整合帳戶層及其定價模型](#integration-accounts) 。

<a name="connectors"></a>

## <a name="connectors"></a>連接器

Azure Logic Apps 連接器可協助您的邏輯應用程式存取雲端或內部部署中的應用程式、服務和系統，方法是提供 [觸發](#triggers)程式、 [動作](#actions)或兩者。 連接器會分類為 Standard 或 Enterprise。 如需這些連接器的總覽，請參閱 [Azure Logic Apps 的連接器](../connectors/apis-list.md)。 如果您想要在邏輯應用程式中使用的 REST Api 沒有預建的連接器，您可以建立 [自訂連接器](/connectors/custom-connectors)，而這只是那些 rest api 周圍的包裝函式。 自訂連接器的計費方式為標準連接器。 下列各節提供有關如何計費觸發程式和動作的詳細資訊。

<a name="triggers"></a>

## <a name="triggers"></a>觸發程序

觸發程式一律是邏輯應用程式工作流程中的第一個步驟，而且是特殊的動作，可在符合特定準則或發生特定事件時，建立並執行邏輯應用程式實例。 觸發程序會以不同動作影響邏輯應用程式計量方式。 以下是存在於 Azure Logic Apps 中的各種觸發程式：

* **週期觸發**程式：您可以使用此不屬於任何服務或系統的一般觸發程式來啟動任何邏輯應用程式工作流程，並建立邏輯應用程式實例，此實例會根據您在觸發程式中設定的週期間隔來執行。 例如，您可以設定每隔三天執行一次或以更複雜的排程執行的週期觸發程式。

* **輪詢觸發**程式：您可以使用這個更特製化的迴圈觸發程式（通常與特定服務或系統的受控連接器相關聯），根據您在觸發程式中設定的週期間隔，檢查符合建立和執行邏輯應用程式實例之準則的事件或訊息。 即使未建立任何邏輯應用程式實例（例如略過觸發程式），Logic Apps 服務也會將每個輪詢要求視為執行。 您可以透過邏輯應用程式設計工具來設定觸發程序，以指定輪詢間隔。

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook 觸發**程式：您可以使用 webhook 觸發程式來等候用戶端將要求傳送至特定端點 URL 的邏輯應用程式，而不是使用輪詢觸發程式。 傳送至 webhook 端點的每個要求都會計為動作執行。 例如，要求和 HTTP Webhook 觸發程式都是一般的 webhook 觸發程式。 某些服務或系統的連接器也會有 webhook 觸發程式。

<a name="actions"></a>

## <a name="actions"></a>動作

Azure Logic Apps 計量「內建」動作（例如 HTTP）作為原生動作。 例如，內建動作包括 HTTP 呼叫、來自 Azure Functions 或 API 管理的呼叫，以及控制流程步驟（例如條件、迴圈和 switch 語句）。 每個動作都有自己的動作類型。 例如，呼叫 [連接器](/connectors) 的動作會有 "ApiConnection" 類型。 這些連接器會分類為標準或企業連接器，並根據其各自的 [定價](https://azure.microsoft.com/pricing/details/logic-apps)進行計量。 *預覽版*中的企業連接器會以標準連接器的形式收費。

Azure Logic Apps 將所有成功和失敗的動作都計量為執行。 不過，Logic Apps 不會計量這些動作：

* 因為條件不符而略過的動作
* 因為邏輯應用程式在完成前停止而不會執行的動作

針對在迴圈內執行的動作，Azure Logic Apps 計算迴圈中每個迴圈的每個動作。 例如，假設您有可處理清單的 "for each" 迴圈。 Logic Apps 會將清單項目數目乘以迴圈中的動作數目，以計量該迴圈中的動作，並新增可啟動迴圈的動作。 因此，10個專案清單的計算是 (10 * 1) + 1，這會產生11個動作執行。

## <a name="disabled-logic-apps"></a>停用的邏輯應用程式

停用的邏輯應用程式不會收取費用，因為它們在停用時無法建立新的實例。 停用邏輯應用程式之後，其目前正在執行的執行個體可能需要一些時間才能完全停止。

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>整合帳戶

[固定的定價模式](https://azure.microsoft.com/pricing/details/logic-apps)適用于[整合帳戶](logic-apps-enterprise-integration-create-integration-account.md)，您可以在其中探索、開發及測試 Azure Logic Apps 中的[B2B 和 EDI](logic-apps-enterprise-integration-b2b.md)和[XML 處理](logic-apps-enterprise-integration-xml.md)功能，而不需額外成本。 每個 Azure 訂用帳戶最多可以有 [特定的整合帳戶限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。 每個整合帳戶最多可儲存特定 [的構件限制](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)，包括交易夥伴、合約、地圖、架構、元件、憑證、批次設定等。

Azure Logic Apps 提供免費、基本和標準的整合帳戶。 Logic Apps 的服務等級協定 (SLA) 支援基本和標準層，但 SLA 不支援免費層，且有區域可用性、輸送量和使用量的限制。 除了免費層整合帳戶之外，每個 Azure 區域中可以有一個以上的整合帳戶。 如需定價費率，請參閱 [Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps/)。

如果您的 [*整合服務環境* (Ise) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)（ [PREMIUM 或 Developer](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)），ise 可以有5個整合帳戶。 若要瞭解如何針對 ISE 使用固定的定價模式，請參閱本主題的先前 [固定定價模型](#fixed-pricing) 一節。 如需定價費率，請參閱 [Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps)。

若要在免費、基本或標準整合帳戶之間做選擇，請參閱這些使用案例描述：

* **Free**：適用于當您想要嘗試探索案例，而不是生產案例。 這一層僅適用于 Azure 中的公用區域，例如美國西部或東南亞，但不適用於 Azure 中國的 [世紀](/azure/china/overview-operations) 或 [Azure Government](../azure-government/documentation-government-welcome.md)。

* **基本**：適用于當您只想要處理訊息，或作為與較大商務實體有交易夥伴關係的小型企業夥伴時

* **標準**：當您有更複雜的 B2B 關係，以及您必須管理的實體數目增加時

<a name="data-retention"></a>

## <a name="data-retention"></a>資料保留

除了在整合服務環境中執行的邏輯應用程式 (ISE) ，儲存在邏輯應用程式執行歷程記錄中的所有輸入和輸出都會根據邏輯應用程式的 [執行保留期限](logic-apps-limits-and-config.md#run-duration-retention-limits)計費。 在 ISE 中執行的邏輯應用程式不會產生資料保留成本。 如需定價費率，請參閱 [Logic Apps 定價](https://azure.microsoft.com/pricing/details/logic-apps)。

為了協助您監視邏輯應用程式的儲存體耗用量，您可以：

* 查看邏輯應用程式每月使用的儲存體單位數（以 GB 為單位）。
* 在邏輯應用程式的執行歷程記錄中，查看特定動作之輸入和輸出的大小。

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>查看邏輯應用程式儲存體耗用量

1. 在 Azure 入口網站中，尋找並開啟邏輯應用程式。

1. 從邏輯應用程式的功能表中，選取 [ **監視**] 底下的 [ **計量**]。

1. 在右側窗格的 [ **圖表標題**] 下，從 [ **度量** ] 清單中選取 [ **儲存體耗用量執行的計費使用量**]。

   此計量會提供您每月收取的儲存體耗用量單位數（以 GB 為單位）。

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>視圖動作輸入和輸出大小

1. 在 Azure 入口網站中，尋找並開啟邏輯應用程式。

1. 在邏輯應用程式的功能表上，選取 **[總覽**]。

1. 在右側窗格的 [ **執行歷程記錄**] 底下，選取具有您想要檢查之輸入和輸出的執行。

1. 在 [ **邏輯應用程式執行**] 下，選擇 [ **執行詳細資料**]。

1. 在 **邏輯應用程式的 [執行詳細資料** ] 窗格中，于 [動作] 資料表中列出每個動作的 [狀態] 和 [持續時間]，然後選取您要查看的動作。

1. 在 [ **邏輯應用程式動作** ] 窗格中，尋找該動作的輸入和輸出在 [ **輸入連結** 和 **輸出] 連結**底下的大小。

## <a name="next-steps"></a>接下來的步驟

* [深入瞭解 Azure Logic Apps](logic-apps-overview.md)
* [建立第一個邏輯應用程式](quickstart-create-first-logic-app-workflow.md)
