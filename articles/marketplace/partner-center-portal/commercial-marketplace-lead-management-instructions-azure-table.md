---
title: Azure 表 |Azure 應用商店
description: 針對 Azure 資料表設定潛在客戶管理。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dsindona
ms.openlocfilehash: f511a60b533d6d1e0b1ae8847d0ee0fb6be3500c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288830"
---
# <a name="configure-lead-management-using-an-azure-table"></a>使用 Azure 表配置潛在客戶管理

如果合作夥伴中心中沒有明確支援客戶關係管理 （CRM） 系統以接收 Azure 應用商店和 AppSource 潛在客戶，則可以使用 Azure 表來處理這些潛在客戶。 然後，您可以選擇匯出資料並將其導入 CRM 系統。 本文中的說明將引導您完成創建 Azure 存儲帳戶以及該帳戶下的 Azure 表的過程。 此外，您可以使用 Microsoft Flow 創建新流，在產品/服務收到潛在客戶時發送電子郵件通知。

## <a name="configure-azure-table"></a>配置 Azure 表

1. 如果您沒有 Azure 帳戶，您可以[建立免費的試用帳戶](https://azure.microsoft.com/pricing/free-trial/)。
2. Azure 帳戶處於活動狀態後，請登錄到 Azure[門戶](https://portal.azure.com)。
3. 在 Azure 門戶中，使用以下過程創建存儲帳戶。  
    1. 選擇 "在左側功能表列中**創建資源**"。  "**新建**"窗格（邊欄）將向右顯示。
    2. 在 **"新建"** 窗格中選擇 **"存儲**"。  **右側將顯示"精選**"清單。
    3. 選擇 **"存儲帳戶**"以開始創建帳戶。  按照文章"[創建存儲帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)"中的說明進行操作。

        ![建立 Azure 儲存體帳戶的步驟](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        有關存儲帳戶的詳細資訊，請選擇[快速入門教程](https://docs.microsoft.com/azure/storage/)。  如需儲存體定價的詳細資訊，請參閱[儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

4. 等待存儲帳戶預配，此過程通常需要幾分鐘時間。  然後，通過選擇 **"查看所有資源**"或從 Azure 門戶的左側導航功能表列中選擇 **"所有資源**"，從 Azure 門戶的**主頁**訪問存儲帳戶。

    ![訪問 Azure 存儲帳戶](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. 從存儲帳戶窗格中，選擇 **"訪問金鑰"** 並複製金鑰的連接*字串*值。 保存此值，因為這是存儲*帳戶連接字串*值，您需要在發佈門戶中提供該值才能接收市場產品的潛在客戶。 

    連接刺痛的一個示例是：

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Azure 儲存體金鑰](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. 從存儲帳戶窗格中，選擇 **"表"** 並選擇 **"表**"以創建表。 輸入表的名稱，然後選擇 **"確定**"。 如果要配置 MS Flow 以在收到潛在客戶時接收電子郵件通知，請根據需要保存此值。

    ![Azure 資料表](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    您可以使用 [Azure 儲存體總管](https://archive.codeplex.com/?p=azurestorageexplorer) \(英文\) 或任何其他工具，來檢視儲存體資料表中的資料。 您還可以在 Azure 表中匯出資料。 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>（可選）將 Microsoft 流與 Azure 表一起使用  

您可以使用 [Microsoft Flow](https://docs.microsoft.com/flow/) 在每次有潛在客戶新增至 Azure 資料表時自動通知。 如果您沒有帳戶，您可以[註冊一個免費帳戶](https://flow.microsoft.com/)。

### <a name="lead-notification-example"></a>潛在客戶通知範例

使用此範例作為指南，以建立簡單的流程，在新的潛在客戶新增至 Azure 資料表時，自動傳送電子郵件通知。 此示例設置一個重複，以便在更新表存儲時每小時發送一次潛在客戶資訊。

1. 登錄到您的 Microsoft 流帳戶。
2. 在左側導覽列上，選取 [我的流程]****。
3. 在頂端導覽列上，選取 [+ 新增]****。  
4. 在下拉清單中，**從空白**中選擇 + 計畫

   ![我的流 = 計畫 - 從空白*](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.  在 *"生成"下的計畫流*視窗上 *，每個*選擇"1"為間隔，"小時"頻率。 此外，如果您願意，請為流指定一個名稱。 選取 [建立]****。

    >[!Note]
    >儘管此示例使用 1 小時間隔，但您可以選擇最適合業務需求的間隔和頻率。

    ![生成計畫流。](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. 選取 [+ 新步驟] ****。
7. 在"*選擇操作*視窗"搜索"過去時間"，然後選擇"在操作下**獲取過去時間**"。

   ![選擇操作。](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. 在 [取得過去時間]**** 視窗中，將 [間隔]**** 設為 1。 從 [時間單位]**** 下拉式清單中，選取 [小時]****。

    >[!Important]
    >確保此間隔和時間單元與步驟 5 中為"重複"配置的間隔和頻率匹配。

    ![設定取得過去時間間隔](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>您可以隨時檢查您的流程，以確認每個步驟都已正確設定。 若要檢查您的流程，請從 [流程] 功能表列中選取 [流程檢查工具]****。

在下一組步驟中，您將連接到 Azure 表，並設置處理邏輯以處理新潛在客戶。

9. 在"獲取過去時間"步驟後，選擇 **"新建步驟**"，然後在 *"選擇操作*"視窗中搜索"獲取實體"。
10. 在 **"操作"** 下，選擇**獲取實體（Azure 表存儲）。**
11. 在**Azure 表存儲**視窗中，提供以下欄位的資訊，然後選擇"**創建**：

    * *連接名稱*- 為在此流和 Azure 表之間建立的連接提供有意義的名稱。
    * *存儲帳戶名稱*- 提供 Azure 表的存儲帳戶的名稱。 您可以在存儲帳戶的訪問**金鑰**頁中找到此項。
    * *共用存儲金鑰*- 為 Azure 表的存儲帳戶提供金鑰值。 您可以在存儲帳戶的訪問**金鑰**頁中找到此項。

        ![Azure 資料表儲存體。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

    按一下"創建"後，您將看到 *"獲取實體"* 視窗。 此處選擇 **"顯示高級選項**"並提供以下欄位的資訊：

       * *表*- 選擇 Azure 表存儲的名稱（從有關如何配置 Azure 表的說明的第 6 步）。 下一個螢幕截圖顯示為此示例選擇"市場潛在客戶"表時的提示。

            ![Azure 表獲取實體。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

        * *篩選器查詢*- 選擇此欄位並將此函數粘貼到欄位中：`Timestamp gt datetime'@{body('Get_past_time')}'`

            ![Azure 表獲取實體 - 篩選資源。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. 現在，您已完成與 Azure 表的連接設置，請選擇 **"新建步驟**"以添加條件以掃描 Azure 表以創建新的潛在客戶。 

13. 在 **"選擇操作"** 視窗中，選擇 **"操作**"，然後選擇 **"條件"控制項**。

    ![Azure 表 - 選擇操作。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. 在 [條件]**** 視窗中，選取 [選擇值]**** 欄位，然後選取快顯視窗中的 [運算式]****。

15. 將 `length(body('Get_entities')?['value'])` 貼入 [fx]****** 欄位。 選取 [確定]**** 來新增此函式。 

16. 若要完成設定條件：
    1. 從下拉清單中選擇"大於"。
    2. 輸入 0 作為值

        ![Azure 表 - 條件。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

在接下來的幾個步驟中，您將根據條件的結果設置要執行的操作。

* 如果條件解析為 **"如果否**"，則不要執行任何操作。
* 如果條件解析為 **If yes**，觸發連接 Office 365 帳戶傳送電子郵件的動作。 

17. 選擇在 **"如果是"** 下**添加操作**。

    ![Azure 表 - 條件，[如果是]。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. 選擇**發送電子郵件（Office 365 Outlook）。**

    ![Azure 表 - 條件，[如果是]，發送電子郵件。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >如果要使用其他電子郵件供應商搜索，請選擇"發送電子郵件通知（郵件）"作為操作。 這些說明將向您展示如何使用 Office 365 Outlook 進行配置，但對於不同的電子郵件供應商，這些說明類似。

19. 在**Office 365 Outlook**視窗中，提供有關以下欄位的資訊：

    1. **收件者** - 輸入將會收到此通知的每個人的電子郵件地址。
    1. **主題**- 為電子郵件提供主題。 例如：新線索！
    1. **正文**- 添加要包含在每封電子郵件中的文本（可選），然後粘貼到正文`body('Get_entities')?['value']`中。

    >[!Note]
    >您可以將額外的靜態或動態資料點插入到這封電子郵件的本文。

    ![Azure 表 - 條件，[如果是]，Office 365 Outlook 視窗。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. 選取 [儲存]**** 以儲存流程。 Microsoft Flow 將會自動測試流程中是否有錯誤。 如果沒有任何錯誤，則流在保存後開始運行。

下一個螢幕擷取會顯示最終流程外觀的範例。

![最終流的一個示例。](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>管理您的流程

運行後管理流非常簡單。 您可以完全控制您的流程。 例如，您可以停止流程、編輯流程、查看執行歷程記錄，以及取得分析。 下一個螢幕擷取會顯示可用來管理流程的選項。 

 ![管理流程](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

流程會繼續執行，直到您使用 [關閉流程]**** 選項停止為止。

如果未收到任何潛在客戶電子郵件通知，則意味著尚未將新潛在客戶添加到 Azure 表中。 如果有任何流故障，您將收到一封電子郵件，如下一個螢幕捕獲中的示例。

 ![流程失敗電子郵件通知](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>將產品/服務配置為將潛在客戶發送到 Azure 表

準備好在發佈門戶中配置產品/服務的首席管理資訊時，請按照以下步驟操作：

1. 導航到產品 **/服務"產品/服務"設置**頁面。
2. 在"潛在客戶管理"部分下選擇 **"連接**"。
3. 在"連接詳細資訊"快顯視窗上，選擇**潛在客戶目標的** **Azure 表**，然後按照前面的步驟粘貼到**存儲帳戶連接字串**欄位，從創建的 Azure 存儲帳戶的連接字串中粘貼。
4. 選取 [儲存]****。 

>[!Note]
>您必須完成配置產品/服務的其餘部分併發布它，然後才能收到產品/服務的潛在客戶。

生成潛在客戶時，Microsoft 會將潛在客戶發送到 Azure 表。 如果配置了流，也會將電子郵件發送到您配置的電子郵件地址。

![潛在客戶管理](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![潛在客戶管理 - 連接詳細資訊](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![潛在客戶管理 - 連接詳細資訊存儲帳戶](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

