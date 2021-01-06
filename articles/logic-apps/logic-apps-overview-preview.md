---
title: Azure Logic Apps 預覽的概觀
description: Azure Logic Apps Preview 是一種雲端解決方案，可讓您建立自動化具狀態和無狀態工作流程，將應用程式、資料、服務和系統與企業層級案例的程式碼進行整合。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 7c15b3a854b533d93bc05f7e5302671711da75c2
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936119"
---
# <a name="overview-azure-logic-apps-preview"></a>總覽： Azure Logic Apps 預覽

> [!IMPORTANT]
> 此公開預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 Azure Logic Apps Preview，您可以透過使用新的 **邏輯應用程式 (Preview)** 資源類型，建立及執行包含具 [*狀態* 和 *無狀態* 工作流程](#stateful-stateless)的邏輯應用程式，以建立應用程式、資料、雲端服務和系統之間的自動化和整合解決方案。 使用這個新的邏輯應用程式類型，您可以建立多個工作流程，這些工作流程是由重新設計的 Azure Logic Apps 預覽執行時間所提供，可提供可攜性、更佳的效能和彈性，以便在不同的裝載環境中部署和執行，而不只是 Azure，也

這有何可能？ 重新設計的執行時間會使用 Azure Functions 擴充性 [模型](../azure-functions/functions-bindings-register.md) ，並且在 Azure Functions 執行時間上裝載為擴充功能。 此架構表示您可以在 Azure Functions 執行的任何位置執行新的邏輯應用程式類型。 您可以在幾乎任何網路拓撲上裝載重新設計的執行時間，並選擇任何可用的計算大小，以處理工作流程所需的必要工作負載。 如需詳細資訊，請參閱 [Azure Functions 簡介](../azure-functions/functions-overview.md) 和 [Azure Functions 觸發程式和](../azure-functions/functions-triggers-bindings.md)系結。

您可以 [從 Azure 入口網站開始](create-stateful-stateless-workflows-azure-portal.md)，或 [在 Visual Studio Code 中建立具有 Azure Logic Apps (Preview) 延伸](create-stateful-stateless-workflows-visual-studio-code.md)模組的專案，藉以建立 **邏輯應用程式 (預覽版)** 資源。 此外，在 Visual Studio Code 中，您可以在開發環境中建立 *並在本機執行* 您的工作流程。 無論您使用入口網站或 Visual Studio Code，都可以在相同類型的裝載環境中部署和執行新的邏輯應用程式類型。

本總覽涵蓋下列領域：

* [Azure Logic Apps 預覽、Azure Logic Apps 多租使用者環境和整合服務環境之間的差異](#preview-differences)。

* 具[狀態和無狀態工作流程之間的差異](#stateful-stateless)，包括[嵌套具狀態和無狀態工作流程](#nested-behavior)之間的行為差異。

* [此公開預覽中的功能](#public-preview-contents)。

* [定價模型的運作方式](#pricing-model)。

* [變更、受限、無法使用或不支援的功能](#limited-unavailable-unsupported)。

* [Azure Logic Apps 預覽中的限制](#limits)。

如需詳細資訊，請參閱下列其他文章：

* [Azure Logic Apps 執行中的任何地方-執行時間深入探討](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [Logic Apps (GitHub 的公開預覽已知問題) ](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Azure Logic Apps 預覽有何不同？

Azure Logic Apps 預覽執行時間使用 [Azure Functions](../azure-functions/functions-overview.md) 擴充性，並裝載為 Azure Functions 執行時間的擴充功能。 此架構表示您可以在 Azure Functions 執行的任何位置執行新的邏輯應用程式類型。 您可以在幾乎任何想要的網路拓撲上裝載 Azure Logic Apps Preview 執行時間，並選擇任何可用的計算大小，以處理工作流程所需的必要工作負載。 如需 Azure Functions 擴充性的詳細資訊，請參閱 [WEBJOB SDK：建立自訂輸入和輸出](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)系結。

使用這個新的方法，Azure Logic Apps Preview 執行時間和您的工作流程都是您應用程式的一部分，您可以將其封裝在一起。 這項功能可讓您輕鬆地部署和執行工作流程，只要將構件複製到裝載環境，然後啟動應用程式即可。 在您將變更部署至生產環境之前，這種方法也會提供更標準化的體驗，讓您在工作流程專案周圍建立 DevOps 管線，以執行必要的測試和驗證。 如需詳細資訊，請參閱 [Azure Logic Apps 執行任何地方-執行時間深入探討](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)。

下表簡要說明工作流程共用資源的方式，根據其執行所在的環境而有所不同。 如需限制的差異，請參閱 [Azure Logic Apps 預覽中的限制](#limits)。

| 環境 | 資源分享和耗用量 |
|-------------|----------------------------------|
| Azure Logic Apps (多租使用者)  | *來自多個租使用者中客戶* 的工作流程，會共用相同的處理 (計算) 、儲存體、網路等等。 |
| Azure Logic Apps (Preview)  | *相同邏輯應用程式中* 的工作流程會共用相同的處理 (計算) 、儲存體、網路等等。 |
| 整合服務環境 (無法在預覽中使用)  | *相同環境* 中的工作流程會共用相同的處理 (計算) 、儲存體、網路等等。 |
||||

同時，您仍然可以使用原始的 Azure Logic Apps 延伸模組，在 Azure 入口網站和 Visual Studio Code 中建立原始的邏輯應用程式類型。 雖然原始和新的邏輯應用程式類型之間的開發體驗不同，但您的 Azure 訂用帳戶可以同時包含這兩種類型。 您可以在 Azure 訂用帳戶中查看及存取所有已部署的邏輯應用程式，但應用程式會組織成自己的類別和區段。

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>具狀態和無狀態工作流程

* *具狀態*

  當您需要保留、審核或參考先前事件的資料時，請建立具狀態工作流程。 這些工作流程會將每個動作及其狀態的輸入和輸出儲存在外部儲存體中，這會在每次執行完成之後，檢查執行詳細資料和歷程記錄。 可設定狀態的工作流程會在中斷發生時提供高彈性。 還原服務和系統之後，您可以從儲存的狀態重建中斷的執行，然後重新執行工作流程以完成。 可設定狀態的工作流程最多可繼續執行一年。

* *無狀態*

  當您不需要儲存、審核或參考外部儲存體中先前事件的資料，以供日後審查時，請建立無狀態工作流程。 這些工作流程只會將每個動作的輸入和輸出儲存 *在記憶體中*，而不會將這些資料傳輸至外部儲存體。 如此一來，無狀態工作流程的執行通常不會超過5分鐘、更快速的效能，並縮短回應時間、提高輸送量，以及降低執行成本，因為執行詳細資料和歷程記錄不會保留在外部儲存體中。 但是，如果發生中斷，則不會自動還原中斷的執行，因此呼叫端需要手動重新提交中斷的執行。 這些工作流程只能以同步方式執行。

  為了更容易進行偵錯工具，您可以啟用無狀態工作流程的執行歷程記錄，這會對效能造成一些影響，然後在完成時停用執行歷程記錄。 如需詳細資訊，請參閱在 Visual Studio Code 中建立具狀態 [和無狀態工作流程](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) ，或 [在 Azure 入口網站中建立具狀態和無狀態工作流程](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless)。

  > [!NOTE]
  > 無狀態工作流程目前僅支援 [受管理連接器](../connectors/apis-list.md#managed-api-connectors)的 *動作*，這些連接器會部署在 Azure 中，而不支援觸發程式。 若要啟動您的工作流程，請選取 [內建的要求、事件中樞或服務匯流排觸發](../connectors/apis-list.md#built-ins)程式。 這些觸發程式會以原生方式在 Azure Logic Apps Preview 執行時間中執行。 如需受限、無法使用或不支援的觸發程式、動作和連接器的詳細資訊，請參閱 [變更、受限、無法使用或不支援的功能](#limited-unavailable-unsupported)。

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>具狀態和無狀態工作流程之間的嵌套行為差異

您可以使用 [要求觸發](../connectors/connectors-native-reqres.md)程式、 [HTTP Webhook 觸發](../connectors/connectors-native-webhook.md)程式，或具有 [APICONNECTIONWEBHOOK 類型](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)並可接收 HTTPS 要求的受控連接器觸發程式，[讓工作流程](../logic-apps/logic-apps-http-endpoint.md)可從存在於相同 **邏輯應用程式** 的其他工作流程中呼叫 (預覽版) 資源。

以下是父工作流程呼叫子工作流程之後，嵌套工作流程可以遵循的行為模式：

* 非同步輪詢模式

  父代不會等候其初始呼叫的回應，但會持續檢查子系的執行歷程記錄，直到子系完成執行為止。 根據預設，具狀態工作流程會遵循此模式，這適用于可能超出 [要求超時限制](../logic-apps/logic-apps-limits-and-config.md)的長時間執行子工作流程。

* 同步模式 ( 「火災別忘了」 ) 

  子系會立即傳迴響應來確認呼叫 `202 ACCEPTED` ，而父系會繼續執行下一個動作，而不會等待子系的結果。 相反地，父系會在子系執行完成時收到結果。 未包含回應動作的子具狀態工作流程一律會遵循同步模式。 針對子具狀態工作流程，您可以使用執行歷程記錄來進行審核。

  若要啟用此行為，請在工作流程的 JSON 定義中，將 `operationOptions` 屬性設定為 `DisableAsyncPattern` 。 如需詳細資訊，請參閱 [觸發程式和動作類型-操作選項](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)。

* 觸發程式並等候

  針對子無狀態工作流程，父系會等待傳回子系結果的回應。 此模式的運作方式類似于使用內建的 [HTTP 觸發程式或動作](../connectors/connectors-native-http.md) 來呼叫子工作流程。 未包含回應動作的子無狀態工作流程會立即傳回 `202 ACCEPTED` 回應，但父系會等待子系完成，然後再繼續下一個動作。 這些行為只適用于子無狀態工作流程。

這個資料表會根據父項和子系是具狀態、無狀態，還是混合的工作流程類型，來指定子工作流程的行為：

| 父工作流程 | 子工作流程 | 子行為 |
|-----------------|----------------|----------------|
| 具狀態 | 具狀態 | 非同步或同步的 `"operationOptions": "DisableAsyncPattern"` 設定 |
| 具狀態 | 無狀態 | 觸發程式並等候 |
| 無狀態 | 具狀態 | 同步 |
| 無狀態 | 無狀態 | 觸發程式並等候 |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>功能

Azure Logic Apps Preview 包含許多目前和額外的功能，例如：

* 從 [390 + 連接器](/connectors/connector-reference/connector-reference-logicapps-connectors) 建立邏輯應用程式和其工作流程，以提供軟體即服務 (SaaS) 和平臺即服務 (PaaS) 應用程式和服務，以及內部部署系統的連接器。

  * 某些受管理的連接器（例如 Azure 服務匯流排、Azure 事件中樞和 SQL Server）執行方式類似于 Azure Logic Apps Preview 執行時間的原生觸發程式和動作，例如要求觸發程式和 HTTP 動作。 如需詳細資訊，請參閱 [Azure Logic Apps 執行內建連接器](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)擴充性。

  * 您可以在沒有整合帳戶的情況下，對液體作業和 XML 作業使用 B2B 動作。 若要使用這些動作，您必須擁有可透過 Azure 入口網站中的個別動作上傳的液體地圖、XML 地圖或 XML 架構，或使用個別的 **maps** 和 **架構** 資料夾，新增至 Visual Studio Code **專案的成品** 資料夾。

  * 建立及部署可在任何地方執行的邏輯應用程式，因為 Azure Logic Apps 服務會產生共用存取簽章 (SAS) 連接字串，這些邏輯應用程式可使用這些連接字串將要求傳送至雲端連線執行時間端點。 Logic Apps 服務會使用其他應用程式設定來儲存這些連接字串，讓您可以在 Azure 中部署時輕鬆地將這些值儲存在 Azure Key Vault 中。

    > [!NOTE]
    > 根據預設， **邏輯應用程式 (預覽版)** 資源會自動啟用其 [系統指派的受控識別](../logic-apps/create-managed-service-identity.md) ，以在執行時間驗證連線。 此身分識別與您建立連接時所使用的驗證認證或連接字串不同。 如果您停用此身分識別，連接將無法在執行時間運作。 若要查看此設定，請在邏輯應用程式的功能表上，選取 [ **設定**] 下的 [身分 **識別**]。

* 建立具有無狀態工作流程的邏輯應用程式，這些應用程式只會在記憶體中執行，使其更快速完成、更快速地回應、具有更高的輸送量，且成本較低，因為執行中的歷程記錄和資料不會保存在外部儲存體中。 （選擇性）您可以啟用執行歷程記錄，以便更輕鬆地進行調試。 如需詳細資訊，請參閱具 [狀態與無狀態邏輯應用程式](#stateful-stateless)。

* 在 Visual Studio Code 開發環境中，于本機執行、測試和偵測邏輯應用程式及其工作流程。

  在您執行並測試邏輯應用程式之前，您可以在工作流程的 **workflow.js** 檔案內加入和使用中斷點，讓偵錯工具更容易進行。 不過，目前只有動作（而非觸發程式）支援中斷點。 如需詳細資訊，請參閱 [在 Visual Studio Code 中建立具狀態和無狀態工作流程](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)。

* 直接將邏輯應用程式及其工作流程從 Visual Studio Code 發佈或部署到各種裝載環境，例如 Azure 和 [Docker 容器](/dotnet/core/docker/introduction)。

* 當您的 Azure 訂用帳戶和邏輯應用程式設定支援時，請使用 [Application Insights](../azure-monitor/app/app-insights-overview.md) 為您的邏輯應用程式啟用診斷記錄和追蹤功能。

> [!NOTE]
> 如需目前已知問題的相關資訊，請參閱 [GitHub 中的 Logic Apps 公開預覽的已知問題頁面](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)。

<a name="pricing-model"></a>

## <a name="pricing-model"></a>定價模式

當您在 Azure 入口網站中建立新的邏輯應用程式類型，或從 Visual Studio Code 進行部署時，您必須選擇主機方案（ [App Service 或 Premium](../azure-functions/functions-scale.md)），邏輯應用程式才能使用。 此方案會決定適用于執行邏輯應用程式的計價模式。 如果您選取 App Service 方案，則也必須選擇 [定價層](../app-service/overview-hosting-plans.md)。

可設定 *狀態* 的工作流程會使用 [外部儲存體](../azure-functions/storage-considerations.md#storage-account-requirements)，因此 [Azure 儲存體的定價](https://azure.microsoft.com/pricing/details/storage/)適用于 Azure Logic Apps Preview 執行時間所執行的儲存體交易。 例如，佇列是用於排程，而資料表和 blob 則是用來儲存工作流程狀態。

> [!NOTE]
> 在公開預覽期間，在 App Service 上執行邏輯應用程式不會在您選取的方案上方產生 *額外* 的費用。

如需適用于這個新資源類型的定價模式詳細資訊，請參閱下列主題：

* [Azure Functions 的級別和裝載](../azure-functions/functions-scale.md)
* [在 Azure App Service 中擴大應用程式](../app-service/manage-scale-up.md)
* [Azure Functions 定價詳細資料](https://azure.microsoft.com/pricing/details/functions/)
* [App Service 定價詳細資料](https://azure.microsoft.com/pricing/details/app-service/)
* [Azure 儲存體定價詳細資料](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>變更、受限、無法使用或不支援的功能

在 Azure Logic Apps Preview 中，這些功能有變更，或是目前受限、無法使用或不受支援：

* **觸發程式和動作**：某些內建的觸發程式無法使用，例如滑動視窗和批次。 若要啟動您的工作流程，請使用 [內建的週期、要求、HTTP、Http Webhook、事件中樞或服務匯流排觸發](../connectors/apis-list.md)程式。 內建的觸發程式和動作會以原生方式在 Azure Logic Apps Preview 執行時間中執行，而受控連接器則是部署在 Azure 中。 在設計工具中，內建的觸發程式和動作會出現在 **內建** 索引標籤底下，而受控連接器觸發程式和動作則出現在 [ **Azure** ] 索引標籤底下。

  > [!NOTE]
  > 若要在 Visual Studio Code 中于本機執行，以 webhook 為基礎的觸發程式和動作需要額外的設定。 如需詳細資訊，請參閱 [在 Visual Studio Code 中建立具狀態和無狀態工作流程](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup)。

  * 針對 *無狀態工作流程*，當您選取觸發程式時， **Azure** 索引標籤不會出現，因為您只能選取 [受管理的連接器 *動作*，而非觸發](../connectors/apis-list.md#managed-api-connectors)程式。 雖然您可以針對無狀態工作流程啟用 Azure 部署的受控連接器，但是設計工具不會顯示任何可供您新增的受控連接器觸發程式。

  * 針對可設定 *狀態的工作流程*（除了下列列出為無法使用的觸發程式和動作），您可以使用 [受管理的連接器觸發程式和動作](../connectors/apis-list.md#managed-api-connectors) 。

  * 這些觸發程式和動作可能已變更，或目前受限、不支援或無法使用：

    * [內部部署資料閘道 *觸發*](../connectors/apis-list.md#on-premises-connectors) 程式 *無法使用，但有可用的* 閘道動作。

    * [自訂連接器](../connectors/apis-list.md#custom-apis-and-connectors) 無法使用。

    * 內建的動作， [Azure Functions 選擇 azure](logic-apps-azure-functions.md) 函式現在是 **azure Function 作業-呼叫 azure 函式**。 此動作目前僅適用于從 **HTTP 觸發** 程式範本建立的函式。

      在 Azure 入口網站中，您可以透過使用者體驗來建立連線，以選取您可以存取的 HTTP 觸發程式函式。 如果您在程式碼視圖中檢查函式動作的 JSON 定義或檔案中的 **workflow.js** ，此動作會使用參考來參考函式 `connectionName` 。 此版本會將函式的資訊抽象化為連接，您可以在專案的 **connections.js** 檔案中找到該連接，這是您在建立連線之後可用的檔案。

      > [!NOTE]
      > 在預覽版本中，函數動作僅支援查詢字串驗證。 Azure Logic Apps Preview 會在進行連線時從函式取得預設金鑰，將該金鑰儲存在應用程式的設定中，並在呼叫函式時，使用金鑰進行驗證。
      >
      > 如同原始版本，如果您在入口網站中更新此金鑰（例如，透過入口網站中的 Azure Functions 體驗），則因為金鑰無效，所以函式動作不再有效。 若要修正此問題，您必須重新建立與您想要呼叫之函式的連線，或使用新的金鑰來更新應用程式的設定。

    * 內建動作（內嵌程式 [代碼執行 javascript 程式碼](logic-apps-add-run-inline-code.md) ）現在是 **內嵌程式碼作業-以內嵌 javascript** 執行。

      * 內嵌程式碼作業動作不再需要整合帳戶。

      * 如果您使用 macOS 或 Linux，當您在 Visual Studio Code 中使用 Azure Logic Apps (Preview) 延伸模組時，目前無法使用 **內嵌程式碼作業** 。

      * 如果您在內嵌程式碼作業動作中進行變更，您必須重新開機邏輯應用程式。

      * 內嵌程式碼作業動作有 [更新的限制](logic-apps-overview-preview.md#inline-code-limits)。

    * 某些 [內建的 B2B 觸發程式和整合帳戶的動作](../connectors/apis-list.md#integration-account-connectors) 無法使用，例如一般檔案 **編碼和** 解碼動作。

    * 內建動作 [Azure Logic Apps 選擇邏輯應用程式工作流程](logic-apps-http-endpoint.md) 現在是 **工作流程作業-在此工作流程應用程式中叫用工作流程**。

* **主控方案可用性**：無論您是在 Azure 入口網站中建立新的 **邏輯應用程式 (預覽版)** 資源類型，或從 Visual Studio Code 部署，您只能在 Azure 中使用 Premium 或 App Service 主控方案。 取用主機方案無法使用，且不支援部署此資源類型。 您可以從 Visual Studio Code 部署至 Docker 容器，但不能部署到 [整合服務環境 (ISE) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。

* **平行分支**：目前您無法透過新的設計工具體驗來新增平行分支。 不過，您仍然可以透過原始設計工具體驗來新增這些分支，並讓它們出現在新的設計工具中。

  1. 在設計工具的底部，選取 **新的 Canvas** 控制項來停用新的體驗。

  1. 將平行分支加入至您的工作流程。

  1. 再次選取 **新的畫布** 控制項，以啟用新的體驗。

* **縮放控制項**：縮放控制項目前無法在設計工具上使用。

* **Visual Studio Code 中的中斷點調試** 程式：雖然您可以在工作流程的 **workflow.js** 檔案中加入和使用中斷點，但目前只有動作（而非觸發程式）支援中斷點。 如需詳細資訊，請參閱 [在 Visual Studio Code 中建立具狀態和無狀態工作流程](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)。

<a name="limits"></a>

## <a name="updated-limits"></a>更新的限制

雖然 Azure Logic Apps 預覽版的許多限制與 [多租使用者 Azure Logic Apps 的限制](logic-apps-limits-and-config.md)相同，但 Azure Logic Apps Preview 的限制已變更。

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>HTTP 超時限制

針對單一輸入呼叫或輸出呼叫，這些觸發程式和動作的超時限制為230秒 (3.9 分鐘) ：

* 輸出要求： HTTP 觸發程式、HTTP 動作
* 輸入要求：要求觸發程式，HTTP Webhook 觸發程式，HTTP Webhook 動作

相較之下，以下是在邏輯應用程式及其工作流程執行所在的其他環境中，這些觸發程式和動作的超時限制：

* 多租使用者 Azure Logic Apps：120秒 (2 分鐘) 
* 整合服務環境：240秒 (4 分鐘) 

如需詳細資訊，請參閱 [HTTP 限制](logic-apps-limits-and-config.md#http-limits)。

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>受控連接器

受管理的連接器限制為每個連線每分鐘50個要求。 若要使用連接器節流問題，請參閱 [Azure Logic Apps 中的處理節流問題 (429-「太多要求」錯誤) ](handle-throttling-problems-429-errors.md#connector-throttling)。

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>內嵌程式碼作業 (執行 JavaScript 程式碼) 

針對單一邏輯應用程式定義，內嵌程式碼作業動作（ [**執行 JavaScript 程式碼**](logic-apps-add-run-inline-code.md)）具有下列更新的限制：

* 最大程式碼字元數從1024個字元增加到100000個字元。

* 執行程式碼的最長持續時間會從五秒增加為15秒。

如需詳細資訊，請參閱 [邏輯應用程式定義限制](logic-apps-limits-and-config.md#definition-limits)。

## <a name="next-steps"></a>後續步驟

* [在 Azure 入口網站中建立具狀態和無狀態工作流程](create-stateful-stateless-workflows-azure-portal.md)
* [在 Visual Studio Code 中建立具狀態和無狀態工作流程](create-stateful-stateless-workflows-visual-studio-code.md)
* [GitHub 中 Logic Apps 公開預覽的已知問題頁面](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

此外，我們也想要聽取您的 Azure Logic Apps Preview 體驗！

* 針對 bug 或問題，請 [在 GitHub 中建立您的問題](https://github.com/Azure/logicapps/issues)。
* 針對問題、要求、批註和其他意見反應，請 [使用此意見反應表單](https://aka.ms/lafeedback)。