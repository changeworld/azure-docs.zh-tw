---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67534585"
---
1. 建立與執行處理序伺服器所在電腦的遠端桌面連線。 
2. 執行 cspsconfigtool.exe 以啟動 Azure Site Recovery 處理序伺服器設定工具。
    - 此工具會在您第一次登入處理序伺服器時自動啟動。
    - 如果未自動開啟，請在桌面上按一下其鍵盤快速鍵。

3. 在 [設定伺服器 FQDN 或 IP] 中，指定要用來註冊處理序伺服器的設定伺服器名稱或 IP 位址。
4. 在 [組態伺服器連接埠] 中，確定已指定 443。 這是組態伺服器用於接聽要求的連接埠。
5. 在 [連線複雜密碼] 中，指定設定組態伺服器時要指定的複雜密碼。 若要尋找複雜密碼：
    -  在組態伺服器上，瀏覽至 Site Recovery 安裝資料夾 * *\home\svssystems\bin\** ：
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - 執行下列命令以列印出目前的複雜密碼：
    ```
    genpassphrase.exe -n
    ```

6. 在 [資料傳輸連接埠] 中，除非您已指定自訂連接埠，否則請保留預設值。

7. 按一下 [儲存] 以儲存設定，然後註冊處理序伺服器。

    
    ![註冊處理序伺服器](./media/site-recovery-vmware-register-process-server/register-ps.png)
