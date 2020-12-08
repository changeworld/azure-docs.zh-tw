---
title: 教學課程 - 將一般用戶端應用程式連線至 Azure IoT Central | Microsoft Docs
description: 本教學課程說明如何以裝置開發人員身分，將執行 C、C#、Java、JavaScript 或 Python 用戶端應用程式的裝置與您的 Azure IoT Central 應用程式連線。 您可以新增可讓操作員與連線裝置互動的檢視，藉以修改自動產生的裝置範本。
author: dominicbetts
ms.author: dobett
ms.date: 11/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 9eeb8174c900b0f548144231e65643c9559f75e3
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96126066"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application"></a>教學課程：建立用戶端應用程式並將其連線至您的 Azure IoT Central 應用程式

*本文適用於解決方案建置人員和裝置開發人員。*

本教學課程說明如何以裝置開發人員身分，將用戶端應用程式與您的 Azure IoT Central 應用程式連線。 應用程式會模擬控溫器裝置的行為。 應用程式在連線至 IoT Central 時，將會傳送控溫器裝置型號的型號識別碼。 IoT Central 會使用型號識別碼來擷取裝置型號，並為您建立裝置範本。 您可將自訂和檢視新增至裝置範本，讓操作員能與裝置互動。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立和執行裝置程式碼，並查看其是否與您的 IoT Central 應用程式連線。
> * 檢視裝置所傳送的模擬遙測。
> * 將自訂檢視新增至裝置範本。
> * 發佈裝置範本。
> * 使用檢視管理裝置屬性。
> * 呼叫命令以控制裝置。

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-central-connect-device-c](../../../includes/iot-central-connect-device-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-central-connect-device-csharp](../../../includes/iot-central-connect-device-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-central-connect-device-java](../../../includes/iot-central-connect-device-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-central-connect-device-nodejs](../../../includes/iot-central-connect-device-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-central-connect-device-python](../../../includes/iot-central-connect-device-python.md)]

:::zone-end

## <a name="view-raw-data"></a>檢視未經處理資料

身為裝置開發人員，您可以使用 [未經處理資料] 檢視，來檢查裝置傳送到 IoT Central 的未經處理資料：

:::image type="content" source="media/tutorial-connect-device/raw-data.png" alt-text="未經處理資料檢視":::

在此檢視中，您可以選取要顯示的資料行，並設定要檢視的時間範圍。 [未模型化的資料] 資料行會顯示裝置內不符合裝置範本中任何屬性或遙測定義的資料。

## <a name="next-steps"></a>後續步驟

如果您想要繼續進行這套 IoT Central 教學課程並深入了解如何建置 IoT Central 解決方案，請參閱：

> [!div class="nextstepaction"]
> [建立閘道裝置範本](./tutorial-define-gateway-device-type.md)

身為裝置開發人員，您現在已了解如何使用 Java 建立裝置的基本概念，以下是一些建議的後續步驟：

* 閱讀[什麼是裝置範本？](./concepts-device-templates.md)，以深入了解在您實作裝置程式碼時裝置範本的角色。
* 如需深入了解如何向 IoT Central 註冊裝置，以及 IoT Central 如何保護裝置連線，請參閱[連線至 Azure IoT Central](./concepts-get-connected.md)。
* 參閱[遙測、屬性和命令承載](concepts-telemetry-properties-commands.md)，以深入了解裝置與 IoT Central 交換的資料。
