---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67534585"
---
1. 建立與運行進程伺服器的電腦的遠端桌面連線。 
2. 運行 cspsconfigtool.exe 以啟動 Azure 網站恢復過程伺服器組態工具。
    - 首次登錄進程伺服器時，將自動啟動該工具。
    - 如果未自動打開，請按一下桌面上的快捷方式。

3. 在**佈建服務器 FQDN 或 IP**中 ，指定要向其註冊進程伺服器的佈建服務器的名稱或 IP 位址。
4. 在**佈建服務器埠**中，確保指定 443。 這是佈建服務器偵聽請求的埠。
5. 在 **"連接密碼"中**，指定設置佈建服務器時指定的密碼。 要查找密碼：
    -  在佈建服務器上，導航到網站恢復安裝資料夾 =*[home_svssystems]bin\**：
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - 運行以下命令以列印出當前密碼：
    ```
    genpassphrase.exe -n
    ```

6. 在**資料傳輸埠**中，保留預設值，除非您指定了自訂埠。

7. 按一下"**保存**設置"，然後註冊進程伺服器。

    
    ![註冊進程伺服器](./media/site-recovery-vmware-register-process-server/register-ps.png)
