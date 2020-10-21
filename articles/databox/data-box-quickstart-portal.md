---
title: Microsoft Azure 資料箱的快速入門 | Microsoft Docs
description: 在本快速入門中，了解如何針對匯入訂單，使用 Azure 入口網站來部署 Azure 資料箱。 設定 Azure 資料箱並複製要上傳至 Azure 的資料。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 81dee0c63db711b39ff8fd2123623a16b2b9cb91
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122784"
---
# <a name="get-started-with-azure-data-box-to-import-data-into-azure"></a>開始使用 Azure 資料箱將資料匯入至 Azure

::: zone target="docs"

本快速入門說明如何針對匯入訂單，使用 Azure 入口網站來部署 Azure 資料箱。 這些步驟包括如何連接纜線、進行設定，以及將資料複製到資料箱，以便上傳至 Azure。 快速入門的步驟會在 Azure 入口網站和裝置的本機 Web UI 上執行。

如需部署和追蹤的詳細逐步指示，請移至[教學課程：訂購 Azure 資料箱](data-box-deploy-ordered.md)

::: zone-end 

::: zone target="chromeless"

本指南說明如何使用 Azure 入口網站來部署用於匯入資料的 Azure 資料箱。 這些步驟包括檢查必要條件、以纜線連接您的裝置，以及將資料複製到您的裝置，以將其上傳至 Azure。

::: zone-end

::: zone target="docs"
 
## <a name="prerequisites"></a>必要條件

開始之前：

- 請確定您用於資料箱服務的訂用帳戶是下列其中一種類型：
    - Microsoft Enterprise 合約 (EA)。 深入了解 [EA 訂用帳戶](https://azure.microsoft.com/pricing/enterprise-agreement/)。
    - 雲端解決方案提供者 (CSP)。 深入了解 [Azure CSP 方案](/azure/cloud-solution-provider/overview/azure-csp-overview)。
    - Microsoft Azure 贊助。 深入了解 [Azure 贊助方案](https://azure.microsoft.com/offers/ms-azr-0036p/)。 

- 請確定您有訂用帳戶的擁有者或參與者存取權，才能建立資料箱訂單。
- 檢閱[資料箱的安全性指導方針](data-box-safety.md)。
- 您有一部主機電腦，其中包含要複製到資料箱的資料。 您的主機電腦必須符合下列條件：
    - 執行[支援的作業系統](data-box-system-requirements.md)。
    - 連線至高速網路。 強烈建議您具有至少一個 10 GbE 的連線。 如果無法使用 10 GbE 連線，也可以使用 1 GbE 資料連結，但是複製速度會受到影響。 
- 您必須能夠進入放置資料箱所在的平坦表面。 如果您想要將裝置放在標準機架上，您的資料中心機架中需要有 7U 插槽。 您可以將裝置平放或垂直放置在機架中。
- 您已購買下列纜線，以將您的資料箱連線至主機電腦。
    - 兩條 10 GbE SFP+ Twinax 銅纜線 (與 DATA 1、DATA 2 網路介面搭配使用)
    - 一條 RJ-45 CAT 6 網路線 (與 MGMT 網路介面搭配使用)
    - 一條 RJ-45 CAT 6A 或一條 RJ-45 CAT 6 網路線 (與 DATA 3 網路介面搭配使用，分別設定為 10 Gbps 或 1 Gbps)

::: zone-end 

::: zone target="chromeless"

## <a name="prerequisites"></a>必要條件

在您開始前，請確定您已經：

1. 完成[教學課程：訂購 Azure 資料箱](data-box-deploy-ordered.md)。
2. 收到資料箱，且入口網站中的訂購狀態為 [已交付]。 
3. 檢閱[資料箱安全指導方針](data-box-safety.md)。
4. 收到一條用於 100-TB 儲存體裝置的接地電源線。
5. 有權存取主機電腦，以將其中的資料複製到資料箱。 您的主機電腦必須符合下列條件：
    - 執行[支援的作業系統](data-box-system-requirements.md)。
    - 連線至高速網路。 強烈建議您具有至少一個 10 GbE 的連線。 如果無法使用 10 GbE 連線，也可以使用 1 GbE 資料連結，但是複製速度會受到影響。 
6. 有一個平面可放置資料箱。 若要將裝置平穩或筆直地放在標準機架上，您的機架中需要有 7U 插槽。

::: zone-end

::: zone target="docs"

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="order"></a>單

這個步驟需要大約 5 分鐘。

1. 在 Azure 入口網站中建立新的 Azure 資料箱資源。
2. 選取針對此服務啟用的現有訂用帳戶，然後選擇 [匯入] 作為轉移類型。 提供資料所在的 [來源國家/地區]，以及資料傳輸的 [Azure 目的地區域]。
3. 選取 [資料箱]。 可用容量上限為 80 TB，您可以針對較大的資料大小建立多個訂單。
4. 輸入訂單詳細資料和出貨資訊。 如果您的區域可使用服務，請提供通知電子郵件地址、檢閱摘要，然後建立訂單。

一旦建立訂單後，裝置就準備出貨。



## <a name="cable"></a>連接纜線 

這個步驟需要大約 10 分鐘。

收到資料箱時，請執行下列步驟以連接纜線、連線並開啟裝置。 此步驟約需要 10 分鐘。

1. 如果有任何證據表明裝置已遭竄改或損毀，請勿繼續操作。 請連絡 Microsoft 支援服務以向您運送替換裝置。
2. 在將裝置連接纜線之前，請確定您有下列的纜線：
    
    - (已包含) 額定電流為 10 A 或更高的接地電源線，一端帶有 IEC60320 C-13 接點，用於連接裝置。
    - 一條 RJ-45 CAT 6 網路線 (與 MGMT 網路介面搭配使用)
    - 兩條 10 GbE SFP+ Twinax 銅纜線 (搭配 10 Gbps DATA 1、DATA 2 網路介面使用)
    - 一條 RJ-45 CAT 6A 或一條 RJ-45 CAT 6 網路線 (與 DATA 3 網路介面搭配使用，分別設定為 10 Gbps 或 1 Gbps)

3. 移除裝置並將其放在平面上。 
    
4. 將裝置接上纜線，如下所示。  

    ![已連接纜線的資料箱裝置後擋板](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)  

    1. 將電源線接上裝置。
    2. 您可以使用 RJ-45 CAT 6 網路纜線，將主機電腦連接到裝置上的管理連接埠 (MGMT)。 
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
 
1. 如果使用 Windows 主機，請使用 SMB 相容的檔案複製工具，例如 Robocopy。 針對 NFS 主機，使用 `cp` 命令或 `rsync` 來複製資料。 將工具連接到您的裝置，並開始將資料複製到共用。 如需有關如何使用 Robocopy 來複製資料的詳細資訊，請移至 [Robocopy](/previous-versions/technet-magazine/ee851678(v=msdn.10))。
2. 使用路徑連線到共用：`\\<IP address of your device>\ShareName`。 若要取得共用存取認證，請前往資料箱之本機 Web UI 中的 [連線並複製] 頁面。
3. 請確定共用和資料夾名稱，以及資料遵循 [Azure 儲存體和資料箱服務限制](data-box-limits.md)中所述的指導方針。

## <a name="ship-to-azure"></a>寄送到 Azure 

此作業約需要 10-15 分鐘來完成。

1. 前往本機 Web UI 中的 [準備寄送] 頁面，並開始準備寄送。 
2. 從本機 Web UI 關閉裝置。 從裝置移除纜線。 
3. 回郵出貨標籤應該顯示在電子墨水顯示器上。 如果電子墨水顯示器未顯示標籤，請從 Azure 入口網站下載出貨標籤，然後插入裝置上隨附的透明保護套中。
4. 鎖上盒子，並運送給 Microsoft。 

## <a name="verify-data"></a>驗證資料

完成這項作業的時間取決於您的資料大小。

1. 當資料箱裝置連線到 Azure 資料中心網路時，會自動開始將資料上傳到 Azure。 
2. Azure 資料箱服務會通知您，已透過 Azure 入口網站完成資料複製。 

    1. 檢查所有失敗的錯誤記錄，並採取適當的動作。
    2. 請先確認您的資料位於儲存體帳戶中，再從來源予以刪除。

## <a name="clean-up-resources"></a>清除資源

完成此步驟需要 2-3 分鐘。

- 訂單處理之前，您可以取消 Azure 入口網站中的資料箱訂單。 一旦處理訂單後，就無法取消訂單。 訂單會進行直到達到已完成的階段為止。 若要取消訂單，請前往 [概觀]，然後從命令列按一下 [取消]。

- 一旦 Azure 入口網站中的狀態顯示為 [已完成] 或是 [已取消] 後，您就可以刪除訂單。 若要刪除訂單，請前往 [概觀]，然後從命令列按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已部署 Azure 資料箱，以協助將您的資料匯入 Azure。 若要深入了解 Azure 資料箱管理，請進入下列教學課程： 

> [!div class="nextstepaction"]
> [使用 Azure 入口網站來管理資料箱](data-box-portal-admin.md)

::: zone-end