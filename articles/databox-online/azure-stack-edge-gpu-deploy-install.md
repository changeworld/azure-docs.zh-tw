---
title: 打開包裝、裝設機架、連接 Azure Stack Edge Pro GPU 實體裝置纜線的安裝教學課程 | Microsoft Docs
description: 關於安裝 Azure Stack Edge Pro GPU 的第二個教學課程將說明如何打開包裝，然後接上實體裝置的機架和纜線。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/02/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Pro in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: b9f3b564e36939197acd532a37f9a6098fbc870f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90900008"
---
# <a name="tutorial-install-azure-stack-edge-pro-with-gpu"></a>教學課程：安裝搭配 GPU 的 Azure Stack Edge Pro

本教學課程說明如何安裝搭配 GPU 的 Azure Stack Edge Pro 實體裝置。 安裝程序包括打開包裝、以機架掛接裝置，並為裝置接上纜線。 

安裝大約需要兩小時才能完成。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 打開裝置包裝
> * 以機架掛接裝置
> * 接上裝置纜線

## <a name="prerequisites"></a>必要條件

安裝實體裝置的必要條件如下：

### <a name="for-the-azure-stack-edge-resource"></a>針對 Azure Stack Edge 資源

在您開始前，請確定：

* 您已完成[準備部署搭配 GPU 的 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) 中的所有步驟。
    * 您已建立 Azure Stack Edge 資源以部署裝置。
    * 您已產生啟用金鑰，以啟用具有 Azure Stack Edge 資源的裝置。

 
### <a name="for-the-azure-stack-edge-pro-physical-device"></a>針對 Azure Stack Edge Pro 實體裝置

在部署裝置之前：

- 確定裝置安全地放在平坦、穩定的工作介面。
- 確認您要設定的網站具有：
    - 獨立來源的標準 AC 電源。

        -或-
    - 具有不斷電供應系統 (UPS) 的機架配電裝置 (PDU)。
    - 您要掛接裝置的機架上有 1U 插槽可供使用。

### <a name="for-the-network-in-the-datacenter"></a>針對資料中心內的網路

開始之前：

- 檢閱部署 Azure Stack Edge Pro 的網路需求，並根據每個需求設定資料中心網路。 如需詳細資訊，請參閱 [Azure Stack Edge Pro 網路需求](azure-stack-edge-system-requirements.md#networking-port-requirements)。

- 確定網際網路頻寬至少有 20 Mbps，以便讓裝置能夠發揮最大功能。


## <a name="unpack-the-device"></a>打開裝置包裝

這個裝置是以單一箱裝出貨。 完成下列步驟以打開裝置的包裝。 

1. 將箱子放置在平坦的表面上。
2. 檢查箱子及包裝發泡材料有無損毀、割痕、浸水，或任何其他明顯損傷。 如果箱子或包裝嚴重損毀，請不要打開它。 請連絡 Microsoft 支援服務，以協助您評估裝置是否正常運作。
3. 打開箱子。 打開箱子包裝之後，請確定您有：
    - 一個單一機殼的 Azure Stack Edge Pro 裝置
    - 兩條電源線
    - 一個滑軌套件組件
    - 安全、環境和法規資訊手冊

如果您未收到此處列出所有項目，請[連絡 Microsoft 支援服務](azure-stack-edge-contact-microsoft-support.md)。 下一步是利用機架掛接裝置。


## <a name="rack-the-device"></a>安裝裝置機架

裝置必須安裝在標準的 19 吋機架上。 使用下列程序，在標準 19 吋機架上裝載您的裝置。

> [!IMPORTANT]
> Azure Stack Edge Pro 裝置需要機架掛接才能正常運作。


### <a name="prerequisites"></a>必要條件

- 開始之前，請閱讀「安全、環境和法規資訊手冊」中的安全指示。 此手冊隨附於裝置。
- 開始在最靠近機架機箱底部的配置空間中安裝滑軌。
- 安裝工具式滑軌時：
    -  您需要提供八個螺絲：#10-32、#12-24、#M5 或 #M6。 螺絲頭直徑必須小於 10 公釐 (0.4 吋)。
    -  您需要一字型螺絲起子。

### <a name="identify-the-rail-kit-contents"></a>找出滑軌套件內容

找出用於安裝滑軌套件組件的元件：
- 兩個 A7 Dell ReadyRails II 滑軌組件
- 兩個黏扣帶

    ![找出滑軌套件內容](./media/azure-stack-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>安裝及移除免工具滑軌 (方形孔或圓形孔機架)

> [!TIP]
> 這個選項免用工具，因為其不需要工具就能將滑軌安裝到機架中的無螺紋方形孔或圓形孔，以及從中移除滑軌。

1. 將標示 **FRONT** 的左邊和右邊滑軌端件朝內放置，並將每個端件放入垂直機架凸緣前側的洞孔中。
2. 將每個端件對齊所需 U 形空間的底端和頂端洞孔。
3. 嚙合滑軌後端，直到它完全裝在垂直機架凸緣且閂鎖卡入定位為止。 重複上述步驟，將前端件安置在垂直機架凸緣上。
4. 若要移除滑軌，請拉起端件中央的閂鎖解除按鈕並取下每個滑軌。

    ![安裝及移除免工具滑軌](./media/azure-stack-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>安裝及移除工具式滑軌 (已穿孔的機架)

> [!TIP]
> 這個選項需用到工具，因為其需要工具 (一字型螺絲起子__) 才能將滑軌安裝到機架中的螺紋圓形孔，以及從中移除滑軌。

1. 使用一字型螺絲起子，從前端和後端安裝托架移除定位銷。
2. 拉開並旋轉滑軌閂鎖子組件，從安裝托架將其移除。
3. 使用兩組螺絲，將左右兩側的安裝滑軌固定到前端垂直機架凸緣。
4. 靠著尾端垂直機架凸緣，將左右兩邊的背面托架向前滑動，並使用兩組螺絲加以固定。

    ![安裝及移除工具式滑軌](./media/azure-stack-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>在機架中安裝系統

1. 從機架中拉出內部滑軌，直到它們卡入定位為止。
2. 找到系統每一側的尾端滑軌螺柱，將其降低放入滑動組件上的尾端 J 形槽。 向下轉動系統，直到所有滑軌螺柱裝入 J 形槽為止。
3. 將系統向內推，直到鎖桿卡入定位為止。
4. 按住兩個滑軌上的滑動解鎖按鈕，並將系統滑入機架。

    ![在機架中安裝系統](./media/azure-stack-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>從機架中移除系統

1. 找到內部滑軌側邊的鎖桿。
2. 將每個鎖桿旋轉至其解除位置，將其解除鎖定。
3. 牢牢抓住系統的側邊，將它向前推，直到滑軌螺柱位於 J 形槽前端為止。 提起系統並從機架取出，將它放在平面上。

    ![從機架中移除系統](./media/azure-stack-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>嚙合及解除閉合閂鎖

> [!NOTE]
> 對於未配備閉合閂鎖的系統，使用螺絲固定系統 (如此程序的步驟 3 所述)。

1. 面向前方，找到系統任一側的閉合閂鎖。
2. 閂鎖會在系統推入機架時自動嚙合，而向上拉起閂鎖就會解除。
3. 若要固定系統，以便在機架或其他不穩定的環境中運送，請找出每個閂鎖底下牢固裝配的螺絲，並使用 2 號十字型螺絲起子鎖緊每個螺絲。

    ![嚙合及解除閉合閂鎖](./media/azure-stack-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>接上裝置纜線

配置纜線，然後將裝置接上纜線。 下列程序說明如何為 Azure Stack Edge Pro 裝置接上電源線和網路線。

開始為裝置接上纜線之前，您需要下列項目：

- 已打開包裝並掛接機架的 Azure Stack Edge Pro 實體裝置。
- 兩條電源線。
- 至少一條連接到管理介面的 1-GbE RJ-45 網路線。 裝置上有兩個 1-GbE 網路介面：一個管理介面和一個資料介面。
- 每個要設定的資料網路介面各一條 25-GbE SFP + 銅纜線。 至少一個來自連接埠 2、連接埠 3、連接埠 4、連接埠 5 或連接埠 6 的資料網路介面必須連接到網際網路 (使用與 Azure 的連線)。  
- 可以存取兩個配電裝置 (建議)。
- 至少一個 1-GbE 網路交換器，可將 1-GbE 網路介面連線到網際網路以取得資料。 如果連線的交換器並非至少 1-GbE，將無法存取本機 Web UI。 如果使用 25/10 GbE 介面來取得資料，您需要 25-GbE 或 10-GbE 交換器。 

> [!NOTE]
> - 如果您只連接一個資料網路介面，建議您使用 25/10-GbE 網路介面 (例如連接埠 3、連接埠 4、連接埠 5 或連接埠 6) 將資料傳送至 Azure。 
> - 為了達到最佳效能以及處理大量資料，請考慮連接所有資料連接埠。
> - Azure Stack Edge Pro 裝置應連接到資料中心網路，以便擷取來自資料來源伺服器的資料。

在 Azure Stack Edge Pro 裝置上：

- 前端面板有磁碟機和電源按鈕。

    - 裝置前面有 10 個磁碟插槽。
    - 插槽 0 有用來作為作業系統磁碟的 240-GB SATA 磁碟機。 插槽 1 是空的，而插槽 2 到 6 是用來作為資料磁碟的 NVMe SSD。 插槽 7 到 9 也是空的。
- 裝置背面配置了備援的電源供應器 (PSU)。
- 裝置背面有六個網路介面：

    - 兩個 1-Gbps 介面。
    - 四個 25-Gbps介面 (也可以當作 10-Gbps 介面)。
    - 基礎板管理控制器 (BMC)。

- 裝置背面有兩張對應至 6 個連接埠的網路卡：

    - **自訂 Microsoft Qlogic Cavium 25G NDC 介面卡** - 連接埠 1 到連接埠 4。
    - **Mellanox 雙連接埠 25G ConnectX-4 通道網路介面卡** -連接埠 5 和連接埠 6。

如需支援這些網路卡的纜線、交換器及收發器清單，請移至：

- [Qlogic Cavium 25G NDC 介面卡互通性對照表](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)。
- [Mellanox 雙連接埠 25G ConnectX-4 通道網路介面卡相容產品](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)。  

 
執行下列步驟來為裝置接上電源線和網路線。

1. 識別裝置背面上的各個連接埠。 視裝置中的 GPU 數目而定，您可能已收到工廠寄出的下列其中一個裝置。

    - 具有 2 個周邊元件連接 (PCI) 插槽和一個 GPU 的裝置

        ![已連接纜線的裝置背面](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    - 具有 3 個 PCI 插槽和一個 GPU 的裝置

        ![已連接纜線的裝置背面](./media/azure-stack-edge-gpu-deploy-install/ase-three-pci-slots-one-gpu.png)

    - 具有 3 個 PCI 插槽和二個 GPU 的裝置

        ![已連接纜線的裝置背面](./media/azure-stack-edge-gpu-deploy-install/ase-three-pci-slots-two-gpu.png)

2. 在裝置的前面找出磁碟插槽和電源按鈕。

    ![裝置的前面](./media/azure-stack-edge-gpu-deploy-install/ase-gpu-device-front-plane-labeled.png)

3. 將電源線連接至機箱中的每個 PSU。 為了確保高可用性，請安裝兩個 PSU，並將其連接到不同的電源。
4. 將電源線連接至機架配電裝置 (PDU)。 請確定兩個 PSU 使用不同的電源。
5. 按下電源按鈕來開啟裝置。
6. 將 1-GbE 網路介面連接埠 1 連接到用來設定實體裝置的電腦。 連接埠 1 會作為管理介面。
    
    > [!NOTE]
    > 如果要將電腦直接連線到您的裝置 (不透過交換器)，請使用跳接纜線或 USB 乙太網路介面卡。

7. 將連接埠 2、連接埠 3、連接埠 4、連接埠 5 或連接埠 6 的其中一或多個連接埠連接到資料中心網路/網際網路。

    - 如果連接到連接埠 2，請使用 1-GbE RJ-45 網路線。
    - 針對 10/25-GbE 網路介面，請使用 SFP + 銅纜線。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure Stack Edge Pro 的相關主題，像是如何：

> [!div class="checklist"]
> * 打開裝置包裝
> * 安裝裝置機架
> * 接上裝置纜線

前進至下一個教學課程，以了解如何連線至您的裝置。

> [!div class="nextstepaction"]
> [連線 Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-connect.md)
