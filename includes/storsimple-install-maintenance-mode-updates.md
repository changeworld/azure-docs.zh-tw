---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "67174014"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>透過 Windows PowerShell for StorSimple 安裝維護模式更新
1. 如果您尚未這樣做，請存取裝置序列主控台並選取選項 1 [使用完整存取權登入] ****。 
2. 輸入密碼。 預設密碼為 **Password1**。
3. 在命令提示字元中，輸入：
   
     `Get-HcsUpdateAvailability` 
4. 系統將通知您是否有可用的更新，以及更新是否為破壞性或非破壞性更新。 若要套用干擾性更新，您需要使裝置進入維護模式。 如需相關指示，請參閱[步驟 2：進入維護模式](../articles/storsimple/storsimple-update-device.md#step2)。
5. 當裝置處於維護模式時，在命令提示字元中輸入： `Start-HcsUpdate`
6. 系統將提示您進行確認。 當您確認更新之後，它們將安裝於您目前正在存取的控制站上。 安裝更新之後，將重新啟動控制站。 
7. 監視更新的狀態。 輸入：
   
    `Get-HcsUpdateStatus`
   
    如果 `RunInProgress` 是 `True`，表示更新仍在進行中。 如果 `RunInProgress` 是 `False`，表示已完成更新。  
8. 當目前的控制站上已安裝更新並重新啟動後，請連接到另一個控制站，然後執行步驟 1 到 6。
9. 更新這兩個控制站之後，結束維護模式。 如需相關指示，請參閱[步驟 4：結束維護模式](../articles/storsimple/storsimple-update-device.md#step4)。

