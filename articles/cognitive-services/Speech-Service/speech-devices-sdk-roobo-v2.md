---
title: 語音設備 SDK Roobo 智慧音訊開發套件 v2 - 語音服務
titleSuffix: Azure Cognitive Services
description: 使用語音設備 SDK、Roobo 智慧音訊開發工具組 v2 入門的先決條件和說明。
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 2f325c80877068c53d690bd7ff74f768dab3a174
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371586"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>設備： Roobo 智慧音訊開發套件 v2

本文為 Roobo 智慧音訊開發工具組2提供了特定于設備的資訊。

## <a name="set-up-the-development-kit"></a>設定開發套件

1. 開發套件有兩個 micro USB 連接器。 左側的連接器用來對開發套件供電，在下圖中會以 Power 來醒目提示。 右邊的連接器則用來控制開發套件，在圖中會標示為 Debug。 
    ![連接開發套件](media/speech-devices-sdk/roobo-v2-connections.png)
1. 使用 micro USB 纜線將電源連接埠連接至電腦或電源配接器，以對開發套件供電。 上方面板底下會亮起綠色電源指示燈。
1. 要控制開發套件，請使用第二根微型 USB 電纜將調試埠連接到電腦。 使用高品質的電纜以確保可靠的通信至關重要。
1. 以圓形方式定向您的開發套件 - 直立，麥克風面向天花板，如上圖所示


## <a name="development-information"></a>開發資訊

有關詳細資訊，請參閱[Roobo 開發指南](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)。

## <a name="audio-recordplay"></a>音訊錄製/播放

DDK2 音訊操作可通過以下方式執行：
* 使用 ALSA 開源庫及其應用程式。
* 使用 appmainprog 介面執行應用程式開發。 DDK2音訊相關軟體框架使用標準ALSA框架，您可以使用libasound. 所以直接開發軟體。 因此，您可以使用 ALSA 的錄製和播放直接錄製和播放音訊。
