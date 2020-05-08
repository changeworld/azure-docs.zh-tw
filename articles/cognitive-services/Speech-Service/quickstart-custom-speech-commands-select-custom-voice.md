---
title: 快速入門：使用自訂命令搭配自訂語音（預覽）-語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您將指定自訂命令應用程式的輸出語音。
services: cognitive-services
author: anhoang
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: anhoang
ms.openlocfilehash: c2b9b4d51e89975d988ed94bf85695bd8a1cc770
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872454"
---
# <a name="quickstart-use-custom-commands-with-custom-voice-preview"></a>快速入門：使用自訂命令搭配自訂語音（預覽）

在[前一篇文章](./quickstart-custom-speech-commands-create-parameters.md)中，我們建立了新的自訂命令專案來回應具有參數的命令。

在本文中，我們將為我們所建立的應用程式選取自訂輸出語音。

## <a name="select-a-custom-voice"></a>選取自訂語音

1. 開啟[我們先前建立](./quickstart-custom-speech-commands-create-parameters.md)的專案
1. 從左窗格中選取 [**設定**]
1. 從中間窗格選取 [**自訂語音**]
1. 從資料表中選取想要的自訂或公用語音
1. 選取 [儲存]  。

> [!div class="mx-imgBorder"]
> ![含有參數的範例句子](media/custom-speech-commands/select-custom-voice.png)

> [!NOTE]
> - 對於**公用語音**，**類神經類型**僅適用于特定區域。 若要檢查可用性，請參閱[依區域/端點的標準和類神經語音](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)。
> - 針對**自訂**語音，您可以從 [自訂語音專案] 頁面建立它們。 請參閱[開始使用自訂語音](./how-to-custom-voice.md)。

應用程式現在會以選取的語音回應，而不是預設的語音。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [快速入門：使用語音 SDK 連接到自訂命令應用程式（預覽）](./quickstart-custom-speech-commands-speech-sdk.md)

