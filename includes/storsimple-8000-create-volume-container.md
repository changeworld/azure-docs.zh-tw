---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 467af776af95cf035121250fdcadd2fee65d9805
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "67174024"
---
#### <a name="to-create-a-volume-container"></a>建立磁碟區容器
1. 移至 StorSimple 裝置管理員服務，然後按一下 [裝置]****。 從表格式的裝置清單中，選取並按一下裝置。 

    ![磁碟區容器刀鋒視窗](./media/storsimple-8000-create-volume-container/createvolumecontainer1.png)

2. 在裝置儀表板中，按一下 [+ 新增磁碟區容器]****

    ![磁碟區容器刀鋒視窗](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

3. 在 [新增磁碟區容器]**** 刀鋒視窗中：
   
   1. 會自動選取裝置。
   2. 提供磁碟區容器的 [名稱]****。 名稱長度必須是 3 到 32 個字元。 一旦建立磁碟區容器，您就無法將它重新命名。
   3. 選取 [啟用雲端儲存體加密] **** ，以啟用從裝置傳送至雲端的資料加密。
   4. 提供並確認長度為 8 到 32 個字元的 [雲端儲存體加密金鑰]****。 裝置會使用這個金鑰來存取加密的資料。
   5. 選取要與這個磁碟區容器相關聯的 [儲存體帳戶]****。 您可以選擇現有的儲存體帳戶，或服務建立時所產生的預設帳戶。 您也可以使用 [新增]**** 選項，指定未連結至此服務訂用帳戶的儲存體帳戶。
   6. 如果您要取用所有的可用頻寬，請在 [指定頻寬]**** 下拉式清單中選取 [無限制]****。 您也可以將這個選項設為 [自訂]**** 來使用頻寬控制項，並指定介於 1 Mbps 到 1000 Mbps 之間的值。
      如果您有可用的頻寬使用量資訊，您可以藉由指定 [選取頻寬範本]****，嘗試根據排程來配置頻寬。 如需逐步程序，請參閱[新增頻寬範本](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template)。

      ![磁碟區容器刀鋒視窗](./media/storsimple-8000-create-volume-container/createvolumecontainer6b.png)
   7. 按一下頁面底部的 [新增]  。

        ![磁碟區容器刀鋒視窗](./media/storsimple-8000-create-volume-container/createvolumecontainer6.png)
   
       成功建立磁碟區容器時，您會收到通知。

       ![磁碟區容器建立通知](./media/storsimple-8000-create-volume-container/createvolumecontainer8.png)

   新建立的磁碟區容器會列在您裝置的磁碟區容器清單中。

   ![新增磁碟區容器刀鋒視窗](./media/storsimple-8000-create-volume-container/createvolumecontainer9.png)


