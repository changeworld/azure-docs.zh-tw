---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 02274bacb66a33ef54e07bc8113d7db46d4d5296
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174169"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>若要新增 StorSimple 備份原則

1. 請移至 StorSimple 裝置，並按一下 [備份原則]****。

2. 在 [備份原則]**** 刀鋒視窗中，從命令列按一下 [+ 新增原則]****。
   
    ![新增備份原則](./media/storsimple-8000-add-backup-policy-u2/addbupol1.png)

3. 在 [建立備份原則]**** 刀鋒視窗中，執行下列步驟︰
   
   1. [選取裝置]**** 會根據您所選取的裝置自動填入。
   
   2. 指定包含 3 到 150 個字元的備份**策略名稱**。 建立原則之後，您就無法重新命名原則。
       
   3. 若要將磁碟區指派給此備份原則，請選取 [新增磁碟區]****，然後從表格式的磁碟區清單中按一下核取方塊，將一或多個磁碟區指派給此備份原則。

       ![新增備份原則](./media/storsimple-8000-add-backup-policy-u2/addbupol2.png)

   4. 若要定義此備份原則的排程，請按一下 [第一個排程]****，然後修改下列參數︰

       ![新增備份原則](./media/storsimple-8000-add-backup-policy-u2/addbupol3.png)

       1. 針對 [快照集類型]****，請選取 [雲端]**** 或 [本機]****。

       2. 指示備份頻率（指定數位，然後從下拉清單中選擇 **"天"** 或 **"周**"。

       3. 輸入保留排程。

       4. 輸入備份原則的開始日期與時間。

       5. 按一下 [確定]**** 可定義排程。

   5. 按一下 [建立]**** 可建立備份原則。

       ![新增備份原則](./media/storsimple-8000-add-backup-policy-u2/addbupol4.png)
   
   6. 建立備份原則時，您會收到通知。 新增的原則會以表格式檢視顯示在 [備份原則]**** 刀鋒視窗。

       ![新增備份原則](./media/storsimple-8000-add-backup-policy-u2/addbupol7.png)

