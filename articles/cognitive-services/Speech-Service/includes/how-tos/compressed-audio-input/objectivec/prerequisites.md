---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 28f6ef7248fada8286bbe90c868cd9f947f0435d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943846"
---
使用[GStreamer](https://gstreamer.freedesktop.org)實現處理壓縮音訊。 出於許可原因，GStreamer 二進位檔案未編譯並與語音 SDK 連結。 相反，需要構建包含這些功能的包裝庫，並使用 SDK 隨應用一起提供。

要構建此包裝庫，請首先下載並安裝[GStreamer SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg)。 然後，下載[包裝庫](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)的**Xcode**專案。

在**Xcode**中打開專案並為通用**iOS 設備**目標構建專案 - 它*不起作用*為特定目標構建該專案。

生成步驟將生成一個動態框架包，該捆綁庫具有動態庫，用於所有名稱為`GStreamerWrapper.framework`的必需體系結構。

此框架必須包含在使用語音服務 SDK 的壓縮音訊流的所有應用中。

在**Xcode**專案中應用以下設置來實現此目的：

1. 將`GStreamerWrapper.framework`您剛剛構建的和認知服務語音 SDK 的框架（可以[從這裡](https://aka.ms/csspeech/iosbinary)下載）複製到包含示例專案的目錄。
1. 調整*專案設置*中框架的路徑。
   1. 在 **"嵌入式二進位檔案"** 標題下的 **"常規**"選項卡中，將 SDK 庫添加為框架：**添加嵌入式二進位檔案** > **添加其他...** >導航到您選擇的目錄並選擇兩個框架。
   1. 移至 [組建設定]**** 索引標籤，然後啟動 [所有]**** 設定。
1. 將目錄 `$(SRCROOT)/..` 新增至 [搜尋路徑]**** 標題下方的 [架構搜尋路徑]__。
