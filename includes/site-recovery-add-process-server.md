---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: c9a0d4387511bbfa033bcb90d9f83e1a7bb39719
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67173684"
---
1. 啟動 Azure Site Recovery UnifiedSetup.exe
2. 在 [**開始之前**] 中，選取 [**新增額外進程伺服器以相應放大部署**]。

   ![新增處理序伺服器](./media/site-recovery-add-process-server/ps-page-1.png)

3. 在 [設定**伺服器詳細資料**] 中，指定設定伺服器的 IP 位址和複雜密碼。

   ![新增處理序伺服器 2](./media/site-recovery-add-process-server/ps-page-2.png)
4. 在 [**網際網路設定**] 中，指定在設定伺服器上執行的提供者如何透過網際網路連接到 Azure Site Recovery。

   ![新增處理序伺服器 3](./media/site-recovery-add-process-server/ps-page-3.png)

   * 如果您想要使用電腦上目前設定的 proxy 來連線，請選取 [**使用現有的 proxy 設定連線]**。
   * 如果您想要讓提供者直接連線，請選取 **[不使用 proxy 直接連線]**。
   * 如果現有的 proxy 需要驗證，或您想要使用自訂 proxy 進行提供者連線，請選取 **[使用自訂 proxy 設定連線]**。

     * 如果您使用自訂 Proxy，您必須指定位址、連接埠以及認證。
     * 如果您使用 Proxy，您應該已經獲准存取服務 URL。

5. 在 [**必要條件檢查**] 中，安裝程式會執行檢查，以確定可以執行安裝。 如果出現有關「**通用時間同步檢查**」的警告，請確認系統時鐘的時間（[**日期和時間**] 設定）與時區相同。

     ![新增處理序伺服器 4](./media/site-recovery-add-process-server/ps-page-4.png)

6. 在 [環境詳細資料] 中，選取您是否要複寫 VMware VM。 如果是的話，安裝程式會檢查是否已安裝 PowerCLI 6.0。

     ![新增處理序伺服器 5](./media/site-recovery-add-process-server/ps-page-5.png)

7. 在 [安裝位置]**** 中，選取您要安裝二進位檔及儲存快取的位置。 您選取的磁碟機至少必須有 5 GB 的可用磁碟空間，但我們建議快取磁碟機至少有 600 GB 的可用空間。
     ![新增處理序伺服器 5](./media/site-recovery-add-process-server/ps-page-6.png)

8. 在 [**網路選取**] 中，指定設定伺服器用來傳送和接收復寫資料的接聽程式（網路介面卡和 SSL 埠）。 連接埠 9443 是用來傳送及接收複寫流量的預設連接埠，但您可以修改此連接埠號碼，以符合您的環境需求。 除了連接埠 9443 之外，我們也會開啟網頁伺服器用來協調複寫作業的連接埠 443。 請勿使用連接埠 443 來傳送或接收複寫流量。

     ![新增處理序伺服器 6](./media/site-recovery-add-process-server/ps-page-7.png)
9. 在 [摘要]**** 中檢閱資訊，然後按一下 [安裝]****。 安裝完成時，會產生複雜密碼。 在您啟用複寫時會需要它，所以請將它複製並保存在安全的位置。

     ![新增處理序伺服器 7](./media/site-recovery-add-process-server/ps-page-8.png)
