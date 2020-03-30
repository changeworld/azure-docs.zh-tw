---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174006"
---
#### <a name="to-enter-maintenance-mode"></a>進入維護模式
1. 在序列主控台功能表中，選擇選項 1 ([以完整存取權限登入]****)。
2. 輸入密碼。 預設密碼為 **Password1**。
3. 在命令提示字元中，輸入：
   
     `Enter-HcsMaintenanceMode`
4. 您將會看到警告訊息，告知您維護模式將中斷所有 I/O 要求並提供與 Azure 傳統入口網站的連線，而系統將提示您進行確認。 輸入 **Y** 會進入 [維護] 模式。
   
    這兩個控制站都將重新啟動。 完成重新啟動時，會出現另一個訊息，指出裝置處於維護模式。

