---
title: Microsoft Azure Data Box Heavy 的快速入門 | Microsoft Docs
description: 在本快速入門中，了解如何使用 Azure 入口網站部署 Azure Data Box Heavy，包括如何接線、設定和複製資料以上傳至 Azure。
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 9eda54ad23e06149910fe69ec16588f49829a5a5
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122818"
---
::: zone target = "docs"

# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站部署 Azure Data Box Heavy

本快速入門說明如何使用 Azure 入口網站來部署 Azure Data Box Heavy。 這些步驟包括如何連接纜線、進行設定，以及將資料複製到 Data Box Heavy，以便上傳至 Azure。 快速入門會在 Azure 入口網站和裝置的本機 Web UI 上執行。

如需部署和追蹤的詳細逐步指示，請移至[教學課程：訂購 Azure Data Box Heavy](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Prerequisites

在您部署裝置之前，請完成安裝位置、資料箱服務和裝置的下列設定必要條件。

### <a name="for-installation-site"></a>針對安裝位置

在您開始前，請確定：

- 裝置可以通過您所有的入口。 裝置尺寸：寬度：26 吋 長度：48 吋 高度：28 吋。
- 如果打算將裝置安裝在一樓以外的樓層，您可透過電梯或坡道取得裝置。
- 您有兩個人可處理裝置。 裝置的重量約為 500 磅。 而且附有輪子。
- 您的資料中心內有塊平坦區域，且靠近可用的網路連線，以容納具有此磁碟使用量的裝置。

### <a name="for-service"></a>針對服務

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>針對裝置

在您開始前，請確定：

- 您已檢閱過 [Data Box Heavy 的安全性指導方針](data-box-safety.md)。
- 您已將主機電腦連線到資料中心網路。 Data Box Heavy 會從這部電腦複製資料。 您的主機電腦必須執行[支援的作業系統](data-box-heavy-system-requirements.md)。
- 您的筆記型電腦有用來連接到本機 UI 及設定裝置的 RJ-45 纜線。 使用筆記型電腦，一次設定好裝置的每個節點。
- 您的資料中心有高速網路，且您有至少一個 10 GbE 連線。
- 每個裝置節點都需要一條 40-Gbps 的纜線或 10-Gbps 的纜線。 選擇與 Mellanox MCX314A-BCCT 網路介面相容的纜線：
    - 若使用 40-Gbps 的纜線，纜線的裝置端必須是 QSFP+。
    - 若使用 10-G 的纜線，您需要將 SFP+ 纜線插入其中一端上的 10-Gbps 交換器，並在插入裝置的一端使用 QSFP+ 對 SFP+ 的配接器 (或是 QSA 配接器)。
- 電源線已放入裝置背面的線槽中。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="order"></a>單

這個步驟需要大約 5 分鐘。

1. 在 Azure 入口網站中建立新的 Azure 資料箱資源。
2. 選取針對此服務啟用的現有訂用帳戶，然後選擇 [匯入]  作為轉移類型。 提供資料所在的 [來源國家/地區]  ，以及資料傳輸的 [Azure 目的地區域]  。
3. 選取 [Data Box Heavy]  。 可用容量上限為 770 TB，您可以針對較大的資料大小建立多個訂單。
4. 輸入訂單詳細資料和出貨資訊。 如果您的區域可使用服務，請提供通知電子郵件地址、檢閱摘要，然後建立訂單。

一旦建立訂單後，裝置就準備出貨。

::: zone-end

::: zone target = "chromeless"

## <a name="cable-and-connect-to-your-device"></a>以纜線連接到您的裝置

在檢閱過必要條件後，請連接纜線並讓裝置連線。

::: zone-end

## <a name="cable-for-power"></a>電源線

此步驟約需要 5 分鐘。

收到 Data Box Heavy 時，請執行下列步驟以接上裝置電源線並開啟裝置。

1. 如果有任何證據表明裝置已遭竄改或損毀，請勿繼續操作。 請連絡 Microsoft 支援服務以向您運送替換裝置。
2. 將裝置移至安裝位置並鎖定後輪。
3. 將四條電源線全部接到裝置背面的電源供應器。
4. 使用前面的電源按鈕來開啟裝置節點。

## <a name="cable-first-node-for-network"></a>將纜線接到第一個網路節點

此步驟約需要 10-15 分鐘來完成。

1. 您可以使用 RJ-45 CAT 6 網路纜線，將主機電腦連接到裝置上的管理連接埠 (MGMT)。
2. 針對資料使用 Twinax QSFP+ 銅纜線連接至少一個 40 Gbps (最好超過 1 Gbps) 網路介面、DATA 1 或 DATA 2。 如果使用 10-Gbps 交換器，應使用 Twinax SFP + 銅纜線和 QSFP+ 對 SFP+ 配接器 (QSA 配接器) 連接 40-Gbps 的網路介面來傳輸資料。
3. 將裝置接上纜線，如下所示。  

    ![Data Box Heavy 已接線](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>設定第一個節點

此步驟約需要 5-7 分鐘來完成。

1. 若要取得裝置密碼，請前往 [Azure 入口網站](https://portal.azure.com)中的 [一般] > [裝置詳細資料]  。 同一組密碼會使用於裝置的兩個節點。
2. 將靜態 IP 位址 192.168.100.5 和子網路 255.255.255.0，指派到您要用來連接至 Data Box Heavy 之電腦上的乙太網路介面卡。 在 `https://192.168.100.10` 存取裝置的本機 Web UI。 開啟裝置後，連線可能需要最多 5 分鐘。
3. 使用 Azure 入口網站中的密碼登入。 您會看到錯誤指出網站的安全性憑證有問題。 請依照瀏覽器專屬指示，繼續前往網頁。
4. 根據預設，介面的網路設定 (不包含 MGMT) 會設定為 DHCP。 如有需要，您可以將這些介面設定為靜態，並提供 IP 位址。

## <a name="cable-and-configure-the-second-node"></a>接線並設定第二個節點

此步驟約需要 15-20 分鐘來完成。

遵循用於第一個節點的步驟，以在裝置上連接及設定第二個節點。  


::: zone target = "docs"

## <a name="copy-data"></a>複製資料

完成這項作業的時間取決於您的資料大小，以及用於複製資料的網路速度。
 
1. 平行使用這兩個 40 Gbps 資料介面，將資料複製到這兩個裝置節點。

    - 如果使用 Windows 主機，請使用 SMB 相容的檔案複製工具，例如 [Robocopy](/previous-versions/technet-magazine/ee851678(v=msdn.10))。
    - 針對 NFS 主機，使用 `cp` 命令或 `rsync` 來複製資料。
2. 使用以下路徑連線到裝置上的共用：`\\<IP address of your device>\ShareName`。 若要取得共用存取認證，請前往 Data Box Heavy 的本機 Web UI 中的 [連線並複製]  頁面。
3. 請確定共用和資料夾名稱，以及資料遵循 [Azure 儲存體和 Data Box Heavy 服務限制](data-box-heavy-limits.md)中所述的指導方針。

## <a name="prepare-to-ship"></a>準備寄送

完成這項作業的時間取決於您的資料大小。

1. 在資料複製完成且未發生錯誤後，前往本機 Web UI 中的 [準備寄送]  頁面，並開始準備寄送。
2. 在這兩個節點上順利完成**準備寄送**後，從本機 Web UI 關閉裝置。

## <a name="ship-to-azure"></a>寄送到 Azure

此作業約需要 15-20 分鐘來完成。

1. 移除纜線，然後將其放回裝置背面的線槽。
2. 與區域性貨運公司排定取貨時間。
3. 連絡 [Data Box Operations](mailto:DataBoxOps@microsoft.com)，告知取貨事宜並取得寄返標籤。
4. 退貨標籤應清楚顯示在裝置的前面板上。

## <a name="verify-data"></a>驗證資料

完成這項作業的時間取決於您的資料大小。

1. 當 Data Box Heavy 裝置連線到 Azure 資料中心網路時，資料就會自動上傳到 Azure。
2. Data Box 服務會通知您，已透過 Azure 入口網站完成資料複製。

    1. 檢查所有失敗的錯誤記錄，並採取適當的動作。
    2. 請先確認您的資料位於儲存體帳戶中，再從來源予以刪除。

## <a name="clean-up-resources"></a>清除資源

完成此步驟需要 2-3 分鐘。

- 訂單處理之前，您可以在 Azure 入口網站中取消 Data Box Heavy 訂單。 一旦處理訂單後，就無法取消訂單。 訂單會進行直到達到已完成的階段為止。 若要取消訂單，請前往 [概觀]  ，然後從命令列按一下 [取消]  。

- 一旦 Azure 入口網站中的狀態顯示為 [已完成]  或是 [已取消]  後，您就可以刪除訂單。 若要刪除訂單，請前往 [概觀]  ，然後從命令列按一下 [刪除]  。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已部署 Data Box Heavy，以協助將您的資料匯入 Azure。 若要深入了解 Azure Data Box Heavy 管理，請進入下列教學課程：

> [!div class="nextstepaction"]
> [使用 Azure 入口網站來管理 Data Box Heavy](data-box-portal-admin.md)

::: zone-end