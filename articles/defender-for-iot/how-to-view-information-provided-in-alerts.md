---
title: 在警示中查看資訊
description: 從 [警示] 視窗中選取警示，以查看詳細資料。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/03/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 804cdbd6266f2e77b5562d914bac089fce80f645
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839131"
---
# <a name="view-information-in-alerts"></a>在警示中查看資訊

從 [ **警示** ] 視窗中選取警示，以查看警示詳細資料。 詳細資料包含下列資訊：

- 警示中繼資料

- 流量、裝置和事件的相關資訊

- 裝置對應中連線裝置的連結

- 安全性分析師和系統管理員所定義的批註

- 調查事件的建議

## <a name="alert-metadata"></a>警示中繼資料

警示詳細資料包含下列警示中繼資料：

  - 警示識別碼

  - 觸發警示的原則引擎

  - 觸發警示的日期和時間

:::image type="content" source="media/how-to-work-with-alerts-sensor/internet-connectivity-detection-unauthorized.png" alt-text="偵測到未經授權的網際網路連線。":::

## <a name="information-about-devices-traffic-and-the-event"></a>裝置、流量和事件的相關資訊

警示訊息提供下列資訊：

  - 偵測到的裝置。

  - 在裝置之間偵測到的流量，例如通訊協定和函式代碼。

  - 深入瞭解事件的含意。

當您決定如何管理警示事件時，可以使用這種資訊。

## <a name="links-to-connected-devices-in-the-device-map"></a>裝置對應中連線裝置的連結

若要深入瞭解連線至偵測到之裝置的裝置，您可以在 [警示] 中選取裝置映射，並在地圖中查看已連線的裝置。

:::image type="content" source="media/how-to-work-with-alerts-sensor/rcp-operation-failed.png" alt-text="RCP 操作失敗。":::

:::image type="content" source="media/how-to-work-with-alerts-sensor/devices-screen-populated.png" alt-text="裝置螢幕擷取畫面。":::

地圖會篩選到您選取的裝置，以及其他連線到該裝置的裝置。 此對應也會顯示警示中偵測到之裝置的 [ **快速屬性** ] 對話方塊。

## <a name="comments-defined-by-security-analysts-and-administrators"></a>安全性分析師和系統管理員所定義的批註 

警示可能包含預先定義的批註清單。 例如，批註可以是要採取之緩和措施的指示，或是與事件相關的個人名稱。

當您管理警示事件時，您可以選擇最能反映事件狀態的批註或批註，或您為了調查警示所採取的步驟。

選取的批註會儲存在警示訊息中。 使用批註可在調查警示事件期間，增強個人和小組之間的通訊。 因此，批註可以加速事件回應時間。

系統管理員或安全性分析師預先定義批註。 選取的批註不會轉送到轉送規則中所定義的夥伴系統。

在您檢查警示中的資訊之後，您可以執行各種不同的取證步驟，以引導您管理警示事件。 例如：

- 分析最近的裝置活動 (資料-挖掘報表) 。 

- 分析同時發生 (事件時間軸) 的其他事件。 

-  (PCAP 檔) 分析詳盡的事件流量。

## <a name="pcap-files"></a>PCAP 檔案

在某些情況下，您可以從警示訊息存取 PCAP 檔。 如果您想要更多有關相關網路流量的詳細資訊，這可能會很有用。

若要下載 PCAP 檔案，請選取 [ :::image type="content" source="media/how-to-work-with-alerts-sensor/download-pcap.png" alt-text="下載] 圖示。"::: 在 [ **警示詳細資料** ] 對話方塊的右上方。

## <a name="recommendations-for-investigating-an-event"></a>調查事件的建議 

警示的 **建議** 區域會顯示可能有助於您進一步瞭解事件的資訊。 在管理警示事件或對裝置或網路採取動作之前，請先參閱此資訊。

## <a name="see-also"></a>請參閱

[加速警示工作流程](how-to-accelerate-alert-incident-response.md)

[管理警示事件](how-to-manage-the-alert-event.md)
