---
title: 快速入門：適用於 C# Unity 的語音 SDK 平台設定 - 語音服務
titleSuffix: Azure Cognitive Services
description: 使用本指南來設定您的平台，以搭配使用 C# Unity 與語音服務 SDK。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 7678ffa1684fb1fe1ea198e9e1836a152cd51b4b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "88926630"
---
本指南說明如何安裝適用於 [Unity](https://unity3d.com/) 的[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)。

> [!NOTE]
> 適用於 Unity 的語音 SDK 支援 Windows Desktop (x86 和 x64) 或通用 Windows 平台 (x86、x64、ARM/ARM64)、Android (x86、ARM32/64) 以及 iOS (x64 模擬器、ARM32 和 ARM64)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Prerequisites

本快速入門需要：

- [Unity 2018.3 或更新版本](https://store.unity.com/)與[新增 UWP ARM64 支援的 Unity 2019.1](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)。
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。 15.9 版或更高版本的 Visual Studio 2017 也可以接受。
- 如需 Windows ARM64 支援，請安裝[適用於 ARM64 的選擇性建置工具和適用於 ARM64 的 Windows 10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

若要安裝適用於 Unity 的語音 SDK，請遵循下列步驟：

1. 下載和開啟[適用於 Unity 的語音 SDK](https://aka.ms/csspeech/unitypackage)，其會封裝為 Unity 資產套件 (.unitypackage)，而且應該已經與 Unity 建立關聯。 當資產套件開啟時，[匯入 Unity 套件]  對話方塊隨即出現。 您可能需要建立並開啟空白的專案，才能讓此步驟正常執行。

   [![Unity 編輯器中的 [匯入 Unity 套件] 對話方塊](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. 確定所有檔案皆已選取，然後選取 [匯入]  。 一會兒之後，Unity 資產套件就會匯入到您的專案中。

如需將資產套件匯入到 Unity 的詳細資訊，請參閱 [Unity 文件](https://docs.unity3d.com/Manual/AssetPackages.html)。

您現在可以繼續進行下列[後續步驟](#next-steps)。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [windows](../quickstart-list.md)]
