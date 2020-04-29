---
title: 從 SAP Business 倉儲載入資料
description: 使用 Azure Data Factory 從 SAP Business 倉儲（BW）複製資料
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/22/2019
ms.openlocfilehash: 96b23696164514ad2f16de72f0f76aa237ffce2e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415845"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 SAP Business 倉儲複製資料
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文說明如何使用 Azure Data Factory，透過 Open Hub 將資料從 SAP Business 倉儲（BW）複製到 Azure Data Lake Storage Gen2。 您可以使用類似的程式，將資料複製到其他[支援的接收資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。

> [!TIP]
> 如需從 SAP BW 複製資料的一般資訊（包括 SAP BW 開放式中樞整合和差異抽取流程），請參閱[使用 Azure Data Factory，透過 Open hub 從 SAP Business 倉儲複製資料](connector-sap-business-warehouse-open-hub.md)。

## <a name="prerequisites"></a>先決條件

- **Azure Data Factory**：如果您沒有，請遵循步驟來[建立資料](quickstart-create-data-factory-portal.md#create-a-data-factory)處理站。

- **SAP BW 開啟目的地類型為「資料庫資料表」的中樞目的地（OHD）**：若要建立 OHD，或檢查您的 OHD 是否已正確設定以進行 Data Factory 整合，請參閱本文的[SAP BW 開啟中樞目的地](#sap-bw-open-hub-destination-configurations)設定一節。

- **SAP BW 使用者需要下列許可權**：

  - 遠端函式呼叫（RFC）和 SAP BW 的授權。
  - **S_SDSAUTH**授權物件之「執行」活動的許可權。

- **具有 SAP .net connector 3.0 的[自我裝載整合執行時間（IR）](concepts-integration-runtime.md#self-hosted-integration-runtime) **。 請遵循下列設定步驟：

  1. 安裝和註冊自我裝載整合執行時間3.13 版或更新版本。 （本文稍後會說明此程式）。

  2. 從 SAP 的網站下載[64 位 Sap Connector For Microsoft .net 3.0](https://support.sap.com/en/product/connectors/msnet.html) ，並將它安裝在與自我裝載 IR 相同的電腦上。 在安裝期間，請確定您在 [**選擇性安裝程式步驟**] 對話方塊中選取 [將**元件安裝到 GAC** ]，如下圖所示：

     ![設定 SAP .NET Connector 對話方塊](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>從 SAP BW 開放式中樞執行完整複本

在 Azure 入口網站，移至您的資料處理站。 選取 [**作者 & 監視器**]，以在個別的索引標籤中開啟 Data Factory UI。

1. 在 [**現在就開始**吧] 頁面上，選取 [**資料複製**] 以開啟 [資料複製] 工具。

2. 在 [**屬性**] 頁面上，指定工作**名稱**，然後選取 **[下一步]**。

3. 在 [**來源資料存放區**] 頁面上，選取 [ **+ 建立新連接**]。 從連接器資源庫選取 [ **SAP BW 開啟中樞**]，然後選取 [**繼續**]。 若要篩選連接器，您可以在 [搜尋] 方塊中輸入**SAP** 。

4. 在 [**指定 SAP BW 開啟中樞**連線] 頁面上，依照下列步驟建立新的連接。

   ![建立 SAP BW 開啟中樞連結服務頁面](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. 從 [透過**整合運行**時間連線] 清單中，選取現有的自我裝載 IR。 或者，如果您還沒有的話，請選擇建立一個。

      若要建立新的自我裝載 IR，請選取 [ **+ 新增**]，然後選取 [**自我**裝載]。 輸入**名稱**，然後選取 **[下一步]**。 選取 [**快速安裝**] 以在目前的電腦上安裝，或遵循所提供的**手動設定**步驟。

      如[必要條件](#prerequisites)中所述，請確定您已將 SAP Connector for Microsoft .net 3.0 安裝在自我裝載 IR 執行所在的同一部電腦上。

   2. 填入 [SAP BW**伺服器名稱**、[**系統編號**]、[**用戶端識別碼]、** [**語言**] （如果不是**EN**）、[**使用者名稱**] 和 [**密碼**]。

   3. 選取 [**測試連接**] 以驗證設定，然後選取 **[完成]**。

   4. 隨即建立新的連接。 選取 [下一步]  。

5. 在 [**選取開啟中樞目的地**] 頁面上，流覽 SAP BW 中可用的開放中樞目的地。 選取要從中複製資料的 OHD，然後選取 **[下一步]**。

   ![選取 SAP BW 開啟中樞目的地資料表](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. 指定篩選準則（如果您需要的話）。 如果您的 OHD 只包含具有單一要求識別碼之單一資料傳輸程式（DTP）執行的資料，或者您確定您的 DTP 已完成，而您想要複製資料，請清除 [**排除最後一個要求**] 核取方塊。

   若要深入瞭解這些設定，請參閱本文的[SAP BW 開啟中樞目的地](#sap-bw-open-hub-destination-configurations)設定一節。 選取 [**驗證**] 以再次檢查將傳回的資料。 然後選取 [下一步]  。

   ![設定 SAP BW 開啟中樞篩選器](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. 在 [**目的地資料存放區**] 頁面上，選取 [ **+ 建立新的連接** > **] Azure Data Lake Storage Gen2** > **繼續**]。

8. 在 [**指定 Azure Data Lake Storage 連接**] 頁面上，依照下列步驟來建立連線。

   ![建立 ADLS Gen2 連結服務頁面](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. 從 [**名稱**] 下拉式清單中選取支援 Data Lake Storage Gen2 的帳戶。
   2. 選取 [完成]**** 以建立連線。 然後選取 [下一步]  。

9. 在 [**選擇輸出檔案或資料夾**] 頁面上，輸入**copyfromopenhub**作為輸出檔案夾名稱。 然後選取 [下一步]  。

   ![選擇輸出檔案夾頁面](media/load-sap-bw-data/choose-output-folder.png)

10. 在 [**檔案格式設定**] 頁面上，選取 **[下一步]** 以使用預設設定。

    ![指定接收格式頁面](media/load-sap-bw-data/specify-sink-format.png)

11. 在 [**設定**] 頁面上，展開 [**效能設定**]。 針對 [**複製平行**處理原則的程度] （例如5）輸入值，以平行方式從 SAP BW 載入。 然後選取 [下一步]  。

    ![設定複製設定](media/load-sap-bw-data/configure-copy-settings.png)

12. 在 [摘要]  頁面上，檢閱設定。 然後選取 [下一步]  。

13. 在 [**部署**] 頁面上，選取 [**監視**] 來監視管線。

    ![部署頁面](media/load-sap-bw-data/deployment.png)

14. 請注意，系統會自動選取頁面左側的 [**監視**] 索引標籤。 [**動作**] 資料行會包含 [查看活動-執行詳細資料] 的連結，以及重新執行管線。

    ![管線監視視圖](media/load-sap-bw-data/pipeline-monitoring.png)

15. 若要查看與管線執行相關聯的活動執行，請選取 [**動作**] 資料行中的 [**查看活動執行**]。 管線中只有一個活動 (複製活動)，所以您只會看到一個項目。 若要切換回 [管線-執行] 視圖，請選取頂端的 [**管線**] 連結。 選取 [重新整理]**** 可重新整理清單。

    ![活動監視畫面](media/load-sap-bw-data/activity-monitoring.png)

16. 若要監視每個複製活動的執行詳細資料，請選取 [**詳細資料**] 連結，這是 [活動-監視] 視圖中 [**動作**] 下方的眼鏡圖示。 可用的詳細資料包括從來源複製到接收的資料量、資料輸送量、執行步驟和持續時間，以及所使用的設定。

    ![活動監視詳細資料](media/load-sap-bw-data/activity-monitoring-details.png)

17. 若要查看**最大要求識別碼**，請回到 [活動監視] 視圖，然後選取 [**動作**] 底下的 [**輸出**]。

    ![活動輸出畫面](media/load-sap-bw-data/activity-output.png)

    ![活動輸出詳細資料檢視](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>從 SAP BW 開放式中樞進行增量複製

> [!TIP]
> 請參閱[SAP BW 開啟中樞連接器差異抽取流程](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow)，以瞭解 Data Factory 中的 SAP BW 開放式中樞連接器如何從 SAP BW 複製增量資料。 本文也可以協助您瞭解基本連接器設定。

現在，讓我們繼續從 SAP BW 開放式集線器設定增量複製。

增量複製會使用以**要求識別碼**為基礎的「高水位線」機制。 該識別碼會在 DTP SAP BW 開放式中樞目的地中自動產生。 下圖顯示此工作流程：

![累加式複製工作流程流程圖](media/load-sap-bw-data/incremental-copy-workflow.png)

在 [data factory] 的 [**開始**使用] 頁面上，選取 [**從範本建立管線**] 以使用內建範本。

1. 搜尋**SAP BW** ，以尋找並選取**從 SAP BW 到 Azure Data Lake Storage Gen2**範本的累加式複製。 此範本會將資料複製到 Azure Data Lake Storage Gen2。 您可以使用類似的工作流程，複製到其他接收類型。

2. 在範本的主頁面上，選取或建立下列三個連接，然後選取視窗右下角的 [**使用此範本**]。

   - **Azure blob 儲存體**：在此逐步解說中，我們會使用 azure blob 儲存體來儲存高水位線，這是*複製的最大要求識別碼*。
   - **SAP BW 開啟中樞**：這是用來複製資料的來源。 請參閱先前的完整複製逐步解說，以取得詳細設定。
   - **Azure Data Lake Storage Gen2**：這是要將資料複製到其中的接收。 請參閱先前的完整複製逐步解說，以取得詳細設定。

   ![SAP BW 範本的累加式複製](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. 此範本會產生包含下列三個活動的管線，並使其連結至成功：*查閱*、*資料複製*和*Web*。

   移至 [管線**參數**] 索引標籤。您會看到需要提供的所有設定。

   ![從 SAP BW 設定進行增量複製](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**：指定要從中複製資料的開放中樞資料表名稱。

   - **Data_Destination_Container**：指定要將資料複製到其中的目的地 Azure Data Lake Storage Gen2 容器。 如果容器不存在，則 Data Factory 複製活動會在執行期間建立一個。
  
   - **Data_Destination_Directory**：指定要複製資料的 Azure Data Lake Storage Gen2 容器下的資料夾路徑。 如果路徑不存在，則 Data Factory 複製活動會在執行期間建立路徑。
  
   - **HighWatermarkBlobContainer**：指定用來儲存高水位線值的容器。

   - **HighWatermarkBlobDirectory**：指定容器下的資料夾路徑來儲存高水位線值。

   - **HighWatermarkBlobName**：指定用`requestIdCache.txt`來儲存高水位線值的 blob 名稱，例如。 在 Blob 儲存體中，移至 HighWatermarkBlobContainer + HighWatermarkBlobDirectory + HighWatermarkBlobName 的對應路徑，例如*container/path/requestIdCache。* 建立具有內容0的 blob。

      ![Blob 內容](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**：在此範本中，我們會使用 WebActivity 來呼叫 Azure Logic Apps 來設定 Blob 儲存體中的高水位線值。 或者，您可以使用 Azure SQL Database 來儲存它。 使用預存程式活動來更新值。

      您必須先建立邏輯應用程式，如下圖所示。 然後，貼上**HTTP POST URL**。

      ![邏輯應用程式設定](media/load-sap-bw-data/logic-app-config.png)

      1. 移至 Azure 入口網站。 選取新的**Logic Apps**服務。 選取 [ **+ 空白邏輯應用程式**] 以移至**Logic Apps 設計**工具。

      2. **在收到 HTTP 要求時**，建立的觸發程式。 指定 HTTP 要求主體，如下所示：

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. 新增 [**建立 blob** ] 動作。 針對 [**資料夾路徑**] 和 [ **Blob 名稱**]，使用您先前在*HighWatermarkBlobContainer + HighWatermarkBlobDirectory*和*HighWatermarkBlobName*中設定的相同值。

      4. 選取 [儲存]  。 然後，複製**HTTP POST URL**的值，以在 Data Factory 管線中使用。

4. 提供 Data Factory 管線參數之後，**請選取** > **[檢查] [完成]** 以叫用執行來驗證設定。 或者，選取 [**發行**] 以發行所有變更，然後選取 [**新增觸發**程式] 來執行執行。

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW 開啟中樞目的地設定

本節介紹如何設定 SAP BW 端，以在 Data Factory 中使用 SAP BW 開放式中樞連接器來複製資料。

### <a name="configure-delta-extraction-in-sap-bw"></a>在 SAP BW 中設定差異解壓縮

如果您同時需要歷程記錄複製和增量複製，或是只有增量複製，請在 SAP BW 中設定差異提取。

1. 建立開啟的中樞目的地。 您可以在 [SAP 交易 RSA1] 中建立 OHD，這會自動建立必要的轉換和資料傳輸程式。 套用下列設定：

   - **ObjectType**：您可以使用任何物件類型。 在這裡，我們會使用**InfoCube**作為範例。
   - **目的地類型**：選取 [**資料庫資料表**]。
   - **資料表的索引鍵**：選取 [技術] [**金鑰**]。
   - [**解壓縮**]：選取 [**保留資料]，並將記錄插入資料表**。

   ![建立 SAP BW OHD 差異解壓縮對話方塊](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![建立 SAP BW OHD delta2 解壓縮對話方塊](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   您可能會增加適用于 DTP 的平行執行中 SAP 工作進程的數目：

   ![建立-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. 在處理常式鏈中排程 DTP。

   只有在尚未壓縮必要的資料列時，cube 的差異 DTP 才會運作。 請確定 BW cube 壓縮不是在 DTP 的開放中樞資料表之前執行。 最簡單的方法是將 DTP 整合到您現有的程式鏈。 在下列範例中，會將 DTP （OHD）插入到*調整* *（匯總匯總）和折*迭（cube 壓縮）步驟之間的程式鏈。

   ![建立 SAP BW 進程鏈流程圖](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>在 SAP BW 中設定完整解壓縮

除了差異解壓縮以外，您可能還想要完全解壓縮相同的 SAP BW InfoProvider。 如果您想要執行完整複本但不是增量，或想要重新同步處理[差異解壓縮](#resync-delta-extraction)，這通常適用。

同一個 OHD 不能有多個 DTP。 因此，您必須先建立額外的 OHD，再進行差異提取。

![建立 SAP BW OHD full](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

針對完整 load OHD，請選擇不同于差異解壓縮的選項：

- 在 OHD 中：將 [**解壓縮**] 選項設定為 [**刪除資料] 和 [插入記錄**]。 否則，當您在 BW 程式鏈中重複 DTP 時，將會多次解壓縮資料。

- 在 [DTP：將**解壓縮模式**設定為**完整**]。 建立 OHD 之後，您必須立即將自動建立的 DTP 從**差異**變更為**Full** ，如下圖所示：

   ![建立為「完整」解壓縮設定的 SAP BW OHD 對話方塊](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- 在 Data Factory 的 BW 開放式中樞連接器中： [關閉] [**排除上一個要求**]。 否則，將不會解壓縮任何內容。

您通常會以手動方式執行完整的 DTP。 或者，您可以建立完整 DTP 的程式鏈。 這通常是獨立于現有程式鏈之外的個別鏈。 不論是哪一種情況，請*先確定 DTP 已完成，再使用 Data Factory 複製開始解壓縮*。 否則，只會複製部分資料。

### <a name="run-delta-extraction-the-first-time"></a>第一次執行差異解壓縮

第一個差異解壓縮在技術上是*完整的解壓縮*。 根據預設，SAP BW 開放式中樞連接器會在複製資料時排除最後一個要求。 針對第一個差異解壓縮，Data Factory 複製活動不會解壓縮任何資料，直到後續的 DTP 在具有個別要求識別碼的資料表中產生差異資料為止。 有兩種方式可以避免這種情況：

- 關閉第一個差異解壓縮的 [**排除最後一個要求**] 選項。 第一次啟動差異解壓縮之前，請確定第一個差異 DTP 已完成。
-  使用 resyncing 差異解壓縮的程式，如下一節所述。

### <a name="resync-delta-extraction"></a>重新同步處理差異解壓縮

下列案例會變更 SAP BW cube 中的資料，但差異 DTP 不會考慮：

- SAP BW 選擇性刪除（使用任何篩選準則的資料列）
- SAP BW 要求刪除（錯誤的要求）

SAP 開放式中樞目的地不是資料超市控制的資料目標（在2015之後，所有 SAP BW 都支援封裝）。 因此，您可以從 cube 刪除資料，而不需要變更 OHD 中的資料。 接著，您必須使用 Data Factory 重新同步處理 cube 的資料：

1. 在 Data Factory 中執行完整的解壓縮（藉由使用 SAP 中的完整 DTP）。
2. 刪除開放中樞資料表中的所有資料列，以取得差異 DTP。
3. 將差異 DTP 的狀態設定為 [已**提取**]。

在此之後，所有後續的差異 DTPs 和 Data Factory 差異提取都會如預期般運作。

若要將差異 DTP 的狀態設定為 [已**提取**]，您可以使用下列選項，以手動方式執行差異 dtp：

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>後續步驟

深入瞭解 SAP BW 開放式中樞連接器支援：

> [!div class="nextstepaction"]
>[SAP Business 倉儲開放式中樞連接器](connector-sap-business-warehouse-open-hub.md)
