---
title: 使用 Power BI 應用程式分析 Azure 成本
description: 本文將說明如何安裝及使用 Azure 成本管理 Power BI 應用程式。
author: bandersmsft
ms.author: banders
ms.date: 06/16/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.openlocfilehash: 53340c72a6456b24b52cff6d7eda9d4a34db6564
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2020
ms.locfileid: "84888195"
---
# <a name="analyze-cost-with-the-azure-cost-management-power-bi-app-for-enterprise-agreements-ea"></a>使用適用於 Enterprise 合約 (EA) 的 Azure 成本管理 Power BI 應用程式來分析成本

本文將說明如何安裝及使用 Azure 成本管理 Power BI 應用程式。 此應用程式可協助您在 Power BI 中分析和管理 Azure 成本。 您可以使用應用程式來監視成本和使用量趨勢，並找出成本最佳化選項來減少支出。

您會將應用程式下載到 Power BI Desktop。 您可以直接使用應用程式，也可以加以修改來擴充預設的篩選、檢視和視覺效果，以符合您的需求。 然後，使用其來聯結額外資料，以建立自訂報表來取得整體商務成本的完整觀點。

Azure 成本管理 Power BI 應用程式目前僅支援具有 [Enterprise 合約](https://azure.microsoft.com/pricing/enterprise-agreement/)的客戶。

## <a name="prerequisites"></a>必要條件

- 安裝和使用應用程式所需的 [Power BI Pro 授權](/power-bi/service-self-service-signup-for-power-bi)
- 若要連結資料，您必須使用[企業系統管理員](../manage/understand-ea-roles.md)帳戶

## <a name="installation-steps"></a>安裝步驟

若要安裝應用程式：

1. 開啟 [Azure 成本管理 Power BI 應用程式](https://aka.ms/costmgmt/ACMApp)。
2. 在 [Power BI AppSource] 頁面上選取 [立即取得]。
3. 選取 [繼續] 以同意使用規定與隱私權原則。
4. 在 [安裝此 Power BI 應用程式] 方塊中，選取 [安裝]。
5. 如有需要，請建立工作區，然後選取 [繼續]。
6. 安裝完成時，即會顯示新應用程式已就緒的通知。
7. 選取 [移至應用程式]。
8. 在 [開始使用您的新應用程式] 中，從 [連結您的資料] 底下，選取 [連結]。  
  ![開始使用您的新應用程式 - 連結](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-data2.png)
9. 在出現的對話方塊中，於 **BillingProfileIdOrEnrollmentNumber** 中輸入您的 EA 註冊號碼。 指定要取得資料的月份數。 保留**註冊號碼**的預設**範圍**值，然後選取 [下一步]。  
  ![輸入 EA 註冊資訊](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  
10. 下一個對話方塊會與 Azure 連結，並取得保留執行個體建議所需的資料。 *保留已設定的預設值*，然後選取 [登入]。  
  ![連接到 Azure](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png)  
11. 最後的安裝步驟會連結到您的 EA 註冊，而且需要[企業系統管理員](../manage/understand-ea-roles.md)帳戶。 選取 [登入]，以向您的 EA 註冊進行驗證。 此步驟也會在 Power BI 中啟動資料重新整理動作。  
  ![連結到 EA 註冊](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png)  
    > [!NOTE]
    > 資料重新整理程序可能需要相當長的時間才能完成。 時間長度取決於指定的月份數，以及需要同步的資料量。
12. 若要檢查資料重新整理狀態，請在工作區中選取 [資料集] 索引標籤。 在重新整理後的時間戳記旁查看。 如果狀態仍在更新中，您會看到顯示重新整理正在進行中的指示。  
  ![重新整理資料](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/data-refresh2.png)

資料重新整理完成之後，請選取 [Azure 成本管理應用程式] 來查看預先建立的報表。

## <a name="reports-available-with-the-app"></a>應用程式的可用報表

以下是應用程式中可用的報表。

**使用者入門** - 提供實用的文件連結，以及可提供意見反應的連結。

**帳戶概觀** - 此報表會顯示每月的資訊摘要，包括：

- 根據信用額度計算的費用
- 新的購買項目
- Azure Marketplace 費用
- 超額部分和總費用

**訂用帳戶和資源群組的使用量** - 提供一段時間的成本檢視和圖表，以顯示訂用帳戶和資源群組的成本。

**服務的使用量** - 提供 MeterCategory 在某段時間內的使用量。 您可以追蹤使用量資料，並深入探索任何異常狀況，以了解使用量高點或低點。

**前 5 個使用量驅動程式** - 此報表會顯示 5 個 MeterCategory 和對應 MeterName 的已篩選成本摘要。

**Windows Server AHB 使用量** - 此報告會顯示已啟用 Azure Hybrid Benefit 的虛擬機器數目。 其也會顯示虛擬機器所使用的核心/vCPU 計數。

![Azure Hybrid Benefit 完整報表](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png)

此報表也會識別已啟用 **Hybrid Benefit**，但「少於」 8 個 vCPU 的 Windows VM。 此外，有 8 個「或以上」的 vCPU，卻**未啟用** Hybrid Benefit 的情形也會顯示。 這項資訊可協助您充分運用 Hybrid Benefit。 將權益套用至您最耗費資源的虛擬機器，以最大化可省下的成本。

![Azure Hybrid Benefit – 少於 8 個 vCPU 及未啟用 vCPU](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png)

**RI 退款** - 此報表可協助您了解每個區域、訂用帳戶、資源群組或資源所套用的保留執行個體 (RI) 權益在何處，以及有多少數量。 此報表會使用分攤的使用量資料來顯示此檢視。

您可以在 chargetype 上套用篩選，以檢視 RI 使用量過低的資料。

如需有關分攤資料的詳細資訊，請參閱[取得 Enterprise 合約保留成本和使用量](/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea)。

**RI 節省量** - 此報表會顯示訂用帳戶、資源群組和資源層級上保留所累積的節省量。 其會顯示：

- 使用保留的成本
- 如果保留未套用於使用量，則顯示預估的隨選成本
- 從保留中省下的累積成本

 此報表會從總節省量減去任何使用量過低的保留浪費成本。 沒有保留就不會發生浪費。

您可以使用分攤的使用量資料來建立資料。

<a name="shared-recommendation"></a>
**VM RI 涵蓋範圍 (共用建議)** - 此報表會在所選期間內依隨選 VM 使用量和 RI VM 使用量進行分割。 其中會針對共用範圍上的 VM RI 購買提供建議。

若要使用此報表，請選取向下鑽研的篩選條件。

![VM RI 涵蓋範圍報表 - 選取向下鑽研](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png)

選取您想要分析的區域。 然後選取執行個體大小彈性群組，依此類推。

針對每個向下鑽研層級，下列篩選條件會套用至報表：

- 右邊的涵蓋範圍資料是篩選條件，顯示使用隨選費率計費的使用量有多少，以及保留所涵蓋的使用量有多少。
- 建議也會一併篩選。

建議資料表會根據使用的 VM 大小，提供購買保留的建議。

「正規化後的大小」和「正規化後的建議數量」值可協助您將購買正規化為執行個體大小彈性群組的大小下限。 如果您打算只購買一個保留來用於執行個體大小彈性群組中的所有大小，則這項資訊會很實用。

![RI 建議](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recomendations.png)

**VM RI 涵蓋範圍 (單一建議)** - 此報表會在所選期間內依隨選 VM 使用量和 RI VM 使用量進行分割。 其中會針對訂用帳戶範圍上的 VM RI 購買提供建議。

如需如何使用報表的詳細資訊，請參閱 [VM RI 涵蓋範圍 (共用建議)](#shared-recommendation) 一節。

**RI 購買** - 此報表會顯示在指定期間內的 RI 購買。

**價位表** - 此報表會顯示計費帳戶或 EA 註冊專屬價格的詳細清單。

## <a name="troubleshoot-problems"></a>問題疑難排解

如果您在 Power BI 應用程式中遇到問題，下列疑難排解資訊可能會有幫助。

### <a name="error-processing-the-data-in-the-dataset"></a>處理資料集中的資料時發生錯誤

您可能會收到錯誤訊息，內容為：

```
There was an error when processing the data in the dataset.
Data source error: {"error":{"code":"ModelRefresh_ShortMessage_ProcessingError","pbi.error":{"code":"ModelRefresh_ShortMessage_ProcessingError","parameters":{},"details":[{"code":"Message","detail":{"type":1,"value":"We cannot convert the value \"Required Field: 'Enr...\" to type List."}}],"exceptionCulprit":1}}} Table: <TableName>.
```

此時會出現資料表名稱，而不是 `<TableName>`。

#### <a name="cause"></a>原因

預設**範圍**值 `Enrollment Number` 已在成本管理的連線中變更。

#### <a name="solution"></a>解決方法

重新連線至成本管理，並將**範圍**值設定為 `Enrollment Number`。 請勿輸入組織的註冊號碼，而應輸入與下圖所示完全相同的 `Enrollment Number`。

![輸入 EA 註冊資訊](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  

### <a name="budgetamount-error"></a>BudgetAmount 錯誤

您可能會收到錯誤訊息，內容為：

```
Something went wrong
There was an error when processing the data in the dataset.
Please try again later or contact support. If you contact support, please provide these details.
Data source error: The 'budgetAmount' column does not exist in the rowset. Table: Budgets.
```

#### <a name="cause"></a>原因

發生此錯誤的原因是基礎中繼資料包含錯誤 (bug)。 發生此問題的原因是，在 Azure 入口網站的**成本管理 > 預算**下沒有可用的預算。 我們正在部署到 Power BI Desktop 和 Power BI 服務的流程中處理此錯誤 (bug) 修正。 

#### <a name="solution"></a>解決方法

- 在錯誤 (bug) 修正完成之前，您可以在 Azure 入口網站的計費帳戶/EA 註冊層級新增測試預算來解決此問題。 測試預算會解除與 Power BI 的連線中斷情況。 如需關於建立預算的詳細資訊，請參閱[教學課程：建立和管理 Azure 預算](tutorial-acm-create-budgets.md)。


### <a name="invalid-credentials-for-azureblob-error"></a>AzureBlob 錯誤的無效認證

您可能會收到錯誤訊息，內容為：

```
Failed to update data source credentials: The credentials provided for the AzureBlobs source are invalid.
```

#### <a name="cause"></a>原因

如果您變更 AutoFitComboMeter Blob 連線的驗證方法，就會發生此錯誤。

#### <a name="solution"></a>解決方法

1. 連線到您的資料。
1. 輸入 EA 註冊和月數之後，請確定您將驗證方法保留為**匿名**的預設值，並將隱私權等級設為**無**。  
  ![連接到 Azure](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png)  
1. 在下一個頁面上，將驗證方法設定為 **OAuth2**，並將隱私權等級設為**無**。 接著，登入以驗證您的註冊。 此步驟也會重新整理 Power BI 資料。


## <a name="data-reference"></a>資料參考

下列資訊彙總可透過應用程式取得的資料。 另外還有一些 API 連結，可提供資料欄位和值的深入詳細資訊。

| **資料表參考** | **說明** |
| --- | --- |
| **AutoFitComboMeter** | 包含在應用程式中的資料，可將 RI 建議和使用量正規化為執行個體系列群組中的大小下限。 |
| [**餘額摘要**](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary#response) | Enterprise 合約的餘額摘要。 |
| [**預算**](/rest/api/consumption/budgets/get#definitions) | 預算詳細資料，可針對現有的預算目標檢視實際成本或使用量。 |
| [**價位表**](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet#see-also) | 所提供帳單設定檔或 EA 註冊適用的計量費率。 |
| [**RI 費用**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges#response) | 過去 24 個月內與您保留執行個體相關的費用。 |
| [**RI 建議 (共用)** ](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response) | 保留執行個體購買建議，以您過去 7、30 或 60 天的所有訂用帳戶使用量趨勢為依據。 |
| [**RI 建議 (單一)** ](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response-1) | 保留執行個體購買建議，以您過去 7、30 或 60 天的單一訂用帳戶使用量趨勢為依據。 |
| [**RI 使用量詳細資料**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#response) | 您現有保留執行個體上個月的耗用量詳細資料。 |
| [**RI 使用量摘要**](/rest/api/consumption/reservationssummaries/list) | 每日 Azure 保留使用量的百分比。 |
| [**使用量詳細資料**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | EA 註冊中指定帳單設定檔的已耗用數量和預估費用明細。 |
| [**已分攤的使用量詳細資料**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | EA 註冊中指定帳單設定檔的已耗用數量和預估分攤費用明細。 |

## <a name="next-steps"></a>後續步驟

如需有關設定資料、重新整理、共用報表及其他報表自訂項目的詳細資訊，請參閱下列文章：

- [設定排定的重新整理時間](/power-bi/refresh-scheduled-refresh)
- [與同事和其他人共用 Power BI 儀表板和報表](/power-bi/service-share-dashboards)
- [在 Power BI 服務中訂閱您自己和其他人的報表和儀表板](/power-bi/service-report-subscribe)
- [將報表從 Power BI 服務下載至 Power BI Desktop](/power-bi/service-export-to-pbix)
- [在 Power BI 服務和 Power BI Desktop 中儲存報表](/power-bi/service-report-save)
- [匯入資料集以在 Power BI 服務中建立報表](/power-bi/service-report-create-new)
