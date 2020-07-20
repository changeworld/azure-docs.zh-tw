---
title: 訂購 Azure Data Box Heavy 的教學課程 | Microsoft Docs
description: 深入了解部署必要條件以及如何訂購 Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 91d78975106299c3b3a6df6d8dee05f337dd339a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83742981"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>教學課程：訂購 Azure Data Box Heavy


Azure Data Box Heavy 是一項混合式解決方案，可讓您以快速、簡便而可靠的方式，將內部部署資料匯入 Azure 中。 您可將資料傳輸至 Microsoft 提供的 770 TB (約略的可用容量) 儲存裝置，然後將裝置寄回。 這項資料隨後會上傳至 Azure。

本教學課程說明如何訂購 Azure Data Box Heavy。 在本教學課程中，您會了解：

> [!div class="checklist"]
> * Data Box Heavy 的必要條件
> * 訂購 Data Box Heavy
> * 追蹤訂單狀態
> * 取消訂單

## <a name="prerequisites"></a>Prerequisites

在您部署裝置之前，請完成下列資料箱服務與裝置的組態必要條件。

### <a name="for-installation-site"></a>針對安裝位置

在您開始前，請確定：

- 裝置可通過標準規格的出入通道。 但請務必確定裝置可以通過您所有的入口。 裝置尺寸：寬度：26 吋 長度：48 吋 高度：28 吋。
- 如果打算將裝置安裝在一樓以外的樓層，必須透過電梯或坡道搬運裝置。 裝置的重量約為 500 磅。
- 確定您的資料中心有平坦的區域，且靠近可用的網路連線，以容納具有此磁碟使用量的裝置。

### <a name="for-service"></a>針對服務

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>針對裝置

在您開始前，請確定：
- 您的裝置已拆開包裝。
- 您已將主機電腦連線到資料中心網路。 Data Box Heavy 會從這部電腦複製資料。 您的主機電腦必須執行支援的作業系統，如 [Azure Data Box Heavy 系統需求](data-box-system-requirements.md)中所述。
- 您的筆記型電腦必須有用來連接到本機 UI 及設定裝置的 RJ-45 纜線。 使用筆記型電腦，一次設定好裝置的每個節點。
- 您的資料中心必須有高速網路。 強烈建議您具有至少一個 10 GbE 的連線。
- 每個裝置節點都需要一條 40 Gbps 或 10 Gbps 的纜線。 選擇與 [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) 網路介面相容的纜線：

    - 若使用 40 Gbps 的纜線，纜線的裝置端必須是 QSFP+。
    - 若使用 10 Gbps 的纜線，您需要將 SFP+ 纜線插入其中一端上的 10 G 交換器，並在插入裝置的一端使用 QSFP+ 對 SFP+ 的配接器 (或是 QSA 配接器)。
    - 裝置隨附電源線。

## <a name="order-data-box-heavy"></a>訂購 Data Box Heavy

請在 Azure 入口網站中執行下列步驟，以訂購裝置。

1. 使用您的 Microsoft Azure 認證在以下 URL 登入：[https://portal.azure.com](https://portal.azure.com)。
2. 選取 [+ 建立資源]，然後搜尋「Azure 資料箱」。 選取 [Azure 資料箱]。
    
   [![搜尋 Azure 資料箱 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. 選取 [建立]。

4. 確認您的區域是否適用資料箱服務。 輸入或選取下列資訊，然後選取 [套用]。

    |設定  |值  |
    |---------|---------|
    |訂用帳戶     | 為資料箱服務選取 EA、CSP 或 Azure 贊助訂用帳戶。 <br> 訂用帳戶會連結到您的帳單帳戶。       |
    |傳輸類型     | 選取 [匯入至 Azure]。        |
    |來源國家/區域     | 選取您的資料目前所在的國家/地區。         |
    |目的地 Azure 區域     | 選取要傳輸資料的 Azure 區域。        |

    [![選取資料箱系列的可用性](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. 選取 [Data Box Heavy]。 單一訂單的最大可用容量是 770 TB。

    [![選取 [Data Box Heavy]](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. 在 [訂單] 中，指定 [訂單詳細資料]。 輸入或選取下列資訊，然後選取 [下一步]。
    
    |設定  |值  |
    |---------|---------|
    |名稱     | 提供用來追蹤訂單的易記名稱。 <br> 名稱長度可介於 3 到 24 個字元之間，且可以是字母、數字和連字號。 <br> 名稱必須以字母或數字為開頭或結尾。      |
    |資源群組     | 使用現有的群組或建立新群組。 <br> 資源群組是適用於資源而可一併管理或部署的邏輯容器：         |
    |目的地 Azure 區域     | 選取儲存體帳戶的區域。 <br> 如需詳細資訊，請移至[區域可用性](https://azure.microsoft.com/global-infrastructure/services/?products=databox)。        |
    |儲存體目的地     | 從儲存體帳戶、受控磁碟或兩者中進行選擇。 <br> 根據指定的 Azure 區域，從現有儲存體帳戶的篩選清單中選取一或多個儲存體帳戶。 <br>Data Box Heavy 最多可以與 10 個儲存體帳戶連結。 <br> 您也可以建立新的**一般用途 v1**、**一般用途 v2** 或 **Blob 儲存體帳戶**。 <br>請參閱[您的裝置支援的儲存體帳戶](data-box-heavy-system-requirements.md#supported-storage-accounts)。 <br>支援具有虛擬網路的儲存體帳戶。 若要允許資料箱服務使用受保護的儲存體帳戶來運作，請在儲存體帳戶網路防火牆設定內啟用受信任的服務。 如需詳細資訊，請參閱如何[新增 Azure 資料箱服務作為受信任的服務](../storage/common/storage-network-security.md#exceptions)。|

    如果使用儲存體帳戶作為儲存體目的地，您就會看到下列螢幕擷取畫面：

    ![儲存體帳戶的 Data Box Heavy 訂單](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    如果您以儲存體帳戶作為儲存體目的地，同時又使用 Data Box Heavy 從內部部署 VHD 建立受控磁碟，則必須提供下列資訊：

    |設定  |值  |
    |---------|---------|
    |資源群組     | 如果您想要從內部部署 VHD 建立受控磁碟，請建立新的資源群組。 資源群組必須是在資料箱服務建立受控磁碟的 Data Box Heavy 訂單之前建立的，您才能使用現有的資源群組。 <br> 指定以分號分隔的多個資源群組。 最多支援 10 個資源群組。|

    ![受控磁碟的 Data Box Heavy 訂單](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    針對受控磁碟指定的儲存體帳戶不能當成暫存的儲存體帳戶來使用。 資料箱服務會先將 VHD 以分頁 Blob 形式上傳至暫存的儲存體帳戶，然後再將它轉換為受控磁碟並移至資源群組。 如需詳細資訊，請參閱[確認資料上傳至 Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure)。

7. 在 [交貨地址] 中，提供您的姓名、公司的名稱和郵寄地址，以及有效的電話號碼。 選取 [驗證地址]。 

    服務會驗證交貨地址以確認服務可用性。 如果服務可提供至指定的交貨地址，您將會收到該項通知。 選取 [下一步] 。

8. 在 [通知詳細資料] 中，指定電子郵件地址。 服務會將關於任何訂單狀態更新的電子郵件通知傳送至指定的電子郵件地址。

    建議您使用群組電子郵件，以便在群組中的管理員離開時繼續接收通知。

9. 檢閱與訂單、連絡人、通知和隱私權條款有關的資訊**摘要**。 請勾選隱私權條款合約的對應方塊。

10. 選取 [訂單]。 建立訂單需要幾分鐘的時間。


## <a name="track-the-order"></a>追蹤訂單狀態

在您下訂單之後，可以從 Azure 入口網站來追蹤訂單狀態。 請移至您的 Data Box Heavy 訂單，然後移至 [概觀] 以檢視狀態。 入口網站會顯示處於 [已訂購] 狀態的訂單。

如果裝置無法使用，您會收到通知。 如果可使用服務，Microsoft 會識別要寄送的裝置，並準備出貨。 在裝置準備期間，會執行下列動作：

- 系統會針對與裝置相關聯的每個儲存體帳戶建立 SMB 共用。
- 針對每個共用，會產生例如使用者名稱和密碼的存取認證。
- 也會產生可協助將裝置解除鎖定的裝置密碼。
- Data Box Heavy 會鎖定，防止任何對裝置的未經授權存取。

裝置準備完成後，入口網站會顯示訂單處於 [已處理] 狀態。

![Data Box Heavy 訂單已處理](media/data-box-overview/data-box-order-status-processed.png)

接著，Microsoft 會準備裝置，並透過區域貨運公司派送給您。 在裝置送達後，您會收到追蹤號碼。 入口網站會顯示訂單處於 [已分派] 狀態。

![Data Box Heavy 訂單已分派](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>取消訂單

若要取消此訂單，請在 Azure 入口網站中移至 [概觀]，然後從命令列按一下 [取消]。

下訂單之後，您可以在訂單狀態標示為已處理之前的任何時間點，取消訂單。
 
若要刪除已取消的訂單，請移至 [概觀]，然後從命令列按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure Data Box Heavy 的相關主題，像是：

> [!div class="checklist"]
> * Prerequisites
> * 訂購 Data Box Heavy
> * 追蹤訂單狀態
> * 取消訂單

請繼續進行下一個教學課程，以了解如何設定 Data Box Heavy。

> [!div class="nextstepaction"]
> [設定您的 Azure Data Box Heavy](./data-box-heavy-deploy-set-up.md)
