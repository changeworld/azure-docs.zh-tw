---
title: 在感應器上使用警示
description: 使用警示來協助您增強網路的安全性和操作。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 00207ffb8480ae99c2f1aad74183fca9ea45ee17
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839091"
---
# <a name="work-with-alerts-on-your-sensor"></a>在感應器上使用警示

使用警示來協助您增強網路的安全性和操作。 警示提供下列資訊：

- 與授權網路活動的偏差

- 通訊協定和操作異常

- 可疑的惡意程式碼流量

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="偵測位址掃描。":::

警示管理選項可讓使用者：

- 指示感應器學習偵測為授權流量的活動。

- 確認審核警示。

- 指示感應器將偵測到的事件與相同裝置和可比較的流量靜音。

有其他工具可協助您增強和加速警示調查。 例如：

  - 新增警示審核者的教學批註。

  - 建立用來顯示 SOC 解決方案的警示群組。 

  - 搜尋特定警示;檢查相關的 PCAP 檔;查看裝置對應中偵測到的裝置和其他已連線的裝置，或將警示詳細資料傳送給夥伴系統。

  - 將警示轉寄給合作夥伴廠商： SIEM systems、MSSP systems 等等。

## <a name="alerts-and-engines"></a>警示和引擎

當感應器引擎偵測到需要您注意的網路流量和行為變更時，就會觸發警示。 本文描述每個引擎觸發的警示類型。

| 警示類型 | 描述 |
|-|-|
| 原則違規警示 | 當原則違規引擎偵測到來自先前所學流量的偏差時，就會觸發。 例如： <br /> -偵測到新的裝置。  <br /> -在裝置上偵測到新的設定。 <br /> -未定義為程式設計裝置的裝置會執行程式設計變更。 <br /> -已變更的固件版本。 |
| 通訊協定違規警示 | 當通訊協定違規引擎偵測到不符合通訊協定規格的封包結構或域值時，就會觸發。 | 
| 操作警示 | 當作業引擎偵測到網路操作事件或裝置無法運作時，即會觸發。 例如，網路裝置已透過「停止 PLC」命令停止，或感應器上的介面停止監視流量。 |
| 惡意程式碼警示 | 當惡意程式碼引擎偵測到惡意網路活動時觸發。 例如，引擎會偵測到已知的攻擊，例如 Conficker。 |
| 異常警示 | 當異常引擎偵測到偏差時，就會觸發。 例如，裝置正在執行網路掃描，但未定義為掃描裝置。 |

您可以使用工具來啟用和停用感應器引擎。 警示不會從停用的引擎觸發。 請參閱 [控制受監視的流量](how-to-control-what-traffic-is-monitored.md)。

## <a name="alerts-and-sensor-reporting"></a>警示和感應器報告

當您產生資料採礦、風險評定和攻擊向量報表時，可以計算警示中反映的活動。 當您管理這些事件時，感應器會據以更新報告。

例如：

  - 在所定義子網中的裝置與位於子網外部的裝置之間的未授權連線 (公用) 將會出現在 [資料採礦 *網際網路活動* ] 報告和 [風險評定 *網際網路連線* ] 區段中。 這些裝置獲得授權之後 (學習) 之後，就會在產生這些報告時計算這些裝置。

  - 在網路裝置上偵測到的惡意程式碼事件，會在風險評量報告中報告。 當惡意程式碼事件的警示 *靜音* 時，不會在風險評量報告中計算受影響的裝置。

## <a name="see-also"></a>請參閱

- [學習和智慧型 IT 學習模式](how-to-control-what-traffic-is-monitored.md#learning-and-smart-it-learning-modes)
- [查看警示中提供的資訊](how-to-view-information-provided-in-alerts.md)
- [管理警示事件](how-to-manage-the-alert-event.md)
- [加速警示工作流程](how-to-accelerate-alert-incident-response.md)
