---
title: 檢視警示
description: 根據各種類別來查看警示，並使用搜尋功能來協助您尋找感興趣的警示。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 4803691a82a97cd2be5fa3beafd4419010e7a9c9
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839146"
---
# <a name="view-alerts"></a>檢視警示

本文說明如何查看您的感應器所觸發的警示，並使用警示工具來管理它們。

您可以根據各種類別來查看警示，例如已封存或已釘選的警示。 或者，您也可以搜尋感興趣的警示，例如以 IP 或 MAC 位址為基礎的警示。  

您也可以從感應器儀表板查看警示。

檢視警示：

- 從側邊功能表選取 [ **警示** ]。 [ **警示** ] 視窗會顯示感應器偵測到的警示。

  :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-screen.png" alt-text="警示畫面的視圖。":::

## <a name="view-alerts-by-category"></a>依類別查看警示

您可以根據 **警示** 主視圖的各種類別來查看警示。 選取警示以檢查詳細資料及管理事件。

| 依類型排序 | 描述 |
|--|--|
| **重要警示** | 依重要性排序的警示。 |
| **釘選的警示** | 使用者釘選以進行進一步調查的警示。 釘選的警示不會封存，而且會儲存14天的釘選資料夾。 |
| **最近的警示** | 依時間排序的警示。 |
| **認可的警示** | 已認可、未處理或已靜音和 unmuted 的警示。 |
| **封存的警示** | 系統會自動封存系統的警示。 只有系統管理員使用者可以存取它們。 |

## <a name="search-for-alerts-of-interest"></a>搜尋感興趣的警示

**警示** 主視圖提供各種搜尋功能，可協助您尋找感興趣的警示。

:::image type="content" source="media/how-to-work-with-alerts-sensor/main-alerts-view.png" alt-text="警示學習螢幕擷取畫面。":::

### <a name="text-search"></a>文字搜尋 

使用 [ **免費搜尋** ] 選項可依文字、數位或字元來搜尋警示。

若要搜尋：

- 在 [ **免費搜尋** ] 欄位中輸入必要的文字，然後在鍵盤上按 enter 鍵。

若要清除搜尋：

- 刪除 [ **免費搜尋** ] 欄位中的文字，然後按鍵盤上的 enter 鍵。

### <a name="device-group-or-device-ip-address-search"></a>裝置群組或裝置 IP 位址搜尋

搜尋參考特定 IP 位址或裝置群組的警示。 裝置群組會建立在裝置對應中。

若要使用 advanced 濾波器：

1. 從 **警示** 主視圖中選取 [ **Advanced 濾波器**]。

2. 選擇裝置群組或裝置。

3. 選取 [確認]。

4. 若要清除搜尋，請選取 [ **全部清除**]。

### <a name="security-versus-operational-alert-search"></a>安全性與操作警示搜尋

切換查看操作和安全性警示：

- **安全性** 警示代表潛在的惡意程式碼流量、網路異常、原則違規和通訊協定違規。

- **操作** 警示代表網路異常、原則違規和通訊協定違規。

如果未選取任何選項，則會顯示所有警示。

:::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-security.png" alt-text="警示畫面上的安全性。":::

## <a name="alert-window-options"></a>警示視窗選項

警示訊息提供下列動作：

- 選取 :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-an-alert-icon.png" border="false"::: 以確認警示。

- 選取 :::image type="icon" source="media/how-to-work-with-alerts-sensor/unacknowledge-an-alert-icon.png" border="false"::: 以 unacknowledge 警示。

- 選取 :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: 以釘選警示。

- 選取 :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: 以取消釘選警示。

- 選取 :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-all-alerts-icon.png" border="false"::: 即可確認所有警示。

- 選取 :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-and-acknowledge-all-alerts.png" border="false"::: 以瞭解並確認所有警示。

- 選取此 :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-csv.png" border="false"::: 選項可將警示清單匯出至 CSV 檔案，然後選取 [匯出] 選項。 針對 [一般匯出至 CSV] 選項，選擇 [ **警示匯出** ]。 或者選擇 [ **擴充警示匯出** ]，以便在 CSV 檔案中新增不同的資料列，以取得警示的其他相關資訊。

- 選取 :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-pdf.png" border="false"::: 圖示以將警示報表下載為 PDF 檔案。

- 選取 :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-pcap.png" border="false"::: 圖示以下載 PCAP 檔案。 您可以使用 Wireshark （免費的網路通訊協定分析器）來查看該檔案。

- 選取 :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-filtered-pcap.png" border="false"::: 即可下載篩選過的 PCAP 檔案，其中只包含相關的警示封包，進而減少輸出檔案大小，並允許更專注的分析。 您可以使用 Wireshark 來查看檔案。

- 選取 :::image type="icon" source="media/how-to-work-with-alerts-sensor/show-alert-in-timeline.png" border="false"::: 圖示以在事件時間軸中顯示警示。

- 選取 :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: 圖示以釘選警示。

- 選取 :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: 要取消釘選警示的圖示。

- 選取 :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-icon.png" border="false"::: 即可核准 (安全性分析師和系統管理員) 的流量。

- 選取裝置以將其顯示在裝置對應中。

## <a name="next-steps"></a>後續步驟

[管理警示事件](how-to-manage-the-alert-event.md)

[加速警示工作流程](how-to-accelerate-alert-incident-response.md)
