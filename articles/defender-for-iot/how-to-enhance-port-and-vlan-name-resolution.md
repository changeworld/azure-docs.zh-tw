---
title: 增強連接埠和 VLAN 名稱解析
description: 自訂您感應器上的埠和 VLAN 名稱，以擴充裝置解析度。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/13/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9c976671bccb420ae24d8def7a6574098d86ce6d
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98803583"
---
# <a name="enhance-port-and-vlan-name-resolution"></a>增強連接埠和 VLAN 名稱解析

您可以自訂感應器上的埠和 VLAN 名稱，以擴充裝置解析度。

## <a name="customize-port-names"></a>自訂埠名稱

適用于 IoT 的 Azure Defender 會自動將名稱指派給最通用的保留埠，例如 DHCP 或 HTTP。 您可以針對 Defender for IoT 偵測到的其他埠自訂埠名稱。 例如，將名稱指派給非保留的埠，因為該埠會顯示異常高的活動。

這些名稱會在下列情況出現：

  - 您可以從裝置對應中選取 **裝置群組** 。

  - 您會建立可提供埠資訊的小工具。

### <a name="view-custom-port-names-in-the-device-map"></a>在裝置對應中查看自訂埠名稱

包含使用者所定義名稱的埠會出現在裝置對應的 [已知的 **應用程式** ] 群組中。

:::image type="content" source="media/how-to-enrich-asset-information/applications-v2.png" alt-text="裝置地圖的螢幕擷取畫面，其中顯示已知的應用程式群組。":::

### <a name="view-custom-port-names-in-widgets"></a>在 widget 中查看自訂埠名稱

您所定義的埠名稱會顯示在涵蓋依埠的流量的 widget 中。

:::image type="content" source="media/how-to-enrich-asset-information/traffic-v2.png" alt-text="涵蓋流量。":::

若要定義自訂埠名稱：

1. 選取 [ **系統設定** ]，然後選取 [ **標準別名**]。

2. 選取 [ **新增埠別名**]。

    :::image type="content" source="media/how-to-enrich-asset-information/edit-aliases.png" alt-text="新增埠別名。":::

3. 輸入埠號碼、選取 [ **TCP/UDP**]，或選取 [ **兩者**]，然後新增名稱。

4. 選取 [儲存]。

## <a name="configure-vlan-names"></a>設定 VLAN 名稱

您可以使用裝置 VLAN 號碼和標記來擴充裝置清查資料。 除了資料擴充之外，您還可以查看每個 VLAN 的裝置數量，並依 VLAN widget 來查看頻寬。

Vlan 支援是根據 802.1 q (，最多可達 VLAN ID 4094) 。

有兩種方法可供您抓取 VLAN 資訊：

- **自動探索**：根據預設，感應器會自動探索 vlan。 使用流量偵測到的 Vlan 會顯示在 VLAN 設定畫面、資料採礦報告，以及其他包含 VLAN 資訊的報表中。 未使用的 Vlan 不會顯示。 您無法編輯或刪除這些 Vlan。 

  您應該為每個 VLAN 新增唯一的名稱。 如果您未新增名稱，則 VLAN 號碼會出現在所有已回報 VLAN 的位置。

- **手動新增**：您可以手動新增 vlan。 您必須為每個手動新增的 VLAN 新增唯一的名稱，而且您可以編輯或刪除這些 Vlan。

VLAN 名稱最多可包含50個 ASCII 字元。

> [!NOTE]
> 在感應器和管理主控台之間，不會同步處理 VLAN 名稱。 您也必須在管理主控台上定義名稱。  
若為 Cisco 參數，請將下列程式程式碼新增至範圍設定： `monitor session 1 destination interface XX/XX encapsulation dot1q` 。 在該命令中， *xx/xx* 是埠的名稱和編號。

設定 Vlan：

1. 在側邊功能表上，選取 [ **系統設定**]。

2. 在 [ **系統設定** ] 視窗中，選取 [ **VLAN**]。

    :::image type="content" source="media/how-to-enrich-asset-information/edit-vlan.png" alt-text="使用系統設定來編輯您的 Vlan。":::

3. 在每個 VLAN ID 旁邊新增唯一的名稱。

## <a name="next-steps"></a>後續步驟

查看各種報表中的擴充裝置資訊：

- [調查裝置清查中的感應器偵測](how-to-investigate-sensor-detections-in-a-device-inventory.md)
- [感應器趨勢和統計資料包表](how-to-create-trends-and-statistics-reports.md)
- [感應器資料採礦查詢](how-to-create-data-mining-queries.md)
