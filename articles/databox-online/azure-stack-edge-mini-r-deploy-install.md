---
title: 安裝 Azure Stack Edge Mini R 實體裝置的教學課程 | Microsoft Docs
description: 關於安裝 Azure Stack Edge Mini R 裝置的第二個教學課程將說明如何接通實體裝置的電源和網路。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Mini R device in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 4f7656337b12cf477c5c71d861d031919e0d55d6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463773"
---
# <a name="tutorial-install-azure-stack-edge-mini-r"></a>教學課程：安裝 Azure Stack Edge Mini R

本教學課程說明如何安裝 Azure Stack Edge Mini R 實體裝置。 安裝程序包括為裝置接上纜線。

安裝作業可能需要大約 30 分鐘才能完成。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 檢查裝置
> * 接上裝置纜線

## <a name="prerequisites"></a>必要條件

安裝實體裝置的必要條件如下：

### <a name="for-the-azure-stack-edge-resource"></a>針對 Azure Stack Edge 資源

在您開始前，請確定：

* 您已完成[準備部署 Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md) 中的所有步驟。
    * 您已建立 Azure Stack Edge 資源以部署裝置。
    * 您已產生啟用金鑰，以啟用具有 Azure Stack Edge 資源的裝置。

 
### <a name="for-the-azure-stack-edge-mini-r-physical-device"></a>針對 Azure Stack Edge Mini R 實體裝置

在部署裝置之前：

- 確定裝置安全地放在平坦、穩定的工作介面。
- 確認您要設定的網站具有：
    - 獨立來源的標準 AC 電源，或
    - 機架電源配置單位 (PDU)。 
    

### <a name="for-the-network-in-the-datacenter"></a>針對資料中心內的網路

開始之前：

- 檢閱部署 Azure Stack Edge Mini R 的網路需求，並根據每個需求設定資料中心網路。 如需詳細資訊，請參閱 [Azure Stack Edge 網路需求](azure-stack-edge-mini-r-system-requirements.md#networking-port-requirements)。

- 確定網際網路頻寬至少有 20 Mbps，以便讓裝置能夠發揮最大功能。 <!-- engg TBC -->


## <a name="inspect-the-device"></a>檢查裝置

這個裝置是以單箱出貨。 完成下列步驟以打開裝置的包裝。

1. 將箱子放置在平坦的表面上。
2. 檢查裝置外箱是否有任何損毀。 開啟外箱並檢查裝置。 若外箱或裝置有任何損毀，請連絡 Microsoft 支援服務，以協助您評估裝置是否可以正常運作。
3. 開啟外箱之後，請確定箱中內容包含：
    - 一個可攜的 Azure Stack Edge Mini R 裝置，且附加側邊緩衝器
    - 已有一個電池和背板與裝置連結。 
    - 將電池連接到電源的一條電源線 

如果您未收到上述所有項目，請連絡 Azure Stack Edge 支援人員。 下一步是為裝置接線。


## <a name="cable-the-device"></a>接上裝置纜線

下列程序說明如何為 Azure Stack Edge 裝置接上電源線和網路線。

開始為裝置接上纜線之前，您需要下列項目：

- 要安裝的 Azure Stack Edge Mini R 實體裝置。
- 一條電源纜線。
- 至少一條連接到管理介面的 1-GbE RJ-45 網路線。 裝置上有兩個 1-GbE 網路介面：一個管理介面和一個資料介面。
- 每個要設定的資料網路介面各一條 10-GbE SFP+ 銅纜線。 至少一個來自連接埠 3 或連接埠 4 的資料網路介面必須連接到網際網路 (使用與 Azure 的連線)。  
- 存取一個電源分配單元 (建議)。

> [!NOTE]
> - 如果您只連接一個資料網路介面，建議您使用 10-GbE 網路介面 (例如連接埠 3 或連接埠 4) 將資料傳送至 Azure。 
> - 為了達到最佳效能以及處理大量資料，請考慮連接所有資料連接埠。
> - Azure Stack Edge 裝置應連接到資料中心網路，以便擷取來自資料來源伺服器的資料。 <!-- engg TBC -->

在 Azure Stack Edge 裝置上：

- 前端面板具有 SSD 電訊廠商資訊。 

    - 裝置在插槽中有 1 個 SSD 硬碟。 
    - 裝置也有 CFx 卡，可作為作業系統磁碟的儲存體。
    
- 前端面板具有網路介面且可存取 Wi-Fi。

    - 2 個 1 GbE RJ 45 網路介面。 這些是裝置的本機 UI 上的連接埠 1 和連接埠 2。
    - 2 個 10 GbE SFP+ 的網路介面。 這些是裝置的本機 UI 上的連接埠 3 和連接埠 4。 
    - 一個已附加 Wi-Fi 收發器的 Wi-Fi 收訊器。

- 前端面板也有電源按鈕。 

- 後端面板包含安裝在裝置上的電池和背板。 


執行下列步驟來為裝置接上電源線和網路線。

1. 識別裝置前端平面上的各種網路和儲存體元件。

    ![裝置上的網路和儲存體介面](./media/azure-stack-edge-mini-r-deploy-install/ports-front-plane.png)

2. 在裝置前端的左側角落底部找到電源按鈕。 

    ![裝置前端平面具有裝置電源按鈕](./media/azure-stack-edge-mini-r-deploy-install/device-power-button.png)

3. 電池已連接到裝置的後端平面。 找出位於電池上的第二個電源按鈕。 

    ![裝置前端平面具有電池電源按鈕](./media/azure-stack-edge-mini-r-deploy-install/battery-power-button.png)


    將電源線的一端連接到電池，另一端連接到電源插座。 

    ![電源線連接](./media/azure-stack-edge-mini-r-deploy-install/power-cord-connector-1.png) 

    僅使用電池執行時 (電池未連接到電源)，前端的電源開關和電池上的電源開關都應該切換為開啟位置。 當電池連線到電源時，只有裝置前方的電源按鈕應該切換為開啟。 

4. 按下前端平面的電源按鈕開啟裝置。 
    
    > [!NOTE]
    > 若要開啟或關閉裝置電源，您必須在電源按鈕頂端將黑色按鈕按起，然後將電源按鈕切換為開啟或關閉位置。 

5. 如果在此裝置上設定 Wi-Fi，請參閱下列接線圖：

    ![接線以使用 Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)  

    - 將 1-GbE 網路介面連接埠 1 連接到用來設定實體裝置的電腦。 連接埠 1 是專用的管理介面。


    如果使用此裝置的有線配置，請使用下圖：
     
    ![接線以使用網路](./media/azure-stack-edge-mini-r-deploy-install/wired-cabled.png)     
    - 將 1-GbE 網路介面連接埠 1 連接到用來設定實體裝置的電腦。 連接埠 1 是專用的管理介面。
    - 將連接埠 2、連接埠 3 或連接埠 4 的其中一或多個連接埠連接到資料中心網路/網際網路。
    
        - 如果連接到連接埠 2，請使用 RJ-45 網路線。
        - 針對 10-GbE 網路介面，請使用 SFP + 銅纜線。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure Stack Edge 的相關主題，像是如何：

> [!div class="checklist"]
> * 打開裝置包裝
> * 接上裝置纜線

請繼續進行下一個教學課程，以了解如何連線、設定及啟用您的裝置。

> [!div class="nextstepaction"]
> [連線並設定 Azure Stack Edge](./azure-stack-edge-mini-r-deploy-connect.md)
