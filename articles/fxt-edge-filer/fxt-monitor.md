---
title: 監視 Azure FXT Edge Filer
description: 如何監視 Azure FXT Edge Filer 混合式儲存體快取的硬體狀態
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 86e1d74d5e4ab9f6e799c73bcf0d807d0d874f21
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92219712"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>監視 Azure FXT Edge Filer 的硬體狀態

Azure FXT Edge Filer 混合式儲存體快取系統有多個內建于底座的狀態燈，可協助系統管理員瞭解硬體的運作方式。

## <a name="system-health-status"></a>系統健康狀態

若要監視較高層級的快取作業，請使用軟體主控台的 [**儀表板**] 頁面，如[主控台儀表板指南](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)中所述

## <a name="hardware-status-leds"></a>硬體狀態 Led

本節說明 Azure FXT Edge Filer 硬體內建的各種狀態燈。

### <a name="hard-drive-status-leds"></a>硬碟狀態 Led

![硬碟前端、水準、標注標籤 2 (左上角) 、1 (左下角) 和 3 (右邊) ](media/fxt-monitor/fxt-drive-callouts.png)

每個磁片磁碟機貨運公司都有兩個狀態 Led：活動指標 (1) 和狀態指示器 (2) 。

* 當磁片磁碟機正在使用中時，活動 LED (1) 燈。
* 狀態 LED (2) 使用下表中的程式碼來指出磁片磁碟機的條件。

| 磁片磁碟機狀態 LED 狀態              | 意義  |
|-------------------------------------|----------------------------------------------------------|
| 每秒閃爍綠色兩次      | 正在識別磁片磁碟機 *或* <br> 正在準備要移除的磁片磁碟機  |
| Off (unlit)                          | 系統尚未完成啟動 *或* <br>磁片磁碟機已備妥可供移除 |
| 閃爍綠色、琥珀色和關閉       | 預測磁片磁碟機失敗   |
| 每秒閃爍四次的琥珀色 | 磁片磁碟機失敗   |
| 綠燈                         | 磁片磁碟機已上線 |

磁片磁碟機的右邊 (3) 會標示磁片磁碟機的容量和其他資訊。

磁碟機與磁碟機之間的空間會印有磁碟機編號。 在 Azure FXT Edge Filer 中，磁碟機 0 是左上方的磁碟機，磁碟機 1 則在磁碟機 0 的正下方。 在該模式中的編號會繼續。

![FXT 底座中某個硬碟機擴充插槽的相片，其中顯示了磁碟機編號和容量標籤](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>左方控制台

左側前端控制台有各種狀態 LED 指標 (1) 和大型的燈系統健康狀態指標 (2) 。

![左邊的狀態面板，左側有1個標籤狀態指標，而2標籤右側的大型系統健康情況指示器燈](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>控制台狀態指示器

如果系統中有錯誤，則左邊的狀態指標會顯示穩定的琥珀色燈。 下表描述錯誤的可能原因和解決方案。

如果您在嘗試這些解決方案之後仍有錯誤，請 [聯絡支援](fxt-support-ticket.md) 人員以取得協助。

| 圖示 | 描述 | 錯誤狀況 | 可能的解決方案 |
|----------------|---------------|--------------------|----------------------|
| ![磁片磁碟機圖示](media/fxt-monitor/fxt-hd-icon.jpg) | 磁碟機狀態 | 磁片磁碟機錯誤 | 檢查系統事件記錄檔，以判斷磁片磁碟機是否有錯誤，或 <br>執行適當的線上診斷測試;重新開機系統，並執行內嵌診斷 (ePSA) ，或 <br>如果磁片磁碟機是在 RAID 陣列中設定，請重新開機系統，然後輸入主機介面卡設定公用程式 |
|![溫度圖示](media/fxt-monitor/fxt-temp-icon.jpg) | 溫度狀態 | 熱量錯誤-例如，風扇故障或環境溫度超出範圍 | 檢查下列可定址條件： <br>冷卻風扇遺失或失敗 <br>系統已移除系統的封面、金屬片、記憶體模組空白或後置填滿括弧 <br>環境溫度過高 <br>外部氣流被阻擋 |
|![電力圖示](media/fxt-monitor/fxt-electric-icon.jpg) | 電狀態 | 電力誤差-例如，電壓超出範圍、失敗的 PSU 或電壓調節器失敗 |  檢查系統事件記錄檔或系統訊息中的特定問題。 如果發生 PSU 問題，請檢查 PSU 狀態 LED 並視需要重新放置 PSU。 |
|![記憶體圖示](media/fxt-monitor/fxt-memory-icon.jpg) | 記憶體狀態 | 記憶體錯誤 | 檢查系統事件記錄檔或系統訊息，找出失敗的記憶體位置;重新放置記憶體模組。 |
|![PCIe 圖示](media/fxt-monitor/fxt-pcie-icon.jpg) | PCIe 狀態 | PCIe 卡錯誤 | 重新開機系統;更新 PCIe 卡驅動程式;重新安裝卡片 |

### <a name="system-health-status-indicator"></a>系統健康狀態指標

左側 [控制台] 右邊的 [大型亮號] 按鈕表示整體系統狀態，也用來作為系統識別碼模式的單位定位器燈。

按下 [系統健康情況和識別碼] 按鈕，在系統識別碼模式與系統健全狀況模式之間切換。

|系統健康狀態狀態 | 條件 |
|-------------------------------------------|-----------------------------------------------|
| 實心藍色 | 正常操作：系統已開啟、正常運作，而且系統識別碼模式不在使用中。 <br/>如果您想要切換至 [系統識別碼] 模式，請按 [系統健康情況和識別碼] 按鈕。 |
| 閃爍藍色 | 系統識別碼模式為作用中。 如果您想要切換至系統健康模式，請按 [系統健康情況和系統識別碼] 按鈕。 |
| 穩固的琥珀色 | 系統處於「故障安全」模式。 如果問題持續發生，請 [洽詢 Microsoft 客戶服務及支援](fxt-support-ticket.md)。 |
| 閃爍琥珀色 | 系統錯誤。 檢查系統事件記錄檔中的特定錯誤訊息。 如需系統固件和監視系統元件的代理程式所產生之事件和錯誤訊息的相關資訊，請參閱 < 錯誤碼查閱頁面，網址為 qrl.dell.com。 |
