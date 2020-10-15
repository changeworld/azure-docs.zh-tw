---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: e4b366075cb16f62a0e16b5b06da6fb19ffefdb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67174644"
---
1. 登入「資料箱」裝置。 確定裝置已解除鎖定。

    ![資料箱儀表板](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. 移至 [設定網路介面]  。 記下用來連線至用戶端之網路介面的裝置 IP 位址。

    ![資料箱儀表板](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. 移至 [連線並複製]  並按一下 [Rest]  。

    ![資料箱儀表板](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. 從 [存取儲存體帳戶並上傳資料]  對話方塊中，複製 [Blob 服務端點]  。

    ![資料箱儀表板](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. 以系統管理員身分啟動 [記事本]，然後開啟位於 `C:\Windows\System32\Drivers\etc` 的 **hosts** 檔案。
6. 將下列項目新增至 **hosts** 檔案：`<device IP address> <Blob service endpoint>`
7. 使用下圖以供參考。 儲存 **hosts** 檔案。

    ![資料箱儀表板](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
