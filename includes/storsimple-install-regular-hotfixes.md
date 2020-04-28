---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "67174011"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>透過 Windows PowerShell for StorSimple 安裝一般 Hotfix
1. 連接到裝置序列主控台。 如需詳細資訊，請參閱[步驟 1：連接到序列主控台](../articles/storsimple/storsimple-update-device.md#step1)。
2. 在序列主控台功能表中。選取選項 1 [以完整存取權限登入]****。 輸入密碼。 預設密碼為 **Password1**。
3. 在命令提示字元中，輸入：
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > 這個命令只適用於一般的 Hotfix。 您只需在某一個控制站上執行此命令，就會更新這兩個控制站。
    > 您可能在更新程序期間注意到控制站容錯移轉。不過，容錯移轉並不會影響系統的可用性或運作。

4. 出現提示時，請提供包含 Hotfix 檔案的網路共用資料夾所在路徑。
5. 系統將提示您進行確認。 請輸入 **Y** 繼續進行 Hotfix 安裝。

