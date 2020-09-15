---
title: 在 Azure 入口網站中連線、設定及啟用搭配 GPU 的 Azure Stack Edge 裝置的教學課程 | Microsoft Docs
description: 部署 Azure Stack Edge GPU 的教學課程將說明如何連線、設定和啟動實體裝置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 3b500a7d93d19bcc0cd9c23d64ad079f0abfd353
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89421351"
---
# <a name="tutorial-connect-to-azure-stack-edge-with-gpu"></a>教學課程：連線至搭配 GPU 的 Azure Stack Edge

本教學課程說明如何使用本機 Web UI 來連線至搭配上線 GPU 的 Azure Stack Edge 裝置。

連線程序需要約 5 分鐘才能完成。

在本教學課程中，您會了解：

> [!div class="checklist"]
>
> * 必要條件
> * 連線至實體裝置


## <a name="prerequisites"></a>必要條件

在您設定及安裝搭配 GPU 的 Azure Stack Edge 裝置之前，請先確定：

* 您已依照[安裝 Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md) 中的詳細說明安裝實體裝置。


## <a name="connect-to-the-local-web-ui-setup"></a>連線至本機 Web UI 設定

1. 使用靜態 IP 位址 192.168.100.5 和子網路 255.255.255.0，在電腦上設定要連線到 Azure Stack Edge 裝置的乙太網路介面卡。

2. 將電腦連線到裝置上的連接埠 1。 如果要將電腦直接連線到裝置 (不透過交換器)，請使用跳接纜線或 USB 乙太網路介面卡。 請使用下圖來識別您裝置上的連接埠 1。

    ![已連接纜線的裝置後擋板](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    裝置的後擋板看起來可能會略有不同，視您收到的確切型號而定。 如需相關資訊，請參閱[將裝置接上纜線](azure-stack-edge-gpu-deploy-install.md#cable-the-device)。


3. 開啟瀏覽器視窗，然後在 `https://192.168.100.10` 存取裝置的本機 Web UI。  
    在您開啟裝置之後，此動作可能需要花費幾分鐘的時間。

    您會看到錯誤或警告指出網站的安全性憑證有問題。 
   
    ![網站安全性憑證錯誤訊息](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. 選取 [繼續瀏覽此網頁]。  
    這些步驟可能會因您使用的瀏覽器而有所不同。

5. 登入裝置的 Web UI。 預設密碼為 *Password1*。 
   
    ![Azure Stack Edge 裝置的登入頁面](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. 出現提示時，變更裝置系統管理員密碼。  
    新密碼必須包含 8 到 16 個字元。 此密碼必須包含下列其中 3 種字元：大寫、小寫、數字及特殊字元。

您現在位於裝置的**概觀**頁面。 下一步是設定裝置的網路設定。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解：

> [!div class="checklist"]
> * 必要條件
> * 連線至實體裝置


若要了解如何為您的 Azure Stack Edge 裝置設定網路設定，請參閱：

> [!div class="nextstepaction"]
> [設定網路](./azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
