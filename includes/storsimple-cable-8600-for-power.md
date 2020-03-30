---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174015"
---
#### <a name="to-cable-your-device-for-power"></a>將您的裝置接上纜線，以取得電源
> [!NOTE]
> 在 StorSimple 裝置上的兩個機箱都包括備援 PCM。 針對每個機箱，PCM 都必須安裝，並且連接到不同的電源來源，以確保高可用性。
> 
> 

1. 請確定所有 PCM 上的電源開關是在 OFF (關閉) 位置。
2. 在主要機箱上，將電源線連接至兩個 PCM。 在底下的電源佈線圖表中以紅色識別電源線。
3. 確定主要機箱上的兩個 PCM 使用不同的電源來源。
4. 將電源線連接至機架電源分配單元，如電源佈線圖表所示。
5. 針對 EBOD 機箱重複步驟 2 到 4。
6. 將每個 PCM 上的電源開關切換到 ON (開啟) 位置，以開啟 EBOD 機箱。
7. 確認 EBOD 機箱已開啟，方法是檢查 EBOD 控制器背面的綠色 LED 已開啟。
8. 將每個 PCM 的電源開關切換到 ON (開啟) 位置，以開啟主要機箱。
9. 確認系統已開啟，方法是確定裝置控制器 LED 已開啟。
10. 確定 EBOD 控制器和裝置控制站之間的連線為作用中，方法是確認 EBOD 控制器上 SAS 連接埠旁邊的 4 個 LED 是綠色。
    
    > [!IMPORTANT]
    > 若要確保系統的高可用性，我們建議您嚴格遵守電源佈線配置，如下圖所示。
    > 
    > 
    
    ![將您的 4U 裝置接上纜線，以取得電源](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **電源佈線**
    
    | 標籤 | 描述 |
    |:--- |:--- |
    | 1 |主要機箱 |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |控制器 0 |
    | 5 |控制器 1 |
    | 6 |EBOD 控制器 0 |
    | 7 |EBOD 控制器 1 |
    | 8 |EBOD 機箱 |
    | 9 |PDU |

