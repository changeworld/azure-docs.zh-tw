---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: d01cf90c42efbe611748027d0ea47ff8af3e5621
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553075"
---
1. 登入「資料箱」裝置。 確定裝置已解除鎖定。

    ![螢幕擷取畫面顯示裝置顯示為 [已解除鎖定] 的儀表板。](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. 移至 [設定網路介面]  。 記下用來連線至用戶端之網路介面的裝置 IP 位址。

    ![螢幕擷取畫面顯示您可以在其中看到 IP 位址的網路設定。](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. 移至 [連線並複製]  並按一下 [Rest]  。

    ![螢幕擷取畫面顯示 [連線並複製] 窗格，您可以在其中選取 [REST] 做為存取設定。](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. 從 [存取儲存體帳戶並上傳資料]  對話方塊中，複製 [Blob 服務端點]  。

    ![螢幕擷取畫面顯示 [存取儲存體帳戶並上傳資料] 對話方塊，您可以在其中複製 Blob 服務端點。](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. 以系統管理員身分啟動 [記事本]，然後開啟位於 `C:\Windows\System32\Drivers\etc` 的 **hosts** 檔案。
6. 將下列項目新增至 **hosts** 檔案：`<device IP address> <Blob service endpoint>`
7. 使用下圖以供參考。 儲存 **hosts** 檔案。

    ![螢幕擷取畫面顯示已新增 IP 位址和 Blob 服務端點的「記事本」文件。](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
