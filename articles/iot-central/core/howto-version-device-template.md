---
title: 了解 Azure IoT 中心應用程式的裝置範本版本設定 | Microsoft Docs
description: 藉由建立新版本來反覆使用裝置範本，而不會影響您的即時連線裝置
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 772521a8d3181721270d7fe4dbd11b7807c8d90e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82583664"
---
# <a name="create-a-new-device-template-version"></a>建立新的裝置範本版本

*本文適用於解決方案建置人員和裝置開發人員。*

裝置範本包含描述裝置如何與 IoT Central 互動的架構。 這些互動包括遙測、屬性和命令。 裝置和 IoT Central 應用程式都依賴此架構的共用瞭解來交換資訊。 您只能對架構進行有限的變更，而不會中斷合約，這就是為什麼大部分的架構變更都需要新版本的裝置範本。 版本設定裝置範本可讓舊版裝置繼續使用其所瞭解的架構版本，而較新或更新的裝置則使用較新的架構版本。

裝置範本中的架構定義于裝置功能模型（DCM）及其介面中。 裝置範本包括其他資訊，例如雲端屬性、顯示自訂和視圖。 如果您對裝置範本的這些部分進行變更，但未定義裝置與 IoT Central 交換資料的方式，則不需要為範本版本。

您必須發佈任何裝置範本變更，不論它們是否需要更新版本，然後操作員才能使用它。 IoT Central 會阻止您將重大變更發佈至裝置範本，而不需要先設定範本的版本。

> [!NOTE]
> 若要深入瞭解如何建立裝置範本，請參閱[設定和管理裝置範本](howto-set-up-template.md)

## <a name="versioning-rules"></a>版本控制規則

本節將摘要說明適用于裝置範本的版本控制規則。 DCMs 和介面都有版本號碼。 下列程式碼片段顯示環境感應器裝置的 DCM。 DCM 有兩個介面： **DeviceInformation**和**EnvironmentalSensor**。 您可以在欄位結尾查看版本號碼 `@id` 。 若要在 IoT Central UI 中查看這項資訊，請選取 [裝置範本編輯器] 中的 [**查看身分識別**]。

```json
{
  "@id": "urn:contoso:sample_device:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "@id": "urn:contoso:sample_device:deviceinfo:1",
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": {
          "en": "Device Information"
        },
        "contents": [...
        ]
      }
    },
    {
      "@id": "urn:contoso:sample_device:sensor:1",
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:2",
        "@type": "Interface",
        "displayName": {
          "en": "Environmental Sensor"
        },
        "contents": [...
        ]
      }
    }
  ],
  "displayName": {
    "en": "Environment Sensor Capability Model"
  },
  "@context": [
    "http://azureiot.com/v1/contexts/IoTModel.json"
  ]
}
```

* 在 DCM 發佈之後，您就無法移除任何介面，即使是在新版本的裝置範本中也一樣。
* 在 DCM 發佈之後，如果您建立新版本的裝置範本，就可以新增介面。
* 在 DCM 發佈之後，如果您建立新版本的裝置範本，則可以使用較新的版本來取代介面。 例如，如果感應器 v1 裝置範本使用 EnvironmentalSensor v1 介面，您可以建立使用 EnvironmentalSensor v2 介面的感應器 v2 裝置範本。
* 在介面發佈之後，您就無法移除任何介面內容，即使是在新版本的裝置範本中也一樣。
* 在介面發行之後，如果您建立新版本的介面和裝置範本，您可以將專案加入介面的內容中。 您可以新增至介面的專案包括遙測、屬性和命令。
* 在介面發行之後，如果您建立新版本的介面和裝置範本，您可以對介面中的現有專案進行非架構變更。 介面專案的非架構元件包括顯示名稱和語義類型。 您無法變更之介面專案的架構部分為 [名稱]、[功能類型] 和 [架構]。

下列各節將逐步解說在 IoT Central 中修改裝置範本的一些範例。

## <a name="customize-the-device-template-without-versioning"></a>自訂裝置範本而不進行版本設定

您可以編輯裝置功能的某些元素，而不需要為您的裝置範本和介面進行版本。 例如，其中一些欄位包括顯示名稱、語義類型、最小值、最大值、小數位數、色彩、單位、顯示單位、批註和描述。 若要加入其中一個自訂專案：

1. 移至 [**裝置範本**] 頁面。
1. 選取您想要自訂的裝置範本。
1. 選擇 [**自訂**] 索引標籤。
1. 您的裝置功能模型中所定義的所有功能都列在此處。 您可以編輯、儲存和使用所有這些欄位，而不需要為您的裝置範本版本。 如果您想要編輯的欄位是唯讀的，您必須將裝置範本的版本設為 [變更]。 選取您想要編輯的欄位，並輸入任何新的值。
1. 按一下 [檔案] 。 現在，這些值會覆寫一開始儲存在您裝置範本中的任何專案，並在應用程式中使用。

## <a name="version-a-device-template"></a>版本裝置範本

建立新版本的裝置範本會建立範本的草稿版本，可在其中編輯裝置功能模型。 任何已發行的介面都會保留發行，直到它們個別建立版本為止。 若要修改已發佈的介面，請先建立新的裝置範本版本。

當您嘗試在 [自訂] 區段中編輯無法編輯的部分裝置功能模型時，只會將裝置範本的版本設為。

若要建立裝置範本的版本：

1. 移至 [**裝置範本**] 頁面。
1. 選取您要嘗試版本的裝置範本。
1. 按一下頁面頂端的 [**版本**] 按鈕，並為範本指定新的名稱。 IoT Central 建議您可以編輯的新名稱。
1. 按一下 [建立]。
1. 現在您的裝置範本處於草稿模式。 您可以看到您的介面仍處於鎖定狀態。 版本您想要修改的任何介面。

## <a name="version-an-interface"></a>版本介面

設定介面版本可讓您在已建立的介面內新增、更新和移除功能。

若要將介面設為版本：

1. 移至 [**裝置範本**] 頁面。
1. 選取您在草稿模式中的裝置範本。
1. 選取您想要進行版本與編輯之已發行模式中的介面。
1. 按一下 [介面] 頁面頂端的 [**版本**] 按鈕。
1. 按一下 [建立]。
1. 現在您的介面處於草稿模式。 您可以在介面中新增或編輯功能，而不會中斷現有的自訂和視圖。

## <a name="migrate-a-device-across-versions"></a>跨版本遷移裝置

您可以建立多個版本的裝置範本。 經過一段時間後，您將會有多個使用這些裝置範本的已連線裝置。 您可以將裝置從一個版本的裝置範本移轉至另一個版本。 下列步驟說明如何移轉裝置：

1. 移至 [ **Device Explorer** ] 頁面。
1. 選取您需要移轉到另一個版本的裝置。
1. 選擇 [**遷移**]。
1. 選取您想要將裝置遷移至哪個版本號碼的裝置範本，然後選擇 [**遷移**]。

![如何移轉裝置](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>後續步驟

如果您是操作員或解決方案產生器，建議的下一個步驟是瞭解[如何管理您的裝置](./howto-manage-devices.md)。

如果您是裝置開發人員，建議的下一個步驟是閱讀[Azure IoT Edge 裝置和 Azure IoT Central](./concepts-iot-edge.md)的相關資訊。
