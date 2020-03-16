---
title: 設定 Azure Data Box Heavy 的教學課程 | Microsoft Docs
description: 深入了解如何連接 Azure Data Box Heavy 的纜線並且連線
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: dbf3531f84d218543b0ab1fb2199a66bdbde2926
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117246"
---
::: zone target = "docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>教學課程：連接 Azure Data Box Heavy 的纜線並連線

::: zone-end

::: zone target = "chromeless"

# <a name="get-started-with-azure-data-box-heavy"></a>開始使用 Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

本教學課程說明如何連接 Azure Data Box Heavy 的纜線、加以連線並開啟。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 連接 Data Box Heavy 的纜線
> * 連線至 Data Box Heavy

::: zone-end

::: zone target = "chromeless"

本指南會提供相關指示來說明如何檢閱必要條件、連接纜線並將裝置連線、複製資料、上傳至 Azure，然後確認上傳的資料。

::: zone-end

## <a name="prerequisites"></a>Prerequisites

在您開始前，請確定：

1. 您已完成[教學課程：訂購 Azure Data Box Heavy ](data-box-heavy-deploy-ordered.md)。
2. 您已收到 Data Box Heavy，且入口網站中的訂購狀態為 [已交付]  。
3. 您已檢閱 [Data Box Heavy 安全指導方針](data-box-safety.md)。
4. 您的資料中心內必須有塊平坦區域，且靠近可用的網路連線，以容納具有此磁碟使用量的裝置。 此裝置無法掛接在機架上。
5. 您已收到 4 條用於儲存體裝置的接地電源線。
6. 您已將主機電腦連線到資料中心網路。 Data Box Heavy 會從這部電腦複製資料。 您的主機電腦必須執行[支援的作業系統](data-box-heavy-system-requirements.md)。
7. 您的資料中心必須有高速網路。 強烈建議您具有至少一個 10 GbE 的連線。 
8. 您的筆記型電腦必須有用來連接到本機 UI 及設定裝置的 RJ-45 纜線。 使用筆記型電腦，一次設定好裝置的每個節點。
9. 每個裝置節點都需要一條 40-Gbps 的纜線或 10-Gbps 的纜線。
    - 選擇與 [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) 網路介面相容的纜線。
    - 若使用 40-Gbps 的纜線，纜線的裝置端必須是 QSFP+。
    - 若使用 10-Gbps 的纜線，您需要將 SFP+ 纜線插入其中一端上的 10-Gbps 交換器，並在插入裝置的一端使用 QSFP+ 對 SFP+ 的配接器 (或是 QSA 配接器)。

::: zone target = "docs"

## <a name="cable-your-device-for-power"></a>將您的裝置接上纜線，以取得電源

請執行下列步驟以連接裝置的纜線。

1. 檢查裝置是否有任何竄改的跡象，或其他任何明顯的損毀。 如果裝置已遭竄改或嚴重損毀，請勿繼續操作。 請立即[連絡 Microsoft 支援服務](data-box-disk-contact-microsoft-support.md)以協助您評估裝置是否可正常運作，以及是否需要寄送替代品給您。
2. 將裝置移至安裝位置。

    ![Data Box Heavy 裝置安裝位置](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. 將裝置上的後輪鎖住，如下所示。

    ![鎖住 Data Box Heavy 裝置的輪子](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. 找出打開裝置前門和後門的把手。 打開前門並將其移動到與裝置齊平的位置。 對後門執行相同動作。
    裝置運作時，這兩扇門皆必須保持開啟，才能讓空氣適當地由前至後通過裝置。

    ![打開門的 Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. 裝置背面的托盤應該有四條電源線。 拿走托盤上的所有纜線，並將這些纜線放到一旁。

    ![托盤中的 Data Box Heavy 電源線](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. 下一步是識別裝置背面的各種連接埠。 裝置節點有兩個：**NODE1** 和 **NODE2**。 每個節點有四個網路介面：**MGMT**、**DATA1**、**DATA2**、**DATA3**。 **MGMT** 用來設定裝置初始設定期間的管理。 **DATA1**-**DATA3** 是資料連接埠。 **MGMT** 和 **DATA3** 連接埠是 1 Gbps，而 **DATA1**、**DATA2** 可以作為 40-Gbps 的連接埠或 10-Gbps 的連接埠使用。 兩個裝置節點的底部有四個電源供應器 (PSU)，由兩個裝置節點共用。 面向此裝置，從左到右的 **PSU** 分別是 **PSU1**、**PSU2**、**PSU3** 和 **PSU4**。

    ![Data Box Heavy 連接埠](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. 將四條電源線全部接到裝置電源供應器。 綠色 LED 燈會開啟並閃爍。
8. 使用前面的電源按鈕來開啟裝置節點。 壓下電源按鈕並維持幾秒，直到藍色燈號出現。 裝置背面電源供應器的綠色 LED 燈現在應該會持續亮著。 裝置的前端操作面板也包含錯誤 LED 燈。 當錯誤 LED 燈亮起時，表示 PSU 或風扇發生問題，或是磁碟機有問題。  

    ![Data Box Heavy 的前端操作面板](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>將纜線接到第一個網路節點

在裝置的其中一個節點上，執行下列步驟來連接網路纜線。

1. 使用 CAT 6 RJ-45 網路纜線 (圖片中右上的已連結的纜線，用來插入標記的 MGMT)，將主機電腦連線到 1-Gbps 的管理連接埠。
2. 針對資料使用 QSFP+ 纜線 (Fiber 或銅纜線) 連接至少一個 40 Gbps (最好超過 1 Gbps) 網路介面。 如果使用 10-Gbps 交換器，應使用 SFP + 纜線和 QSFP+ 對 SFP+ 配接器 (QSA 配接器) 連接 40-Gbps 的網路介面來傳輸資料。

    ![已連接纜線的 Data Box Heavy 連接埠](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > DATA 1 和 DATA2 已對調，並不符合本機 Web UI 中顯示的內容。
    > 40 Gbps 纜線配接器會在以下列方式插入時進行連線。

    ![Data Box Heavy 的 40-Gbps 纜線配接器](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>設定第一個節點

執行下列步驟，使用本機組態和 Azure 入口網站來設定您的裝置。

1. 從入口網站下載裝置認證。 移至 [一般] > [裝置詳細資料]  。 複製 [裝置密碼]  。 這些密碼會繫結至入口網站中的特定訂單。 對應至 Data Box Heavy 中的兩個節點，您會看到兩個裝置序號。 兩個節點的裝置管理員密碼都相同。

    ![Data Box Heavy 裝置的認證](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. 透過 CAT6 RJ-45 網路纜線將您的用戶端工作站連接到裝置。
3. 使用靜態 IP 位址 `192.168.100.5` 和子網路 `255.255.255.0`，在您要用來連線到裝置的電腦上設定乙太網路介面卡。

    ![連接到本機 Web UI 的 Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. 以下列 URL 連接到裝置的本機 Web UI：`http://192.168.100.10`。 按一下 [進階]  ，然後按一下 [繼續前往 192.168.100.10 (不安全)]  。
5. 您會看到本機 Web UI 的 [登入]  頁面。
    
    - 此頁面上的其中一個節點序號應與入口網站 UI 和本機 Web UI 上的相符。 請記下節點編號與序號的對應。 入口網站中會有兩個節點和兩個裝置序號。 此對應可協助您了解哪個節點對應至哪個序號。
    - 裝置此時處於鎖定狀態。
    - 提供您在上一個步驟中取得的管理員密碼來登入裝置。 按一下 **[登入]** 。

    ![登入 Data Box Heavy 本機 Web UI](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. 在儀表板上，確定網路介面已設定。 裝置節點上有四個網路介面，兩個 1 Gbps 和兩個 40 Gbps。 其中一個 1-Gbps 介面是管理介面，因此無法由使用者設定。 其餘三個網路介面是資料專用，並可以由使用者設定。

- 如果您的環境中啟用了 DHCP，則會自動設定網路介面。
- 如果未啟用 DHCP，請移至 [設定網路介面]，並視需要指派靜態 IP。

    ![Data Box Heavy 儀表板節點 1](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>設定第二個節點

對第二個裝置節點執行[設定第一個節點](#configure-first-node)中詳述的步驟。

![Data Box Heavy 儀表板節點 2](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

在裝置設定完成後，您可以連線至裝置共用，並將資料從您的電腦複製到裝置。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure Data Box Heavy 的相關主題，像是：

> [!div class="checklist"]
> * 連接 Data Box Heavy 的纜線
> * 連線至 Data Box Heavy

請前進到下一個教學課程，以了解如何複製 Data Box Heavy 上的資料。

> [!div class="nextstepaction"]
> [將資料複製到 Azure 資料箱](./data-box-heavy-deploy-copy-data.md)

::: zone-end
