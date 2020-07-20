---
title: 關於 Azure Kinect DK
description: Azure Kinect 開發人員套件 (DK) 工具與整合式服務的概觀。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: overview
ms.date: 06/26/2019
keywords: azure, kinect, 概觀, 開發人員套件, DK, 裝置, 景深, 全身追蹤, 語音, 認知服務, SDK, 軟體開發套件, 韌體
ms.openlocfilehash: 6b3215ba6761521214ebffa2a100c5a1a030ab4c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "85277780"
---
# <a name="about-azure-kinect-dk"></a>關於 Azure Kinect DK

 ![Azure Kinect DK](./media/index/device-image.jpg)

Azure Kinect DK 是具有進階 AI 感應器的開發人員套件，可提供精密的電腦視覺和語音模型。  Kinect 包含景深感應器、具有攝影機的空間麥克風陣列，以及具有多個模式、選項和軟體開發套件 (SDK) 的全方位小型裝置的方向感應器。 您可以在 [Microsoft 線上商店](https://www.microsoft.com/p/azure-kinect-dk/8pp5vxmd9nhq)購買。

Azure Kinect DK 開發環境是由下列多個 SDK 所組成的：

- 感應器 SDK，適用於低階感應器和裝置存取。
- 全身追蹤 SDK，用於進行 3D 全身追蹤。
- 語音認知服務 SDK，用於啟用麥克風存取和 Azure 雲端式語音服務。

此外，認知視覺服務可與裝置的 RGB 攝影機搭配使用。

   ![Azure Kinect SDK 圖表](./media/quickstarts/sdk-diagram.jpg)

## <a name="azure-kinect-sensor-sdk"></a>Azure Kinect 感應器 SDK

Azure Kinect 感應器 SDK 可為 Azure Kinect DK 硬體感應器和裝置設定提供低階感應器存取功能。

若要深入了解 Azure Kinect 感應器 SDK，請參閱[使用感應器 SDK](about-sensor-sdk.md)。

### <a name="azure-kinect-sensor-sdk-features"></a>Azure Kinect 感應器 SDK 功能

感應器 SDK 具有下列功能，可在 Azure Kinect DK 上安裝並執行之後立即運作：

- 景深相機存取與模式控制 (被動 IR 模式，加上寬和窄視角景深模式) 
- RGB 相機存取與控制 (例如，曝光和白平衡) 
- 動作感應器 (陀螺儀和加速計) 存取 
- 同步的景深 RGB 相機串流，並可設定相機之間的延遲 
- 外部裝置同步控制，並可設定裝置之間的延遲偏移 
- 相機框架中繼資料存取，可用於影像解析度、時間戳記等。 
- 裝置校正資料存取 

### <a name="azure-kinect-sensor-sdk-tools"></a>Azure Kinect 感應器 SDK 工具

感應器 SDK 提供了下列工具：

- 檢視器工具，可監視裝置的資料流和設定不同模式。
- 感應器錄製工具和播放讀取器 API (使用 Matroska 容器格式)。
- Azure Kinect DK 的韌體更新工具。

## <a name="azure-kinect-body-tracking-sdk"></a>Azure Kinect 全身追蹤 SDK

全身追蹤 SDK 包含 Windows 程式庫和執行階段，與 Azure Kinect DK 硬體搭配使用時，可進行 3D 全身追蹤。

### <a name="azure-kinect-body-tracking-features"></a>Azure Kinect 全身追蹤功能

隨附的 SDK 提供了下列全身追蹤功能：

- 提供全身分割。
- 針對視角 (FOV) 中的全身或身體每個部分包含符合解剖學理論的正確骨架。
- 為每個身體提供唯一的身分識別。
- 可以追蹤身體一段時間。

### <a name="azure-kinect-body-tracking-tools"></a>Azure Kinect 全身追蹤工具

- 全身追蹤程式有一個檢視器工具，可讓您進行 3D 全身追蹤。

## <a name="speech-cognitive-services-sdk"></a>語音認知服務 SDK

語音 SDK 可啟用與 Azure 連線的語音服務。

### <a name="speech-services"></a>語音服務

- 語音轉文字
- 語音翻譯
- 文字轉語音

>[!NOTE]
>Azure Kinect DK 沒有喇叭。

如需其他詳細資料和資訊，請造訪[語音服務文件](https://docs.microsoft.com/azure/cognitive-services/speech-service/)。

## <a name="vision-services"></a>視覺服務

下列 [Azure 認知視覺服務](https://azure.microsoft.com/services/cognitive-services/directory/vision/)會提供可識別及分析影像和影片中內容的 Azure 服務。

- [電腦視覺](https://azure.microsoft.com/services/cognitive-services/computer-vision/)
- [臉部](https://azure.microsoft.com/services/cognitive-services/face/)
- [影片索引子](https://azure.microsoft.com/services/media-services/video-indexer/)
- [內容仲裁](https://azure.microsoft.com/services/cognitive-services/content-moderator/)
- [自訂視覺](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)

這些服務會不斷演進與改善，因此請記得定期查看是否有新的或其他的[認知服務](https://azure.microsoft.com/services/cognitive-services/)可改善您的應用程式。 如需及早看到新興的服務，請查看[認知服務實驗室](https://labs.cognitive.microsoft.com/)。

## <a name="azure-kinect-hardware-requirements"></a>Azure Kinect 硬體需求

Azure Kinect DK 會將 Microsoft 的最新感應器技術整合到單一的 USB 連線配件中。 如需詳細資訊，請參閱 [Azure Kinect DK 硬體規格](hardware-specification.md)。

## <a name="next-steps"></a>後續步驟

您現在已大致了解 Azure Kinect DK。 下一步便是深入了解並進行設定！

> [!div class="nextstepaction"]
>[快速入門：設定 Azure Kinect DK](set-up-azure-kinect-dk.md)
