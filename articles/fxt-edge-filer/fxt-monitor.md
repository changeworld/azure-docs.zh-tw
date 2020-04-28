---
title: 監視 Azure FXT Edge Filer
description: 如何監視 Azure FXT Edge 檔案管理工具混合式儲存體快取的硬體狀態
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 3f422339af2040ad81c585c0e193e6cb3667b135
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "72254880"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>監視 Azure FXT Edge 檔案管理工具硬體狀態

Azure FXT Edge 檔案管理工具混合式儲存體快取系統會在底座內建多個狀態燈，協助系統管理員瞭解硬體的運作方式。

## <a name="system-health-status"></a>系統健全狀況狀態

若要以較高的層級監視快取作業，請使用 [軟體控制台] 的 [**儀表板**] 頁面，如[控制台儀表板指南](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)中所述

## <a name="hardware-status-leds"></a>硬體狀態 Led

本節說明 Azure FXT Edge 檔案管理工具硬體內建的各種狀態燈。

### <a name="hard-drive-status-leds"></a>硬碟狀態 Led

![硬碟正面、水準、具有標注標籤2（左上角）、1（左下角）和3（右邊）的圖片](media/fxt-monitor/fxt-drive-callouts.png)

每個磁片磁碟機貨運公司都有兩個狀態 Led：活動指示器（1）和狀態指示器（2）。 

* 當磁片磁碟機正在使用中時，活動 LED （1）燈亮起。  
* 狀態 LED （2）會使用下表中的程式碼來指出磁片磁碟機的條件。

| 磁片磁碟機狀態 LED 狀態              | 意義  |
|-------------------------------------|----------------------------------------------------------|
| 每秒閃爍一次綠色      | 識別磁片磁碟機*或* <br> 準備要移除的磁片磁碟機  |
| Off （unlit）                         | 系統尚未完成啟動*或* <br>磁片磁碟機已準備好移除 |
| 閃爍綠色、琥珀色和關閉       | 預測磁片磁碟機失敗   |
| 每秒閃爍四次琥珀色 | 磁片磁碟機失敗   |
| 實心綠色                         | 磁片磁碟機已上線 |

磁片磁碟機的右側（3）會標示磁片磁碟機的容量和其他資訊。

磁碟機與磁碟機之間的空間會印有磁碟機編號。 在 Azure FXT Edge Filer 中，磁碟機 0 是左上方的磁碟機，磁碟機 1 則在磁碟機 0 的正下方。 編號會繼續在該模式中進行。 

![FXT 底座中某個硬碟機擴充插槽的相片，其中顯示了磁碟機編號和容量標籤](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>左方控制台

左側的控制台具有各種狀態 LED 指示器（1）和一個大型的系統健康情況指示器（2）。 

![左方狀態面板，左側有1個標籤狀態指示器，2則標示右側的大型系統健康情況指示器燈](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>控制台狀態指示器 

如果該系統中發生錯誤，左邊的狀態指標會顯示穩定的琥珀色燈。 下表描述錯誤的可能原因和解決辦法。 

如果您在嘗試這些解決方案之後仍有錯誤，請[聯絡支援](fxt-support-ticket.md)人員以取得協助。 

| 圖示 | 描述 | 錯誤狀況 | 可能的解決方案 |
|----------------|---------------|--------------------|----------------------|
| ![磁片磁碟機圖示](media/fxt-monitor/fxt-hd-icon.jpg) | 磁碟機狀態 | 磁片磁碟機錯誤 | 檢查 [系統] 事件記錄檔，判斷磁片磁碟機是否發生錯誤，或 <br>執行適當的線上診斷測試;重新開機系統並執行內嵌診斷（ePSA），或 <br>如果磁片磁碟機是在 RAID 陣列中設定的，請重新開機系統並輸入主機介面卡設定公用程式 |
|![溫度圖示](media/fxt-monitor/fxt-temp-icon.jpg) | 溫度狀態 | 熱錯誤-例如，風扇已故障或環境溫度超出範圍 | 檢查是否有下列可定址條件： <br>冷卻風扇遺失或已失敗 <br>已移除系統的封面、金屬導片、記憶體模組空白或後端填充括弧 <br>環境溫度過高 <br>外部氣流受到阻礙 |
|![電力圖示](media/fxt-monitor/fxt-electric-icon.jpg) | 電力狀態 | 電力錯誤-例如，電壓超出範圍、失敗的 PSU 或失敗的電壓調節器 |  檢查系統事件記錄檔或系統訊息中的特定問題。 如果有 PSU 問題，請檢查 PSU 狀態 LED 並視需要重新拔插 PSU。 | 
|![記憶體圖示](media/fxt-monitor/fxt-memory-icon.jpg) | 記憶體狀態 | 記憶體錯誤 | 檢查系統事件記錄檔或系統訊息，找出失敗記憶體的位置;重新放置記憶體模組。 |
|![PCIe 圖示](media/fxt-monitor/fxt-pcie-icon.jpg) | PCIe 狀態 | PCIe 卡錯誤 | 重新開機系統;更新 PCIe 卡驅動程式;重新安裝卡片 |


### <a name="system-health-status-indicator"></a>系統健全狀況狀態指示器

左側 [控制台] 右邊的 [大亮] 按鈕表示整體系統狀態，也用來做為系統識別碼模式中的單位定位器光源。

按 [系統健全狀況和識別碼] 按鈕，以在系統識別碼模式與系統健康狀態模式之間切換。

|系統健全狀況狀態 | 狀況 |
|-------------------------------------------|-----------------------------------------------|
| 實心藍色 | 正常操作：系統已開啟、正常運作，且系統識別碼模式不在使用中。 <br/>如果您想要切換至 [系統識別碼] 模式，請按 [系統健全狀況和識別碼] 按鈕。 |
| 閃爍藍色 | 系統識別碼模式為使用中。 如果您想要切換到系統健康狀態模式，請按 [系統健全狀況和系統識別碼] 按鈕。 |
| 單色琥珀色 | 系統處於不安全的模式。 如果問題持續發生，請[洽詢 Microsoft 客戶服務及支援](fxt-support-ticket.md)。 |
| 閃爍琥珀色 | 系統錯誤。 檢查系統事件記錄檔中是否有特定的錯誤訊息。 如需系統監視器和監視系統元件之代理程式所產生之事件和錯誤訊息的詳細資訊，請參閱 < 錯誤碼查閱頁面，網址為 qrl.dell.com。 |


