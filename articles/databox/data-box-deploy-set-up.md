---
title: 設定 Azure 資料箱的教學課程 | Microsoft Docs
description: 在本教學課程中，了解如何連接 Azure 資料箱纜線、連線 Azure 資料箱，以及開啟 Azure 資料箱。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: ac87d5040cd572635d81be51308f48a57ddd38e3
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335463"
---
::: zone target="docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>教學課程：連接 Azure 資料箱的纜線並且連線

::: zone-end

::: zone target="chromeless"

## <a name="cable-and-connect-to-your-device"></a>以纜線連接到您的裝置

::: zone-end

::: zone target="docs"

本教學課程說明如何連接 Azure 資料箱的纜線、加以連線並開啟。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 連接資料箱的纜線
> * 連線至資料箱

## <a name="prerequisites"></a>必要條件

在您開始前，請確定：

1. 您已建立 Azure 資料箱的訂單。
    - 針對匯入訂單，請參閱[教學課程：訂購 Azure 資料箱](data-box-deploy-ordered.md)。
    - 針對匯出訂單，請參閱[教學課程：訂購 Azure 資料箱](data-box-deploy-export-ordered.md)
1. 您已收到資料箱，且入口網站中的訂購狀態為 [已交付]。 
    - 黏貼到裝置目前標籤下的透明袋中有出貨標籤。 請妥善保存此標籤，您會在退貨時用到。
    - 歐洲境內某些區域可能會收到封裝在盒子裡的裝置。 打開裝置包裝後，請保留盒子以在送回裝置時使用。
1. 您已檢閱[資料箱安全指導方針](data-box-safety.md)。
1. 您已收到一條用於 100 TB 儲存體裝置的接地電源線。
1. 您有一部主機電腦，用來將資料複製到 (匯入訂單) 或複製出 (匯出訂單) 資料箱。 您的主機電腦必須符合下列條件：
    - 執行[支援的作業系統](data-box-system-requirements.md)。
    - 連線至高速網路。 強烈建議您具有至少一個 10 GbE 的連線。 如果無法使用 10 GbE 連線，也可以使用 1 GbE 資料連結，但是複製速度會受到影響。 
1. 您必須能夠進入放置資料箱所在的平坦表面。 如果您想要將裝置放在標準機架上，您的資料中心機架中需要有 7U 插槽。 您可以將裝置平放或垂直放置在機架中。
1. 您已購買下列纜線，以將您的資料箱連線至主機電腦。
    - 一或多條 10 GbE SFP+ Twinax 銅線或 SFP+ 光纖纜線 (搭配 DATA 1、DATA 2 網路介面使用)。 資料箱具有採用 PCI Express 3.0 網路介面的 Mellanox ConnectX®-3 Pro EN 雙連接埠 10GBASE-T 配接器，所以與這個介面相容的纜線應該有作用。 例如，CISCO SFP-H10GB-CU3M 10GBASE-CU TWINAX SFP +3M 纜線已用於內部測試。 如需詳細資訊，請參閱 [Mellanox 中支援的纜線和交換器清單](https://www.mellanox.com/pdf/firmware/ConnectX3-FW-2_42_5000-release_notes.pdf)。
    - 一條 RJ-45 CAT 6 網路線 (與 MGMT 網路介面搭配使用)
    - 一條 RJ-45 CAT 6A 或一條 RJ-45 CAT 6 網路線 (與 DATA 3 網路介面搭配使用，分別設定為 10 Gbps 或 1 Gbps)

## <a name="cable-your-device"></a>將裝置接上纜線

請執行下列步驟以連接裝置的纜線。

1. 檢查裝置是否有任何竄改的跡象，或其他任何明顯的損毀。 如果裝置已遭竄改或嚴重損毀，請勿繼續操作。 請立即連絡 Microsoft 支援服務以協助您評估裝置是否可正常運作，以及是否需要寄送替代品給您。
2. 將裝置移至您要加以開啟的位置上。 將裝置放在平面上。 裝置也可以放置在標準機架上。
3. 連接電源線和網路線。 在常用的組態中，連線裝置的後擋板如下所示。 根據您的環境，您可以選擇其他[佈線選項](data-box-cable-options.md)。
    
    ![已連接纜線的資料箱裝置後擋板](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. 將電源線連接到標示的電源輸入位置。 電源線的另一端應連接到配電裝置。
    2. 使用 RJ-45 CAT 6 纜線連接一端的 MGMT 連接埠，以及另一端的膝上型電腦。            
    3. 使用 RJ-45 CAT 6A 纜線連接到一端的 DATA 3 連接埠。 如果您透過 RJ-45 CAT 6A 纜線連線，DATA 3 將會設定為 10 GbE，如果透過 RJ-45 CAT 6 纜線連線，則會設定為 1 GbE。
    4. 根據您為了轉送資料而想要連接的網路介面，使用最多兩個 10 GbE SFP + Twinax 銅線或 SFP + 光纖纜線分別連接 DATA 1 和 DATA 2 連接埠。 
    5. 資料連接埠纜線的另一端會透過 10 GbE 交換器連接到主機電腦。

4. 在裝置的前端操作面板上找出電源按鈕。 開啟裝置。

    ![資料箱電源按鈕](media/data-box-deploy-set-up/data-box-powered-door-open.png)

::: zone-end

::: zone target="chromeless"

當您收到裝置之後，您需要以纜線連接到您的裝置。 

## <a name="cable-your-device"></a>將裝置接上纜線

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

::: zone-end

::: zone target="docs"


## <a name="connect-to-your-device"></a>連接到您的裝置

執行下列步驟，以使用本機 Web UI 和入口網站 UI 設定您的裝置。

1. 使用靜態 IP 位址 192.168.100.5 和子網路 255.255.255.0，在您要用來連接到裝置的膝上型電腦上設定乙太網路介面卡。 
2. 連接到裝置的 MGMT 連接埠，並經由 https\://192.168.100.10 存取其本機 Web UI。 在開啟裝置後，此作業最多可能需耗時 5 分鐘。
3. 按一下 [詳細資料]，然後按一下 [繼續瀏覽網頁]。

   ![連線至本機 Web UI](media/data-box-deploy-set-up/data-box-connect-local-web-ui.png)

4. 您會看到本機 Web UI 的 [登入] 頁面。 請確定入口網站 UI 和本機 Web UI 上的裝置序號是相符的。 裝置此時處於鎖定狀態。
5. 登入 [Azure 入口網站](https://portal.azure.com)。
6. 從入口網站下載裝置認證。 移至 [一般] > [裝置詳細資料]。 複製 [裝置密碼]。 裝置密碼會繫結至入口網站中的特定訂單。 

    ![取得裝置認證](media/data-box-deploy-set-up/data-box-device-credentials.png)
    
    
7. 提供您在上一個步驟中從 Azure 入口網站取得的裝置密碼，以登入裝置的本機 Web UI。 按一下 **[登入]** 。
8. 在 [儀表板] 上，確定網路介面已設定。 
   - 如果您的環境中啟用了 DHCP，則會自動設定網路介面。 
   - 如果未啟用 DHCP，請移至 [設定網路介面]，並視需要指派靜態 IP。

     ![裝置儀表板](media/data-box-deploy-set-up/data-box-dashboard-1.png)

設定資料網路介面後，您也可以使用任何 DATA 1 -DATA 3 介面的 IP 位址來存取位於 `https://<IP address of a data network interface>` 的本機 Web UI。 

在裝置設定完成後，您就可以連線至裝置共用並複製資料。 

::: zone-end

::: zone target="chromeless"

## <a name="connect-your-device"></a>連接裝置

1. 若要取得裝置密碼，請前往 [Azure 入口網站](https://portal.azure.com)中的 [一般] > [裝置詳細資料]。
2. 將靜態 IP 位址 192.168.100.5 和子網路 255.255.255.0，指派到您要用來連接至資料箱之電腦上的乙太網路介面卡。 在 `https://192.168.100.10` 存取裝置的本機 Web UI。 開啟裝置後，連線可能需要最多 5 分鐘。 
3. 使用 Azure 入口網站中的密碼登入。 您會看到錯誤指出網站的安全性憑證有問題。 請依照瀏覽器專屬指示，繼續前往網頁。
4. 根據預設，10 Gbps 資料介面 (或 1 Gbps) 的網路設定會設定為 DHCP。 如有需要，您可以將此介面設定為靜態，並提供 IP 位址。 

::: zone-end


::: zone target="docs"

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure 資料箱的相關主題，像是：

> [!div class="checklist"]
> * 連接資料箱的纜線
> * 連線至資料箱

請前進到下一個教學課程，以了解如何複製資料。

> [!div class="nextstepaction"]
> [針對匯入訂單，將您的資料複製到 Azure 資料箱](./data-box-deploy-copy-data.md)

Or

> [!div class="nextstepaction"]
> [針對匯出訂單，從 Azure 資料箱複製資料](./data-box-deploy-export-copy-data.md)

::: zone-end

