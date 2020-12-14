---
title: Log Analytics 中的 IT 服務管理連接器
description: 本文提供 IT 服務管理連接器 (ITSMC) 的總覽，以及如何使用它來監視和管理 Log Analytics 中的 ITSM 工作專案並快速解決問題的相關資訊。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 6c9e2ae420e56c5ef99ff79cdcb49592bc7e049e
ms.sourcegitcommit: 287c20509c4cf21d20eea4619bbef0746a5cd46e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97371980"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>使用 IT 服務管理連接器將 Azure 連接至 ITSM 工具

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

IT 服務管理連接器 (ITSMC) 可讓您將 Azure 連線到支援的 IT 服務管理 (ITSM) 產品或服務。

Azure 服務（例如 Azure Log Analytics 和 Azure 監視器）提供工具來偵測、分析和疑難排解 Azure 和非 Azure 資源的問題。 但是與問題相關的工作專案通常位於 ITSM 產品或服務中。 ITSMC 會提供 Azure 與 ITSM 工具之間的雙向連線，以協助您更快速地解決問題。

ITSMC 支援與下列 ITSM 工具連線：

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> 我們建議 Cherwell 和 Provance 客戶使用 [Webhook 動作](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) ，以 Cherwell 和 Provance 端點作為整合的另一種解決方案。

使用 ITSMC，您可以：

-  根據您的 Azure 警示 (計量警示、活動記錄警示及 Log Analytics 警示) ，在您的 ITSM 工具中建立工作專案。
-  您可以選擇將事件和變更要求資料從您的 ITSM 工具同步處理到 Azure Log Analytics 工作區。

如需法律條款和隱私權原則的詳細資訊，請參閱 [Microsoft 隱私權聲明](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)。

您可以藉由完成下列步驟來開始使用 ITSMC：

1.  [新增 ITSMC。](#add-it-service-management-connector)
2. [將 ITSM 產品/服務與 IT Service Management Connector 連線](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections)
3.  [建立 ITSM 連接。](#create-an-itsm-connection)
4.  [使用連接。](#use-itsmc)
   
   >[!NOTE]
> 您必須依照此順序遵循這些步驟，否則您將會收到錯誤。

##  <a name="add-it-service-management-connector"></a>新增 IT 服務管理連接器

您必須先新增 ITSMC，才能建立連接。

1. 在 [Azure 入口網站中，選取 [ **建立資源**：

   ![顯示 [建立資源] 功能表項目的螢幕擷取畫面。](media/itsmc-overview/azure-add-new-resource.png)

2. 搜尋 Azure Marketplace 中的 **IT 服務管理連接器** 。 選取 [ **建立**]：

   ![顯示 Azure Marketplace 中 [建立] 按鈕的螢幕擷取畫面。](media/itsmc-overview/add-itsmc-solution.png)

3. 在 [ **OMS 工作區** ] 區段中，選取您要安裝 ITSMC 的 Azure Log Analytics 工作區。
   >[!NOTE]
   > * 在從 Microsoft Operations Management Suite (OMS) 到 Azure 監視器的持續轉換過程中，OMS 工作區現在稱為 *Log Analytics 工作區*。
   > * ITSMC 只能安裝在下欄區域的 Log Analytics 工作區中：美國東部、美國西部2、美國中南部、美國中西部、US Gov 亞利桑那州、US Gov 維吉尼亞州、加拿大中部、西歐、英國、東南亞、日本東部、印度中部和澳大利亞東南部。


4. 在 [ **Log Analytics 工作區** ] 區段中，選取您要在其中建立 ITSMC 資源的資源群組：

   ![顯示 Log Analytics 工作區區段的螢幕擷取畫面。](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >在從 Microsoft Operations Management Suite (OMS) 到 Azure 監視器的持續轉換過程中，OMS 工作區現在稱為 *Log Analytics 工作區*。

5. 選取 [確定]。

部署 ITSMC 資源時，視窗右上角會出現通知。


## <a name="create-an-itsm-connection"></a>建立 ITSM 連線

安裝 ITSMC 之後，您可以建立連接。

若要建立連線，您必須準備 ITSM 工具以允許來自 ITSMC 的連接。  

根據您要連線的 ITSM 產品，選取下列其中一個連結以取得指示：

- [System Center Service Manager](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

備妥您的 ITSM 工具之後，請完成下列步驟來建立連接：

1. 在 [ **所有資源**] 中，尋找 **>servicedesk (*您的工作區名稱*)**：

   ![顯示 Azure 入口網站中最近資源的螢幕擷取畫面。](media/itsmc-overview/itsm-connections.png)

1. 在左窗格的 [ **工作區資料來源** ] 底下，選取 [ **ITSM 連接**：

   ![顯示 [ITSM 連接] 功能表項目的螢幕擷取畫面。](media/itsmc-overview/add-new-itsm-connection.png)
   此頁面會顯示連線的清單。
1. 選取 [ **新增連接**]。

4. [依照設定與 ITSM 產品/服務的 ITSMC](./itsmc-connections.md)連線中所述，指定連線設定。

   > [!NOTE]
   >
   > 依預設，ITSMC 會每隔24小時重新整理一次連線的設定資料。 若要立即重新整理連線的資料以反映您所做的任何編輯或範本更新，請在連線的分頁上選取 [ **同步** 處理] 按鈕：
   >
   > ![顯示連接分頁上 [同步處理] 按鈕的螢幕擷取畫面。](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="use-itsmc"></a>使用 ITSMC
   您可以使用 ITSMC 從 Azure 警示、Log Analytics 警示和 Log Analytics 記錄檔記錄中建立工作專案。

## <a name="template-definitions"></a>範本定義
   有工作專案類型可以使用 ITSM 工具所定義的範本。
藉由使用範本，您可以定義欄位，這些欄位會根據定義為動作群組一部分的固定值來自動填入。 您可以在 ITSM 工具中定義範本。 您可以定義您想要將哪一個範本作為動作群組定義的一部分。
      
## <a name="create-itsm-work-items-from-azure-alerts"></a>從 Azure 警示建立 ITSM 工作項目

建立 ITSM 連線之後，您可以在 ITSM 工具中根據 Azure 警示建立工作專案。 若要建立工作專案，您將使用動作群組中的 ITSM 動作。

動作群組提供模組化且可重複使用的方式來觸發 Azure 警示的動作。 您可以在 Azure 入口網站中使用具有計量警示、活動記錄警示和 Azure Log Analytics 警示的動作群組。

> [!NOTE]
> 建立 ITSM 連線之後，您需要等候30分鐘，同步處理常式才會完成。
> 

使用下列程式建立工作專案：

1. 在 [Azure 入口網站中，選取 [  **警示**]。
2. 在畫面頂端的功能表中，選取 [ **管理動作**：

    ![顯示 [管理動作] 功能表項目的螢幕擷取畫面。](media/itsmc-overview/action-groups-selection-big.png)

   [ **建立動作群組** ] 視窗隨即出現。

3. 選取您要在其中建立動作群組的 **訂** 用帳戶和 **資源群組** 。 提供動作群組的 **動作組名** 和 **顯示名稱** 。 選取 **[下一步：通知]**。

    ![顯示 [建立動作群組] 視窗的螢幕擷取畫面。](media/itsmc-overview/action-groups-details.png)

4. 在通知清單中選取 **[下一步：動作]**。
5. 在 [動作] 清單中，選取 [**動作類型**] 清單中的 [ **ITSM** ]。 提供動作的 **名稱** 。 選取代表 **編輯詳細資料** 的畫筆按鈕。
6. 在 **訂** 用帳戶清單中，選取您的 Log Analytics 工作區所在的訂用帳戶。 在 [ **連接** ] 清單中，選取您的 ITSM 連接器名稱。 它後面會接著您的工作區名稱。 例如，MyITSMConnector (MyWorkspace) 。

7. 選取 **工作專案** 類型。

8. 如果您想要以固定值填滿現成的欄位，請選取 [ **使用自訂範本**]。 否則，請在 **範本** 清單中選擇現有的 [範本](#template-definitions)，然後在範本欄位中輸入固定值。

9. 如果您 **針對每個設定專案選取 [建立個別的工作專案**]，則每個設定專案都會有自己的工作專案。 每個設定專案都會有一個工作專案。 它會根據將建立的警示進行更新。

   * 如果您在工作專案下拉式清單中選取 [事件] 或 [警示]：如果您清除 [ **為每個設定專案建立個別的工作專案** ] 核取方塊，則每個警示都會建立新的工作專案。 每個設定專案可能會有一個以上的警示。

   ![顯示 ITSM 票證視窗的螢幕擷取畫面。](media/itsmc-overview/itsm-action-configuration.png)
   
   * 如果您在工作專案下拉式清單中選取 [事件]：如果您針對選項按鈕選取範圍中的 **每個記錄專案選取 [建立個別的工作專案** ]，則每個警示都會建立新的工作專案。 如果您針對選項按鈕選取範圍中的 **每個設定專案選取 [建立個別工作專案** ]，則每個設定專案都會有自己的工作專案。
   ![顯示 ITSM 票證視窗的螢幕擷取畫面。](media/itsmc-overview/itsm-action-configuration-event.png)

10. 選取 [確定]。

當您建立或編輯 Azure 警示規則時，請使用具有 ITSM 動作的動作群組。 當警示觸發時，會在 ITSM 工具中建立或更新工作專案。

> [!NOTE]
>
>- 如需 ITSM 動作價格的詳細資訊，請參閱動作群組的 [定價頁面](https://azure.microsoft.com/pricing/details/monitor/) 。
>
>
>- 當您使用 [ITSM] 動作傳送警示規則時，警示規則定義中的 [簡短描述] 欄位限制為40個字元。


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>將事件和變更要求資料視覺化並加以分析

視您設定連線時的設定而定，ITSMC 最多可以同步處理120天的事件和變更要求資料。 本文的 [下一節](#additional-information) 將提供此資料的記錄檔記錄架構。

您可以使用 ITSMC 儀表板，將事件視覺化並變更要求資料：

![顯示 ITSMC 儀表板的螢幕擷取畫面。](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

儀表板也會提供連接器狀態的相關資訊，您可以使用此資訊作為起點來分析連接問題。

您也可以在服務對應中將與受影響電腦同步的事件視覺化。

服務對應可自動探索 Windows 和 Linux 系統上的應用程式元件，並對應服務之間的通訊。 它可讓您依自己的想法來查看您的伺服器：做為提供重要服務的互連系統。 服務對應顯示任何 TCP 連線架構的伺服器、進程和埠之間的連接。 除了安裝代理程式以外，不需要進行任何設定。 如需詳細資訊，請參閱 [使用服務對應](../insights/service-map.md)。

如果您使用服務對應，可以查看 ITSM 方案中建立的服務台專案，如下所示：

![顯示 Log Analytics 畫面的螢幕擷取畫面。](media/itsmc-overview/itsmc-overview-integrated-solutions.png)


## <a name="additional-information"></a>其他資訊

### <a name="data-synced-from-your-itsm-product"></a>從您的 ITSM 產品同步的資料
事件和變更要求會根據連線的設定，從您的 ITSM 產品同步至 Log Analytics 工作區。

本節說明 ITSMC 所收集資料的一些範例。

**ServiceDesk_CL** 中的欄位會根據您匯入至 Log Analytics 的工作專案類型而有所不同。 以下是兩個工作專案類型的欄位清單：

**工作項目：** **事件**  
ServiceDeskWorkItemType_s="Incident"

**欄位**

- ServiceDeskConnectionName
- 服務台識別碼
- State
- 急迫性
- 影響
- 優先順序
- 升級
- 建立者
- 解決者
- 關閉者
- 來源
- 指派對象
- 類別
- Title
- 描述
- 建立日期
- 關閉日期
- 解決日期
- 上次修改日期
- 電腦


**工作項目：** **變更要求**

ServiceDeskWorkItemType_s="ChangeRequest"

**欄位**
- ServiceDeskConnectionName
- 服務台識別碼
- 建立者
- 關閉者
- 來源
- 指派對象
- Title
- 類型
- 類別
- State
- 升級
- 衝突狀態
- 急迫性
- 優先順序
- 風險
- 影響
- 指派對象
- 建立日期
- 關閉日期
- 上次修改日期
- 要求日期
- 計劃開始日期
- 計劃結束日期
- 工作開始日期
- 工作結束日期
- 描述
- 電腦

## <a name="output-data-for-a-servicenow-incident"></a>ServiceNow 事件的輸出資料

| Log Analytics 欄位 | ServiceNow 欄位 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | State |
| Urgency_s |急迫性 |
| Impact_s |影響|
| Priority_s | 優先順序 |
| CreatedBy_s | 開啟者 |
| ResolvedBy_s | 解決者|
| ClosedBy_s  | 關閉者 |
| Source_s| 連絡人型別 |
| AssignedTo_s | 指派對象  |
| Category_s | 類別 |
| Title_s|  簡短描述 |
| Description_s|  注意 |
| CreatedDate_t|  已開啟 |
| ClosedDate_t| 關閉|
| ResolvedDate_t|已解決|
| 電腦  | 設定項目 |

## <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow 變更要求的輸出資料

| Log Analytics | ServiceNow 欄位 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | 要求者 |
| ClosedBy_s | 關閉者 |
| AssignedTo_s | 指派對象  |
| Title_s|  簡短描述 |
| Type_s|  類型 |
| Category_s|  類別 |
| CRState_s|  State|
| Urgency_s|  急迫性 |
| Priority_s| 優先順序|
| Risk_s| 風險|
| Impact_s| 影響|
| RequestedDate_t  | 要求的日期 |
| ClosedDate_t | 關閉日期 |
| PlannedStartDate_t  |     計劃開始日期 |
| PlannedEndDate_t  |   計劃結束日期 |
| WorkStartDate_t  | 實際開始日期 |
| WorkEndDate_t | 實際結束日期|
| Description_s | 描述 |
| 電腦  | 設定項目 |


## <a name="troubleshoot-itsm-connections"></a>針對 ITSM 連線進行疑難排解
- 如果連接的來源 UI 連線失敗，而您 **在儲存連接訊息時發生錯誤** ，請執行下列步驟：
   - 針對 ServiceNow、Cherwell 和 Provance 連線：  
     - 確定您已正確輸入每個連線的使用者名稱、密碼、用戶端識別碼和用戶端密碼。  
     - 請確定您在對應的 ITSM 產品中有足夠的許可權，才能建立連接。  
   - 針對 Service Manager 連接：  
     - 確定已成功部署 web 應用程式，且已建立混合式連接。 若要確認是否已成功建立與內部部署 Service Manager 電腦的連線，請移至 web 應用程式 URL，如建立 [混合](./itsmc-connections.md#configure-the-hybrid-connection)式連線的檔中所述。  

- 如果 ServiceNow 的資料未同步處理至 Log Analytics，請確定 ServiceNow 實例未進入睡眠狀態。 當 ServiceNow 開發人員實例閒置很長一段時間時，有時會進入睡眠狀態。 如果發生這種情況，請回報問題。
- 如果記錄分析警示引發但未在 ITSM 產品中建立工作專案，如果設定專案未建立/連結至工作專案，或其他資訊，請參閱下列資源：
   -  ITSMC：解決方案會顯示連線、工作專案、電腦等的摘要。 選取具有 **連接器狀態** 標籤的磚。 這樣做會讓您使用相關查詢來 **記錄搜尋** 。 查看記錄檔記錄，以 `LogType_S` `ERROR` 取得詳細資訊。
   - **記錄搜尋** 頁面：使用查詢直接查看錯誤和相關資訊 `*ServiceDeskLog_CL*` 。

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Service Manager web 應用程式部署進行疑難排解
-   如果您有 web 應用程式部署的問題，請確定您有權在訂用帳戶中建立/部署資源。
-   如果您在執行 [腳本](itsmc-service-manager-script.md)時取得 **未設定為物件錯誤實例的物件參考**，請確定您已在 [**使用者** 設定] 區段中輸入有效的值。
-   如果您無法建立服務匯流排轉送命名空間，請確定已在訂用帳戶中註冊必要的資源提供者。 如果未註冊，請從 Azure 入口網站手動建立服務匯流排轉送命名空間。 您也可以在 Azure 入口網站中 [建立混合](./itsmc-connections.md#configure-the-hybrid-connection) 式連線時建立它。


## <a name="contact-us"></a>與我們連絡

如果您有關于 IT 服務管理連接器的查詢或意見反應，請與我們聯絡 [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) 。

## <a name="next-steps"></a>後續步驟
[將 ITSM 產品/服務新增至 IT 服務管理連接器](./itsmc-connections.md)
