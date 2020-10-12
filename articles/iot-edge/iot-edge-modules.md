---
title: 了解模組如何在您的裝置上執行邏輯 - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge 模組是邏輯的容器化單位，可從遠端部署及管理，讓您能夠在 IoT Edge 裝置上執行商務邏輯
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: 9feef9ec04bcc44bc09d55202b72ada4adc94cf5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89017051"
---
# <a name="understand-azure-iot-edge-modules"></a>了解 Azure IoT Edge 模組

Azure IoT Edge 可讓您以 *模組*形式部署和管理邊緣的商務邏輯。 Azure IoT Edge 模組是 IoT Edge 管理的最小計算單位，可以包含 Azure 服務 (例如 Azure 串流分析) 或您自己的解決方案特定程式碼。 若要瞭解如何開發、部署和維護模組，請考慮使用模組的四個概念元素：

* **模組映像**是套件，其中包含定義模組的軟體。
* **模組執行個體**是在 IoT Edge 裝置上執行模組映像的計算單位。 模組執行個體是由 IoT Edge 執行階段啟動。
* **模組身分識別**是一段資訊 (包括安全性認證)，儲存在與每個模組執行個體相關聯的 IoT 中樞。
* **模組對應項**是儲存在 IoT 中樞的 JSON 文件，其中包含模組執行個體的狀態資訊，包括中繼資料、設定和條件。

## <a name="module-images-and-instances"></a>模組映像和執行個體

IoT Edge 模組映像包含應用程式，該應用程式會利用 IoT Edge 執行階段的管理、安全性和通訊功能。 您可以開發自己的模組映像，或從支援的 Azure 服務 (例如 Azure 串流分析) 匯出。
映像存在於雲端中，它們可以在不同的解決方案中更新、變更及部署。 例如，使用機器學習來預測生產線輸出的模組，相較於使用電腦版本以控制無人機的模組，會以個別映像存在。

每次模組映像部署至裝置並且由 IoT Edge 執行階段啟動時，就會建立該模組的新執行個體。 位於世界上不同地方的兩個裝置可以使用相同的模組映像。 不過，在裝置上啟動模組時，每個裝置都會有自己的模組執行個體。

![圖表 - 雲端中的模組映像，裝置上的模組執行個體](./media/iot-edge-modules/image_instance.png)

在實作中，容器映像在存放庫中存在為容器映像，而容器執行個體是裝置上的容器。

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>模組身分識別

當 IoT Edge 執行時間建立新的模組實例時，它會取得對應的模組身分識別。 模組身分識別會儲存在 IoT 中樞，並做為該模組實例所有本機和雲端通訊的定址和安全性範圍。

與模組執行個體相關聯的身分識別取決於在其上執行執行個體的裝置身分識別，以及您提供給解決方案中該模組的名稱。 例如，如果您將使用 Azure 串流分析的模組稱為 `insight`，並且在稱為 `Hannover01` 的裝置上部署，IoT Edge 執行階段會建立稱為 `/devices/Hannover01/modules/insight` 的對應模組身分識別。

很明顯地，在案例中當您需要在相同裝置上部署一個模組映像多次時，您可以使用不同名稱部署相同映像多次。

![圖表 - 模組身分識別在裝置內和裝置之間是唯一的](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>模組對應項

每個模組執行個體也有對應的模組對應項，您可以用來設定模組執行個體。 執行個體和對應項是透過模組身分識別彼此相互關聯。

模組對應項是 JSON 文件，它會儲存模組資訊和設定屬性。 這個概念與 IoT 中樞的[裝置對應項](../iot-hub/iot-hub-devguide-device-twins.md)概念相同。 模組對應項的結構與裝置對應項相同。 用來與這兩種對應項互動的 API 也相同。 兩者之間唯一的差別是用來具現化用戶端 SDK 的身分識別。

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a
// module since it is created with a module’s connection string instead
// of a device connection string.
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings);
await client.OpenAsync();

// Get the module twin
Twin twin = await client.GetTwinAsync();
```

## <a name="offline-capabilities"></a>離線功能

在與 IoT 中樞同步處理之後，Azure IoT Edge 模組可以無限期地離線運作。 IoT Edge 裝置也可以將此離線功能延伸至其他 IoT 裝置。 如需詳細資訊，請參閱[了解適用於 IoT Edge 裝置、模組及子裝置的擴充離線功能](offline-capabilities.md)。

## <a name="next-steps"></a>後續步驟

* [瞭解開發 IoT Edge 課程模組的需求和工具](module-development.md)
* [了解 Azure IoT Edge 執行階段和架構](iot-edge-runtime.md)
