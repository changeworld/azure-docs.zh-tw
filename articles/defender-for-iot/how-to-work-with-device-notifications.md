---
title: 使用裝置通知
description: 通知會提供可能需要您注意的網路活動相關資訊，以及處理此活動的建議。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: d113805322bd45584987460d57ad6bdba241ec10
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839083"
---
# <a name="work-with-device-notifications"></a>使用裝置通知

通知會提供可能需要您注意的網路活動相關資訊，以及處理此活動的建議。 例如，您可能會收到下列通知：

- 非作用中的裝置。 如果裝置不再是您網路的一部分，您可以將它移除。 如果裝置非使用中（例如因為錯誤地從網路中斷連線），請重新連接裝置並關閉通知。

- 在目前僅由 MAC 位址識別的裝置上偵測到 IP 位址。 藉由授權裝置的 IP 位址來回應。

回應通知可改善裝置對應、裝置清查和資料採礦查詢和報告中提供的資訊。 它也可讓您深入瞭解合法的網路變更和潛在的網路錯誤配置。

若要存取通知：

- 選取 [ **系統設定** ]，然後選取 [ **資料增強功能**]。

## <a name="notifications-vs-alerts"></a>通知與警示

除了接收網路活動的通知之外，您可能會收到 *警示*。 通知會提供網路變更或未解決的裝置屬性的相關資訊，而不會顯示威脅。 警示會提供網路偏差的相關資訊，以及可能對網路造成威脅的變更。

若要查看通知：

1. 從主控台的左側功能表窗格中選取 [ **裝置對應** ]。

2. 選取 [ **通知** ] 圖示。 圖示上方的紅色數位表示通知數目。

   :::image type="content" source="media/how-to-enrich-asset-information/notifications-alert-screenshot.png" alt-text="通知圖示。":::

   [ **通知** ] 視窗會顯示感應器偵測到的所有通知。

   :::image type="content" source="media/how-to-enrich-asset-information/notification-screen.png" alt-text="通知。":::

## <a name="filter-the-notifications-window"></a>篩選 [通知] 視窗

使用搜尋篩選器來顯示您感興趣的通知。

| 篩選依據 | 描述 |
|--|--|
| 依類型篩選 | 查看涵蓋特定感興趣區域的通知。 例如，只查看非作用中裝置的通知。 |
| 依日期範圍篩選 | 顯示涵蓋特定時間範圍的通知。 例如，只會查看上周傳送的通知。 |
| 搜尋特定資訊 | 搜尋特定的通知。 |

## <a name="notification-events-and-responses"></a>通知事件和回應

下表描述您可能會收到的通知事件種類，以及處理這些類型的選項。 您可以使用建議的值來更新裝置資訊，或關閉通知。 當您關閉通知時，不會以建議的資訊更新裝置資訊。 如果再次偵測到流量，則會重新傳送通知。

| 通知事件種類 | 描述 | 回應 |
|--|--|--|
| 新的 IP 位址 | 新的 IP 位址會與裝置建立關聯。 可能會偵測到五個案例： <br /><br /> 其他 IP 位址已與裝置建立關聯。 此裝置也會與現有的 MAC 位址相關聯。<br /><br /> 偵測到使用現有 MAC 位址的裝置有新的 IP 位址。 裝置目前不會使用 IP 位址進行通訊。<br /> <br /> 偵測到使用 NetBIOS 名稱的裝置有新的 IP 位址。 <br /><br /> 偵測到 IP 位址為與 MAC 位址相關聯之裝置的管理介面。 <br /><br /> 偵測到使用虛擬 IP 位址的裝置有新的 IP 位址。 | **將其他 IP 設定為裝置** (合併裝置)  <br /> <br />**取代現有的 IP** <br /> <br /> **關閉**<br /> 移除通知。 |
| 非作用中裝置 | 在裝置上偵測到超過60天的流量。 | **刪除** <br /> 如果此裝置不是您網路的一部分，請將它移除。 <br /><br />**關閉** <br /> 如果裝置是您網路的一部分，請移除通知。 如果裝置處於非使用中狀態 (例如，因為錯誤地中斷與網路) 的連線，請關閉通知並重新連接裝置。 |
| 新的 OT 裝置 | 子網包含未定義于 ICS 子網中的 OT 裝置。 <br /><br /> 包含至少一個 OT 裝置的每個子網都可以定義為 ICS 子網。 這有助於區別地圖上的 OT 和 IT 裝置。 | **設定為 ICS 子網** <br /> <br /> **關閉** <br />如果裝置不是子網的一部分，請移除通知。 |
| 未設定任何子網 | 您的網路中目前未設定任何子網。 <br /><br /> 設定子網以在地圖中提供更好的表示方式，以及區別 OT 和 IT 裝置的能力。 | **開啟子網** 設定並設定子網。 <br /><br />**關閉** <br /> 移除通知。 |
| 作業系統變更 | 有一或多個新的作業系統已與裝置建立關聯。 | 選取您要與裝置建立關聯的新 OS 名稱。<br /><br /> **關閉** <br /> 移除通知。 |
| 偵測到子網 | 探索到新的子網。 | **學習**<br />自動新增子網。<br />**開啟子網設定**<br />新增所有缺少的子網資訊。<br />**關閉**<br />移除通知。 |
| 偵測到裝置類型變更 | 已與裝置建立關聯的新裝置類型。 | **設定為 {...}**<br />將新類型與裝置產生關聯。<br />**關閉**<br />移除通知。 |

## <a name="respond-to-many-notifications-simultaneously"></a>同時回應許多通知

您可能需要同時處理數個通知。 例如：

- 如果作業系統升級為一組大型的網路伺服器，您可以指示感應器學習所有升級伺服器的新伺服器版本。 

- 如果特定行中的一組裝置已分階段而不再作用，您可以指示感應器從主控台移除這些裝置。

您可以指示感應器將新偵測到的資訊套用至多個裝置，或略過它。   

若要顯示通知並處理通知：

1. 使用 [ **依類型篩選]、[日期範圍** ] 選項或 [ **全選** ] 選項。 視需要取消選取通知。

2. 藉由選取 [ **學習**] 指示感應器將新偵測到的資訊套用至選取的裝置。 或者，藉由選取 [ **關閉**]，指示感應器忽略新偵測到的資訊。 顯示您可以同時學習和關閉的通知數目，以及必須個別處理的通知數目。

**新 ip** 和 **沒有子網** 設定的事件無法同時處理。 他們需要手動確認。

## <a name="improve-device-os-classification-data-enhancement"></a>改善裝置作業系統分類：資料增強 

感應器會持續 autodiscovers 新的 OT 裝置。 它也會 autodiscovers 先前探索到的裝置（包括作業系統類型）的變更。

在某些情況下，可能會在探索到的作業系統中偵測到衝突。 發生這種情況的原因是您的作業系統版本是指桌面或伺服器系統。 如果發生這種情況，您將會收到具有選用作業系統分類的通知。

:::image type="content" source="media/how-to-enrich-asset-information/enhance-data-screen.png" alt-text="增強資料。":::

調查建議以擴充 OS 分類。 這項資訊會出現在裝置清查、資料採礦報告和其他顯示中。 它也可以改善警示、威脅和風險分析的精確度。

當您接受建議時，將會更新感應器中的 OS 類型資訊。

## <a name="see-also"></a>請參閱

[檢視警示](how-to-view-alerts.md)
