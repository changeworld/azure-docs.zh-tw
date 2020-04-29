---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "67174507"
---
針對您的特定情況，請遵循這些步驟。

### <a name="unregister-a-connected-process-server"></a>取消註冊已連線的進程伺服器

1. 以系統管理員身分建立進程伺服器的遠端連線。
2. 在 [**控制台**] 中，開啟 [**程式] > 卸載程式**。
3. 將程式**Microsoft Azure Site Recovery 行動服務/主要目標伺服器**卸載。
4. **Site Recovery 設定/進程伺服器 Microsoft Azure**卸載程式。
5. 卸載步驟3和4中的程式之後，Site Recovery 設定 **/進程伺服器**相依性卸載 Microsoft Azure。

### <a name="unregister-a-disconnected-process-server"></a>取消註冊已中斷連線的進程伺服器

只有在無法恢復已安裝進程伺服器的機器時，才使用這些步驟。

1. 以系統管理員身分登入設定伺服器。
2. 開啟系統管理命令提示字元，並流覽`%ProgramData%\ASR\home\svsystems\bin`至。
3. 執行此命令以取得一或多個進程伺服器的清單。

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. 否：進程伺服器序號。
    - IP/名稱：執行進程伺服器之電腦的 IP 位址和名稱。
    - 心跳：進程伺服器電腦的最後一個信號。
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. 指定您想要取消註冊之進程伺服器的序號。
5. 取消註冊進程伺服器會從系統中移除其所有詳細資料，並顯示訊息：**已成功將伺服器名稱註冊> （伺服器 IP 位址）**

