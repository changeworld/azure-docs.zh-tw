---
title: Azure 資料表儲存體 |Azure Marketplace
description: 在 Azure 資料表儲存體中設定潛在客戶管理。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dsindona
ms.openlocfilehash: cee28c65b97d4ac163f78b746dc7be9ee210843c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280349"
---
# <a name="lead-management-instructions-for-table-storage"></a>資料表儲存體的潛在客戶管理指示

本文說明如何設定 Azure 資料表儲存體來管理銷售潛在客戶。 資料表儲存體可協助您儲存及修改客戶資訊。

## <a name="configure-table-storage"></a>設定資料表儲存體

1. 如果您沒有 Azure 帳戶，請[建立免費試用帳戶](https://azure.microsoft.com/pricing/free-trial/)。
1. 當您的帳戶處於作用中狀態之後，請登入[Azure 入口網站](https://portal.azure.com)。
1. 在 [Azure 入口網站中，依照下列步驟執行：  
    1. 在左側窗格中選取 [ **+ 建立資源**]。 **新**的窗格隨即開啟。
    1. 在 [**新增**] 窗格中，選取 [**儲存體**]。 **精選**清單會在右側開啟。
    1. 選取 [**儲存體帳戶**]。 然後，依照[建立儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)中的指示進行。

    ![建立 Azure 儲存體帳戶](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    如需儲存體帳戶的詳細資訊，請參閱[快速入門教學](https://docs.microsoft.com/azure/storage/)課程。 如需定價資訊，請參閱[Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

1. 等到您的儲存體帳戶布建完成，這通常需要幾分鐘的時間。 然後，從 [Azure 入口網站] 的 [首頁] 存取帳戶：選取流覽窗格中的 [**查看您的所有資源**] 或 [**所有資源**]。

    ![存取您的 Azure 儲存體帳戶](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. 從您的儲存體帳戶窗格中，複製金鑰的儲存體帳戶連接字串。 將它貼入 Cloud Partner 入口網站中儲存體帳戶的 [**連接字串**] 欄位。

    範例連接字串：

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Azure 儲存體金鑰](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

您可以使用[Azure 儲存體總管](https://azurestorageexplorer.codeplex.com/)或類似的工具來查看資料表儲存體中的資料。 您也可以匯出資料。

## <a name="use-microsoft-flow-with-table-storage-optional"></a>搭配使用 Microsoft Flow 與資料表儲存體（*選擇性*）

您可以使用[Microsoft Flow](https://docs.microsoft.com/flow/) ，在您的資料表儲存體中新增潛在客戶時，自動傳送通知。 如果您沒有 Microsoft Flow 帳戶，請[註冊免費帳戶](https://flow.microsoft.com/)。

### <a name="lead-notification-example"></a>潛在客戶通知範例

這個範例顯示如何建立基本流程。 當新的潛在客戶新增至您的資料表儲存體時，流程會自動傳送電子郵件通知給每小時。

1. 登入您的 Microsoft Flow 帳戶。
1. 在左側導覽窗格中，選取 [我的**流程**]。
1. 在上方導覽列中，選取 [ **+ 新增**]。  
1. 從下拉式清單中，選取 [ **+ 從空白建立**]。
1. 在 [**從空白建立流程**] 底下，選取 [**從空白建立**]。

   ![從空白建立新流程](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. 在連接器和觸發程序搜尋頁面上中，選取 [觸發程序]****。
1. 在 [觸發程序]**** 底下，選取 [週期]****。
1. 在 [**週期**] 視窗中，將 [**間隔**] 的預設設定保留為 [ **1** ]。 從 [**頻率**] 下拉式清單中，選取 [**小時**]。

   >[!NOTE] 
   >這個範例會使用一小時的間隔。 但您可以選取最符合您業務需求的間隔和頻率。

   ![設定週期的1小時頻率](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. 選取 [ **+ 新增步驟**]。
1. 搜尋 [**取得過去時間**]，然後選取 **[選擇動作**] 底下的 [**取得過去時間**]。

    ![尋找並選取 [取得過去時間] 動作](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. 在 [**取得過去時間**] 視窗中，將 [**間隔**] 設定為**1**。  從 [**時間單位**] 下拉式清單中，選取 [**小時**]。
    >[!IMPORTANT] 
    >請確定**間隔**和**時間單位**符合您為週期設定的間隔和頻率（步驟8）。

    ![設定取得過去的時間間隔](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >您可以隨時檢查您的流程，以確認每個步驟都已正確設定：從 [流程] 功能表列選取 [ **flow 檢查**程式]。

在下一組步驟中，您會連接到您的儲存體資料表，並設定處理邏輯來處理新的潛在客戶。

1. 在 [**取得過去時間**] 步驟之後，選取 [ **+ 新增步驟**]，然後搜尋 [**取得實體**]。
1. 在 [動作]**** 底下，選取 [取得實體]****，然後選取 [顯示進階選項]****。
1. 在 [**取得實體**] 視窗中，填寫下欄欄位：

   - **資料表**：資料表儲存體的名稱。 下圖顯示已輸入 "MarketPlaceLeads"：

     ![為 Azure 資料表名稱挑選一個自訂值](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **篩選查詢**：當您選取此欄位時，[**取得過去時間**] 圖示會顯示在快顯視窗中。 選取 [**過去時間**]，以使用此值做為時間戳記來篩選查詢。 或者，您可以將下列函數貼入欄位中：
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![設定篩選查詢函數](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. 選取 [**新增步驟**] 以新增條件，以掃描資料表儲存體中是否有新的潛在客戶。

   ![使用「新增步驟」來新增掃描資料表儲存體的條件](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. 在 [**選擇動作**] 視窗中，選取 [**動作**]，然後選取 [**條件控制**]。

     ![新增條件控制項](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. 在 [**條件**] 視窗中，選取 **[選擇值**]，然後在快顯視窗中選取 [**運算式**]。
1. 在`length(body('Get_entities')?['value'])` ***fx***欄位中貼上。 選取 [確定]**** 來新增此函式。 



     ![將函式加入至條件](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. 根據條件的結果設定要採取的動作。

    1. 從下拉式清單中選取 [**大於**]。
   1. 輸入**0**做為值。

     ![根據條件結果設定動作](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. 如果條件解析為 "If no"，則不會執行任何動作。

    如果條件解析為「如果是」，則會觸發可連接 Office 365 帳戶以傳送電子郵件的動作：
   1. 選取 [**新增動作**]。
   1. 選取 [傳送電子郵件]****。
   1. 在 [**傳送電子郵件**] 視窗中，在下欄欄位中輸入資訊：

      - **To**：將會收到通知之每個人的電子郵件地址。
      - 主旨 **：電子**郵件的主旨。 例如：*新的潛在客戶！*
      - **Body**：您想要包含在每個電子郵件中的文字（選擇性）。 同時以函`body('Get_entities')?['value']`式的形式貼入插入潛在客戶資訊。

        >[!NOTE] 
        >您可以在電子郵件的本文中插入額外的靜態或動態資料點。

      ![設定電子郵件，以供潛在客戶通知使用](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. 選取 [儲存]  以儲存流程。 Microsoft Flow 會自動測試是否有錯誤。 如果沒有任何錯誤，您的流程會在儲存後開始執行。

    下圖顯示最終流程外觀的範例。

    [![最終流程順序](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    （*選取要放大的影像）。*

### <a name="manage-your-flow"></a>管理您的流程

您可以在流程執行後輕鬆地進行管理。 您可以完全控制您的流程。 例如，您可以停止流程、編輯流程、查看執行歷程記錄，以及取得分析。 下圖顯示流程管理選項。

 ![流程管理選項](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

流程會持續執行，直到您選取 [**關閉流程**] 為止。

如果您未收到任何潛在客戶電子郵件通知，表示您的資料表儲存體中未加入任何新的潛在客戶。
如果發生流程失敗，您會收到類似下列範例的電子郵件：

 ![流程失敗電子郵件通知](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>後續步驟

[設定潛在客戶](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
