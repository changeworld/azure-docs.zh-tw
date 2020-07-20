---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 48a3326dbe0e9eed4a5490e720248555586d189c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67174031"
---
### <a name="to-take-a-backup"></a>建立備份

1. 移至您的 StorSimple 裝置管理員服務。 從表格式的裝置清單中，選取並按一下您的裝置，然後按一下 [所有設定]****。 在 [設定]**** 刀鋒視窗中，移至 [設定] > [管理] > [備份原則]****。

    ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu1.png)

2. 在 [備份原則]**** 刀鋒視窗中，按一下 [+ 新增原則]****。

    ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu2.png)

3. 在 [建立備份原則]**** 刀鋒視窗中，為備份原則提供包含 3 到 150 個字元的名稱。

4. 選取要備份的磁碟區。 如果您選取多個磁碟區，這些磁碟區就會群組在一起，以建立可在當機時保持一致的備份。

    ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu4.png)

5. 在 [新增第一個排程]**** 刀鋒視窗中︰

    1. 選取備份類型。 如需更快速的還原，請選取 [**本機**快照]。 針對 [資料復原]，選取 [**雲端**快照]。
    2. 指定備份頻率 (以分鐘、小時、天或週為單位)。
    3. 選取保留時間。 保留選項會根據備份頻率而定。 例如，針對每日原則，可以指定保留幾週，而針對每月原則，則可指定保留幾個月。
    4. 選取備份原則的開始時間和日期。
    5. 按一下 [確定]**** 可建立備份原則。

        ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu5.png) 

6. 按一下 [建立]**** 可開始建立備份原則。 成功建立備份原則時，您會收到通知。 備份原則清單也會加以更新。
      
      ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu9.png)
      
      您現在擁有的備份原則會建立磁碟區資料的排程備份。




