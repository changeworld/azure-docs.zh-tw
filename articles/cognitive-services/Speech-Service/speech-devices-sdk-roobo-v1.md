---
title: 語音設備 SDK Roobo 智慧音訊開發套件 v1 - 語音服務
titleSuffix: Azure Cognitive Services
description: 使用語音設備 SDK、Roobo 智慧音訊開發工具組 v1 入門的先決條件和說明。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 9add5b063b67ddcc4cd5bf93e7f5b570b004e5ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815585"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>設備：羅博智慧音訊開發套件

本文為 Roobo 智慧音訊開發工具組提供特定于設備的資訊。

## <a name="set-up-the-development-kit"></a>設定開發套件

1. 開發套件有兩個 micro USB 連接器。 左側的連接器用來對開發套件供電，在下圖中會以 Power 來醒目提示。 右邊的連接器則用來控制開發套件，在圖中會標示為 Debug。

    ![連接開發套件](media/speech-devices-sdk/qsg-1.png)

1. 使用 micro USB 纜線將電源連接埠連接至電腦或電源配接器，以對開發套件供電。 上方面板底下會亮起綠色電源指示燈。

1. 要控制開發套件，請使用第二根微型 USB 電纜將調試埠連接到電腦。 請務必使用高品質的纜線，以確保可靠的通訊。

1. 將您的開發套件依圓形或線性設定調整方向。

    |開發套件設定|方向|
    |-----------------------------|------------|
    |圓形|直立，麥克風朝向天花板|
    |線性|側邊，麥克風朝向您 (如下圖所示)|

    ![線性開發套件方向](media/speech-devices-sdk/qsg-2.png)

1. 安裝證書並設置聲音設備的許可權。 在命令提示字元視窗中輸入下列命令：

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > 這些命令會使用 Android Debug Bridge `adb.exe`，它是 Android Studio 安裝的一部分。 此工具位於 C:\Users\[使用者名稱]\AppData\Local\Android\Sdk\platform-tools。 您可以將此目錄新增至您的路徑，以便更方便地叫用 `adb`。 否則，您必須在叫用 `adb` 的每個命令中指定 adb.exe 的完整安裝路徑。
    >
    > 如果您看到錯誤`no devices/emulators found`，請檢查您的 USB 電纜已連接，並且是高品質的電纜。 您可以使用 `adb devices` 來確認電腦是否可以與開發套件通訊，以便其可傳回裝置清單。
    >
    > [!TIP]
    > 請將您電腦的麥克風和喇叭靜音，以確定您使用的是開發套件的麥克風。 如此一來，您就不會不小心讓來自電腦的音訊觸發該裝置。

1. 如果要將揚聲器連接到開發套件，可以將其連接到音訊線路。您應該選擇帶有 3.5mm 類比插頭的高品質揚聲器。

    ![Vysor 音訊](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>開發資訊

有關詳細資訊，請參閱[Roobo 開發指南](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)。

## <a name="audio"></a>音訊

Roobo 提供了一種工具，用於捕獲所有音訊到快閃記憶體。 它可以協助您針對音訊問題進行疑難排解。 針對每個開發套件設定提供了工具版本。 在[Roobo 網站上](https://ddk.roobo.com/)，選擇您的設備，然後選擇頁面底部的**Roobo 工具**連結。

## <a name="next-steps"></a>後續步驟

* [運行 Android 示例應用](speech-devices-sdk-android-quickstart.md)
