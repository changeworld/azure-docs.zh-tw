---
title: Azure 表存儲 |Azure 應用商店
description: 在 Azure 表存儲中配置潛在客戶管理。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dsindona
ms.openlocfilehash: cee28c65b97d4ac163f78b746dc7be9ee210843c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280349"
---
# <a name="lead-management-instructions-for-table-storage"></a>表存儲的引線管理說明

本文介紹如何配置 Azure 表存儲來管理銷售線索。 表存儲可説明您存儲和修改客戶資訊。

## <a name="configure-table-storage"></a>設定資料表儲存體

1. 如果沒有 Azure 帳戶，[請創建一個免費試用帳戶](https://azure.microsoft.com/pricing/free-trial/)。
1. 帳戶處於活動狀態後，登錄到[Azure 門戶](https://portal.azure.com)。
1. 在 Azure 門戶中，按照以下步驟操作：  
    1. 選擇 "在左側的窗格中**創建資源**"。 "**新建**"窗格將打開。
    1. 在 **"新建"** 窗格中，選擇 **"存儲**"。 **"精選"** 清單將在右側打開。
    1. 選擇**存儲帳戶**。 然後，按照[創建存儲帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)中的說明操作。

    ![建立 Azure 儲存體帳戶](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    有關存儲帳戶的更多資訊，請參閱[快速入門教程](https://docs.microsoft.com/azure/storage/)。 有關定價資訊，請參閱[Azure 存儲定價](https://azure.microsoft.com/pricing/details/storage/)。

1. 等待存儲帳戶預配，這通常需要幾分鐘時間。 然後，從 Azure 門戶的主頁存取帳戶：選擇 **"查看**功能窗格中的所有資源"或 **"所有資源**"。

    ![訪問 Azure 存儲帳戶](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. 從存儲帳戶窗格中複製金鑰的存儲帳戶連接字串。 將其粘貼到雲合作夥伴門戶中的存儲帳戶的連接**字串**欄位中。

    連接字串示例：

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Azure 儲存體金鑰](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

您可以使用[Azure 存儲資源管理器](https://azurestorageexplorer.codeplex.com/)或類似工具查看表存儲中的資料。 您還可以從中匯出資料。

## <a name="use-microsoft-flow-with-table-storage-optional"></a>將 Microsoft 流與表存儲一起使用 （*可選*）

當潛在客戶添加到表存儲時，可以使用[Microsoft Flow](https://docs.microsoft.com/flow/)自動發送通知。 如果您沒有 Microsoft Flow 帳戶，[請註冊一個免費帳戶](https://flow.microsoft.com/)。

### <a name="lead-notification-example"></a>潛在客戶通知範例

此示例演示如何創建基本流。 當新潛在客戶添加到表存儲時，流會自動每小時發送電子郵件通知。

1. 登入您的 Microsoft Flow 帳戶。
1. 在左側的功能窗格中，選擇 **"我的流**"。
1. 在頂部巡覽列中，選擇 **"新建**"。  
1. 從下拉清單中選擇 **"從空白中創建**"。
1. 在 **"從空白創建流**"下，選擇**從空白創建**。

   ![從空白建立新流程](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. 在連接器和觸發程序搜尋頁面上中，選取 [觸發程序]****。
1. 在 [觸發程序]**** 底下，選取 [週期]****。
1. 在 **"定期"** 視窗中，將 **"間隔**"的預設設置保持**1。** 從 **"頻率**"下拉清單中，選擇 **"小時**"。

   >[!NOTE] 
   >此示例使用一小時間隔。 但是，您可以選擇最適合您業務需求的間隔和頻率。

   ![設置 1 小時頻率以進行重複](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. 選擇 **+新步驟**。
1. 搜索 **"獲取過去時間**"，然後在 **"選擇操作**"下選擇 **"獲取過去時間**"。

    ![查找並選擇"過去時間"操作](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. 在 **"獲取過去時間"視窗中**，將**間隔**設置為**1**。  從 **"時間單位**"下拉清單中，選擇 **"小時**"。
    >[!IMPORTANT] 
    >確保**間隔**和**時間單元**與為重複配置的間隔和頻率匹配（步驟 8）。

    ![設置過去時間間隔](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >您可以隨時檢查流以驗證每個步驟是否配置正確：從"流"功能表列中選擇 **"流檢查器**"。

在下一組步驟中，您將連接到存儲表並設置處理邏輯以處理新潛在客戶。

1. **在"獲取過去時間**"步驟後，選擇 **"新建步驟**"，然後搜索**獲取實體**。
1. 在 [動作]**** 底下，選取 [取得實體]****，然後選取 [顯示進階選項]****。
1. 在 **"獲取實體"** 視窗中，填寫以下欄位：

   - **表**：表存儲的名稱。 下圖顯示了輸入的"市場線索"：

     ![為 Azure 資料表名稱挑選一個自訂值](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **篩選器查詢**：當您選擇此欄位時，"**過去時間"** 圖示將顯示在快顯視窗中。 選擇 **"過去時間**"以將此值用作時間戳記以篩選查詢。 或者，您可以將以下函數粘貼到欄位中：
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![設置篩選器查詢功能](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. 選擇 **"新建步驟**"以添加條件以掃描表存儲以尋找新潛在客戶。

   ![使用"新步驟"添加條件以掃描表存儲](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. 在 **"選擇操作"** 視窗中，選擇 **"操作**"，然後選擇 **"條件控制**"。

     ![添加條件控制](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. 在 **"條件"** 視窗中，**選擇"選擇值**"，然後在快顯視窗中選擇 **"運算式**"。
1. 粘貼`length(body('Get_entities')?['value'])`到***fx***欄位中。 選取 [確定]**** 來新增此函式。 



     ![將函式加入至條件](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. 根據條件的結果設定要採取的動作。

    1. 選擇**大於**下拉清單中。
   1. 輸入**0**作為值。

     ![根據條件結果設置操作](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. 如果條件解析為"如果沒有"，則不要執行任何操作。

    如果條件解析為"如果是"，則觸發連接 Office 365 帳戶以發送電子郵件的操作：
   1. 選擇 **"添加操作**"。
   1. 選取 [傳送電子郵件]****。
   1. 在 **"發送電子郵件**"視窗中，在以下欄位中輸入資訊：

      - **到**： 收到通知的每個人的電子郵件地址。
      - **主題**：電子郵件的主題。 例如：*新線索！*
      - **正文**：您希望在每封電子郵件中包含的文本（可選）。 還粘貼`body('Get_entities')?['value']`為函數以插入潛在客戶資訊。

        >[!NOTE] 
        >您可以在電子郵件正文中插入其他靜態或動態資料點。

      ![設定電子郵件，以供潛在客戶通知使用](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. 選取 [儲存]**** 以儲存流程。 微軟流將自動測試其錯誤。 如果沒有任何錯誤，則流在保存後開始運行。

    下圖顯示了最終流的外觀示例。

    [![最終流程順序](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    （*選擇圖像以放大它。*

### <a name="manage-your-flow"></a>管理您的流程

運行後，可以輕鬆管理流。 您可以完全控制您的流程。 例如，您可以停止流程、編輯流程、查看執行歷程記錄，以及取得分析。 下圖顯示了流管理選項。

 ![流管理選項](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

流繼續運行，直到選擇 **"關閉流**"。

如果您未收到任何潛在客戶電子郵件通知，則表存儲中未添加新潛在客戶。
如果發生流故障，您將看到類似以下示例的電子郵件：

 ![流程失敗電子郵件通知](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>後續步驟

[配置客戶潛在客戶](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
