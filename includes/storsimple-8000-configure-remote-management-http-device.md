---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 473bc0a58fe49c7f454c81402b57ddce7fc745b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67174040"
---
#### <a name="to-configure-remote-management-on-cloud-appliance"></a>在雲端設備上設定遠端管理

1. 在 StorSimple 裝置管理員服務中，按一下 [裝置]****。 選取並按一下連線到服務之裝置清單中的雲端設備。
    ![StorSimple 選取雲端設備](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage1.png)

2. 移至 [設定] > [安全性]**** 可開啟 [安全性設定]**** 刀鋒視窗。

     ![StorSimple 安全性設定](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage2.png)

3. 移至 [遠端管理]**** 區段。 按一下 [遠端管理]**** 方塊。
     ![StorSimple 遠端管理](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage3.png)

4. 在 [遠端管理]**** 刀鋒視窗中：

    1. 確認 [啟用遠端管理]**** 已啟用。
    2. 預設是透過 HTTPS 來連接。 您可以選擇使用 HTTP 來連線。 只有受信任的網路上才接受透過 HTTP 來連接。 確認 HTTP 已啟用。
    3. 從分頁頂端的命令列中，按一下 **.。。然後按一下** [ **下載憑證** ] 以下載遠端系統管理憑證。 您可以指定要儲存此檔案的位置。 此憑證應安裝於您要用來連線至雲端設備的用戶端或主機電腦上。

        ![遠端管理刀鋒視窗](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage4.png)
5. 按一下 [儲存]**** 以及出現提示時，確認變更。