---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174507"
---
根據您的具體情況，請遵循這些步驟。

### <a name="unregister-a-connected-process-server"></a>取消註冊已連接的進程伺服器

1. 以管理員身份建立與進程伺服器的遠端連線。
2. 在**控制台**中，打開**程式>卸載程式**。
3. 卸載程式微軟**Azure 網站恢復移動服務/主目標伺服器**。
4. 卸載程式微軟**Azure 網站恢復配置/進程伺服器**。
5. 卸載步驟 3 和步驟 4 中的程式後，卸載**Microsoft Azure 網站恢復配置/進程伺服器依賴項**。

### <a name="unregister-a-disconnected-process-server"></a>取消註冊斷開連接的進程伺服器

僅當無法恢復安裝進程伺服器的電腦時，才使用這些步驟。

1. 以管理員身份登錄佈建服務器。
2. 打開管理命令提示符，然後流覽到`%ProgramData%\ASR\home\svsystems\bin`。
3. 運行此命令以獲取一個或多個進程伺服器的清單。

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. 否：進程伺服器序號。
    - IP/名稱：運行進程伺服器的電腦的 IP 位址和名稱。
    - 活動訊號：來自進程伺服器電腦的最後一個活動訊號。
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. 指定要取消註冊的進程伺服器的序號。
5. 取消註冊進程伺服器從系統中刪除其所有詳細資訊，並顯示消息：**成功未註冊的伺服器名稱>（伺服器 IP 位址）**

