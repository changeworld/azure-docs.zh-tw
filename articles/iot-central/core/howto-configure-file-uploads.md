---
title: 將檔案從裝置上傳至 Azure 儲存體 |Microsoft Docs
description: 如何設定從您的裝置到雲端的檔案上傳。 設定檔案上傳之後，請在您的裝置上執行檔案上傳。
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: d6fbf84ec3822195f62970dbf08115059ffb7e4a
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122597"
---
# <a name="upload-files-from-your-devices-to-the-cloud"></a>將檔案從您的裝置上傳至雲端

*本主題適用于系統管理員和裝置開發人員。*

IoT Central 可讓您將媒體和其他檔案從已連線的裝置上傳至雲端存放裝置。 您可以在 IoT Central 應用程式中設定檔案上傳功能，然後在您的裝置程式碼中執行檔案上傳。

## <a name="prerequisites"></a>必要條件

您必須是 IoT Central 應用程式中的系統管理員，才能設定檔案上傳。

您需要 Azure 儲存體帳戶和容器來儲存已上傳的檔案。 如果您沒有現有的儲存體帳戶和容器可供使用，請 [在 Azure 入口網站中建立新的儲存體帳戶](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。

## <a name="configure-device-file-uploads"></a>設定裝置檔案上傳

設定裝置檔案上傳：

1. 流覽至您應用程式中的 [系統 **管理** ] 區段。

1. 選取 **裝置檔案上傳**。

1. 選取要使用的儲存體帳戶和容器。 如果儲存體帳戶與您的應用程式位於不同的 Azure 訂用帳戶中，請輸入儲存體帳戶連接字串。

1. 如有必要，請調整上傳時間的上傳時間，以設定上傳要求的有效時間長度。 有效的值為1到24小時。

1. 選取 [儲存]。 當狀態顯示為 **已設定**時，您就可以從裝置上傳檔案。

:::image type="content" source="media/howto-configure-file-uploads/file-upload-configuration.png" alt-text="在應用程式中設定檔案上傳":::

## <a name="disable-device-file-uploads"></a>停用裝置檔案上傳

如果您想要停用裝置檔案上傳至您的 IoT Central 應用程式：

1. 流覽至您應用程式中的 [系統 **管理** ] 區段。

1. 選取 **裝置檔案上傳**。

1. 選取 [刪除]。

## <a name="upload-a-file-from-a-device"></a>從裝置上傳檔案

IoT Central 使用 IoT 中樞的檔案上傳功能，讓裝置可上傳檔案。 如需說明如何從裝置上傳檔案的範例程式碼，請參閱 [IoT Central 檔案上傳裝置範例](/samples/iot-for-all/iotc-file-upload-device/iotc-file-upload-device/)。

## <a name="next-steps"></a>下一步

現在您已瞭解如何在 IoT Central 中設定和執行裝置檔案上傳，建議的下一個步驟是深入瞭解裝置檔案上傳：

- [使用 IoT 中樞將檔案從裝置上傳至雲端 (.NET)](../../iot-hub/iot-hub-csharp-csharp-file-upload.md)
- [使用 IoT 中樞將檔案從裝置上傳至雲端 (Java)](../../iot-hub/iot-hub-java-java-file-upload.md)
- [使用 IoT 中樞將檔案從裝置上傳至雲端 (Node.js)](../../iot-hub/iot-hub-node-node-file-upload.md)
- [使用 IoT 中樞將檔案從裝置上傳至雲端 (Python) ](../../iot-hub/iot-hub-python-python-file-upload.md)