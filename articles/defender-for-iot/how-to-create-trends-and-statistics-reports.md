---
title: 產生趨勢和統計資料包表
description: 使用 Defender 的 IoT 趨勢和統計資料 widget，深入瞭解網路活動、統計資料和趨勢。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/24/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 8bfbacc77843c7978bcb1d364bd93f5e09381144
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811617"
---
# <a name="sensor-trends-and-statistics-reports"></a>感應器趨勢和統計資料包表

## <a name="about-sensor-trends-and-statistics-reports"></a>關於感應器趨勢和統計資料包告

您可以建立 widget 圖形和圓形圖，以深入瞭解網路趨勢和統計資料。 Widget 可以分組在使用者定義的儀表板底下。

> [!NOTE]
> 系統管理員和安全性分析師可以建立趨勢和統計資料包表。

儀表板是由小工具所組成，以圖形方式描述下列類型的資訊：

- 依埠的流量
- 通道頻寬
- 總頻寬
- 作用中 TCP 連接
- 裝置：
  - 新的裝置
  - 忙碌裝置
  - 依廠商的裝置
  - 依 OS 的裝置
  - 中斷連線的裝置
- 以小時為單位的連線能力
- 事件的警示（依類型）
- 資料庫資料表存取
- 通訊協定剖析小工具
- Ethernet 和 IP 位址：
  - CJP 服務的乙太網路和 IP 位址流量
  - CJP 類別的乙太網路和 IP 位址流量
  - 依命令的乙太網路和 IP 位址流量
- Opc：
  - OPC 最上層管理作業
  - OPC 最重要的 i/o 作業
- Siemens S7：
  - 依控制功能 S7 流量
  - 依子 S7 流量
- SRTP
  - 依服務程式代碼 SRTP 流量
  - SRTP 錯誤（依日）
- SuiteLink:
  - SuiteLink 排名最前面的查詢標記
  - SuiteLink 數值標記行為
- IEC-60870 流量（依 ASDU）
- 依函數的 DNP3 流量
- 依服務的 MMS 流量
- 依函數 Modbus 流量
- OPC-UA 流量（依服務）

> [!NOTE]
>  Widget 中的時間是根據感應器時間來設定。

## <a name="create-reports"></a>建立報表

查看儀表板和小工具：

選取側邊功能表上的 **趨勢 & 統計資料** 。

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="調查的螢幕擷取畫面。":::

依預設，會顯示過去7天的偵測結果。 您可以使用篩選工具變更此範圍。 例如，可用文字搜尋。

## <a name="see-also"></a>另請參閱

[風險評量報告](how-to-create-risk-assessment-reports.md) 
[感應器資料採礦查詢](how-to-create-data-mining-queries.md) 
[攻擊向量報告](how-to-create-attack-vector-reports.md)