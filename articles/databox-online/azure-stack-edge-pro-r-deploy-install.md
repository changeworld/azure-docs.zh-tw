---
title: 安裝 Azure Stack Edge Pro R 實體裝置的教學課程 | Microsoft Docs
description: 關於安裝 Azure Stack Edge Pro R 的第二個教學課程將說明如何接通實體裝置的電源和網路。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/18/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Pro R in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: b67da2607d206424f69f53645eda148495ea58ec
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463661"
---
# <a name="tutorial-install-azure-stack-edge-pro-r"></a>教學課程：安裝 Azure Stack Edge Pro R

本教學課程說明如何安裝 Azure Stack Edge Pro R 實體裝置。 安裝程序包括為裝置接上纜線。

安裝作業可能需要大約 30 分鐘才能完成。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 檢查裝置
> * 接上裝置纜線

## <a name="prerequisites"></a>必要條件

安裝實體裝置的必要條件如下：

### <a name="for-the-azure-stack-edge-resource"></a>針對 Azure Stack Edge 資源

在您開始前，請確定：

* 您已完成[準備部署 Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md) 中的所有步驟。
    * 您已建立 Azure Stack Edge 資源以部署裝置。
    * 您已產生啟用金鑰，以啟用具有 Azure Stack Edge 資源的裝置。

 
### <a name="for-the-azure-stack-edge-pro-r-physical-device"></a>針對 Azure Stack Edge Pro R 實體裝置

在部署裝置之前：

- 確定裝置安全地放在平坦、穩定的工作介面。
- 確認您要設定的網站具有：
    - 獨立來源的標準 AC 電源

        -或-
    - 機架電源配置單位 (PDU)。 裝置隨附不斷電供應系統 (UPS)
    

### <a name="for-the-network-in-the-datacenter"></a>針對資料中心內的網路

開始之前：

- 檢閱部署 Azure Stack Edge Pro R 的網路需求，並根據每個需求設定資料中心網路。 如需詳細資訊，請參閱 [Azure Stack Edge Pro R 網路需求](azure-stack-edge-pro-r-system-requirements.md#networking-port-requirements)。

- 確定網際網路頻寬至少有 20 Mbps，以便讓裝置能夠發揮最大功能。


## <a name="inspect-the-device"></a>檢查裝置

這個裝置是以單箱出貨。 完成下列步驟以打開裝置的包裝。

1. 將箱子放置在平坦的表面上。
2. 檢查裝置外箱是否有任何損毀。 開啟外箱並檢查裝置。 若外箱或裝置有任何損毀，請連絡 Microsoft 支援服務，以協助您評估裝置是否可以正常運作。
3. 開啟外箱之後，請確定箱中內容包含：
    - 一個單一機殼的 Azure Stack Edge Pro R 裝置
    - 一個 不斷電供應系統 (UPS)
    - 2 條將裝置連接到 UPS 的短電源線
    - 1 條連接至電源來源的電源線

如果您未收到上述所有項目，請連絡 Azure Stack Edge Pro R 支援人員。 下一步是為裝置接線。


## <a name="cable-the-device"></a>接上裝置纜線

下列程序說明如何為 Azure Stack Edge Pro R 裝置接上電源線和網路線。

開始為裝置接上纜線之前，您需要下列項目：

- 要安裝的 Azure Stack Edge Pro R 實體裝置。
- 一條電源纜線。
- 至少一條連接到管理介面的 1-GbE RJ-45 網路線。 裝置上有兩個 1-GbE 網路介面：一個管理介面和一個資料介面。
- 每個要設定的資料網路介面各一條 10/25-GbE SFP+ 銅纜線。 至少一個來自連接埠 3 或連接埠 4 的資料網路介面必須連接到網際網路 (使用與 Azure 的連線)。  
- 存取一個電源分配單元 (建議)。

> [!NOTE]
> - 如果您只連接一個資料網路介面，建議您使用 25/10-GbE 網路介面 (例如連接埠 3 或連接埠 4) 將資料傳送至 Azure。 
> - 為了達到最佳效能以及處理大量資料，請考慮連接所有資料連接埠。
> - Azure Stack Edge Pro R 裝置應連接到資料中心網路，以便擷取來自資料來源伺服器的資料。

在 Azure Stack Edge Pro R 裝置上：

- 前端面板有磁碟機和電源按鈕。

    - 裝置前面有 8 個磁碟插槽。
    - 裝置中也有 2 個 X M. 2 個 X M.2 SATA 磁碟作為作業系統磁碟。 

- 裝置背面配置了備援的電源供應器 (PSU)。
- 裝置背面有四個網路介面：

    - 兩個 1-Gbps 介面。
    - 兩個 25-Gbps介面 (也可以當作 10-Gbps 介面)。
    - 基礎板管理控制器 (BMC)。

<!--- The back plane has two network cards corresponding to the 4 ports:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

For a full list of supported cables, switches, and transceivers for these network cards, go to [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).-->
 
執行下列步驟來為裝置接上電源線和網路線。

1. 識別裝置背面上的各個連接埠。

    ![已連接纜線的裝置背面](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)

2. 在裝置的前面找出磁碟插槽和電源按鈕。

    ![裝置的前面](./media/azure-stack-edge-pro-r-deploy-install/device-front-plane-labeled-1.png)

3. 將電源線的一端連接到 UPS。 將電源線的另一端連接至機架配電裝置 (PDU)。 
5. 按下電源按鈕來開啟裝置。
6. 將 1-GbE 網路介面連接埠 1 連接到用來設定實體裝置的電腦。 連接埠 1 是專用的管理介面。
7. 將連接埠 2、連接埠 3 或連接埠 4 的其中一或多個連接埠連接到資料中心網路/網際網路。

    - 如果連接到連接埠 2，請使用 RJ-45 網路線。
    - 針對 10/25-GbE 網路介面，請使用 SFP + 銅纜線。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure Stack Edge Pro R 的相關主題，像是如何：

> [!div class="checklist"]
> * 打開裝置包裝
> * 接上裝置纜線

前進至下一個教學課程，以了解如何連線至您的裝置。

> [!div class="nextstepaction"]
> [連線至 Azure Stack Edge Pro R](./azure-stack-edge-pro-r-deploy-connect.md)
