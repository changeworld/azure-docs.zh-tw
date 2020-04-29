---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 51e1fd18b52d7e215ba43be540156199fb41778e
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "67174013"
---
#### <a name="to-attach-the-sas-cables"></a>連接 SAS 纜線
1. 識別主要機箱與 EBOD 機箱。 可以藉由查看個別後擋板來識別兩個機箱。 如需指引，請參閱下圖。 
   
    ![主要機箱與 EBOD 機箱的背面](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **主要機箱與 EBOD 機箱的背面檢視**
   
   | 標籤 | 說明 |
   |:--- |:--- |
   | 1 |主要機箱 |
   | 2 |EBOD 機箱 |
2. 找出主要機箱與 EBOD 機箱的序號。 序號貼紙貼在每個機箱的背後。 兩個機箱上的序號必須相同。 [連絡 Microsoft 支援服務](../articles/storsimple/storsimple-contact-microsoft-support.md) 。 請參閱下圖以協助您尋找序號。
   
    ![顯示序號的機箱後方檢視](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **序號貼紙的位置**
   
   | 標籤 | 說明 |
   |:--- |:--- |
   | 1 |機箱耳 |
3. 接下來，使用提供的 SAS 纜線以連接主要機箱與 EBOD 機箱，如下所示：
   
   1. 識別主要機箱與 EBOD 機箱上的四個 SAS 連接埠。 SAS 連接埠標在主要機箱上標示為 EBOD，對應到 EBOD 機箱上的連接埠 A，如底下的 SAS 佈線圖所示。
   2. 使用提供的 SAS 纜線以連接 EBOD 連接埠與連接埠 A。
   3. 控制器 0 的 EBOD 連接埠應該連接至 EBOD 控制器 0 的連接埠 A。 控制器 1 的 EBOD 連接埠應該連接至 EBOD 控制器 1 的連接埠 A。 如需指引，請參閱下圖。 
      
      ![您裝置的 SAS 纜線](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **SAS 佈線**
      
      | 標籤 | 說明 |
      |:--- |:--- |
      | A |主要機箱 |
      | B |EBOD 機箱 |
      | 1 |控制器 0 |
      | 2 |控制器 1 |
      | 3 |EBOD 控制器 0 |
      | 4 |EBOD 控制器 1 |
      | 5, 6 |主要機箱上的 SAS 連接埠 (標示為 EBOD) |
      | 7、8 |EBOD 機箱上的 SAS 連接埠 (連接埠 A) |

