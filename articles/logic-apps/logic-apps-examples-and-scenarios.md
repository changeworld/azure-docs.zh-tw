---
title: '& 常見案例的範例'
description: 尋找 Azure Logic Apps 的範例、常見案例、教學課程和逐步解說
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: ad5cc696892764ce68d4714ead98b8afd9c37669
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144162"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Azure Logic Apps 的常見情節、範例、教學課程和逐步解說

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)提供[數百個現成可用的連接器](../connectors/apis-list.md)，範圍從內部部署 SQL Server 或 SAP 到 Azure 認知服務，協助您協調和整合不同的服務。 Logic Apps 服務為「無伺服器」，因此您不必擔心縮放比例或執行個體。 您只需要使用觸發程序和工作流程執行的動作，即可定義觸發程序。 基礎平台可處理調整、可用性和效能。 Logic Apps 特別適用于需要跨多個系統和服務協調動作的使用案例和情節。

為了協助您瞭解 Azure Logic Apps 支援的功能和模式，本文說明常見的起始點、範例和案例。

## <a name="common-starting-points-for-logic-app-workflows"></a>邏輯應用程式工作流程的常見起點

每個邏輯應用程式都是以[觸發程序**](../logic-apps/logic-apps-overview.md#logic-app-concepts)開頭，並只有一個觸發程序會啟動邏輯應用程式工作流程，並傳遞任何資料作為該觸發程序的一部分。 某些連接器會提供觸發程序，這些類型有：

* 輪詢觸發程序**：會定期檢查新資料的服務端點。 當新的資料存在時，觸發程序會使用資料建立並執行新的工作流程執行個體作為輸入。

* 推送觸發程序**：在服務端點中接聽資料，並等待特定事件發生。 當事件發生時，會立即引發觸發程序，建立和執行使用任何可用資料作為輸入的新工作流程執行個體。

以下是描述常用觸發程式的範例：

* *輪詢*觸發程式：

  * [**週期**觸發](../connectors/connectors-native-recurrence.md)程式可讓您設定開始日期和時間，加上引發邏輯應用程式的週期。 例如，您可以選取要觸發邏輯應用程式的每週天數和每天時間。 如需詳細資訊，請參閱下列主題：<p>

    * [使用 Azure Logic Apps 排程和執行週期性自動化工作、流程和工作流程](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [教學課程：使用 Azure Logic Apps 建立自動化、以排程為基礎的週期性工作流程](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * [**收到電子郵件時**] 觸發程式可讓您的邏輯應用程式檢查來自 Logic Apps 支援的任何電子郵件提供者的新電子郵件，例如[Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md)、 [Gmail](https://docs.microsoft.com/connectors/gmail/)、 [Outlook.com](https://docs.microsoft.com/connectors/outlook/)等等。

    > [!IMPORTANT]
    > 如果您想要使用 Gmail 連接器，只有 G Suite 的商務帳戶可以在邏輯應用程式中使用此連接器，而不受限制。 如果您有 Gmail 取用者帳戶，您只能使用此連接器搭配特定的 Google 核准服務，或者您可以[建立 Google 用戶端應用程式，以](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application)用來搭配您的 Gmail 連接器進行驗證。 如需詳細資訊，請參閱[Azure Logic Apps 中 Google 連接器的資料安全性和隱私權原則](../connectors/connectors-google-data-security-privacy-policy.md)。

    如需詳細資訊，請參閱下列主題：<p>

    * [教學課程：使用 Azure Logic Apps 建立以核准為基礎的自動化工作流程](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [教學課程：使用 Azure Logic Apps、Azure Functions 和 Azure 儲存體來自動化處理電子郵件的工作](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * [ **Http**觸發](../connectors/connectors-native-http.md)程式可以透過 HTTP 或 HTTPS 呼叫服務端點。 如需詳細資訊，請參閱[使用 HTTP 端點呼叫、觸發或將工作流程嵌套](../logic-apps/logic-apps-http-endpoint.md)。

* *推送*觸發程式：

  * [**要求**觸發](../connectors/connectors-native-reqres.md)程式可以接收傳入的 HTTPS 要求。

  * [**HTTP Webhook** 觸發程序](../connectors/connectors-native-webhook.md)可訂閱服務端點，方法為使用該服務註冊回呼 URL**。 這樣一來，當指定的事件發生時，服務可以只通知觸發程序，讓觸發程序不需要輪詢服務。

在指定的事件發生之後，會引發觸發程式，這會建立新的邏輯應用程式工作流程實例，並在工作流程中執行動作。 您可以在整個工作流程中，從觸發程序存取任何資料。 例如，**新推文**觸發程式上的 Twitter 會將推文內容傳遞至邏輯應用程式執行。 若要開始使用 Azure Logic Apps，請嘗試下列快速入門主題：

* [快速入門：使用 Azure Logic Apps Azure 入口網站建立您的第一個自動化工作流程](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [快速入門：使用 Azure Logic Apps Visual Studio 建立自動化工作、進程和工作流程](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [快速入門：使用 Visual Studio Code 建立和管理自動化邏輯應用程式工作流程](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>控制流程和錯誤處理功能

邏輯應用程式包含豐富的功能，可用於高階控制流程，例如條件、切換、迴圈和範圍。 若要確保解決方案的彈性，您也可以在工作流程中實作錯誤和例外狀況處理。

* 執行以[條件陳述式](../logic-apps/logic-apps-control-flow-conditional-statement.md)和 [Switch 陳述式](../logic-apps/logic-apps-control-flow-switch-statement.md)為基礎的不同動作
* [使用迴圈重複執行步驟或處理陣列和集合中的項目](../logic-apps/logic-apps-control-flow-loops.md)
* [將動作與範圍群組在一起](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [將錯誤和例外狀況處理新增至工作流程](../logic-apps/logic-apps-exception-handling.md)
* [使用案例︰醫療保健公司如何使用邏輯應用程式的例外狀況處理來處理 HL7 FHIR 工作流程](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>建立自訂 Api 和連接器

對於沒有已發佈連接器的系統和服務，您也可以擴充邏輯應用程式。

* [建立自訂 Api 以從 Azure Logic Apps 呼叫](../logic-apps/logic-apps-create-api-app.md)
* [使用輪詢觸發程式模式定期檢查新的資料或事件](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [使用 webhook 觸發程式模式來等候並接聽新的資料或事件](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [使用輪詢動作模式執行長時間執行的工作](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [使用 webhook 動作模式執行長時間執行的工作](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Azure Logic Apps 中的自訂連接器](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>建立企業對企業（B2B）解決方案

對於企業整合解決方案和組織之間的無縫通訊，您可以使用企業整合套件（EIP）搭配 Azure Logic Apps，為這些案例建立自動化可調整的工作流程。 雖然組織使用不同的通訊協定與格式，但他們能以電子方式來交換訊息。 EIP 會將不同的格式轉換成您的組織系統可以處理並支援業界標準通訊協定的格式，包括 AS2、X12、EDIFACT 和 RosettaNet。 若要建立這些解決方案，您要建立整合帳戶，這是個別的 Azure 資源，為您所定義及與邏輯應用程式工作流程搭配使用的成品，提供安全、可擴充且可管理的容器。 例如，成品包括交易夥伴、合約、地圖、架構、憑證及批次設定。

* [總覽：使用 Azure Logic Apps 和企業整合套件的 B2B 企業整合解決方案](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [在 Azure Logic Apps 中建立和管理 B2B 企業整合的整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>存取 Azure 虛擬網路資源

有時候，您的邏輯應用程式和整合帳戶需要存取受保護的資源，例如 Azure 虛擬網路中的虛擬機器（Vm）和其他系統或服務。 若要設定此存取權，您可以建立整合服務環境（ISE），您可以在其中建立和執行邏輯應用程式。 ISE 是 Logic Apps 服務的私用和隔離實例，會使用專用資源（例如儲存體），並與公用、「全域」、多租使用者 Logic Apps 服務分開執行。 分隔隔離的私用實例和公用全域實例也有助於降低其他 Azure 租使用者對您應用程式效能的影響，也就是所謂的「雜訊鄰近程度」效果。

* [總覽：從 Azure Logic Apps 存取 Azure 虛擬網路資源](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [從 Azure Logic Apps 連線到 Azure 虛擬網路](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>部署、管理和監視邏輯應用程式

您可以透過 Visual Studio、Azure DevOps 或其他任何原始檔控制和自動化建置工具，完整地開發及部署邏輯應用程式。 為了支援將工作流程與相依連線部署在資源範本中的功能，邏輯應用程式使用 Azure 資源部署範本。 Visual Studio 工具會自動產生這些範本，您可以將其簽入原始檔控制以便控制版本。 針對工作流程執行狀態的通知和診斷記錄，Azure Logic Apps 也提供了監視和警示。

### <a name="deploy"></a>部署

* [快速入門：使用 Azure Logic Apps Visual Studio 建立自動化工作、進程和工作流程](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [總覽：自動化邏輯應用程式部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [建立 Azure Resource Manager 範本來自動化 Azure Logic Apps 的部署](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [部署 Azure Logic Apps 的 Azure Resource Manager 範本](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [範例：從 Azure Logic Apps 連接到 Azure 服務匯流排佇列，並使用 Azure Pipelines 在 Azure DevOps 中進行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [範例：從 Azure Logic Apps 連接到 Azure 儲存體帳戶，並使用 Azure Pipelines 在 Azure DevOps 中進行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [範例：為 Azure Logic Apps 設定函式應用程式動作，並在 Azure DevOps 中使用 Azure Pipelines 進行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [範例：從 Azure Logic Apps 連接到整合帳戶，並使用中的 Azure Pipelines 進行部署 Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [範例：使用 Azure Logic Apps 協調 Azure Pipelines](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>管理

* [使用 Visual Studio 管理邏輯應用程式](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [建立和管理 B2B 企業整合的整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [管理 Azure Logic Apps 中的整合服務環境（ISE）](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>監視

* [監視執行狀態、查看觸發程式歷程記錄，以及設定 Azure Logic Apps 的警示](../logic-apps/monitor-logic-apps.md)
* [設定 Azure 監視器記錄和收集診斷資料以進行 Azure Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md)
* [在 Azure Logic Apps 中設定 Azure 監視器記錄並收集 B2B 訊息的診斷資料](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [在 Azure Logic Apps 的 Azure 監視器記錄中，查看並建立用於監視和追蹤的查詢](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>處理內容類型、轉換和轉換

您可以使用 Azure Logic Apps [工作流程定義語言](https://aka.ms/logicappsdocs)中的許多函數，來存取、轉換 (Convert) 及轉換 (Transform) 多種內容類型。 例如，您可以使用 `@json()` 和 `@xml()` 工作流程運算式，在字串、JSON 和 XML 之間進行轉換 (Convert)。 Logic Apps 引擎會保留內容類型，以支援在服務之間以不失真的方式進行內容傳輸的功能。

* [處理 Azure Logic Apps 中的內容類型](../logic-apps/logic-apps-content-type.md)，例如`application/`、 `application/octet-stream`和。`multipart/formdata`
* [在運算式中使用函數來進行 Azure Logic Apps 和電源自動化的參考指南](../logic-apps/workflow-definition-language-functions-reference.md)
* [Azure Logic Apps 的工作流程定義語言結構描述](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>其他整合和功能

Azure Logic Apps 與許多服務（例如 Azure Functions、Azure API 管理、Azure App Service 和自訂 HTTP 端點）整合，例如 REST 和 SOAP。

* [從 Azure Logic Apps 呼叫 Azure Functions](../logic-apps/logic-apps-azure-functions.md)
* [教學課程：使用 Azure Functions 和 Azure 服務匯流排來呼叫或觸發邏輯應用程式](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [教學課程：使用 Azure Logic Apps 和 Azure Functions 建立串流客戶深入解析儀表板](../logic-apps/logic-apps-scenario-social-serverless.md)
* [教學課程：建立與 Azure Logic Apps 和 Azure 認知服務整合的函式，以分析 Twitter 文章情感](../azure-functions/functions-twitter-email.md)
* [教學課程：使用 Power BI 和 Azure Logic Apps 建立 AI 供電的社交儀表板](https://aka.ms/logicappsdemo)
* [教學課程：使用 Azure 事件方格和 Logic Apps 監視虛擬機器變更](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [教學課程：搭配連線 IoT 中樞和信箱的 Azure Logic Apps 進行 IoT 遠端監視和通知](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog：使用 Azure Logic Apps 呼叫 SOAP 服務](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>端對端案例

* [白皮書：使用 Azure 服務 (例如 Logic Apps) 進行端對端案例管理整合](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>客戶案例

了解 Azure Logic Apps 以及其他 Azure 服務和 Microsoft 產品如何協助[這些公司](https://aka.ms/logic-apps-customer-stories)提升其靈活度，並藉由簡化、組織、自動化及協調複雜的程序，專注於其核心業務。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Logic Apps 的連接器](../connectors/apis-list.md)
* 瞭解[使用 Azure Logic Apps 的 B2B 企業整合案例](../logic-apps/logic-apps-enterprise-integration-overview.md)
