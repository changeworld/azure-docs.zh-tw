---
title: 使用 Azure 資料表設定 Microsoft 商業 marketplace 潛在客戶管理
description: 瞭解如何使用 Azure 資料表來管理來自 Microsoft AppSource 和 Azure Marketplace 的潛在客戶。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 9814b03e348fc807c04364afbf027369f917670a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131134"
---
# <a name="configure-lead-management-by-using-an-azure-table"></a>使用 Azure 資料表來設定潛在客戶管理

如果合作夥伴中心未明確支援您的客戶關係管理（CRM）系統來接收 Microsoft AppSource 和 Azure Marketplace 潛在客戶，您可以使用 Azure 資料表來處理這些潛在客戶。 然後您可以選擇匯出資料，並將它匯入到您的 CRM 系統。 本文中的指示會引導您完成在該帳戶下建立 Azure 儲存體帳戶和 Azure 資料表的程式。 此外，您可以使用 [電源自動化] 來建立新的流程，以在您的供應專案收到潛在客戶時傳送電子郵件通知。

## <a name="configure-an-azure-table"></a>設定 Azure 資料表

1. 如果您沒有 Azure 帳戶，您可以[建立免費的試用帳戶](https://azure.microsoft.com/pricing/free-trial/)。
1. 當您的 Azure 帳戶處於作用中狀態時，請登入[Azure 入口網站](https://portal.azure.com)。
1. 在 Azure 入口網站中，使用下列程式來建立儲存體帳戶：

    1. 選取左側功能表列中的 [ **+ 建立資源**]。 **新**的窗格會出現在右側。
    1. 在 [**新增**] 窗格中選取 [**儲存體**]。 **精選**清單會出現在右側。
    1. 選取 [**儲存體帳戶**] 以開始建立帳戶。 依照[建立儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)中的指示進行。

        ![建立 Azure 儲存體帳戶的步驟](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        如需儲存體帳戶的詳細資訊，請參閱[快速入門教學](https://docs.microsoft.com/azure/storage/)課程。 如需儲存體價格的詳細資訊，請參閱[儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

1. 等到您的儲存體帳戶布建完成。 此程式通常需要幾分鐘的時間。 然後選取 [**查看您的所有資源**]，從 Azure 入口網站的**首頁**存取您的儲存體帳戶。 您也可以從 Azure 入口網站的左側功能表列中選取 [**所有資源**]。

    ![存取您的 Azure 儲存體帳戶](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

1. 從 [儲存體帳戶] 窗格中，選取 [**存取金鑰**]，並複製金鑰的**連接字串**值。 儲存此值，因為它是**儲存體帳戶連接字串**值，您必須在發佈入口網站中提供，才能接收 Azure Marketplace 供應專案的潛在客戶。 

    以下是連接字串的範例。

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    ![Azure 儲存體金鑰](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

1. 從 [儲存體帳戶] 窗格中，選取 [資料表]，然後選取 [ **+ 資料表** **]** 以建立資料表。 輸入資料表的名稱，然後選取 **[確定]**。 儲存此值的原因是，如果您想要設定流程在收到潛在客戶時接收電子郵件通知，您將會需要它。

    ![Azure 資料表](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    您可以使用[Azure 儲存體總管](https://archive.codeplex.com/?p=azurestorageexplorer)或任何其他工具來查看儲存體資料表中的資料。 您也可以匯出 Azure 資料表中的資料。 

## <a name="optional-use-power-automate-with-an-azure-table"></a>選擇性使用 Azure 資料表的電源自動化 

您可以使用 [[電源自動化](https://docs.microsoft.com/flow/)]，在每次將潛在客戶新增至 Azure 資料表時自動執行通知。 如果您沒有帳戶，您可以[註冊免費帳戶](https://flow.microsoft.com/)。

### <a name="lead-notification-example"></a>潛在客戶通知範例

使用此範例作為指南，以建立簡單的流程，在新的潛在客戶新增至 Azure 資料表時，自動傳送電子郵件通知。 這個範例會設定當資料表儲存體已更新時，每小時傳送潛在客戶資訊的週期。

1. 登入您的 Power 自動化帳戶。
1. 在左側工具列上，選取 [**我的流程**]。
1. 在頂端列上，選取 [ **+ 新增**]。 
1. 在下拉式清單中，選取 [ **+ 排程--從空白**]。

   ![我的流程 + 已排程--從空白](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. 在 [**建立排程的流程**] 視窗中，針對 [**重複間隔**] 選取 [ **1** ] 做為頻率的 [間隔] 和 [**小時**]。 此外，如有需要，請提供流程名稱。 選取 [建立]  。

   >[!NOTE]
   >雖然此範例使用一個小時的間隔，但您可以選取最適合您業務需求的間隔和頻率。

   ![建立排定的流程](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. 選取 [+ 新步驟] ****。
1. 在 [**選擇動作**] 視窗中，搜尋 [**取得過去時間**]。 然後在 [**動作**] 底下，選取 [**取得過去時間**]。

   ![選擇動作](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. 在 [**取得過去時間**] 視窗中，將 [**間隔**] 設定為**1**。 從 [**時間單位**] 下拉式清單中，選取 [**小時**]。

    >[!IMPORTANT]
    >請確定您在步驟8中所傳送的間隔和時間單位，符合您在步驟5中為週期設定的間隔和頻率。

    ![設定取得過去時間間隔](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP] 
   >您可以隨時檢查您的流程，以確認每個步驟都已正確設定。 若要檢查您的流程，請從 [**流程**] 功能表列選取 [ **flow 檢查**程式]。

   在下一組步驟中，您將連接到 Azure 資料表，並設定處理邏輯來處理新的潛在客戶。

1. 在步驟8之後，選取 [ **+ 新增步驟**]。 然後在 [**選擇動作**] 視窗中搜尋 [**取得實體**]。
1. 在 [**動作**] 底下，選取 **[取得實體（Azure 表格儲存體）**]。
1. 在 [ **Azure 表格儲存體**] 視窗中，提供下列方塊的資訊，然後選取 [**建立**]：

    * 連線**名稱**：為您在此流程和 Azure 資料表之間建立的連接提供有意義的名稱。
    * **儲存體帳戶名稱**：提供 Azure 資料表的儲存體帳戶名稱。 您可以在儲存體帳戶的 [**存取金鑰**] 頁面上找到此名稱。
    * **共用儲存體金鑰**：為您的 Azure 資料表的存放區帳戶提供金鑰值。 您可以在儲存體帳戶的 [**存取金鑰**] 頁面上找到此值。

      ![Azure 表格儲存體視窗](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   選取 [**建立**] 之後，[**取得實體**] 視窗隨即出現。 在這裡，選取 [顯示] [ **advanced options**]，並提供下列方塊的資訊：

   * **資料表**：選取您的 Azure 資料表儲存體名稱（從「設定 Azure 資料表」一節中指示的步驟6）。 下圖顯示針對此範例選取 "marketplaceleads" 資料表時的提示。

     ![取得實體視窗](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **篩選查詢**：選取此方塊，然後將此函數貼入方塊中：`Timestamp gt datetime'@{body('Get_past_time')}'`

     ![取得實體，篩選查詢方塊](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. 既然您已完成設定 Azure 資料表的連線，請選取 [**新增步驟**] 來新增條件，以掃描 azure 資料表中的新潛在客戶。

1. 在 [**選擇動作**] 視窗中，選取 [**動作**]。 然後選取 [**條件控制**]。

    ![[選擇動作] 視窗](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. 在 [**條件**] 視窗中，選取 **[選擇值**]。 然後在快顯視窗中選取 [**運算式**]。

1. 貼`length(body('Get_entities')?['value'])`到**fx**方塊中。 選取 [確定]**** 來新增此函式。 

1. 若要完成設定條件：
    1. 從下拉式清單中選取 [**大於**]。
    2. 輸入**0**做為值。

        ![條件視窗](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   在接下來的幾個步驟中，您會根據條件的結果設定要採取的動作：

   * 如果 [條件] 解析為 [**否**]，則不執行任何動作。
   * 如果條件解析為 **If yes**，觸發連接 Office 365 帳戶傳送電子郵件的動作。 

1. 在 **[如果是]** 底下選取 [**新增動作**]。

    ![條件視窗，如果是，則新增動作](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. 選取 **[傳送電子郵件（Office 365 Outlook）**]。

    ![條件視窗，如果是，則傳送電子郵件](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >若要使用不同的電子郵件提供者，請搜尋並選取 [**傳送電子郵件通知（郵件）** ] 做為動作。 指示會示範如何使用 Office 365 Outlook 進行設定，但不同的電子郵件提供者的指示類似。

1. 在 [Office 365 Outlook] 視窗中，提供下列方塊的資訊：

    1. **若要**：輸入將取得此通知之每位使用者的電子郵件地址。
    1. 主旨 **：提供**電子郵件的主旨。 其中一個例子就是**新的潛在客戶！**
    1. **主體**：新增您想要包含在每個電子郵件中的文字（選擇性），然後貼`body('Get_entities')?['value']`上。

    >[!NOTE]
    >您可以將額外的靜態或動態資料點插入到這封電子郵件的本文。

    ![條件視窗，如果是，則為 Office 365 Outlook 視窗](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. 選取 [儲存]  以儲存流程。 電源自動化會自動測試流程是否有錯誤。 如果沒有任何錯誤，您的流程會在儲存後開始執行。

下圖顯示最終流程外觀的範例。

![最終流程的範例](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>管理您的流程

在您的流程執行後加以管理很容易。 您可以完全控制您的流程。 例如，您可以停止流程、編輯流程、查看執行歷程記錄，以及取得分析。 下圖顯示可用來管理流程的選項。

 ![管理流程](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

流程會繼續執行，直到您使用 [關閉流程]**** 選項停止為止。

如果您未收到任何潛在客戶電子郵件通知，這表示新的潛在客戶尚未新增至 Azure 資料表。 如果有任何流程失敗，您會收到類似此範例的電子郵件。

 ![流程失敗電子郵件通知](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>設定您的供應專案以將潛在客戶傳送至 Azure 資料表

當您準備好在發佈入口網站中為您的供應專案設定潛在客戶管理資訊時，請遵循下列步驟。

1. 移至供應專案的**供應專案設定**頁面。
1. 選取 [**潛在客戶管理**] 區段下的 **[連接]** 。
1. 在 [連線**詳細資料**] 快顯視窗中，選取 [ **Azure 資料表**] 作為 [**潛在客戶目的地**]。 在 [**儲存體帳戶連接字串**] 方塊中遵循先前的步驟，從您建立的 Azure 儲存體帳戶貼上連接字串。
1. **連絡人電子郵件**：為公司中的人員提供電子郵件，當收到新的潛在客戶時，應該會收到電子郵件通知。 您可以提供多個電子郵件，方法是以分號分隔。
1. 選取 [確定]  。

若要確定您已成功連接到潛在客戶目的地，請選取 [**驗證**] 按鈕。 如果成功，您會在潛在客戶目的地中擁有測試潛在客戶。

>[!NOTE]
>您必須先完成供應專案的其餘部分設定並加以發佈，才能接收供應專案的潛在客戶。

當產生潛在客戶時，Microsoft 會將潛在客戶傳送至 Azure 資料表。 如果您已設定流程，電子郵件也會傳送至您所設定的電子郵件地址。

![潛在客戶管理](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![潛在客戶管理，連線詳細資料](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![潛在客戶管理，連線詳細資料儲存體帳戶](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

