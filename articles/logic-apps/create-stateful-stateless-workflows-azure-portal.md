---
title: 在 Azure 入口網站中建立 Logic Apps 預覽工作流程
description: 使用 Azure 入口網站的 Azure Logic Apps 預覽版，建立及執行自動化和整合案例的工作流程。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: d10689937a037469399863395e0190e399334bd3
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96924236"
---
# <a name="create-stateful-and-stateless-workflows-in-the-azure-portal-with-azure-logic-apps-preview"></a>使用 Azure Logic Apps Preview 在 Azure 入口網站中建立具狀態和無狀態工作流程

> [!IMPORTANT]
> 此公開預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

有了 [Azure Logic Apps preview](logic-apps-overview-preview.md)，您可以從新的 **邏輯應用程式 (Preview)** 資源類型，藉由建立及執行邏輯應用程式（包含 Azure 入口網站中的具 [*狀態* 和 *無狀態* 工作流程](logic-apps-overview-preview.md#stateful-stateless)），在應用程式、資料、雲端服務和系統之間建立自動化及整合解決方案。 使用這個新的邏輯應用程式類型，您可以建立多個工作流程，這些工作流程是由重新設計的 Azure Logic Apps 預覽執行時間所提供，可提供可攜性、更佳的效能和彈性，以便在不同的裝載環境中部署和執行，而不只是 Azure，也 若要深入瞭解新的邏輯應用程式類型，請參閱 [Azure Logic Apps 預覽的總覽](logic-apps-overview-preview.md)。

![顯示 Azure 入口網站的螢幕擷取畫面，其中顯示「邏輯應用程式 (預覽) 」資源的工作流程設計工具。](./media/create-stateful-stateless-workflows-azure-portal/azure-portal-logic-apps-overview.png)

在 Azure 入口網站中，您可以從建立新的 **邏輯應用程式 (Preview)** 資源開始。 雖然您也可以 [使用 Azure Logic Apps (Preview) 擴充功能在 Visual Studio Code 中建立專案](create-stateful-stateless-workflows-visual-studio-code.md)，但這兩種方法都能讓您在相同的裝載環境中部署和執行邏輯應用程式。

同時，您仍然可以建立原始的邏輯應用程式類型。 雖然原始和新的邏輯應用程式類型在入口網站中的開發體驗不同，但您的 Azure 訂用帳戶可以包含這兩種類型。 您可以在 Azure 訂用帳戶中查看及存取所有已部署的邏輯應用程式，但應用程式會組織成自己的類別和區段。

本文說明如何使用 **邏輯應用程式 (預覽版)** 資源類型，以及執行下列高階工作，在 Azure 入口網站中建立邏輯應用程式和工作流程：

* 建立新的邏輯應用程式資源，並新增空白的工作流程。

* 新增觸發程式和動作。

* 觸發工作流程執行。

* 查看工作流程的執行歷程記錄。

* 在部署後啟用或開啟 Application Insights。

* 啟用無狀態工作流程的執行歷程記錄。

> [!NOTE]
> 如需目前已知問題的相關資訊，請參閱 [GitHub 中的 Logic Apps 公開預覽的已知問題頁面](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)。

## <a name="prerequisites"></a>Prerequisites

* Azure 帳戶和訂用帳戶。 如果您沒有訂用帳戶，請[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* [Azure 儲存體帳戶](../storage/common/storage-account-overview.md)，因為 **邏輯應用程式 (預覽版)** 資源是由 Azure Functions 所提供，且具有 [類似于函式應用程式的儲存體需求](../azure-functions/storage-considerations.md)。 您可以使用現有的儲存體帳戶，也可以事先或在建立邏輯應用程式時建立儲存體帳戶。

  > [!NOTE]
  > 可設定[狀態的邏輯應用程式](logic-apps-overview-preview.md#stateful-stateless)會執行儲存體交易，例如使用佇列來排程和儲存資料表和 blob 中的工作流程狀態。 這些交易會產生 [Azure 儲存體費用](https://azure.microsoft.com/pricing/details/storage/)。 如需具狀態邏輯應用程式如何將資料儲存在外部儲存體的詳細資訊，請參閱具 [狀態與無狀態](logic-apps-overview-preview.md#stateful-stateless)。

* 若要在本文中建立相同的範例邏輯應用程式，您需要使用 Microsoft 公司或學校帳戶登入的 Office 365 Outlook 電子郵件帳戶。

  如果您選擇使用 Azure Logic Apps 所支援的不同 [電子郵件連接器](/connectors/)，例如 Outlook.com 或 [Gmail](../connectors/connectors-google-data-security-privacy-policy.md)，您仍然可以遵循此範例，一般的整體步驟都相同，但您的使用者介面和選項可能會有一些不同的方式。 例如，如果您使用 Outlook.com 連接器，請使用您的個人 Microsoft 帳戶改為登入。

* 若要測試您在本文中建立的範例邏輯應用程式，您需要可將呼叫傳送至要求觸發程式的工具，這是範例邏輯應用程式的第一個步驟。 如果您沒有這樣的工具，您可以下載、安裝及使用 [Postman](https://www.postman.com/downloads/)。

* 如果您使用支援 [Application Insights](../azure-monitor/app/app-insights-overview.md)的設定來建立邏輯應用程式，您可以選擇性地啟用邏輯應用程式的診斷記錄和追蹤。 當您建立邏輯應用程式時，或在部署之後，您可以這麼做。 您必須有 Application Insights 的實例，但您可以在建立邏輯應用程式時或部署之後， [事先](../azure-monitor/app/create-workspace-resource.md)建立此資源。

## <a name="create-the-logic-app-resource"></a>建立邏輯應用程式資源

1. 使用您的 Azure 帳戶認證登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 入口網站搜尋方塊中，輸入 `logic app preview` ，然後選取 [ **邏輯應用程式] ([預覽])**。

   ![顯示 Azure 入口網站搜尋方塊的螢幕擷取畫面，其中已選取 [邏輯應用程式預覽] 搜尋詞彙和 [邏輯應用程式 (預覽) ] 資源。](./media/create-stateful-stateless-workflows-azure-portal/find-logic-app-resource-template.png)

1. 在 [ **邏輯應用程式 (預覽])** 頁面上，選取 [ **新增**]。

1. 在 [ **建立邏輯應用程式 (預覽)** ] 頁面的 [ **基本** ] 索引標籤上，提供邏輯應用程式的相關資訊。

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **訂用帳戶** | 是 | <*Azure-subscription-name*> | 要用於邏輯應用程式的 Azure 訂用帳戶。 |
   | **資源群組** | 是 | <*Azure-resource-group-name*> | Azure 資源群組，您可以在其中建立邏輯應用程式和相關資源。 此資源名稱在不同區域中必須是唯一的，而且只能包含字母、數位、連字號 (**-**) 、底線 (**_**) 、括弧 (**( # B6**) **.** 和句點 () 。 <p><p>這個範例會建立名為的資源群組 `Fabrikam-Workflows-RG` 。 |
   | **邏輯應用程式名稱** | 是 | <*logic-app-name*> | 用於邏輯應用程式的名稱。 此資源名稱在不同區域中必須是唯一的，而且只能包含字母、數位、連字號 (**-**) 、底線 (**_**) 、括弧 (**( # B6**) **.** 和句點 () 。 <p><p>這個範例會建立名為的邏輯應用程式 `Fabrikam-Workflows` 。 <p><p>**注意**：您的邏輯應用程式名稱會自動取得後置詞， `.azurewebsites.net` 因為 **邏輯應用程式 (Preview)** 資源是由 Azure Functions 所支援，其使用相同的應用程式命名慣例。 |
   | **發佈** | 是 | <*部署-環境*> | 邏輯應用程式的部署目的地。 您可以選取 [ **工作流程** ] 或 Docker 容器來部署到 Azure。 <p><p>此範例會使用 **工作流程**，也就是在 Azure 中 **(預覽版) 資源的邏輯應用程式** 。 <p><p>如果您選取 [ **Docker 容器**]，請 [在邏輯應用程式的設定中指定要使用的容器](#set-docker-container)。 |
   | **區域** | 是 | <*Azure-region*> | 建立資源群組和資源時要使用的 Azure 區域。 <p><p>此範例會使用「美國西部」。 |
   |||||

   以下是範例：

   ![顯示 Azure 入口網站和 [建立邏輯應用程式 (預覽) ] 頁面的螢幕擷取畫面。](./media/create-stateful-stateless-workflows-azure-portal/create-logic-app-resource-portal.png)

1. 接下來，在 [ **主機** ] 索引標籤上，提供有關要用於邏輯應用程式之儲存體解決方案和主控方案的資訊。

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **儲存體帳戶** | 是 | <*Azure-storage-account-name*> | 用於儲存體交易的 [Azure 儲存體帳戶](../storage/common/storage-account-overview.md) 。 此資源名稱在不同區域中必須是唯一的，且只有數位和小寫字母的3-24 個字元。 請選取現有的帳戶或建立新的帳戶。 <p><p>這個範例會建立名為的儲存體帳戶 `fabrikamstorageacct` 。 |
   | **方案類型** | 是 | <*Azure-裝載方案*> | 用來部署邏輯應用程式的 [主控方案](../app-service/overview-hosting-plans.md) ，也就是 [**Premium**](../azure-functions/functions-scale.md#premium-plan) 或 [**app service 方案**](../azure-functions/functions-scale.md#app-service-plan)。 您的選擇會影響您稍後可以選擇的定價層。 <p><p>此範例會使用 **App service 方案**。 <p><p>**注意**：類似于 Azure Functions， **邏輯應用程式 (預覽版)** 資源類型需要主控方案和定價層。 此資源類型不支援或無法使用耗用量主控方案。 如需詳細資訊，請參閱下列主題： <p><p>- [Azure Functions 規模和裝載](../azure-functions/functions-scale.md) <br>- [App Service 定價詳細資料](https://azure.microsoft.com/pricing/details/app-service/) <p><p> |
   | **Windows 方案** | 是 | <*方案名稱*> | 要使用的計畫名稱。 請選取現有的方案，或提供新方案的名稱。 <p><p>此範例使用名稱 `Fabrikam-Service-Plan`。 |
   | **SKU 和大小** | 是 | <*定價層*> | 用來裝載邏輯應用程式的 [定價層](../app-service/overview-hosting-plans.md) 。 您的選擇會受到您先前選擇的方案類型所影響。 若要變更預設層，請選取 [ **變更大小**]。 然後，您可以根據所需的工作負載，選取其他定價層。 <p><p>此範例會針對 **開發/測試** 工作負載使用免費的 **F1 定價層**。 如需詳細資訊，請參閱 [App Service 定價詳細資料](https://azure.microsoft.com/pricing/details/app-service/)。 |
   |||||

1. 接下來，如果您的建立和部署設定支援使用 [Application Insights](../azure-monitor/app/app-insights-overview.md)，您可以選擇性地為您的邏輯應用程式啟用診斷記錄和追蹤。

   1. 在 [ **監視** ] 索引標籤的 [ **Application Insights**] 下，將 [ **啟用 Application Insights** ] 設定為 **[是]** 。

   1. 針對 **Application Insights** 設定，請選取現有的 Application Insights 實例，或是如果您想要建立新的實例，請選取 [ **建立新** 的]，並提供您要使用的名稱。

1. 在 Azure 驗證邏輯應用程式的設定之後，請在 [ **審核 + 建立** ] 索引標籤上選取 [ **建立**]。

   例如：

   ![顯示 Azure 入口網站及新邏輯應用程式資源設定的螢幕擷取畫面。](./media/create-stateful-stateless-workflows-azure-portal/check-logic-app-resource-settings.png)

   Azure 完成部署之後，您的邏輯應用程式會自動上線且正在執行，但因為沒有工作流程存在，所以不會執行任何動作。

1. 在 [部署完成] 頁面上，選取 [ **移至資源** ]，讓您可以開始建立您的工作流程。

   ![顯示 Azure 入口網站和已完成部署的螢幕擷取畫面。](./media/create-stateful-stateless-workflows-azure-portal/logic-app-completed-deployment.png)

<a name="set-docker-container"></a>

## <a name="specify-docker-container-for-deployment"></a>指定部署的 Docker 容器

如果您在建立邏輯應用程式時選取了 **Docker 容器** ，請確定您在 Azure 入口網站建立 **邏輯應用程式 (預覽版)** 資源之後，提供要用於部署之容器的相關資訊。

1. 在 Azure 入口網站中，移至您的邏輯應用程式資源。

1. 在邏輯應用程式功能表的 [ **設定**] 底下，選取 [ **容器設定**]。 提供 Docker 容器映射的詳細資料和位置。

   ![顯示邏輯應用程式功能表的螢幕擷取畫面，其中已選取 [容器設定]。](./media/create-stateful-stateless-workflows-azure-portal/logic-app-deploy-container-settings.png)

1. 當您完成時，請儲存您的設定。

<a name="add-workflow"></a>

## <a name="add-a-blank-workflow"></a>新增空白工作流程

1. 在 Azure 開啟資源之後，請在邏輯應用程式的功能表上選取 [ **工作流程**]。 在 [ **工作流程** ] 工具列上，選取 [ **加入**]。

   ![顯示邏輯應用程式資源功能表的螢幕擷取畫面，其中已選取 [工作流程]，然後在工具列上抽選取 [新增]。](./media/create-stateful-stateless-workflows-azure-portal/logic-app-add-blank-workflow.png)

1. 在 [ **新增工作流程** ] 窗格開啟之後，提供工作流程的名稱，然後選擇具 [**狀態** 或 **無狀態**](logic-apps-overview-preview.md#stateful-stateless) 的工作流程類型。 當您完成時，選取 [建立]  。

   這個範例會加入名為的空白具狀態工作流程 `Fabrikam-Stateful-Workflow` 。 預設會啟用工作流程，但在您新增觸發程式和動作之前，並不會執行任何動作。

   ![顯示新加入的空白具狀態工作流程「Fabrikam-具狀態工作流程」的螢幕擷取畫面。](./media/create-stateful-stateless-workflows-azure-portal/logic-app-blank-workflow-created.png)

1. 接下來，在設計工具中開啟空白工作流程，讓您可以新增觸發程式和動作。

   1. 從 [工作流程] 清單中選取空白工作流程。

   1. 在 [工作流程] 功能表的 [ **開發人員**] 底下，選取 [ **設計** 工具]。

      在設計工具介面上，[ **選擇** 作業提示] 已出現，且預設為選取狀態，讓 [ **新增觸發** 程式] 窗格也會出現 [開啟]。

      ![顯示開啟的工作流程設計工具的螢幕擷取畫面，其中已選取設計工具介面上的 [選擇作業]。](./media/create-stateful-stateless-workflows-azure-portal/opened-logic-app-designer-blank-workflow.png)

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-an-action"></a>新增觸發程式和動作

這個範例會建立具有下列步驟的工作流程：

* 內建的 [要求觸發](../connectors/connectors-native-reqres.md)**程式會在收到 HTTP 要求時** 接收傳入的呼叫或要求，並建立其他服務或邏輯應用程式可以呼叫的端點。

* [Office 365 Outlook 動作](../connectors/connectors-create-api-office365-outlook.md)，**傳送電子郵件**。

* 內建的 [回應動作](../connectors/connectors-native-reqres.md)，您可以用來傳送回復，並將資料傳回給呼叫端。

### <a name="add-the-request-trigger"></a>新增「要求」觸發程序

在您可以將觸發程式加入空白工作流程之前，請確定工作流程設計工具已開啟，並已在設計工具介面上選取 [ **選擇操作** 提示]。

1. 在設計工具介面旁的 [ **新增觸發** 程式] 窗格中，于 [ **選擇** 作業搜尋] 方塊下，檢查是否已選取 [ **內建** ] 索引標籤。 此索引標籤會顯示 Azure Logic Apps 中以原生方式執行的觸發程式。

1. 在 [ **選擇** 作業搜尋] 方塊中，輸入 `when a http request` ，然後選取在 **收到 HTTP 要求時** 所命名的內建要求觸發程式。

   ![顯示設計工具和 [新增觸發程式] 窗格的螢幕擷取畫面，其中已選取 [收到 HTTP 要求時] 觸發程式。](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger.png)

   當觸發程式出現在設計工具上時，觸發程式的詳細資料窗格會開啟，以顯示觸發程式的屬性、設定和其他動作。

   ![顯示 [收到 HTTP 要求時] 觸發程式的螢幕擷取畫面，其中已選取 [觸發程式詳細資料] 窗格開啟。](./media/create-stateful-stateless-workflows-azure-portal/request-trigger-added-to-designer.png)

   > [!TIP]
   > 如果 [詳細資料] 窗格未出現，請確定已在設計工具上選取觸發程式。

1. 如果您需要從設計工具中刪除專案，請 [遵循下列步驟，從設計工具中刪除專案](#delete-from-designer)。

1. 若要儲存您的工作，請在設計工具工具列上選取 [ **儲存**]。

   當您第一次儲存工作流程，且該工作流程從要求觸發程式開始時，Logic Apps 服務會自動為要求觸發程式所建立的端點產生 URL。 稍後，當您測試工作流程時，您會將要求傳送至此 URL，此 URL 會引發觸發程式並啟動工作流程執行。

### <a name="add-the-office-365-outlook-action"></a>新增 Office 365 Outlook 動作

1. 在設計工具中，于您加入的觸發程式底下，選取 [ **新增步驟**]。

   [ **選擇** 作業提示] 會出現在設計工具上，然後重新開啟 [ **新增動作** ] 窗格，讓您可以選取下一個動作。

   > [!NOTE]
   > 如果 [ **新增動作** ] 窗格顯示錯誤訊息「無法讀取未定義的屬性 ' filter '」，請儲存您的工作流程、重載頁面、重新開啟您的工作流程，然後再試一次。

1. 在 [ **新增動作** ] 窗格的 [ **選擇** 作業搜尋] 方塊底下，選取 [ **Azure**]。 此索引標籤會顯示 Azure 中可用和部署的受控連接器。

   > [!NOTE]
   > 如果 [ **新增動作** ] 窗格顯示錯誤訊息，請 `The access token expiry UTC time '{token-expiration-date-time}' is earlier than current UTC time '{current-date-time}'` 儲存您的工作流程、重載頁面、重新開啟您的工作流程，然後再次嘗試新增動作。

   此範例會使用名為 [ **傳送電子郵件 (V2)** 的 Office 365 Outlook 動作。

   ![顯示設計工具和 [新增動作] 窗格的螢幕擷取畫面，其中已選取 Office 365 Outlook 的 [傳送電子郵件] 動作。](./media/create-stateful-stateless-workflows-azure-portal/find-send-email-action.png)

1. 在動作的詳細資料窗格中，選取 [ **建立** 連線] 索引標籤上的 [登入]，讓您能夠建立電子郵件帳戶的 **連線** 。

   ![顯示設計工具和 [傳送電子郵件 (V2) ] 詳細資料窗格的螢幕擷取畫面，其中已選取 [登入]。](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-sign-in.png)

1. 當系統提示您同意存取您的電子郵件帳戶時，請使用您的帳號憑證登入。

   > [!NOTE]
   > 如果您收到錯誤訊息， `Failed with error: 'The browser is closed.'. Please sign in again` 請檢查您的瀏覽器是否封鎖協力廠商 cookie。 如果這些 cookie 遭到封鎖，請嘗試新增 `https://portal.azure.com` 至可使用 cookie 的網站清單。 如果您使用的是 incognito 模式，請確定協力廠商 cookie 在使用該模式時不會遭到封鎖。
   > 
   > 如有必要，請重載頁面、開啟您的工作流程、再次新增電子郵件動作，然後嘗試建立連接。

   在 Azure 建立連接之後，[ **傳送電子郵件** ] 動作會出現在設計工具上，並預設為選取。 如果未選取該動作，請選取該動作，如此一來，也會開啟其 [詳細資料] 窗格。

1. 在動作的詳細資料窗格中，于 [ **參數** ] 索引標籤上提供動作所需的資訊，例如：

   ![顯示設計工具和 [傳送電子郵件] 詳細資料窗格的螢幕擷取畫面，其中已選取 [參數] 索引標籤。](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-details.png)

   | 屬性 | 必要 | 值 | 描述 |
   |----------|----------|-------|-------------|
   | **若要** | 是 | <your-email-address> | 電子郵件收件者，可以是您的電子郵件地址以供測試之用。 此範例使用虛構的電子郵件 `sophiaowen@fabrikam.com` 。 |
   | **主旨** | 是 | `An email from your example workflow` | 電子郵件主旨 |
   | **本文** | 是 | `Hello from your example workflow!` | 電子郵件內文內容 |
   ||||

   > [!NOTE]
   > 在 [ **設定**]、[ **靜態結果**] 或 [在索引標籤 **後執行** ] 的詳細資料窗格中進行任何變更時，請務必選取 [ **完成** ] 來認可這些變更，然後再切換索引標籤或將焦點變更至設計工具。 否則，設計工具將不會保留您的變更。

1. 儲存您的工作。 在設計工具的工具列上，選取 [儲存]  。

接下來，若要測試您的工作流程，請手動觸發執行。

## <a name="trigger-the-workflow"></a>觸發工作流程

在此範例中，工作流程會在要求觸發程式收到輸入要求時執行，此要求會傳送至觸發程式所建立的端點 URL。 當您第一次儲存工作流程時，Logic Apps 服務會自動產生此 URL。 因此，在您可以傳送此要求以觸發工作流程之前，您必須先尋找此 URL。

1. 在工作流程設計工具中，選取 **收到 HTTP 要求時** 所命名的要求觸發程式。

1. 在 [詳細資料] 窗格開啟之後，在 [ **參數** ] 索引標籤上尋找 [ **HTTP POST URL** ] 屬性。 若要複製產生的 URL，請選取 [ **複製 url** ] (複製檔案] 圖示) ，然後將 url 儲存到目前的其他位置。 URL 的格式如下：

   `http://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

   ![顯示 [HTTP POST URL] 屬性中的 [要求觸發程式] 和 [端點 URL] 設計工具的螢幕擷取畫面。](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger-url.png)

   在此範例中，URL 看起來像這樣：

   `https://fabrikam-workflows.azurewebsites.net:443/api/Fabrikam-Stateful-Workflow/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxxxxXXXXxxxxxXXXXxxxXXXXxxxxXXXX`

   > [!TIP]
   > 您也可以在邏輯應用程式的 [ **總覽** ] 窗格中，于 [ **工作流程 URL** ] 屬性中找到端點 URL。
   >
   > 1. 在 [資源] 功能表上，選取 **[總覽**]。
   > 1. 在 [ **總覽** ] 窗格中，尋找 [ **工作流程 URL** ] 屬性。
   > 1. 若要複製端點 URL，請將指標移到端點 URL 文字的結尾，然後選取 [ **複製到剪貼** 簿]， (複製檔案] 圖示) 。

1. 若要藉由傳送要求來測試 URL，請開啟 [Postman](https://www.postman.com/downloads/) 或您慣用的工具來建立和傳送要求。

   此範例會使用 Postman 繼續進行。 如需詳細資訊，請參閱 [Postman 消費者入門](https://learning.postman.com/docs/getting-started/introduction/)。

   1. 在 [Postman] 工具列上，選取 [ **新增**]。

      ![顯示已選取 [新增] 按鈕之 Postman 的螢幕擷取畫面](./media/create-stateful-stateless-workflows-azure-portal/postman-create-request.png)

   1. 在 [建立 **新** 的] 窗格的 [ **建立區塊**] 下，選取 [ **要求**]。

   1. 在 [ **儲存要求** ] 視窗的 [ **要求名稱**] 底下，提供要求的名稱，例如 `Test workflow trigger` 。

   1. 在 [ **選取要儲存的集合或資料夾**] 底下，選取 [ **建立集合**]。

   1. 在 [**所有集合**] 下，為要建立的集合提供用來組織要求的名稱，按 enter，然後選取 [**儲存到 <*的集合名稱* >**。 這個範例會使用 `Logic Apps requests` 做為集合名稱。

      Postman 的 [要求] 窗格隨即開啟，讓您可以將要求傳送至要求觸發程式的端點 URL。

      ![顯示 Postman 與開啟的要求窗格的螢幕擷取畫面](./media/create-stateful-stateless-workflows-azure-portal/postman-request-pane.png)

   1. 在 [要求] 窗格的 [方法] 清單旁的 [位址] 方塊中，目前顯示 [ **取得** ] 作為預設要求方法，貼上您先前複製的 URL，然後選取 [ **傳送**]。

      ![在已選取 [傳送] 按鈕的 [位址] 方塊中顯示 Postman 和端點 URL 的螢幕擷取畫面](./media/create-stateful-stateless-workflows-azure-portal/postman-test-endpoint-url.png)

      當觸發程式引發時，範例工作流程會執行並傳送類似此範例的電子郵件：

      ![顯示 Outlook 電子郵件的螢幕擷取畫面，如範例中所述](./media/create-stateful-stateless-workflows-azure-portal/workflow-app-result-email.png)

## <a name="review-run-history"></a>檢閱執行歷程記錄

針對可設定狀態的工作流程，在每個工作流程執行之後，您可以查看執行歷程記錄，包括整體執行的狀態、觸發程式，以及每個動作及其輸入和輸出。

1. 在 [Azure 入口網站] 中，選取工作流程功能表上的 [ **監視**]。

   [ **監視** ] 窗格會顯示該工作流程的執行歷程記錄。

   ![顯示工作流程的 [監視] 窗格和執行歷程記錄的螢幕擷取畫面。](./media/create-stateful-stateless-workflows-azure-portal/find-run-history.png)

   > [!TIP]
   > 如果最近的執行狀態未出現，請在 [ **監視** ] 窗格工具列上 **，選取 [** 重新整理]。 由於不符準則或找不到任何資料，而略過的觸發程式不會執行。

   | 執行狀態 | 描述 |
   |------------|-------------|
   | **已中止** | 執行已停止或未完成，因為發生外部問題，例如系統中斷或已過期的 Azure 訂用帳戶。 |
   | **取消** | 已觸發並啟動執行，但收到取消要求。 |
   | **失敗** | 執行中至少有一個動作失敗。 未設定工作流程中的後續動作來處理失敗。 |
   | **執行中** | 執行已觸發且正在進行中，但此狀態也可能會因為 [動作限制](logic-apps-limits-and-config.md) 或 [目前的定價方案](https://azure.microsoft.com/pricing/details/logic-apps/)而受到節流的執行。 <p><p>**秘訣**：如果您設定 [診斷記錄](monitor-logic-apps-log-analytics.md)，您可以取得任何發生之節流事件的相關資訊。 |
   | **已成功** | 執行成功。 如果任何動作失敗，工作流程中的後續動作會處理該失敗。 |
   | **逾時** | 執行時間已過，因為目前的持續時間超過執行持續時間限制，此限制是由 [ [**執行歷程記錄保留天數** ] 設定](logic-apps-limits-and-config.md#run-duration-retention-limits)所控制。 執行時間的計算方式是使用執行時間的開始時間和該開始時間的執行時間限制。 <p><p>**注意**：如果執行的持續時間也超過目前的 *執行歷程記錄保留限制*，也就是由 [ [**執行歷程記錄保留天數** ] 設定](logic-apps-limits-and-config.md#run-duration-retention-limits)所控制，則會從 [執行歷程記錄] 依每日清除作業來清除執行。 執行時間是否已用盡或完成時，一律會使用執行的開始時間和 *目前* 的保留限制來計算保留期限。 因此，如果您減少執行中執行的持續時間限制，則執行時間會超出。不過，執行會根據執行的持續時間是否超過保留限制，從執行歷程記錄中保留或清除。 |
   | **等候** | 因為先前仍在執行的工作流程實例，所以執行尚未啟動或暫停。 |
   |||

1. 若要檢查執行中每個步驟的狀態，請選取您要檢查的執行。

   [執行詳細資料] 視圖隨即開啟，並顯示執行中每個步驟的狀態。

   ![顯示執行詳細資料檢視的螢幕擷取畫面，其中包含工作流程中每個步驟的狀態。](./media/create-stateful-stateless-workflows-azure-portal/review-run-details.png)

   以下是工作流程中每個步驟可能具有的狀態：

   | 動作狀態 | 圖示 | 描述 |
   |---------------|------|-------------|
   | 已中止 | ![「已中止」動作狀態的圖示][aborted-icon] | 動作已停止或未完成，因為發生外部問題，例如系統中斷或已過期的 Azure 訂用帳戶。 |
   | 已取消 | ![「已取消」動作狀態的圖示][cancelled-icon] | 動作正在執行，但收到取消要求。 |
   | Failed | ![「失敗」動作狀態的圖示][failed-icon] | 動作失敗。 |
   | 執行中 | ![「正在執行」動作狀態的圖示][running-icon] | 動作目前正在執行。 |
   | 已略過 | ![「略過」動作狀態的圖示][skipped-icon] | 因為前一個動作失敗，所以已略過動作。 動作的 `runAfter` 條件需要先前的動作成功完成，才能執行目前的動作。 |
   | 成功 | ![「成功」動作狀態的圖示][succeeded-icon] | 動作成功。 |
   | 成功但重試 | ![「已成功重試」動作狀態的圖示][succeeded-with-retries-icon] | 動作已成功，但只在一或多次重試後才會成功。 若要檢查重試歷程記錄，請在 [執行歷程記錄詳細資料] 視圖中選取該動作，讓您可以查看輸入和輸出。 |
   | 逾時 | ![「超時」動作狀態的圖示][timed-out-icon] | 因為該動作的設定所指定的超時限制，所以已停止動作。 |
   | 等候 | ![「等待中」動作狀態的圖示][waiting-icon] | 適用于正在等候呼叫端輸入要求的 webhook 動作。 |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-azure-portal/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-azure-portal/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-azure-portal/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-azure-portal/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-azure-portal/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-azure-portal/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-azure-portal/waiting.png

1. 若要檢查特定步驟的輸入和輸出，請選取該步驟。

   ![顯示所選 [傳送電子郵件] 動作中之輸入和輸出的螢幕擷取畫面。](./media/create-stateful-stateless-workflows-azure-portal/review-step-inputs-outputs.png)

1. 若要進一步檢查該步驟的原始輸入和輸出，請選取 [ **顯示原始輸入** ] 或 [ **顯示原始輸出**]。

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>在部署後啟用或開啟 Application Insights

在工作流程執行期間，您的邏輯應用程式會發出遙測和其他事件。 您可以使用此遙測來更清楚地瞭解工作流程的執行狀況，以及 Logic Apps 執行時間如何以各種方式運作。 您可以使用 [Application Insights](../azure-monitor/app/app-insights-overview.md)來監視您的工作流程，以提供近乎即時的遙測 (即時計量) 。 當您使用此資料來診斷問題、設定警示和建立圖表時，這項功能可協助您更輕鬆地調查失敗和效能問題。

如果您的邏輯應用程式建立和部署設定支援使用 [Application Insights](../azure-monitor/app/app-insights-overview.md)，您可以選擇性地為您的邏輯應用程式啟用診斷記錄和追蹤。 當您在 Azure 入口網站中建立邏輯應用程式，或在部署之後建立邏輯應用程式時，可以這麼做。 您必須有 Application Insights 的實例，但您可以在建立邏輯應用程式時或部署之後， [事先](../azure-monitor/app/create-workspace-resource.md)建立此資源。

若要在已部署的邏輯應用程式上啟用 Application Insights 或開啟 Application Insights 儀表板（如果已啟用），請遵循下列步驟：

1. 在 Azure 入口網站中，尋找您已部署的邏輯應用程式。

1. 在邏輯應用程式功能表的 [ **設定**] 底下，選取 [ **Application Insights**]。

1. 如果未啟用 Application Insights，請在 [ **Application Insights** ] 窗格中選取 [ **開啟 Application Insights**。 更新窗格之後， **請選取底部** 的 [套用]。

   如果 Application Insights 已啟用，請在 [ **Application Insights** ] 窗格中，選取 [ **View Application Insights data**]。

Application Insights 開啟之後，您就可以檢查邏輯應用程式的各種計量。

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>啟用無狀態工作流程的執行歷程記錄

若要更輕鬆地進行無狀態工作流程的偵錯工具，您可以啟用該工作流程的執行歷程記錄，然後在完成時停用執行歷程記錄。 針對 Azure 入口網站遵循這些步驟，或者如果您是在 Visual Studio Code 中工作，請參閱 [Visual Studio Code 中建立具狀態和無狀態工作流程](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless)。

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並開啟您的 **邏輯應用程式 (Preview)** 資源。

1. 在邏輯應用程式的功能表上 **，選取 [** 設定] 下的 [**設定**]。

1. 在 [ **應用程式設定** ] 索引標籤上，選取 [ **新增應用程式設定**]。

1. 在 [ **新增/編輯應用程式設定** ] 窗格的 [ **名稱** ] 方塊中，輸入此作業選項名稱： 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. 在 [ **值** ] 方塊中，輸入下列值： `WithStatelessRunHistory`

   例如：

   ![顯示 Azure 入口網站和邏輯應用程式 (預覽) 資源的螢幕擷取畫面，其中會開啟 [設定] > [新增應用程式設定] < [新增/編輯應用程式設定] 窗格開啟和 [工作流程]。{yourWorkflowName}.OperationOptions "選項設定為" WithStatelessRunHistory "。](./media/create-stateful-stateless-workflows-azure-portal/stateless-operation-options-run-history.png)

1. 若要完成這項工作，請選取 **[確定]**。 **在 [設定**] 窗格工具列上，選取 [**儲存**]。

1. 若要在完成時停用執行歷程記錄，請將 `Workflows.{yourWorkflowName}.OperationOptions` 屬性設定為 `None` ，或刪除屬性及其值。

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>從設計工具中刪除專案

若要從設計工具中刪除工作流程中的專案，請遵循下列其中一項步驟：

* 選取專案，開啟專案的快捷方式功能表 (Shift + F10) ，然後選取 [ **刪除**]。 請選取 [確定] 確認。

* 選取專案，然後按下 delete 鍵。 請選取 [確定] 確認。

* 選取專案，讓該專案的 [詳細資料] 窗格開啟。 在窗格的右上角，開啟省略號 (**...**) ] 功能表，然後選取 [ **刪除**]。 請選取 [確定] 確認。

  ![螢幕擷取畫面，其中顯示已開啟的 [詳細資料] 窗格以及 [選取的省略號] 按鈕和 [刪除] 命令在設計工具上選取的專案。](./media/create-stateful-stateless-workflows-azure-portal/delete-item-from-designer.png)

  > [!TIP]
  > 如果看不到省略號功能表，請將瀏覽器視窗的寬度展開至足夠的範圍，讓詳細資料窗格顯示右上角的省略號 (**...**) 按鈕。

<a name="troubleshoot"></a>

## <a name="troubleshoot-problems-and-errors"></a>針對問題和錯誤進行疑難排解

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>設計工具選擇器針對先前建立的工作流程遺漏新的觸發程式和動作

Azure Logic Apps Preview 支援 Azure Function 作業、液體作業和 XML 作業的內建動作，例如 **Xml 驗證** 和 **轉換 xml**。 不過，針對先前建立的邏輯應用程式，這些動作可能不會出現在設計工具中，讓您選取邏輯應用程式是否使用延伸模組套件組合的過期版本 `Microsoft.Azure.Functions.ExtensionBundle.Workflows` 。

若要修正此問題，請遵循下列步驟來刪除過期的版本，讓擴充功能配套可以自動更新為最新版本。

> [!NOTE]
> 此特定解決方案只適用于您使用 Azure 入口網站建立的 **邏輯應用程式 (預覽版)** 資源，而不是使用 Visual Studio Code 和 Azure Logic Apps (Preview) 延伸模組所建立和部署的邏輯應用程式。 請參閱 [Visual Studio Code 中的設計工具缺少支援的觸發程式和動作](create-stateful-stateless-workflows-visual-studio-code.md#missing-triggers-actions)。

1. 在 Azure 入口網站中，停止您的邏輯應用程式。

   1. 在邏輯應用程式功能表上，選取 [概觀]  。

   1. 在 [ **總覽** ] 窗格的工具列上，選取 [ **停止**]。

1. 在邏輯應用程式功能表的 [ **開發工具**] 底下，選取 [ **Advanced tools**]。

1. 在 [ **Advanced Tools** ] 窗格中，選取 [ **Go**]，這會開啟邏輯應用程式的 Kudu 環境。

1. 在 [Kudu] 工具列上，開啟 [ **Debug 主控台** ] 功能表，然後選取 [ **CMD**]。 

   主控台視窗隨即開啟，讓您可以使用命令提示字元流覽至組合資料夾。 或者，您可以流覽出現在主控台視窗中的目錄結構。

1. 流覽至下列資料夾，其中包含現有套件組合的已設定版本資料夾：

   `...\home\data\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. 刪除現有套件組合的版本資料夾。 在主控台視窗中，您可以執行此命令，將取代為 `{bundle-version}` 現有的版本：

   `rm -rf {bundle-version}`

   例如：`rm -rf 1.1.3`

   > [!TIP]
   > 如果您收到「許可權被拒」或「使用中的檔案」之類的錯誤，請重新整理瀏覽器中的頁面，然後再次嘗試上述步驟，直到刪除資料夾為止。

1. 在 Azure 入口網站中，返回邏輯應用程式的 **[總覽** ] 頁面，然後選取 [ **重新開機**]。

   入口網站會自動取得並使用最新的組合。

## <a name="next-steps"></a>後續步驟

我們想要聽取您對於此公開預覽體驗的意見！

* 針對 bug 或問題，請 [在 GitHub 中建立您的問題](https://github.com/Azure/logicapps/issues)。
* 針對問題、要求、批註和其他意見反應，請 [使用此意見反應表單](https://aka.ms/lafeedback)。
