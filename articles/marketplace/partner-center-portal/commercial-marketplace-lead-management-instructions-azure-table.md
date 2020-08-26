---
title: 使用 Azure 資料表儲存體進行潛在客戶管理 - Microsoft 商業市集
description: 了解如何使用 Azure 資料表儲存體來針對 Microsoft AppSource 和 Azure Marketplace 設定潛在客戶
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 08/25/2020
ms.openlocfilehash: db2bae9d9e1c9658937e725a04d919743ff9999e
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855731"
---
# <a name="use-azure-table-storage-to-manage-commercial-marketplace-leads"></a>使用 Azure 資料表儲存體來管理商業市集潛在客戶

如果您的客戶關係管理 (CRM) 系統在合作夥伴中心中尚未明確支援接收 Microsoft AppSource 和 Azure Marketplace 潛在客戶，您可以使用 Azure 資料表儲存體來處理這些潛在客戶。 您接著可以選擇匯出該資料，並將其匯入到您的 CRM 系統。 此文章說明如何建立 Azure 儲存體帳戶，並在該帳戶底下建立資料表。 此外，您可以使用 Power Automate 建立新的流程，來讓系統在您的供應項目接收到潛在客戶時傳送電子郵件通知。

## <a name="configure-an-azure-storage-account"></a>設定 Azure 儲存體帳戶

1. 如果您沒有 Azure 帳戶，您可以[建立免費的試用帳戶](https://azure.microsoft.com/pricing/free-trial/)。
1. 在您的 Azure 帳戶啟用之後，請登入 [Azure 入口網站](https://portal.azure.com)。
1. 在 Azure 入口網站中，使用下列程序建立儲存體帳戶：

    1. 在左側功能表列中，選取 [+ 建立資源]。 [新增] 窗格隨即在右側出現。
    1. 在 [新增] 窗格中，選取 [儲存體]。 [精選] 清單隨即在右側出現。
    1. 選取 [儲存體帳戶] 以開始建立帳戶。 請遵循[建立儲存體帳戶](../../storage/common/storage-quickstart-create-account.md?tabs=azure-portal) \(部分機器翻譯\) 中的指示。

        :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png" alt-text="建立 Azure 儲存體帳戶的步驟。":::

        如需儲存體帳戶的詳細資訊，請參閱[快速入門教學課程](../../storage/index.yml)。 如需儲存體定價的詳細資訊，請參閱[儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

1. 等候儲存體帳戶佈建完成。 此程序通常需要幾分鐘的時間。

## <a name="create-a-table-in-your-storage-account"></a>在儲存體帳戶中建立資料表

1. 從 Azure 入口網站 **的 [首頁** ] 中，選取 [ **查看您的所有資源** ] 以存取您的儲存體帳戶。 您也可以從 Azure 入口網站的左側功能表中選取 [所有資源]。

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png" alt-text="存取您的 Azure 儲存體帳戶。":::

1. 從您的儲存體帳戶窗格，選取 [存取金鑰] 並複製該金鑰的 [連接字串] 值。 請儲存此值，因為其為您必須在發佈入口網站中加以提供，以便接收您 Azure Marketplace 供應項目之潛在客戶的「儲存體帳戶連接字串」。

    以下是連接字串的範例。

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png" alt-text="Azure 儲存體金鑰。":::


1. 從您的儲存體帳戶窗格，選取 [資料表] 並選取 [+ 資料表] 以建立資料表。 輸入資料表的名稱，然後選取 [確定]。 請儲存此值，因為如果您想要設定流程以在接收到潛在客戶時接收電子郵件通知，便會需要此值。

    ![Azure 資料表](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    您可以使用 [Azure 儲存體總管](https://archive.codeplex.com/?p=azurestorageexplorer) \(英文\) 或任何其他工具，來檢視儲存體資料表中的資料。 您也可以將 Azure 資料表中的資料匯出。

## <a name="optional-use-power-automate-to-get-lead-notifications"></a>(選擇性) 使用 Power Automate 來取得潛在客戶通知

您可以使用 [Power Automate](https://docs.microsoft.com/flow/) 來在每次有潛在客戶新增至您的 Azure 儲存體資料表時自動通知。 如果您沒有帳戶，您可以[註冊免費的帳戶](https://flow.microsoft.com/)。

### <a name="lead-notification-example"></a>潛在客戶通知範例

此範例會建立流程，以在有新的潛在客戶新增至 Azure 資料表儲存體時，自動傳送電子郵件通知。 此範例會設定在資料表儲存體更新時，定期於每小時傳送潛在客戶資訊。

1. 登入您的 Power Automate 帳戶。
1. 在左側列上，選取 [我的流程]。
1. 在頂端列上，選取 [+ 新增]。
1. 在下拉式清單中，選取 [已排程 - 從頭開始]。

   ![[我的流程] + [已排程 - 從頭開始]](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. 在 [建置預定流程] 視窗中，針對 [重複間隔] 選取 **1** 作為間隔，並選取 [小時] 作為頻率。 此外，如有需要，請為流程命名。 選取 [建立]。

   >[!NOTE]
   >雖然此範例使用一小時的間隔，但是您可以選取最適合您業務需求的間隔和頻率。

   ![建置預定流程](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. 選取 [+ 新步驟] 。
1. 在 [選擇動作] 視窗中，搜尋**取得過去時間**。 然後在 [動作] 底下，選取 [取得過去時間]。

   ![選擇動作](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. 在 [取得過去時間] 視窗中，將 [間隔] 設定為 **1**。 從 [時間單位] 下拉式清單中，選取 [小時]。

    >[!IMPORTANT]
    >請確定您在步驟 8 中所傳送的間隔及時間單位符合您在步驟 5 中針對 [週期] 所設定的 [間隔] 和 [頻率]。

    ![設定 [取得過去時間] 間隔](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP]
   >您可以隨時檢查您的流程，以確認每個步驟都已正確設定。 若要檢查您的流程，請從 [流程] 功能表列中選取 [流程檢查工具]。

   在下一組步驟中，您將會連線到您的資料表，並設定處理邏輯來處理新的潛在客戶。

1. 選取 [+ 新步驟] 。 然後在 [選擇動作] 視窗中，搜尋**取得實體**。
1. 在 [動作] 底下，選取 [取得實體 (Azure 資料表儲存體)]。
1. 在 [Azure 資料表儲存體] 視窗中，為下列方塊提供資訊，然後選取 [建立]：

    * **連線名稱**：為您正在此流程與資料表之間建立的連線提供具意義的名稱。
    * **儲存體帳戶名稱**：提供您資料表的儲存體帳戶名稱。 您可以在儲存體帳戶的 [存取金鑰] 頁面上找到此名稱。
    * **共用儲存體金鑰**：提供您資料表的儲存體帳戶金鑰值。 您可以在儲存體帳戶的 [存取金鑰] 頁面上找到此值。

      ![[Azure 資料表儲存體] 視窗](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   在您選取 [建立] 之後，[取得實體] 視窗隨即出現。 在這裡，請選取 [顯示進階選項]，然後為下列方塊提供資訊：

   * **資料表**：選取您資料表的名稱 (從[建立資料表](#create-a-table-in-your-storage-account))。 下圖顯示在此範例中選取 [資料表] 時的提示 `marketplaceleads` 。

     ![[取得實體] 視窗](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **篩選查詢**：選取此方塊，然後將下列函式貼到方塊中：`Timestamp gt datetime'@{body('Get_past_time')}'`

     ![[取得實體]，[篩選查詢] 方塊](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. 您已完成針對 Azure 資料表連線的設定，現在請選取 [新增步驟] 以新增條件來在 Azure 資料表中掃描新的潛在客戶。

1. 在 [選擇動作] 視窗中，選取 [動作]。 然後選取 [條件控制項]。

    ![[選擇動作] 視窗](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. 在 [條件] 視窗中，選取 [選擇值]。 然後在快顯視窗中選取 [運算式]。

1. 將 `length(body('Get_entities')?['value'])` 貼入 [fx] 方塊。 選取 [確定] 來新增此函式。

1. 若要完成設定條件：
    1. 從下拉式清單中選取 [大於]。
    2. 輸入 **0** 作為值。

        ![[條件] 視窗](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   在接下來幾個步驟中，您會設定根據條件的結果所要採取的動作：

   * 如果條件解析為 [如果否]，則不執行任何動作。
   * 如果條件解析為 **If yes**，觸發連接 Office 365 帳戶傳送電子郵件的動作。

1. 在 [如果是] 底下，選取 [新增動作]。

    ![[條件] 視窗，[如果是]，[新增動作]](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. 選取 [傳送電子郵件 (Office 365 Outlook)]。

    ![[條件] 視窗，[如果是]，[傳送電子郵件]](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >若要使用不同的電子郵件提供者，請改為搜尋並選取 [傳送電子郵件通知 (郵件)] 作為動作。 雖然上述指示是示範如何使用 Office 365 Outlook 進行設定，但適用於不同電子郵件提供者的指示也相當類似。

1. 在[Office 365 Outlook] 視窗中，為下列方塊提供資訊：

    1. **收件者**：輸入每個將會收到此通知的人的電子郵件地址。
    1. **主體**：提供電子郵件的主旨。 其中一個範例是**新的潛在客戶！**
    1. **主體**：新增您想要納入每封電子郵件的文字 (選擇性)，然後貼到 `body('Get_entities')?['value']` 中。

    >[!NOTE]
    >您可以將額外的靜態或動態資料點插入到這封電子郵件的本文。

    ![[條件] 視窗，[如果是]，[Office 365 Outlook] 視窗](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. 選取 [儲存] 以儲存流程。 Power Automate 會自動測試流程是否有錯誤。 如果沒有任何錯誤，您的流程會在儲存之後開始執行。

下圖顯示最終流程外觀的範例。

![最終流程的範例](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>管理您的流程

在流程開始執行後加以管理是件很容易的工作。 您可以完全控制您的流程。 例如，您可以停止流程、編輯流程、查看執行歷程記錄，以及取得分析。 下圖顯示可用來管理流程的選項。

 ![管理流程](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

流程會繼續執行，直到您使用 [關閉流程] 選項停止為止。

如果您未收到任何潛在客戶電子郵件通知，表示新的潛在客戶還未新增至 Azure 資料表。 如果有任何流程失敗，您將會收到如此範例所示的電子郵件。

 ![流程失敗電子郵件通知](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>設定供應項目以將潛在客戶傳送至 Azure 資料表

當您準備好在發行入口網站中設定供應項目的潛在客戶管理資訊時，請遵循下列步驟。

1. 移至供應項目的 [供應項目設定] 頁面。

1. 在 [潛在客戶] 區段底下，選取 [連線]。

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-table/customer-leads.png" alt-text="潛在客戶":::

1. 在 [連線詳細資料] 快顯視窗中，選取 [Azure 資料表] 作為 [潛在客戶目的地]。 
     ![潛在客戶管理，[連線詳細資料]](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

1. 將您在先前步驟中所建立之 Azure 儲存體帳戶的連接字串貼到 [儲存體帳戶連接字串] 方塊中。
     ![潛在客戶管理，[連線詳細資料]，儲存體帳戶](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

1. **連絡人電子郵件**：提供公司收到新的潛在客戶時，會收到電子郵件通知之員工的電子郵件地址。 您可以提供多個電子郵件，方法是以分號分隔。

1. 選取 [確定]。

為確定您已成功連線到潛在客戶目的地，請選取 [驗證] 按鈕。 如果成功，即會在潛在客戶目的地中看到測試潛在客戶。

>[!NOTE]
>您必須完成供應項目的其餘設定並加以發佈，才能接收到該供應項目的潛在客戶。

產生潛在客戶時，Microsoft 便會將該潛在客戶傳送到 Azure 資料表。 如果您已設定流程，系統也會將電子郵件傳送到您所設定的電子郵件地址。
