---
title: 透過 Azure 入口網站管理 Azure 資料箱、Azure Data Box Heavy |Microsoft Docs
description: 說明如何使用 Azure 入口網站來管理您的 Azure 資料箱和 Azure Data Box Heavy。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: 978d1de158f93b06d08ff93605405573000b6b6e
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92123710"
---
# <a name="use-the-azure-portal-to-administer-your-azure-data-box-and-azure-data-box-heavy"></a>使用 Azure 入口網站來管理您的 Azure 資料箱和 Azure Data Box Heavy

本文同時適用于 Azure 資料箱和 Azure Data Box Heavy。 本文說明一些可以在 Azure 資料箱裝置上執行的複雜工作流程和管理工作。 您可以透過 Azure 入口網站或透過本機 web UI 來管理資料箱裝置。

本文著重於可使用 Azure 入口網站執行的工作。 使用 Azure 入口網站來管理訂單、管理資料箱裝置，並在繼續完成時追蹤訂單狀態。

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]


## <a name="cancel-an-order"></a>取消訂單

在下過訂單之後，您可能基於各種原因需要取消訂單。 

針對匯入和匯出訂單，您只能在訂單處理之前取消訂單。 一旦處理訂單且已備妥資料箱裝置之後，就無法取消訂單。

執行下列步驟以取消訂單。

1.  移至 [概觀 > 取消]****。

    ![取消訂單 1](media/data-box-portal-admin/cancel-order1.png)

2.  填寫取消訂單的原因。  

    ![取消訂單 2](media/data-box-portal-admin/cancel-order2.png)

3.  一旦訂單取消後，入口網站會更新訂單狀態，並將它顯示為 [已取消]****。

## <a name="clone-an-order"></a>複製訂單

在某些情況下，複製會很有用。 例如，某個使用者已使用資料箱來傳送一些資料。 當產生更多資料時，就需要另一個資料箱裝置將該資料傳輸到 Azure。 在此情況下，只需複製相同的訂單即可。

> [!IMPORTANT]
> 複製無法用於匯出訂單。 您只能複製匯入訂單。

請執行下列步驟來複製匯入順序。

1.  移至 [概觀 > 複製]****。 

    ![複製訂單 1](media/data-box-portal-admin/clone-order1.png)

2.  所有的訂單詳細資料保持不變。 訂單名稱是原始訂單名稱附加上「-複製」**。 請選取核取方塊，以確認您已檢閱隱私權資訊。 按一下 [建立]  。

會在幾分鐘內建立複製，且入口網站更新會顯示新的訂單。


## <a name="delete-order"></a>刪除訂單

在完成訂單時，您可能需要刪除訂單。 訂單會包含您的個人資訊，例如姓名、地址和連絡資訊。 刪除訂單時，會將此個人資訊刪除。

您只能將已完成或已取消的訂單刪除。 執行下列步驟來刪除訂單。

1. 移至 **所有資源**。 搜尋您的訂單。

2. 按一下您想要刪除的訂單，並移至 [概觀]****。 從命令列按一下 [刪除]****。

    ![刪除資料箱訂單 1](media/data-box-portal-admin/delete-order1.png)

3. 當系統提示時，請輸入訂單的名稱來確認訂單刪除。 按一下 **[刪除]** 。

## <a name="download-shipping-label"></a>下載出貨標籤

如果您資料箱的電子墨水顯示器無法正常運作且無法顯示寄回出貨標籤，您可能需要下載出貨標籤。 Data Box Heavy 沒有電子筆墨顯示，因此此工作流程不適用於 Data Box Heavy。

執行下列步驟，以下載出貨標籤。

1.  移至 [概觀 > 下載出貨標籤]****。 此選項只有在裝置出貨之後才可使用。 

    ![下載出貨標籤](media/data-box-portal-admin/download-shipping-label.png)

2.  這會下載下列退貨標籤。 儲存標籤並將它列印出來。將標籤折迭並插入裝置上的清除套管中。 請確保標籤清楚可見。 將裝置上來自先前運送的貼紙全數移除。

    ![範例出貨標籤](media/data-box-portal-admin/example-shipping-label.png)

## <a name="edit-shipping-address"></a>編輯交貨地址

一旦下單後，您可能需要編輯交貨地址。 此功能只有在裝置寄出之前才能使用。 在裝置寄出之後，此選項便無法使用。

執行下列步驟以編輯訂單。

1. 移至 [訂單詳細資料 > 編輯交貨地址]****。

    ![編輯交貨地址 1](media/data-box-portal-admin/edit-shipping-address1.png)

2. 編輯並驗證交貨地址，然後儲存變更。

    ![編輯交貨地址 2](media/data-box-portal-admin/edit-shipping-address2.png)

## <a name="edit-notification-details"></a>編輯通知詳細資料

您可能需要變更要接收訂單狀態電子郵件的使用者。 例如，需要在裝置已送達或收取時通知的使用者。 資料複製完成時，可能需要通知另一位使用者，以便他在將來源中的資料刪除之前，可以確認資料位於 Azure 儲存體帳戶。 在這些情況下，您可以編輯通知詳細資料。

請執行下列步驟來編輯通知詳細資料。

1. 移至 [訂單詳細資料 > 編輯通知詳細資料]****。

    ![編輯通知詳細資料 1](media/data-box-portal-admin/edit-notification-details1.png)

2. 您現在可以編輯通知詳細資料，然後儲存變更。
 
    ![編輯通知詳細資料 2](media/data-box-portal-admin/edit-notification-details2.png)


## <a name="download-order-history"></a>下載訂單記錄

當資料箱訂單處理完成後，裝置磁碟上的資料即會清除。 當裝置清除完成後，您即可在 Azure 入口網站中下載訂單記錄。

執行下列步驟以下載訂單記錄。

1. 在您的資料箱訂單中，移至 [概觀]****。 確認訂單已完成。 如果訂單已完成，且裝置清除也已完成，請移至 [訂單詳細資料]****。 [下載訂單記錄]**** 選項可供使用。

    ![下載訂單記錄](media/data-box-portal-admin/download-order-history-1.png)

2. 按一下 [下載訂單記錄]****。 在下載的記錄中，您會看到貨運公司追蹤記錄的記錄。 會有兩組記錄對應至 Data Box Heavy 上的兩個節點。 如果您捲動到此記錄的底部，將可看到下列項目的連結：
    
   - **複製記錄** -在資料從 data Box 複製到您的 Azure 儲存體帳戶期間錯誤的檔案清單， (匯入順序) 或 *反向* (匯出順序) 。
   - **Audit logs** -包含有關如何在 Azure 資料中心以外時開啟和存取資料箱上的共用的資訊。
   - 匯**入順序中的 BOM**檔案-具有檔案清單 (也稱為檔案資訊清單) ，您可以在**寄送準備**期間下載這些檔案，並具有檔案名、檔案大小和檔案總和檢查碼。
   - **輸出順序中的詳細資訊記錄** ：當資料從 Azure 儲存體帳戶複製到資料箱時，具有檔案名、檔案大小和總和檢查碼計算的檔案清單。

   以下是來自匯入順序的訂單歷程記錄範例。

    ```output
    -------------------------------
    Microsoft Data Box Order Report
    -------------------------------
    Name                                               : DataBoxTestOrder                              
    StartTime(UTC)                                     : 10/31/2018 8:49:23 AM +00:00                       
    DeviceType                                         : DataBox                                           
    -------------------
    Data Box Activities
    -------------------
    Time(UTC)                 | Activity                       | Status          | Description  
    
    10/31/2018 8:49:26 AM     | OrderCreated                   | Completed       |                                                   
    11/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |                                                   
    11/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 11/3/2018 1:36:43        PM at AMSTERDAM-NLD                                                                                
    11/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 11/4/2018        8:23:30 PM at AMSTERDAM-NLD                                                                        
    11/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time :          11/4/2018 11:43:34 PM at AMSTERDAM-NLD                                                               
    11/5/2018 1:38:20 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LEIPZIG-DEU. Local Time :        11/5/2018 1:38:20 AM at LEIPZIG-DEU                                                                
    11/5/2018 2:31:07 AM      | ShippingToCustomer             | InProgress      | Processed at LEIPZIG-DEU. Local Time : 11/5/2018          2:31:07 AM at LEIPZIG-DEU                                                                            
    11/5/2018 4:05:58 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LEIPZIG-DEU. Local Time :            11/5/2018 4:05:58 AM at LEIPZIG-DEU                                                                    
    11/5/2018 4:35:43 AM      | ShippingToCustomer             | InProgress      | Transferred through LUTON-GBR. Local Time :              11/5/2018 4:35:43 AM at LUTON-GBR                                                                         
    11/5/2018 4:52:15 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LUTON-GBR. Local Time :              11/5/2018 4:52:15 AM at LUTON-GBR                                                                        
    11/5/2018 5:47:58 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 10/5/2018 5:47:58 AM at LONDON-HEATHROW-GBR                                                
    11/5/2018 6:27:37 AM      | ShippingToCustomer             | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            11/5/2018 6:27:37 AM at LONDON-HEATHROW-GBR                                                            
    11/5/2018 6:39:40 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 11/5/2018 6:39:40 AM at LONDON-HEATHROW-GBR                                                    
    11/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in LAMBETH-GBR. Local        Time : 11/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
    11/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 11/5/2018            9:13:24 AM at LAMBETH-GBR                                                                               
    11/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 11/5/2018        12:03:04 PM at LAMBETH-GBR                                                                          
    1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25        PM at LAMBETH-GBR                                                                                       
    1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at LAMBETH-GBR. Local Time : 1/25/2019          8:03:55 PM at LAMBETH-GBR                                                                            
    1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in LAMBETH-GBR. Local Time :            1/25/2019 8:04:58 PM at LAMBETH-GBR                                                                    
    1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
    1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
    1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR                                                   
    1/26/2019 2:17:10 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility BRUSSELS-BEL. Local Time        : 1/26/2019 2:17:10 PM at BRUSSELS-BEL                                                              
    1/26/2019 2:31:57 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        2:31:57 PM at BRUSSELS-BEL                                                                          
    1/26/2019 3:37:53 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        3:37:53 PM at BRUSSELS-BEL                                                                          
    1/27/2019 11:01:45 AM     | ShippingToDataCenter           | InProgress      | Departed Facility in BRUSSELS-BEL. Local Time :          1/27/2019 11:01:45 AM at BRUSSELS-BEL                                                                 
    1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD.            Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
    1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019            9:07:57 AM at AMSTERDAM-NLD                                                                             
    1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019            1:35:56 PM at AMSTERDAM-NLD                                                                            
    1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019        2:57:48 PM at AMSTERDAM-NLD                                                                         
    1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |                                              
    1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully                  
    1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.                               
    1/29/2019 4:55:00 PM      | DataCopy                       | Started         |                                                 
    2/2/2019 7:07:34 PM       | DataCopy                       | Completed       | Copy Completed.                                   
    2/4/2019 7:47:32 PM       | SecureErase                    | Started         |                                                  
    2/4/2019 8:01:10 PM       | SecureErase                    | Completed       | Azure Data Box:DEVICESERIALNO has been sanitized          according to NIST 800-88 Rev 1.                                                                       

    ------------------
    Data Box Log Links
    ------------------

    Account Name         : Gus                                                       
    Copy Logs Path       : databoxcopylog/DataBoxTestOrder_CHC533180024_CopyLog_73a81b2d613547a28ecb7b1612fe93ca.xml
    Audit Logs Path      : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024
    BOM Files Path       : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024      
    ```

    然後，您可以移至儲存體帳戶並檢視複製記錄。

![登入儲存體帳戶](media/data-box-portal-admin/logs-in-storage-acct-2.png)

您也可以檢視包含稽核記錄和 BOM 檔案的監管鏈記錄。

![儲存體帳戶中的記錄2](media/data-box-portal-admin/logs-in-storage-acct-1.png)

## <a name="view-order-status"></a>檢視訂單狀態

當入口網站中的裝置狀態出現變更時，系統會以電子郵件通知您。

### <a name="statuses-for-import-order"></a>匯入順序的狀態

以下是匯入順序的狀態。

|訂單狀態 |描述 |
|---------|---------|
|訂購時間     | 已成功下單。 <br>如果裝置可供使用，Microsoft 會識別要寄送的裝置，並準備該裝置。 <br> 如果裝置無法立即提供使用，系統將會在裝置可供使用時處理該訂單。 訂單的處理可能需花費數天到數個月的時間。 如果無法在 90 天內完成訂單，系統將會取消該訂單並通知您。         |
|已處理     | 訂單處理已完成。 裝置已根據您的訂單在資料中心做好寄送準備。         |
|已分派     | 訂單已出貨。 在入口網站中使用顯示在訂單上的追蹤識別碼來追蹤運輸狀態。        |
|已傳遞     | 裝置已寄送至訂單中指定的地址。        |
|已取貨     |您寄回的裝置已經由快遞公司收取並掃描。         |
|已收到     | 您的裝置已由 Azure 資料中心收到並掃描。 <br> 確認裝置之後，裝置上傳便會開始。      |
|資料複製     | 資料複製進行中。 在 Azure 入口網站中追蹤您訂單的複製進度。 <br> 等候資料複製完成。 |
|已完成       |已順利完成訂單。<br> 從伺服器刪除內部部署資料之前，請確認資料已位於 Azure 中。         |
|已完成，但發生錯誤| 資料複製已完成，但在複製期間發生錯誤。 <br> 使用 Azure 入口網站中提供的路徑來檢閱複製記錄。 [當上傳完成但發生錯誤時，請參閱複製記錄檔的範例](./data-box-logs.md#upload-completed-with-errors)。   |
|已完成，但出現警告| 資料複製已完成，但您的資料已修改。 資料有不重要的 blob 或檔案名錯誤，這些錯誤是藉由變更檔案或 blob 名稱來修正。 <br> 使用 Azure 入口網站中提供的路徑來檢閱複製記錄。 記下您的資料中所做的修改。 [當上傳完成但出現警告時，請參閱複製記錄檔的範例](./data-box-logs.md#upload-completed-with-warnings)。   |
|已取消            |訂單便會取消。 <br> 可能是您取消訂單或發現錯誤，而服務將訂單取消。 如果無法在 90 天內完成訂單，系統也將會取消該訂單並通知您。     |
|清除 | 裝置上的資料已清除。 當訂單記錄在 Azure 入口網站中可供下載時，即視為已完成裝置清除。|

### <a name="statuses-for-export-order"></a>匯出順序的狀態

以下是匯出順序的狀態。

|訂單狀態 |描述 |
|---------|---------|
|訂購時間     | 已成功放置匯出順序。 <br>如果裝置可供使用，Microsoft 會識別要寄送的裝置，並準備該裝置。 <br> 如果裝置無法立即提供使用，系統將會在裝置可供使用時處理該訂單。 訂單的處理可能需花費數天到數個月的時間。 如果無法在 90 天內完成訂單，系統將會取消該訂單並通知您。         |
|已取消            |訂單便會取消。 <br> 您已取消訂單 (您只能在訂單處理之前取消) 或發生錯誤，且服務已取消訂單。 如果無法在 90 天內完成訂單，系統也將會取消該訂單並通知您。     |
|已處理     | 訂單處理已完成。 根據您的訂單，裝置會針對資料中心內的資料複製做好準備。 建立裝置共用。         |
|正在複製資料     | 從指定的 Azure 儲存體帳戶複製到裝置的資料正在進行中。 在 Azure 入口網站中追蹤您訂單的複製進度。 <br> 等候資料複製完成。 |
|複製已完成     | 從指定的 Azure 儲存體帳戶複製到裝置的資料已完成。 詳細資訊記錄檔 (如果依序) 啟用選項，且在您的儲存體帳戶中建立複製記錄檔。 詳細資訊記錄檔包含所有檔案的相關資訊， (名稱、路徑、計算總和檢查碼) 複製到裝置。 複製記錄檔包含複製程式的摘要，包括因任何錯誤而無法複製的檔案清單。<br> 儲存體帳戶資料會維持不變。 |
|複製已完成，但發生錯誤| 資料複製已完成，但在複製期間發生錯誤。 <br> 使用 Azure 入口網站提供的路徑，檢查 Azure 儲存體帳戶中的複製記錄檔。 [當下載完成但發生錯誤時，請參閱複製記錄檔的範例](./data-box-logs.md#upload-completed-with-errors)。   |
|複製已完成，但出現警告| Azure 儲存體帳戶的資料複製已完成，但資料有非重大錯誤。 <br> 使用 Azure 入口網站中提供的路徑來檢閱複製記錄。 記下非重大錯誤。 [當下載完成但出現警告時，請參閱複製記錄檔的範例](./data-box-logs.md#upload-completed-with-warnings)。   |
|複製失敗，發生錯誤| Azure 儲存體帳戶的資料複製失敗，且訂單已結束。 將不會寄送裝置。<br> 使用 Azure 入口網站提供的路徑，檢查 Azure 儲存體帳戶中的複製記錄檔。 [當下載失敗且發生錯誤時，請參閱複製記錄檔的範例](./data-box-logs.md#upload-completed-with-errors)。   |
|已分派     |訂單已出貨。 在入口網站中使用顯示在訂單上的追蹤識別碼來追蹤運輸狀態。        |
|已傳遞     |裝置已寄送至訂單中指定的地址。        |
|已取貨     |您寄回的裝置已經由快遞公司收取並掃描。         |
|已收到     | 您的裝置已由 Azure 資料中心收到並掃描。 <br> 會檢查出貨。      |
|已完成           |訂單已完成。     |
|清除 | 裝置上的資料已清除。 當訂單記錄在 Azure 入口網站中可供下載時，即視為已完成裝置清除。|

> [!NOTE]
> 如果從 Azure 儲存體帳戶將資料匯出到資料箱的複製作業完成，但發生錯誤或警告，則裝置仍會寄送。 只有在複製失敗時，才會終止訂單，而裝置也不會寄出。


如果使用自我管理的傳送，則在複製完成之後，以及在您收到裝置之前，您將會看到下列狀態 (而不是上表所述的) ：

|訂單狀態 |描述 |
|---------|---------|
|準備好在 Azure 資料中心取貨      |裝置已備妥可在 Azure 資料中心上挑選。        |
|已取貨    |您已挑選裝置。         |
|準備好在 Azure 資料中心接收     |您可以在 Azure 資料中心接收裝置。        |
|已收到     |裝置會在 Azure 資料中心收到。      |





## <a name="next-steps"></a>下一步

- 瞭解如何針對 [資料箱和 Data Box Heavy 問題進行疑難排解](data-box-troubleshoot.md)。