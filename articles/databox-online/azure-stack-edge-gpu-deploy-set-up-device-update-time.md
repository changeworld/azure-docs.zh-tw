---
title: 在 Azure 入口網站中連線、設定及啟用搭配 GPU 的 Azure Stack Edge Pro 裝置的教學課程 | Microsoft Docs
description: 部署 Azure Stack Edge Pro GPU 的教學課程將說明如何連線、設定和啟動實體裝置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 1f86b0fc847ade3153c8eaddb0d82bd968913b46
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90899661"
---
# <a name="tutorial-configure-device-settings-for-azure-stack-edge-pro-with-gpu"></a>教學課程：設定搭配 GPU 的 Azure Stack Edge Pro 裝置設定

本教學課程說明如何為搭配上線 GPU 的 Azure Stack Edge Pro 裝置設定裝置相關設定。 您可以透過本機 Web UI 來設定裝置名稱、更新伺服器和時間伺服器。

裝置設定可能需要約 5-7 分鐘才能完成。

在本教學課程中，您會了解：

> [!div class="checklist"]
>
> * 必要條件
> * 設定裝置設定
> * 設定更新 
> * 設定時間

## <a name="prerequisites"></a>必要條件

在設定搭配 GPU 的 Azure Stack Edge Pro 裝置相關設定之前，請確定：

* 對於實體裝置：

    - 您已依照[安裝 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md) 中的詳細說明安裝實體裝置。
    - 您已在裝置上設定網路，也啟用並設定了計算網路，如[教學課程：設定搭配 GPU 的 Azure Stack Edge Pro 網路](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。


## <a name="configure-device-settings"></a>設定裝置設定

請遵循這些步驟來設定裝置的相關設定：

1. 在**裝置**頁面上，採取下列步驟：

    1. 為裝置提供自訂名稱。 自訂名稱長度必須介於 1 到 13 個字元，且包含字母、數字及連字號。

    2. 為您的裝置提供 **DNS 網域**。 此網域用來將裝置設定為檔案伺服器。

    3. 若要驗證並套用所設定的時間設定，請選取 [套用]。

        ![本機 Web UI 的「裝置」頁面 1](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

        如果您已變更裝置名稱和 DNS 網域，裝置上自動產生的自我簽署憑證將無法使用。 設定憑證時，必須選擇下列其中一個選項： 
        
        - 產生並下載裝置憑證。 
        - 攜帶您自己的裝置憑證，包括簽署鏈結。
    

        ![本機 Web UI 的「裝置」頁面 2](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-3.png)

    4. 當裝置名稱和 DNS 網域變更時，會建立 SMB 端點。  

    5. 套用設定之後，請選取 [下一步:更新伺服器]。

        ![本機 Web UI 的「裝置」頁面](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-4.png)

## <a name="configure-update"></a>設定更新

1. 您現在可以在**更新**頁面中設定要下載裝置更新的位置。  

    - 您可以直接從 **Microsoft Update 伺服器**取得更新。

        ![本機 Web UI 的「更新伺服器」頁面](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-2.png)

        也可以選擇從 **Windows Server Update Services** (WSUS) 部署更新。 提供 WSUS 伺服器的路徑。
        
        ![本機 Web UI 的「更新伺服器」頁面](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-3.png)

        > [!NOTE] 
        > 如果已設定個別的 Windows Update 伺服器，而且您選擇透過 https (而不是 http) 連線，則需要用來連線到更新伺服器的簽署鏈結憑證。 如需如何建立和更新憑證的詳細資訊，請移至[管理憑證](azure-stack-edge-j-series-manage-certificates.md)。 

2. 選取 [套用]。
3. 設定更新伺服器之後，選取 [下一步:時間]。
    

## <a name="configure-time"></a>設定時間

請依照下列步驟設定裝置上的時間設定。 

> [!IMPORTANT]
> 雖然您可自由選擇是否要設定時間，但我們強烈建議您在裝置的區域網路上設定主要 NTP 和次要 NTP 伺服器。 如果本機伺服器無法使用，則可以設定公用 NTP 伺服器。

NTP 伺服器是必要的，因為您的裝置必須讓時間同步，才能與您的雲端服務提供者進行驗證。

1. 您可以在**時間**頁面中選取裝置的時區，以及主要和次要 NTP 伺服器。  
    
    1. 在 [時區] 下拉式清單中，選取與裝置部署所在地理位置相對應的時區。
        裝置的預設時區是太平洋標準時間。 裝置將針對所有排程的操作使用這個時區。

    2. 在 [主要 NTP 伺服器]  方塊中，輸入您裝置的主要伺服器，或接受預設值 time.windows.com。  
        請確定您的網路允許 NTP 流量從您的資料中心通過到網際網路。

    3. (選擇性) 在 [次要 NTP 伺服器]  方塊中，輸入您裝置的次要伺服器。

    4. 若要驗證並套用所設定的時間設定，請選取 [套用]  。

        ![本機 Web UI 的「時間」頁面](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/time-2.png)

2. 套用設定之後，請選取 [下一步:憑證]。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您會了解：

> [!div class="checklist"]
>
> * 必要條件
> * 設定裝置設定
> * 設定更新 
> * 設定時間

若要了解如何為您的 Azure Stack Edge Pro 裝置設定憑證，請參閱：

> [!div class="nextstepaction"]
> [設定憑證](./azure-stack-edge-gpu-deploy-configure-certificates.md)
