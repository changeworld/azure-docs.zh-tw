---
title: 在 Azure 入口網站中連線到 Azure Stack Edge Mini R 的教學課程
description: 了解如何使用本機 Web UI 來連線至 Azure Stack Edge Mini R 裝置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: fe76391a5cfce8d7d39e47131db108ab87e5aed5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463781"
---
# <a name="tutorial-connect-to-azure-stack-edge-mini-r"></a>教學課程：連線至 Azure Stack Edge Mini R

本教學課程說明如何使用本機 Web UI 來連線至 Azure Stack Edge Mini R 裝置。

連線程序需要約 5 分鐘才能完成。

在本教學課程中，您會了解：

> [!div class="checklist"]
>
> * 必要條件
> * 連線至實體裝置



## <a name="prerequisites"></a>必要條件

在您設定及安裝 Azure Stack Edge 裝置之前，請先確定：

* 您已依照[安裝 Azure Stack Edge](azure-stack-edge-mini-r-deploy-install.md) 中的詳細說明安裝實體裝置。


## <a name="connect-to-the-local-web-ui-setup"></a>連線至本機 Web UI 設定

1. 使用靜態 IP 位址 192.168.100.5 和子網路 255.255.255.0，在電腦上設定要連線到 Azure Stack Edge Pro 裝置的乙太網路介面卡。

2. 將電腦連線到裝置上的連接埠 1。 如果要將電腦直接連線到裝置 (不透過交換器)，請使用跳接纜線或 USB 乙太網路介面卡。 請使用下圖來識別您裝置上的連接埠 1。

    ![接線以使用 Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)

[!INCLUDE [azure-stack-edge-gateway-delpoy-connect](../../includes/azure-stack-edge-gateway-deploy-connect.md)]


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解：

> [!div class="checklist"]
> * 必要條件
> * 連線至實體裝置


若要了解如何為您的裝置設定網路設定，請參閱：

> [!div class="nextstepaction"]
> [設定網路](./azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
