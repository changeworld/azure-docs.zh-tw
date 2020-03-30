---
title: 監視 Azure FXT Edge Filer
description: 如何監視 Azure FXT 邊緣檔案伺服器混合存儲緩存的硬體狀態
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 3f422339af2040ad81c585c0e193e6cb3667b135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72254880"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>監視 Azure FXT 邊緣檔程式硬體狀態

Azure FXT 邊緣 Filer 混合存儲緩存系統內置了多個主機殼狀態指示燈，以説明管理員瞭解硬體的工作原理。

## <a name="system-health-status"></a>系統運行狀況狀態

要監視更高級別的快取作業，請使用軟體控制台的**儀表板**頁面，如[控制台儀表板指南](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)中所述

## <a name="hardware-status-leds"></a>硬體狀態 LED

本節介紹 Azure FXT 邊緣檔程式硬體中內置的各種狀態指示燈。

### <a name="hard-drive-status-leds"></a>硬碟狀態 LED

![硬碟正面、水準、標注標籤 2（左上角）、1（左下角）和 3（右側）的圖片](media/fxt-monitor/fxt-drive-callouts.png)

每個磁碟機托架有兩個狀態 LED：活動指示器 （1） 和狀態指示器 （2）。 

* 當磁碟機正在使用時，活動 LED （1） 指示燈亮起。  
* 狀態 LED （2） 使用下表中的代碼指示磁碟機的狀況。

| 驅動狀態 LED 狀態              | 意義  |
|-------------------------------------|----------------------------------------------------------|
| 每秒閃爍綠色兩次      | 識別磁碟機*或* <br> 準備拆卸磁碟機  |
| 關閉（未點亮）                         | 系統尚未完成啟動*或* <br>磁碟機已準備好被移除 |
| 閃爍綠色、琥珀色和關閉       | 預測磁碟機故障   |
| 每秒呈琥珀色閃爍四次 | 磁碟機失敗   |
| 純綠色                         | 磁碟機已連線 |

磁碟機 （3） 的右側標有磁碟機的容量和其他資訊。

磁碟機與磁碟機之間的空間會印有磁碟機編號。 在 Azure FXT Edge Filer 中，磁碟機 0 是左上方的磁碟機，磁碟機 1 則在磁碟機 0 的正下方。 編號繼續以該模式進行。 

![FXT 底座中某個硬碟機擴充插槽的相片，其中顯示了磁碟機編號和容量標籤](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>左側控制台

左前控制台具有各種狀態 LED 指示燈 （1） 和大型照明系統運行狀況指示燈 （2）。 

![左側狀態面板，左側有 1 個標記狀態指示燈，右側 2 個標記大型系統運行狀況指示燈](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>控制台狀態指示器 

如果系統中出現錯誤，左側的狀態指示燈將顯示穩定的琥珀色光。 下表描述了錯誤的可能原因和解決方案。 

如果在嘗試這些解決方案後仍有錯誤，[請與支援人員聯繫](fxt-support-ticket.md)以尋求説明。 

| 圖示 | 描述 | 錯誤狀況 | 可能的解決方案 |
|----------------|---------------|--------------------|----------------------|
| ![磁碟機圖示](media/fxt-monitor/fxt-hd-icon.jpg) | 磁碟機狀態 | 磁碟機錯誤 | 檢查系統事件日誌以確定磁碟機是否有錯誤，或者 <br>運行適當的連線診斷測試;重新開機系統並運行嵌入式診斷 （ePSA），或 <br>如果磁碟機在 RAID 陣列中配置，請重新開機系統並輸入主機介面卡配置實用程式 |
|![溫度圖示](media/fxt-monitor/fxt-temp-icon.jpg) | 溫度狀態 | 熱誤差 - 例如，風扇出現故障或環境溫度超範圍 | 檢查以下可定址條件： <br>冷卻風扇丟失或出現故障 <br>系統蓋、空氣罩、記憶體模組空白或背面填充支架被移除 <br>環境溫度過高 <br>外部氣流受阻 |
|![電圖示](media/fxt-monitor/fxt-electric-icon.jpg) | 電氣狀態 | 電氣錯誤 - 例如，電壓超範圍、PSU 故障或電壓調節器故障 |  檢查系統事件日誌或系統消息中是否出現特定問題。 如果出現 PSU 問題，請檢查 PSU 狀態 LED，並根據需要重新安裝 PSU。 | 
|![記憶體圖示](media/fxt-monitor/fxt-memory-icon.jpg) | 記憶體狀態 | 記憶體錯誤 | 檢查系統事件日誌或系統消息中故障記憶體的位置;重新安裝記憶體模組。 |
|![PCIe 圖示](media/fxt-monitor/fxt-pcie-icon.jpg) | PCIe 狀態 | PCIe 卡錯誤 | 重新開機系統;更新 PCIe 卡驅動程式;重新安裝卡 |


### <a name="system-health-status-indicator"></a>系統運行狀況指示器

左側控制台右側的大照明按鈕指示整體系統狀態，並在系統 ID 模式下用作單元定位燈。

按系統運行狀況和 ID 按鈕在系統 ID 模式和系統運行狀況模式之間切換。

|系統運行狀況狀態 | 條件 |
|-------------------------------------------|-----------------------------------------------|
| 實心藍色 | 正常運行：系統處於打開狀態，工作正常，系統 ID 模式未啟動。 <br/>如果要切換到系統 ID 模式，請按系統運行狀況和 ID 按鈕。 |
| 閃爍的藍色 | 系統 ID 模式處於活動狀態。 如果要切換到系統運行狀況模式，請按系統運行狀況和系統 ID 按鈕。 |
| 純琥珀色 | 系統處於故障安全模式。 如果問題仍然存在，請聯繫[Microsoft 客戶服務和支援](fxt-support-ticket.md)。 |
| 呈琥珀色閃爍 | 系統故障。 檢查系統事件日誌中有關特定錯誤訊息。 有關由系統固件和監視系統元件的代理生成的事件和錯誤訊息的資訊，請參閱 qrl.dell.com 中的"錯誤代碼查找"頁。 |


