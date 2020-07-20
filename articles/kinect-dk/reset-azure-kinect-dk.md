---
title: 重設 Azure Kinect DK
description: 說明如何將 Azure Kinect DK 裝置重設為其原廠映像
author: Teresa-Motiv
ms.author: v-tea
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/11/2020
ms.topic: how-to
keywords: kinect, 重設
ms.custom:
- CI 113492
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: f851579712a9549c88f1ca652aea4f31457da132
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201893"
---
# <a name="reset-azure-kinect-dk"></a>重設 Azure Kinect DK

您可能會遇到必須將 Azure Kinect DK 重設回其原廠映像的情況 (例如，韌體更新未正確安裝)。

1. 關閉 Azure Kinect DK 的電源。 若要這麼做，請移除 USB 纜線和電源線。
  ![顯示螺絲位置且包含重設按鈕的圖。](media/reset-azure-kinect-dk-diagram.png)
1. 若要尋找重設按鈕，請移除腳架鎖孔中的螺絲。
1. 重新連接電源線。
1. 將已拉直的迴紋針尖端插入空的螺絲孔 (在腳架鎖孔中)。
1. 使用迴紋針來輕輕按住重設按鈕。
1. 當您按住重設按鈕時，請重新連接 USB 纜線。
1. 大約 3 秒之後，電源指示燈會變成琥珀色。 光線變更之後，請放開重設按鈕。  
   
   當您放開重設按鈕之後，電源指示燈會在裝置重設時閃爍白色和琥珀色。 
1. 等待電源指示燈變成純白色。
1. 將螺絲重新放入腳架鎖孔中，蓋住重設按鈕。
1. 使用 Azure Kinect 檢視器來確認韌體已重設。 若要這麼做，請啟動 [Azure Kinect 檢視器](azure-kinect-viewer.md)，然後選取 [裝置韌體版本資訊]  ，以查看您 Azure Kinect DK 上安裝的韌體版本。

請務必確定您已在裝置上安裝最新的韌體。 若要取得最新的韌體版本，請使用 Azure Kinect 韌體工具。 如需如何查看韌體狀態的詳細資訊，請參閱[查看裝置韌體版本](azure-kinect-firmware-tool.md#check-device-firmware-version)。

## <a name="related-topics"></a>相關主題

- [關 於 Azure Kinect DK](about-azure-kinect-dk.md)
- [設定 Azure Kinect DK](set-up-azure-kinect-dk.md)
- [Azure Kinect DK 硬體規格：作業環境](hardware-specification.md#operating-environment)
- [Azure Kinect 韌體工具](azure-kinect-firmware-tool.md)
- [Azure Kinect 檢視器](azure-kinect-viewer.md)
- [在多個 Azure Kinect DK 裝置之間進行同步](multi-camera-sync.md)
