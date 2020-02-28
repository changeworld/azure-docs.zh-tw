---
title: 使用 Microsoft Flow 自動化 Azure Application Insights 程序
description: 了解如何利用 Application Insights Connector，使用 Microsoft Flow 來快速自動執行可重複的程序。
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 7566ae87f92707180b09d50eb6e5eeccedae85b9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77655086"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>使用適用於 Microsoft Flow 的連接器自動執行 Azure Application Insights 程序

您是否發現自己在遙測資料上不斷重複地執行相同查詢，以檢查服務是否正常運作？ 想要將此類查詢自動化以尋找趨勢和異常，然後針對它們建立您自己的工作流程嗎？ 適用于 Microsoft Flow 的 Azure 應用程式 Insights connector 是適合這些用途的工具。

有了此整合，您就能立即自動執行許多流程，而不需撰寫任何一行程式碼。 在您使用 Application Insights 動作建立流程後，此流程會自動執行 Application Insights Analytics 查詢。

您也可以新增額外的動作。 Microsoft Flow 提供數百個動作。 例如，您可以使用 Microsoft Flow 自動傳送電子郵件通知，或是在 Azure DevOps 中建立 Bug。 您也可以使用提供給適用於 Microsoft Flow 之連接器的任何一種[範本](https://ms.flow.microsoft.com/connectors/shared_applicationinsights/?slug=azure-application-insights)。 這些範本可加快建立流程的程序。

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). -->

## <a name="create-a-flow-for-application-insights"></a>建立 Application Insights 的流程

在本教學課程中，您將瞭解如何建立流量分析 autocluster 演算法的流程，以將 web 應用程式資料中的屬性分組。 此流程會以電子郵件自動傳送結果，這只是如何將 Microsoft Flow 和 Application Insights Analytics 一起使用的其中一個範例。

### <a name="step-1-create-a-flow"></a>步驟 1：建立流程

1. 登入 [Microsoft Flow](https://flow.microsoft.com)，然後選取 [我的流程]。
2. 按一下 [**新增**]、[已**排程]-從空白**。

    ![從排程的空白建立新流程](./media/automate-with-flow/1-create.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>步驟 2：建立流程的觸發程序

1. 在 [**建立排程的流程**] 快顯視窗中，填寫您的流程名稱，以及您希望流程執行的頻率。

    ![以輸入頻率和間隔設定排程週期](./media/automate-with-flow/2-schedule.png)

1. 按一下 [建立]。

### <a name="step-3-add-an-application-insights-action"></a>步驟 3：新增 Application Insights 動作

1. 搜尋**Application Insights**。
2. 按一下 [ **Azure 應用程式 Insights-視覺化分析查詢**]。

    ![選擇動作： Azure 應用程式 Insights 視覺化分析查詢](./media/automate-with-flow/3-visualize.png)

3. 選取 [新增步驟]。

### <a name="step-4-connect-to-an-application-insights-resource"></a>步驟 4：連線到 Application Insights 資源

若要完成此步驟，您需要資源的應用程式識別碼和 API 金鑰。 您可以從 Azure 入口網站擷取這些資訊，如下圖所示：

![Azure 入口網站中的應用程式識別碼](./media/automate-with-flow/5apiaccess.png)

![Azure 入口網站中的 API 金鑰](./media/automate-with-flow/6apikey.png)

為您的連線提供名稱，以及應用程式識別碼和 API 金鑰。

   ![Microsoft Flow 連線視窗](./media/automate-with-flow/4-connection.png)

如果 [連接] 方塊不會立即顯示，而是直接進入查詢，請按一下方塊右上方的省略號。 然後選取 [我的連線] 或使用現有的連接。

按一下 [建立]。

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>步驟 5：指定 Analytics 查詢和圖表類型
此範例查詢會選取最後一天內的失敗要求，並將它們與作業發生的例外狀況相互關聯。 Analytics 會根據 operation_Id 識別碼使其相互關聯。 查詢接著會使用自動叢集演算法將結果分段。

建立自己的查詢時，先確認它們可在 Analytics 中正常運作，再將其新增到您的流程中。

- 新增下列分析查詢，然後選取 [HTML 表格] 圖表類型。 然後選取 [**新增步驟**]。

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```
    
    ![Analytics 查詢設定畫面](./media/automate-with-flow/5-query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>步驟 6：設定傳送電子郵件的流程

1. 搜尋 **Office 365 Outlook**。
2. 按一下 [Office 365 Outlook - 傳送電子郵件]。

    ![Office 365 Outlook 選取視窗](./media/automate-with-flow/6-outlook.png)

1. 在 [**傳送電子郵件**] 視窗中：

   a. 輸入收件者的電子郵件地址。

   b. 輸入電子郵件的主旨。

   c. 按一下 [內文] 方塊中的任意處，然後在右方開啟的動態內容功能表上，選取 [內文]。

   e. 選取 [**顯示高級選項**]

1. 在 [動態內容] 功能表上：

    a. 選取 [附件名稱]。

    b. 選取 [附件內容]。
    
    c. 在 [為 HTML] 方塊中選取 [是]。

    ![Office 365 Outlook 設定](./media/automate-with-flow/7-email.png)

### <a name="step-7-save-and-test-your-flow"></a>步驟 7：儲存並測試流程

Haga clic en **Guardar**.

您可以等候觸發程式執行此動作，或按一下頂端的 [![燒杯測試 圖示](./media/automate-with-flow/testicon.png)**測試**]。

選取 [**測試**] 之後：

1. 選取 **[我將執行觸發程式動作**]。
2. 選取 [**執行流程**]。

當流程執行時，您在電子郵件清單中指定的收件者會收到如下所示的電子郵件訊息。

![範例電子郵件](./media/automate-with-flow/flow9.png)

## <a name="next-steps"></a>後續步驟

- 深入了解建立 [Analytics 查詢](../../azure-monitor/log-query/get-started-queries.md)。
- 深入了解 [Microsoft Flow](https://ms.flow.microsoft.com)。

<!--Link references-->
