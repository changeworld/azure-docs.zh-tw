---
title: 快速入門：匯出 Microsoft Azure 資料箱的資料
description: 了解如何在 Azure 入口網站中快速匯出您的 Azure 資料箱資料
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: quickstart
ms.date: 07/17/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 01fdaa95dd396e5545b65bada2a9d6410169230b
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2020
ms.locfileid: "90052455"
---
# <a name="quickstart-get-started-with-azure-data-box-to-export-data-from-azure"></a>快速入門：開始使用 Azure 資料箱從 Azure 匯出資料

本快速入門將說明如何使用 Azure 入口網站將資料從 Azure 匯出至您的位置。 這些步驟包括如何從 Azure 連接纜線、設定和複製資料。 快速入門會在 Azure 入口網站和裝置的本機 Web UI 上執行。

如需部署和追蹤的詳細逐步指示，請移至[教學課程：建立 Azure 資料箱的匯出訂單](data-box-deploy-export-ordered.md)

## <a name="prerequisites"></a>必要條件

開始之前：

* 請確定您用於資料箱服務的訂用帳戶是下列其中一種類型：
  * Microsoft Enterprise 合約 (EA)。 深入了解 [EA 訂用帳戶](https://azure.microsoft.com/pricing/enterprise-agreement/)。
  * 雲端解決方案提供者 (CSP)。 深入了解 [Azure CSP 方案](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)。
  * Microsoft Azure 贊助。 深入了解 [Azure 贊助方案](https://azure.microsoft.com/offers/ms-azr-0036p/)。

* 請確定您有訂用帳戶的擁有者或參與者存取權，才能建立資料箱訂單。
* 檢閱[資料箱的安全性指導方針](data-box-safety.md)。
* 您有 Azure 資料箱裝置，可將資料移至您的位置。 您的主機電腦必須符合下列條件：
  * 執行[支援的作業系統](data-box-system-requirements.md)。
  * 連線至高速網路。 強烈建議您具有至少一個 10 GbE 的連線。 如果無法使用 10 GbE 連線，也可以使用 1 GbE 資料連結，但是複製速度會受到影響。
* 您必須能夠進入放置資料箱所在的平坦表面。 如果您想要將裝置放在標準機架上，您的資料中心機架中需要有 7U 插槽。 您可以將裝置平放或垂直放置在機架中。
* 您已購買下列纜線，可將您的資料箱連線至主機電腦。
  * 兩條 10 GbE SFP+ Twinax 銅纜線 (與 DATA 1、DATA 2 網路介面搭配使用)
  * 一條 RJ-45 CAT 6 網路線 (與管理 (MGMT) 網路介面搭配使用)
  * 一條 RJ-45 CAT 6A 或一條 RJ-45 CAT 6 網路線 (與 DATA 3 網路介面搭配使用，分別設定為 10 Gbps 或 1 Gbps)

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="order"></a>單

這個步驟需要大約 10 分鐘。

1. 在 Azure 入口網站中建立新的 Azure 資料箱資源。
2. 選取針對此服務啟用的現有訂用帳戶，然後選擇 [匯出至 Azure] 作為轉移類型。 提供資料所在的 [來源 Azure 區域]，以及用於資料傳輸的 [目的地國家/地區]。
3. 選取 [資料箱]。 可用容量上限為 80 TB，您可以針對較大的資料大小建立多個訂單。
4. 選取 [新增儲存體帳戶和匯出類型]，然後**選取匯出選項**。
5. 輸入訂單詳細資料和出貨資訊。 如果您的區域可使用服務，請提供通知電子郵件地址、檢閱摘要，然後建立訂單。

一旦建立訂單後，裝置就準備出貨。

## <a name="cable"></a>連接纜線

這個步驟需要大約 10 分鐘。

收到資料箱時，請執行下列步驟以連接纜線、連線並開啟裝置。 此步驟約需要 10 分鐘。

1. 如果有任何證據表明裝置已遭竄改或損毀，請勿繼續操作。 請連絡 Microsoft 支援服務以向您運送替換裝置。
2. 在將裝置連接纜線之前，請確定您有下列的纜線：

   * 額定電流為 10 A 或更高的接地電源線 (已包含)，一端帶有 IEC60320 C-13 接點，用於連接裝置。
   * 一條 RJ-45 CAT 6 網路線 (與管理 (MGMT) 網路介面搭配使用)
   * 兩條 10 GbE SFP+ Twinax 銅纜線 (搭配 10 Gbps DATA 1、DATA 2 網路介面使用)
   * 一條 RJ-45 CAT 6A 或一條 RJ-45 CAT 6 網路線 (與 DATA 3 網路介面搭配使用，分別設定為 10 Gbps 或 1 Gbps)

3. 移除裝置並將其放在平面上。

4. 將裝置接上纜線，如下所示。  

    ![已連接纜線的資料箱裝置後擋板](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. 將電源線接上裝置。
    2. 您可以使用 RJ-45 CAT 6 網路纜線，將主機電腦連接到裝置上的 MGMT 連接埠。
    3. 針對資料使用 SFP+ Twinax 銅纜線連接至少一個 10 Gbps (最好超過 1 Gbps) 網路介面、DATA 1 或 DATA 2。
    4. 開啟裝置。 電源按鈕位於裝置前端面板上。

## <a name="connect"></a>連線

此步驟約需要 5-7 分鐘來完成。

1. 若要取得裝置密碼，請前往 [Azure 入口網站](https://portal.azure.com)中的 [一般] > [裝置詳細資料]。
2. 將靜態 IP 位址 192.168.100.5 和子網路 255.255.255.0，指派到您要用來連接至資料箱之電腦上的乙太網路介面卡。 在 `https://192.168.100.10` 存取裝置的本機 Web UI。 開啟裝置後，連線可能需要最多 5 分鐘。
3. 使用 Azure 入口網站中的密碼登入。 您會看到錯誤指出網站的安全性憑證有問題。 請依照瀏覽器專屬指示，繼續前往網頁。
4. 根據預設，10 Gbps 資料介面 (或 1 Gbps) 的網路設定會設定為 DHCP。 如有需要，您可以將此介面設定為靜態，並提供 IP 位址。

## <a name="copy-data"></a>複製資料

完成這項作業的時間取決於您的資料大小和網路速度。

1. 如果使用 Windows 用戶端，請使用 SMB 相容的檔案複製工具，例如 Robocopy。 針對 NFS 主機，使用 `cp` 命令或 `rsync` 來複製資料。 如需有關如何使用 Robocopy 來複製資料的詳細資訊，請移至 [Robocopy](https://technet.microsoft.com/library/ee851678.aspx)。
2. 連線到裝置共用，並開始將資料複製到主機電腦。
<!-- 1. Connect to the device shares using the path:`\\<IP address of your device>\ShareName`. To get the share access credentials, go to the **Connect & copy** page in the local web UI of the Data Box. --> 

## <a name="ship-to-azure"></a>寄送到 Azure

此作業約需要 10-15 分鐘來完成。

1. 前往本機 Web UI 中的 [準備寄送] 頁面，並開始準備寄送。
2. 從本機 Web UI 關閉裝置。 從裝置移除纜線。
3. 纏繞裝置隨附的電源線，並將其安全地放在裝置背後。
4. 回郵出貨標籤應該顯示在電子墨水顯示器上。 如果電子墨水顯示未顯示標籤，請移至 Azure 入口網站中的 [概觀] > [下載出貨標籤]。 下載出貨標籤並貼在裝置上。
5. 如果是寄回裝置，請使用您的地區貨運公司來排定取貨。
6. 一旦貨運業者收取資料箱並進行掃描，入口網站的訂單狀態會更新為 [已取貨]。 此外，也會顯示追蹤識別碼。

## <a name="clean-up-resources"></a>清除資源

完成此步驟需要 2-3 分鐘。

* 訂單處理之前，您可以取消 Azure 入口網站中的資料箱訂單。 一旦訂單進入處理程序後，就無法取消訂單。 訂單會進行直到達到已完成的階段為止。 若要取消訂單，請前往 [概觀]，然後從命令列選取 [取消]。

* 一旦 Azure 入口網站中的狀態顯示為 [已完成] 或是 [已取消] 後，您就可以刪除訂單。 若要刪除訂單，請前往 [概觀]，然後從命令列選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已部署將資料從 Azure 匯出至 Azure 資料箱的訂單。 若要深入了解 Azure 資料箱管理，請進入下列教學課程：

> [!div class="nextstepaction"]
> [使用 Azure 入口網站來管理資料箱](data-box-portal-admin.md)
