---
title: 寄回 Azure 資料箱的教學課程 | Microsoft Docs
description: 了解如何將您的 Azure 資料箱寄給 Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 03fe40cea5891e87f42f0d6a50eedee80a68f25e
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83200528"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>教學課程：送回 Azure 資料箱，並確認資料上傳至 Azure

::: zone-end

::: zone target="chromeless"

## <a name="return-data-box-and-verify-data-upload-to-azure"></a>送回資料箱，並確認資料上傳至 Azure

::: zone-end

::: zone target="docs"

本教學課程將說明如何送回 Azure 資料箱，並確認資料已上傳至 Azure。

在本教學課程中，您將了解如下所列的主題：

> [!div class="checklist"]
> * Prerequisites
> * 準備寄送
> * 將資料箱寄送給 Microsoft
> * 確認資料上傳至 Azure
> * 清除資料箱的資料

## <a name="prerequisites"></a>Prerequisites

在您開始前，請確定：

- 您已完成[教學課程：將資料複製到 Azure 資料箱並確認](data-box-deploy-copy-data.md)。 
- 複製作業已完成。 如果複製作業正在進行，則無法執行寄送準備。

## <a name="prepare-to-ship"></a>準備寄送

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

資料複製完成之後，您就可以準備寄送裝置。 裝置到達 Azure 資料中心時，資料就會自動上傳到 Azure。

## <a name="prepare-to-ship"></a>準備寄送

在您準備寄送之前，請確定複製作業已完成。

1. 前往本機 Web UI 中的 [準備寄送]  頁面，並開始準備寄送。 
2. 從本機 Web UI 關閉裝置。 從裝置移除纜線。 

後續步驟則取決於您退回裝置的地點。

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>寄回資料箱

確保裝置的資料副本完整且 [準備寄送]  執行成功。 根據您寄送裝置的區域，程序會有所不同。

::: zone-end

## <a name="in-us-canada-europe"></a>[美國、加拿大、歐洲](#tab/in-us-canada-europe)

如果在美國、加拿大或歐洲退回裝置，請執行下列步驟。

1. 確定裝置的電源已關閉，然後移除纜線。 
2. 纏繞裝置隨附的電源線，並將其安全地放在裝置背後。
3. 請確定出貨標籤已顯示在電子筆墨顯示器上，並安排貨運業者取貨。 如果標籤損毀、遺失或未顯示在電子筆墨顯示器上，請與 Microsoft 支援服務連絡。 若支援人員提出建議，則您可以前往 Azure 入口網站中的 [概觀] > [下載出貨標籤]  。 下載出貨標籤並貼在裝置上。 
4. 如果要送回裝置，請安排由 UPS 取貨。 若要排定取貨時間：

    - 致電給當地的 UPS (國家/地區特定的免付費電話號碼)。
    - 在您的電話中提供反向出貨追蹤號碼，如 E-ink 顯示或您列印出的標籤中所示。
    - 若未提供追蹤號碼，UPS 將在取貨期間要求您支付額外的費用。

    除了排定取貨時間，您也可以在最接近的托運地點托運該資料箱。
4. 一旦貨運業者收取資料箱並進行掃描，入口網站的訂單狀態會更新為 [已取貨]  。 此外，也會顯示追蹤識別碼。

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>確認資料上傳至 Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>清除資料箱的資料
 
完成上傳到 Azure 之後，資料箱會根據 [NIST SP 800-88 修訂 1 指導方針](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)清除磁碟中的資料。

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]



::: zone-end


## <a name="in-australia"></a>[澳大利亞](#tab/in-australia)

澳洲的 Azure 資料中心有額外的安全性通知。 所有送達的貨物都必須有預先通知。 在澳洲寄送可採取下列步驟。


1. 保留用來寄送裝置以供退貨的原始外盒。
2. 確保裝置的資料副本完整且 [準備寄送]  執行成功。
3. 關閉裝置電源並移除纜線。
4. 纏繞裝置隨附的電源線，並將其安全地放在裝置背後。
5. 透過 [DHL 連結](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference)線上預約取貨服務。

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>確認資料上傳至 Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>清除資料箱的資料
 
完成上傳到 Azure 之後，資料箱會根據 [NIST SP 800-88 修訂 1 指導方針](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)清除磁碟中的資料。

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="in-japan"></a>[日本](#tab/in-japan) 

1. 保留用來寄送裝置以供退貨的原始外盒。
2. 關閉裝置電源並移除纜線。
3. 纏繞裝置隨附的電源線，並將其安全地放在裝置背後。
4. 在理貨單上寫下貴公司名稱和地址資訊，作為您的寄件者資訊。
5. 使用下列電子郵件範本傳送電子郵件給 Quantium Solutions。

    - 如果日本郵局運費到付託運單未隨附或遺失，請在這封電子郵件註明。 Quantium Solutions (Japan) 會要求日本郵局在取貨時提供理貨單。
    - 如果您有多個訂單，請透過電子郵件確保每件都會順利取貨。

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

3. 在預約取貨時間後，接收來自 Quantium Solutions 的電子郵件確認。 電子郵件確認也會包含運費到付理貨單的資訊。

如有需要，您可以透過下列資訊連絡 Quantium Solutions 支援人員 (日文)： 

- 電子郵件：Customerservice.JP@quantiumsolutions.com 
- 電話：03-5755-0150 

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>確認資料上傳至 Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>清除資料箱的資料
 
完成上傳到 Azure 之後，資料箱會根據 [NIST SP 800-88 修訂 1 指導方針](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)清除磁碟中的資料。

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="in-singapore"></a>[新加坡](#tab/in-singapore) 

1. 保留用來寄送裝置以供退貨的原始外盒。
2. 記下追蹤號碼 (您可以在資料箱本機 Web UI 的 [準備寄送] 頁面上看到參考編號)。 準備出貨步驟成功完成之後，即會顯示此號碼。 從此頁面下載出貨標籤，並貼到包裝盒上。 
3. 關閉裝置電源並移除纜線。
4. 纏繞裝置隨附的電源線，並將其安全地放在裝置背後。 
5. 使用下列包含追蹤號碼的電子郵件範本，傳送電子郵件給 SingPost 客戶服務。

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pick-up - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > 針對在工作天收到的預約要求：
   > - 在下午 3 點之前，取貨是在下一個工作天上午 9 點到下午 1 點。
   > - 在下午 3 點之後，取貨是在下一個工作天下午 2 點到下午 6 點。  

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>確認資料上傳至 Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>清除資料箱的資料
 
完成上傳到 Azure 之後，資料箱會根據 [NIST SP 800-88 修訂 1 指導方針](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)清除磁碟中的資料。

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end


<!--## [In Korea](#tab/in-korea) 

1. Retain the original box used to ship the device for return shipment.
2. Note down the tracking number (shown as reference number on the Prepare to Ship page of the Data Box local web UI). This is available after the prepare to ship step successfully completes. Download the shipping label from this page and paste on the packing box. 
3. Power off the device and remove the cables.
4. Spool and securely place the power cord that was provided with the device in the back of the device. 

Request pickup  
If consignment note is present:  

1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number to arrange for a collection.
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.  
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.  
4. You will receive an email confirmation for the pickup schedule.  

Exception process
If the consignment note is not present:
1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number. Specify that you need a new consignment note to arrange for a collection. Provide sender (customer), receiver information (Azure datacenter), and reference number (service request number).
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.
4. You get a verbal confirmation if request is made via telephone.  
::: zone target="chromeless"

## Verify data upload to Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## Erasure of data from Data Box
 
Once the upload to Azure is complete, the Data Box erases the data on its disks as per the [NIST SP 800-88 Revision 1 guidelines](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end
-->

## <a name="self-managed"></a>[自我管理](#tab/in-selfmanaged) 

如果您在日本、新加坡、韓國和西歐使用資料箱，並已在建立訂單期間選取自我管理寄送選項，請遵循下列指示。 

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

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>確認資料上傳至 Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>清除資料箱的資料
 
完成上傳到 Azure 之後，資料箱會根據 [NIST SP 800-88 修訂 1 指導方針](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)清除磁碟中的資料。

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

