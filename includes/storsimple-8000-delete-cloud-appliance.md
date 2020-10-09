---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ac708eb2ac79a74b8f4e09a7306a42665b3aca94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67174036"
---
#### <a name="to-delete-a-cloud-appliance"></a>將雲端設備刪除

1. 登入 Azure 入口網站。
2. 您只能刪除不包含資料的已停用裝置。 請先刪除裝置上的資料，或者也可以將磁碟區容器中的資料[容錯移轉](../articles/storsimple/storsimple-8000-device-failover-cloud-appliance.md)到另一個裝置。 資料一旦刪除後，您就可以停用裝置。
3. 在 StorSimple 裝置管理員服務頁面中，按一下 [裝置]****，然後選取裝置。 按一下滑鼠右鍵，然後選取 [停用]****。
4. 在停用裝置後，以滑鼠右鍵按一下裝置並選取 [刪除]****。

    ![選取已停用的裝置，然後按一下 [刪除]](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance1.png)

5. 輸入裝置名稱以確認刪除。 刪除裝置之後，會更新裝置清單。

    ![確認刪除](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance2.png)

6. 將裝置刪除之後，您會收到通知。

    ![裝置刪除成功通知](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance4.png)

7. 裝置清單會更新以表示已刪除的裝置。

    ![更新的裝置清單](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance5.png)
