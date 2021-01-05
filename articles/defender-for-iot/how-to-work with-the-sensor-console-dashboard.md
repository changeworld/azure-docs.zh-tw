---
title: 使用感應器主控台儀表板
description: 儀表板可讓您快速查看網路的安全性狀態。 它會在時間軸上提供整個系統的整體威脅概要，以及相關裝置的相關資訊。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/03/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 735b1ce4391598d05a1bf0b4486503092f4de37d
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839119"
---
# <a name="the-dashboard"></a>儀表板

儀表板可讓您快速查看網路的安全性狀態。 它會在時間軸上提供整個系統的整體威脅概要，以及相關裝置的相關資訊，包括：

- 不同嚴重性層級的警示：

- 重大

- 主要

- Minor

- 警告

- 頁面中心內的兩個量測計會指出每秒 (PPS) 的封包，以及 (UA) 的未認可警示。 **PPS** 是系統每秒認可的封包數目。 **UA** 是尚未認可的警示數目。

- 未確認的警示清單及其描述。

- 具有警示描述的時程表。

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/dashboard-alert-screen-v2.png" alt-text="儀表板":::

## <a name="viewing-the-latest-alerts"></a>查看最新的警示

未認可的警示 (UA) 量測計的頁面中央會指出這類警示的數目。 若要查看警示清單，請選取 [儀表板] 頁面底部的 [ **更多警示** ]，或選取側邊功能表上的 [ **警示** ]。

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unhandled-alerts-list.png" alt-text="未確認的警示":::

## <a name="status-boxes"></a>狀態方塊

本節將說明每個狀態方塊。

| Status Box 和量測計 | 描述 |
| -------------- | -------------- |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/critical-alert-status-box-v2.png" alt-text="重大警示"::: | **重大警示** -頁面上方中間的方塊指出重大警示的數目。 選取此方塊，即可在時間軸上以及量測計底下的清單中顯示警示的描述（如果有的話）。                              |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/major-alert-status-box-v2.png" alt-text="重大警示"::: | **主要警示** -頁面右上方的方塊指出主要警示的數目。 選取此方塊，即可在時間軸上以及量測計底下的清單中顯示警示的描述（如果有的話）。                                     |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/minor-alert-status-box-v2.png" alt-text="次要警示"::: | **次要警示** -頁面左下方的方塊指出次要警示的數目。 選取此方塊，即可在時間軸上以及量測計底下的清單中顯示警示的描述（如果有的話）。                                   |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/warnings-alert-status-box-v2.png" alt-text="警告警示"::: | **警告警示** -頁面底部中間的方塊指出警告警示的數目。 選取此方塊，即可在時間軸上以及量測計底下的清單中顯示警示的描述（如果有的話）。                             |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/all-alert-status-box-v2.png" alt-text="所有警示"::: | **所有警示** -頁面右下方的方塊指出重大、主要、次要和警告警示的總數。 選取此方塊，即可在時間軸上以及量測計底下的清單中顯示警示的描述（如果有的話）。 |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/packets-per-second-gauge-v2.png" alt-text="每秒封包數"::: | **每秒封包數 (PPS)** -pps 度量是網路效能的指標。 |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unacknowledged-events-gauge-v2.png" alt-text=" (UA) 的未認可事件 "::: | 未 **認可的事件**-此計量指出未認可的事件數目。

## <a name="using-the-timeline"></a>使用時間軸

警示會沿著包含日期和時間資訊的垂直時程表顯示。

時間軸會以圖形顯示：

- 重大警示

- 重大警示

- 次要警示

- 警告警示

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/timeline-of-events.png" alt-text="時間軸圖形":::

## <a name="viewing-alerts"></a>檢視警示

選取警示方塊底部的向下箭號 **V** ，以顯示警示專案和裝置資訊。

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/extended-alert-screen.png" alt-text="警示專案和裝置資訊":::

- 選取裝置或 **顯示裝置** 以顯示實體模式對應。 受的裝置會反白顯示。

- 選取 [ :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/excel-icon.png" alt-text="Excel"::: ]，匯出有關警示的 CSV 檔案。

- 僅限系統管理員和安全性分析師—選取 [ :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/approve-all-icon.png" alt-text="全部認可"::: ] 以 **確認所有** 相關聯的警示。

- 選取警示專案以查看警示的類型和描述：

- 選取 :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pdf-icon.png" alt-text="pdf":::以將警示報表下載為 pdf 檔案。

- 選取 [ :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pin-icon.png" alt-text="釘":::選] 以釘選或取消釘選警示。

- 選取 [ :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/download-icon.png" alt-text="下載"::: ] 以下載包含網路通訊協定分析的 PCAP 檔案來調查警示。

- 選取 [ :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/cloud-download-icon.png" alt-text="雲端"::: ] 以下載篩選過的 PCAP 檔案，其中只包含與警示相關的封包，藉此減少輸出檔案大小，並允許更專注的分析。 您可以使用 [Wireshark](https://www.wireshark.org/)來加以查看。

- 選取 [ :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/navigate-icon.png" alt-text="流覽"::: ]，在要求的警示時流覽至事件時間軸。

- 僅限系統管理員和安全性分析師-將警示的狀態從 [未認可] 變更為 [已認可]。 選取 [學習] 以核准偵測到的活動。

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unauthorized-internet-connectivity-detection-v3.png" alt-text="偵測到未經授權的網際網路連線":::

## <a name="see-also"></a>請參閱

[在感應器上使用警示](how-to-work-with-alerts-on-your-sensor.md)
