---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 7106e139108681e1908b20d2daac5e619a63555d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422265"
---
處理壓縮的音訊會使用[GStreamer](https://gstreamer.freedesktop.org)來執行。 基於授權原因，GStreamer 二進位檔不會使用語音 SDK 進行編譯和連結。 相反地，必須使用 SDK 來建立包含這些函式的包裝函式程式庫，並將其隨附于應用程式。

若要建立此包裝函式程式庫，請先下載並安裝[GSTREAMER SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg)。 然後，下載包裝函式連結[庫](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)的**Xcode**專案。

在**Xcode**中開啟專案，並為**一般 iOS 裝置**目標建立它，這將*無法*針對特定目標建立。

組建步驟會針對名稱為的所有必要架構，使用動態連結程式庫產生動態架構組合`GStreamerWrapper.framework`。

此架構必須包含在搭配語音服務 SDK 使用壓縮音訊串流的所有應用程式中。

將下列設定套用至您的**Xcode**專案，以完成此動作：

1. 將`GStreamerWrapper.framework`您剛才建立的認知服務語音 SDK 的架構，複製到包含範例專案的目錄，您可以從[這裡](https://aka.ms/csspeech/iosbinary)下載。
1. 調整 [*專案設定*] 中架構的路徑。
   1. 在**內嵌二進位檔案**標題底下的 [**一般**] 索引標籤中，新增 SDK 程式庫作為架構： [新增] [**內嵌二進位** > 檔] [**新增其他 ...** ] > 流覽至您選擇的目錄，然後選取這兩個架構。
   1. 移至 [組建設定]**** 索引標籤，然後啟動 [所有]**** 設定。
1. 將目錄 `$(SRCROOT)/..` 新增至 [搜尋路徑]**** 標題下方的 [架構搜尋路徑]__。
