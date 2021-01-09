---
title: 寄回匯出訂單中 Azure 資料箱的教學課程 | Microsoft Docs
description: 了解如何在匯出訂單完成後，將您的 Azure 資料箱寄送給 Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 4b5bbe19ddeda7c6b313186b7e24532a294f7253
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680847"
---
# <a name="tutorial-return-azure-data-box"></a>教學課程：退回 Azure 資料箱

本教學課程將說明如何送回 Azure 資料箱，並在裝置收到 Azure 資料之後清除資料。

在本教學課程中，您將了解如下所列的主題：

> [!div class="checklist"]
>
> * 必要條件
> * 準備寄送
> * 將資料箱寄送給 Microsoft
> * 清除資料箱的資料

## <a name="prerequisites"></a>必要條件

在您開始前，請確定：

* 您已完成[教學課程：從 Azure 資料箱複製資料](data-box-deploy-export-copy-data.md)。
* 複製作業已完成。 如果複製作業正在進行，則無法執行寄送準備。

## <a name="prepare-to-ship"></a>準備寄送

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

後續步驟則取決於您退回裝置的地點。

## <a name="ship-data-box-back"></a>寄回資料箱

確保裝置的資料複製已完成且 [準備寄送] 執行成功。 根據您寄送裝置的區域，程序會有所不同。

## <a name="us-canada-europe"></a>[美國、加拿大、歐洲](#tab/in-us-canada-europe)

如果在美國、加拿大或歐洲退回裝置，請執行下列步驟。

1. 確定裝置的電源已關閉，然後移除纜線。 
2. 纏繞裝置隨附的電源線，並將其安全地放在裝置背後。
3. 請確定出貨標籤已顯示在電子筆墨顯示器上，並安排貨運業者取貨。 如果標籤損毀、遺失或未顯示在電子筆墨顯示器上，請與 Microsoft 支援服務連絡。 若支援人員提出建議，則移至 Azure 入口網站中的 [概觀] > [下載出貨標籤]。 下載出貨標籤並貼在裝置上。 
4. 如果要送回裝置，請安排由 UPS 取貨。 若要排定取貨時間：

   - 致電給當地的 UPS (國家/地區特定的免付費電話號碼)。
   - 在您的電話中提供反向出貨追蹤號碼，如 E-ink 顯示或您列印出的標籤中所示。 若未提供追蹤號碼，UPS 將在取貨時要求您支付額外的費用。
   - 如果您在取貨時遇到任何問題，或 UPS 要求您支付額外費用，請連絡 Azure 資料箱營運中心。 傳送電子郵件至 [adbops@microsoft.com](mailto:adbops@microsoft.com)。

    除了排定取貨時間，您也可以在最接近的托運地點托運該資料箱。
4. 一旦貨運業者收取資料箱並進行掃描，入口網站的訂單狀態會更新為 [已取貨]。 此外，也會顯示追蹤識別碼。

## <a name="australia"></a>[澳大利亞](#tab/in-australia)

澳洲的 Azure 資料中心有額外的安全性通知。 所有送達的貨物都必須有預先通知。 在澳洲寄送可採取下列步驟。

1. 保留用來寄送裝置以供退貨的原始外盒。
2. 確保裝置的資料副本完整且 [準備寄送] 執行成功。
3. 關閉裝置電源並移除纜線。
4. 纏繞裝置隨附的電源線，並將其安全地放在裝置背後。
5. 透過 [DHL 連結](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference)線上預約取貨服務。

## <a name="japan"></a>[日本](#tab/in-japan)

1. 保留用來寄送裝置以供退貨的原始外盒。
2. 關閉裝置電源並移除纜線。
3. 纏繞裝置隨附的電源線，並將其安全地放在裝置背後。
4. 在理貨單上寫下貴公司名稱和地址資訊，作為您的寄件者資訊。
5. 使用下列電子郵件範本傳送電子郵件給 Quantium Solutions。

    * 如果日本郵局運費到付託運單未隨附或遺失，請在這封電子郵件註明。 Quantium Solutions (Japan) 會要求日本郵局在取貨時提供理貨單。
    * 如果您有多個訂單，請透過電子郵件確保每件都會順利取貨。

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

6. 在預約取貨時間後，接收來自 Quantium Solutions 的電子郵件確認。 電子郵件確認也會包含運費到付理貨單的資訊。

如有需要，您可以透過下列資訊連絡 Quantium Solutions 支援人員 (日文)： 

* 電子郵件：Customerservice.JP@quantiumsolutions.com
* 電話：03-5755-0150

## <a name="singapore"></a>[新加坡](#tab/in-singapore)

1. 保留用來寄送裝置以供退貨的原始外盒。
2. 記下追蹤號碼 (您可以在資料箱本機 Web UI 的 [準備寄送] 頁面上看到參考編號)。 準備出貨步驟成功完成之後，即會顯示此號碼。 從此頁面下載出貨標籤，並貼到包裝盒上。
3. 關閉裝置電源並移除纜線。
4. 纏繞裝置隨附的電源線，並將其安全地放在裝置背後。 
5. 使用下列包含追蹤號碼的電子郵件範本，傳送電子郵件給 SingPost 客戶服務。

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > 針對在工作天收到的預約要求：
   >
   > * 在下午 3 點之前，取貨是在下一個工作天上午 9 點到下午 1 點。
   > * 在下午 3 點之後，取貨是在下一個工作天下午 2 點到下午 6 點。  

## <a name="south-africa"></a>[南非](#tab/in-sa)

1. 保留用來包裝裝置以供送回的原始外盒。
2. 記下裝置的本機 Web UI 上所顯示的參考編號 (運貨單號碼)。 [準備寄送執行] 成功之後，就會顯示此編號。
3. 下載並列印可於裝置本機 Web UI 上找到的出貨標籤，並將其貼到運送包裹上。
4. 若要預約 DHL 取貨，請選擇下列其中一個選項：

    * 於下午 2:00 之前致電客戶服務連絡中心 (電話是 **+27(0) 11 9213600**)、選取第 1 個選項，然後指定運貨單號碼。
    * 使用下列範本，寄送電子郵件至 [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com)：

    ```output
    To: Priority.Support@dhl.com
    Subject: Pickup request for Microsoft Azure
    Body: Need pick up for the below shipment
      *  DHL tracking number (reference number/waybill number)
      *  Requested pickup date: yyyy/mm/dd;time:HH MM
    ```

    * 或者，您也可以將包裹拿到最近的 DHL 服務據點。

5. 如果您遇到任何問題，請寄送電子郵件至 [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com)，並於其中詳述您遇到的問題，然後在 [主旨:] 行放入運貨單號碼。 您也可以致電 +27(0)119213902。

## <a name="hong-kong"></a>[香港](#tab/in-hk)

1. 將要送回的裝置包裝到原始外盒內。
2. 記下裝置的本機 Web UI 上顯示的參考編號 (反向出貨追蹤號碼)。 [準備寄送執行] 成功之後，就會顯示此編號。
3. 下載並列印可於裝置本機 Web UI 上找到的出貨標籤，並將其貼到運送包裹上。
4. 纏繞裝置隨附的電源線，並將其安全地放在裝置背後。
5. 在上班時間 (星期一到星期五上午 9 點到下午 6 點) 撥打 **Quantium Solutions** 熱線，號碼是 **(852) 2318 1213**。  
6. 報上送回出貨標籤上的 Microsoft Azure Pickup 和參考編號與追蹤號碼 (位於條碼上方) 以便安排收貨。
7. 您會獲得排定取貨時間的口頭確認。 如果快遞人員未到府收貨，請撥打 Quantium Solutions 熱線以便尋求替代安排。
8. 在向 Quantium 預約取貨後，請使用下列範本向 [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com) 進行確認：

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

如果您遇到任何問題，請寄送電子郵件給 Data Box Operations Asia [adbo@microsoft.com](mailto:adbo@microsoft.com) 並於主旨標頭中提供工作名稱和所遇到的問題。

## <a name="self-managed"></a>[自我管理](#tab/in-selfmanaged)

如果您在日本、新加坡、韓國、印度、南非或西歐使用資料箱，並已在建立訂單期間選取自我管理寄送選項，請遵循下列指示。

1. 在此步驟成功完成之後，請記下資料箱本機 Web UI [準備寄送] 頁面上所顯示的授權碼。
2. 關閉裝置電源並移除纜線。 纏繞裝置隨附的電源線，並將其安全地放在裝置背後。
3. 當您準備好傳回裝置時，請使用下列範本將電子郵件傳送給 Azure 資料箱作業小組。
    
    ```
    To: adbops@microsoft.com 
    Subject: Request for Azure Data Box drop-off for order: ‘orderName’ 
    Body: 
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

---

## <a name="erasure-of-data-from-data-box"></a>清除資料箱的資料

當裝置到達 Azure 資料中心後，資料箱會根據 [NIST SP 800-88 修訂 1 指導方針](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)清除磁碟中的資料。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解相關主題，像是：

> [!div class="checklist"]
> * 必要條件
> * 準備寄送
> * 將資料箱寄送給 Microsoft
> * 清除資料箱的資料

請進入下一篇文章，以了解如何管理您的資料箱。

> [!div class="nextstepaction"]
> [透過 Azure 入口網站管理資料箱](./data-box-portal-admin.md)


