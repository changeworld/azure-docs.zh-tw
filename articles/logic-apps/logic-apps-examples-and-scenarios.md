---
title: 常見方案&示例
description: 查找 Azure 邏輯應用的示例、常見方案、教程和演練
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: c64483f18b2deb061704a4eb7171e575fdd561e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164622"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Azure Logic Apps 的常見情節、範例、教學課程和逐步解說

[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)通過提供[數百個即用型連接器](../connectors/apis-list.md)（從本地 SQL Server 或 SAP 到 Azure 認知服務）來説明您協調和集成不同的服務。 Logic Apps 服務為「無伺服器」，因此您不必擔心縮放比例或執行個體。 您只需要使用觸發程序和工作流程執行的動作，即可定義觸發程序。 基礎平台可處理調整、可用性和效能。 邏輯應用對於需要協調跨多個系統和服務的操作的用例和方案特別有用。

為了説明您瞭解 Azure 邏輯應用支援的功能和模式，本文介紹常見起點、示例和方案。

## <a name="common-starting-points-for-logic-app-workflows"></a>邏輯應用工作流的常見起點

每個邏輯應用程式都是以[觸發程序**](../logic-apps/logic-apps-overview.md#logic-app-concepts)開頭，並只有一個觸發程序會啟動邏輯應用程式工作流程，並傳遞任何資料作為該觸發程序的一部分。 某些連接器會提供觸發程序，這些類型有：

* 輪詢觸發程序**：會定期檢查新資料的服務端點。 當新的資料存在時，觸發程序會使用資料建立並執行新的工作流程執行個體作為輸入。

* 推送觸發程序**：在服務端點中接聽資料，並等待特定事件發生。 當事件發生時，會立即引發觸發程序，建立和執行使用任何可用資料作為輸入的新工作流程執行個體。

以下是描述常用觸發器的示例：

* *輪詢*觸發器：

  * [**通過定期**觸發器](../connectors/connectors-native-recurrence.md)，您可以設置開始日期和時間以及觸發邏輯應用的重複。 例如，您可以選取要觸發邏輯應用程式的每週天數和每天時間。 如需詳細資訊，請參閱下列主題：<p>

    * [使用 Azure Logic Apps 排程和執行週期性自動化工作、流程和工作流程](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [教程：使用 Azure 邏輯應用創建基於計畫的自動定期工作流](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * **收到電子郵件時**觸發器允許您的邏輯應用檢查來自邏輯應用支援的任何郵件供應商的新電子郵件，例如[Office 365](../connectors/connectors-create-api-office365-outlook.md)Outlook、Gmail、Outlook.com 等。 [Gmail](https://docs.microsoft.com/connectors/gmail/) [Outlook.com](https://docs.microsoft.com/connectors/outlook/) 如需詳細資訊，請參閱下列主題：<p>

    * [教程：使用 Azure 邏輯應用創建基於審批的自動化工作流](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [教程：使用 Azure 邏輯應用、Azure 函數和 Azure 存儲自動執行任務以處理電子郵件](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * [ **HTTP**觸發器](../connectors/connectors-native-http.md)可以通過 HTTP 或 HTTPS 調用服務終結點。 有關詳細資訊，請參閱使用[HTTP 終結點 調用、觸發器或嵌套工作流](../logic-apps/logic-apps-http-endpoint.md)。

* *推送*觸發器：

  * [**請求**觸發器](../connectors/connectors-native-reqres.md)可以接收傳入的 HTTPS 請求。

  * [**HTTP Webhook** 觸發程序](../connectors/connectors-native-webhook.md)可訂閱服務端點，方法為使用該服務註冊回呼 URL**。 這樣一來，當指定的事件發生時，服務可以只通知觸發程序，讓觸發程序不需要輪詢服務。

發生指定事件後，觸發器將觸發，這將創建新的邏輯應用工作流實例並在工作流中運行操作。 您可以在整個工作流程中，從觸發程序存取任何資料。 例如，Twitter**上一個新的推文**觸發器將推文內容傳遞到邏輯應用運行中。 要開始使用 Azure 邏輯應用，請嘗試以下快速入門主題：

* [快速入門：使用 Azure 邏輯應用創建第一個自動化工作流 - Azure 門戶](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [快速入門：使用 Azure 邏輯應用創建自動化任務、流程和工作流 - 視覺化工作室](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [快速入門：使用 Visual Studio 代碼創建和管理自動邏輯應用工作流](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>控制流和錯誤處理功能

邏輯應用包括用於高級控制流的豐富功能，例如條件、交換器、迴圈和作用域。 若要確保解決方案的彈性，您也可以在工作流程中實作錯誤和例外狀況處理。

* 執行以[條件陳述式](../logic-apps/logic-apps-control-flow-conditional-statement.md)和 [Switch 陳述式](../logic-apps/logic-apps-control-flow-switch-statement.md)為基礎的不同動作
* [使用迴圈重複執行步驟或處理陣列和集合中的項目](../logic-apps/logic-apps-control-flow-loops.md)
* [將動作與範圍群組在一起](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [將錯誤和異常處理添加到工作流](../logic-apps/logic-apps-exception-handling.md)
* [使用案例︰醫療保健公司如何使用邏輯應用程式的例外狀況處理來處理 HL7 FHIR 工作流程](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>創建自訂 API 和連接器

對於沒有已發佈連接器的系統和服務，還可以擴展邏輯應用。

* [創建自訂 API 以從 Azure 邏輯應用調用](../logic-apps/logic-apps-create-api-app.md)
* [使用輪詢觸發器模式定期檢查新資料或事件](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [使用 Webhook 觸發器模式等待並偵聽新資料或事件](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [使用輪詢操作模式執行長時間運行的任務](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [使用 Webhook 操作模式執行長時間運行的任務](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Azure 邏輯應用中的自訂連接器](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>構建企業對企業 （B2B） 解決方案

對於企業集成解決方案和組織之間的無縫通信，可以使用 Azure 邏輯應用使用企業集成包 （EIP） 為這些方案構建自動可擴展工作流。 雖然組織使用不同的通訊協定與格式，但他們能以電子方式來交換訊息。 EIP 將不同的格式轉換為組織系統可以處理和支援行業標準協定的格式，包括 AS2、X12、EDIFACT 和羅塞塔網。 要構建這些解決方案，請創建一個集成帳戶，這是一個單獨的 Azure 資源，為定義和與邏輯應用工作流一起使用的專案提供安全、可擴展和可管理的容器。 例如，工件包括交易夥伴、協定、地圖、架構、證書和批次處理配置。

* [概述：B2B 企業集成解決方案與 Azure 邏輯應用和企業集成包](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [在 Azure 邏輯應用中為 B2B 企業集成創建和管理集成帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>訪問 Azure 虛擬網路資源

有時，邏輯應用和集成帳戶需要訪問 Azure 虛擬網路中的安全資源，如虛擬機器 （VM） 和其他系統或服務。 要設置此訪問，您可以創建整合服務環境 （ISE），您可以在其中生成和運行邏輯應用。 ISE 是邏輯應用服務的私有和隔離實例，它使用專用資源（如存儲），並且獨立于公共"全域"多租戶邏輯應用服務運行。 分離隔離的私有實例和公共全域實例還有助於減少其他 Azure 租戶可能對應用性能的影響，也稱為"嘈雜鄰居"效果。

* [概述：從 Azure 邏輯應用訪問 Azure 虛擬網路資源](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [從 Azure Logic Apps 連線到 Azure 虛擬網路](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>部署、管理和監視邏輯應用

您可以透過 Visual Studio、Azure DevOps 或其他任何原始檔控制和自動化建置工具，完整地開發及部署邏輯應用程式。 為了支援將工作流程與相依連線部署在資源範本中的功能，邏輯應用程式使用 Azure 資源部署範本。 Visual Studio 工具會自動產生這些範本，您可以將其簽入原始檔控制以便控制版本。 針對工作流程執行狀態的通知和診斷記錄，Azure Logic Apps 也提供了監視和警示。

### <a name="deploy"></a>部署

* [快速入門：使用 Azure 邏輯應用創建自動化任務、流程和工作流 - 視覺化工作室](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [概述：自動部署邏輯應用](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [創建 Azure 資源管理器範本以自動部署 Azure 邏輯應用](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [為 Azure 邏輯應用部署 Azure 資源管理器範本](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [示例：從 Azure 邏輯應用連接到 Azure 服務匯流排佇列，並在 Azure DevOps 中使用 Azure 管道進行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：從 Azure 邏輯應用連接到 Azure 存儲帳戶，並在 Azure DevOps 中使用 Azure 管道進行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：為 Azure 邏輯應用設置函數應用操作，並在 Azure DevOps 中使用 Azure 管道進行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：從 Azure 邏輯應用連接到集成帳戶，並在 Azure DevOps 中使用 Azure 管道進行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [示例：使用 Azure 邏輯應用協調 Azure 管道](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>管理

* [使用視覺化工作室管理邏輯應用](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [創建和管理 B2B 企業集成的集成帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [在 Azure 邏輯應用中管理整合服務環境 （ISE）](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>監視

* [監視運行狀態、查看觸發器歷史記錄並為 Azure 邏輯應用設置警報](../logic-apps/monitor-logic-apps.md)
* [設置 Azure 監視器日誌並收集 Azure 邏輯應用的診斷資料](../logic-apps/monitor-logic-apps-log-analytics.md)
* [在 Azure 邏輯應用中設置 Azure 監視器日誌並收集 B2B 消息的診斷資料](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [查看和創建查詢，以便監視和跟蹤 Azure 邏輯應用的 Azure 監視器日誌](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>處理內容類型、轉化和轉換

您可以使用 Azure Logic Apps [工作流程定義語言](https://aka.ms/logicappsdocs)中的許多函數，來存取、轉換 (Convert) 及轉換 (Transform) 多種內容類型。 例如，您可以使用 `@json()` 和 `@xml()` 工作流程運算式，在字串、JSON 和 XML 之間進行轉換 (Convert)。 Logic Apps 引擎會保留內容類型，以支援在服務之間以不失真的方式進行內容傳輸的功能。

* [處理 Azure 邏輯應用中的內容類型](../logic-apps/logic-apps-content-type.md)，如`application/``application/octet-stream`和 。`multipart/formdata`
* [在 Azure 邏輯應用和電源自動化運算式中使用函數的參考指南](../logic-apps/workflow-definition-language-functions-reference.md)
* [Azure Logic Apps 的工作流程定義語言結構描述](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>其他整合和功能

Azure 邏輯應用與許多服務（如 Azure 函數、Azure API 管理、Azure 應用服務和自訂 HTTP 終結點（例如 REST 和 SOAP）集成。

* [從 Azure 邏輯應用調用 Azure 函數](../logic-apps/logic-apps-azure-functions.md)
* [教程：使用 Azure 函數和 Azure 服務匯流排調用或觸發邏輯應用](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [教程：使用 Azure 邏輯應用和 Azure 函數創建流式客戶洞察儀表板](../logic-apps/logic-apps-scenario-social-serverless.md)
* [教程：創建與 Azure 邏輯應用和 Azure 認知服務集成的函數，以分析 Twitter 帖子情緒](../azure-functions/functions-twitter-email.md)
* [教程：使用 Power BI 和 Azure 邏輯應用構建 AI 支援的社交儀表板](https://aka.ms/logicappsdemo)
* [教程：使用 Azure 事件網格和邏輯應用監視虛擬機器更改](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [教學課程：搭配連線 IoT 中樞和信箱的 Azure Logic Apps 進行 IoT 遠端監視和通知](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [博客：使用 Azure 邏輯應用調用 SOAP 服務](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>端對端案例

* [白皮書：使用 Azure 服務 (例如 Logic Apps) 進行端對端案例管理整合](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>客戶案例

了解 Azure Logic Apps 以及其他 Azure 服務和 Microsoft 產品如何協助[這些公司](https://aka.ms/logic-apps-customer-stories)提升其靈活度，並藉由簡化、組織、自動化及協調複雜的程序，專注於其核心業務。

## <a name="next-steps"></a>後續步驟

* 瞭解[邏輯應用的連接器](../connectors/apis-list.md)
* 瞭解[與 Azure 邏輯應用的 B2B 企業集成方案](../logic-apps/logic-apps-enterprise-integration-overview.md)
