---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: c9a0d4387511bbfa033bcb90d9f83e1a7bb39719
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67173684"
---
1. 啟動 Azure Site Recovery UnifiedSetup.exe
2. 在**開始之前**，選擇 **"添加其他進程伺服器以擴展部署**"。

   ![新增處理序伺服器](./media/site-recovery-add-process-server/ps-page-1.png)

3. 在**佈建服務器詳細資訊**中，指定佈建服務器的 IP 位址和密碼。

   ![新增處理序伺服器 2](./media/site-recovery-add-process-server/ps-page-2.png)
4. 在**Internet 設置**中，指定在佈建服務器上運行的提供程式如何通過 Internet 連接到 Azure 網站恢復。

   ![新增處理序伺服器 3](./media/site-recovery-add-process-server/ps-page-3.png)

   * 如果要與電腦上當前設置的代理連接，請選擇"**連接現有代理設置**"。
   * 如果希望提供程式直接連接，請選擇 **"在沒有代理的情況下直接連接**"。
   * 如果現有代理需要身份驗證，或者要對提供程式連接使用自訂代理，請選擇"**使用自訂代理設置連接**"。

     * 如果您使用自訂 Proxy，您必須指定位址、連接埠以及認證。
     * 如果您使用 Proxy，您應該已經獲准存取服務 URL。

5. 在**先決條件檢查**中，安裝程式運行檢查以確保安裝可以運行。 如果出現有關**全域時間同步檢查**的警告，請驗證系統時鐘上的時間（**日期和時間**設置）是否與時區相同。

     ![新增處理序伺服器 4](./media/site-recovery-add-process-server/ps-page-4.png)

6. 在 [環境詳細資料]**** 中，選取您是否要複寫 VMware VM。 如果是的話，安裝程式會檢查是否已安裝 PowerCLI 6.0。

     ![新增處理序伺服器 5](./media/site-recovery-add-process-server/ps-page-5.png)

7. 在 [安裝位置]**** 中，選取您要安裝二進位檔及儲存快取的位置。 您選取的磁碟機至少必須有 5 GB 的可用磁碟空間，但我們建議快取磁碟機至少有 600 GB 的可用空間。
     ![新增處理序伺服器 5](./media/site-recovery-add-process-server/ps-page-6.png)

8. 在 **"網路選擇**"中，指定佈建服務器發送和接收復制資料的攔截器（網路介面卡和 SSL 埠）。 連接埠 9443 是用來傳送及接收複寫流量的預設連接埠，但您可以修改此連接埠號碼，以符合您的環境需求。 除了連接埠 9443 之外，我們也會開啟網頁伺服器用來協調複寫作業的連接埠 443。 請勿使用連接埠 443 來傳送或接收複寫流量。

     ![新增處理序伺服器 6](./media/site-recovery-add-process-server/ps-page-7.png)
9. 在 [摘要]**** 中檢閱資訊，然後按一下 [安裝]****。 安裝完成時，會產生複雜密碼。 在您啟用複寫時會需要它，所以請將它複製並保存在安全的位置。

     ![新增處理序伺服器 7](./media/site-recovery-add-process-server/ps-page-8.png)
