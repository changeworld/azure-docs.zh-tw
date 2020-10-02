---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: bff9457220efbb6a6b318550a5b2dd9af4ae230f
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361379"
---
針對您的特定情況，請遵循這些步驟。

### <a name="unregister-a-connected-process-server"></a>取消註冊已連線的處理序伺服器

1. 以系統管理員身分建立處理序伺服器的遠端連線。
2. 在 [控制台] 中開啟 [程式集] > [解除安裝程式]。
3. 解除安裝 **Microsoft Azure Site Recovery 行動服務/主要目標伺服器**程式。
4. 解除安裝 **Microsoft Azure Site Recovery Configuration/Process Server** 程式。
5. 將步驟 3 和 4 的程式解除安裝之後，您就可以解除安裝 **Microsoft Azure Site Recovery Configuration/Process Server 相依項目**。

### <a name="unregister-a-disconnected-process-server"></a>取消註冊已中斷連線的處理序伺服器

只有在無法恢復已安裝處理序伺服器的機器時，才使用這些步驟。

1. 以系統管理員身分登入組態伺服器。
2. 開啟系統管理命令提示字元，並瀏覽至 `%ProgramData%\ASR\home\svsystems\bin`。
3. 執行此命令以取得一或多個處理序伺服器的清單。

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. 否：處理序伺服器序號。
    - IP/名稱：執行處理序伺服器的電腦 IP 位址和名稱。
    - Heartbeat：來自處理序伺服器機器的最後一個活動訊號。
    ![螢幕擷取畫面會顯示您處理序伺服器相關資訊的純文字顯示，以及「請選擇上述其中一部伺服器以取消註冊」的文字。(media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. 指定您想要取消註冊的處理序伺服器序號。
5. 取消註冊處理序伺服器會從系統中移除其所有詳細資料，並顯示下列訊息：**已成功取消註冊伺服器名稱 > (server-IP-address)**

