---
title: 快速入門：建立 LUIS 金鑰
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將了解如何建立 LUIS 應用程式並取得金鑰。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 5b1a5ac4867379457d161d07f4f4f2fc2d8ee6c3
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119590"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>快速入門：取得 LUIS 端點金鑰

## <a name="prerequisites"></a>Prerequisites

開始進行本教學課程之前，請務必準備好下列項目：

* LUIS 帳戶。 您可以透過 [LUIS 入口網站](https://www.luis.ai/home)免費取得。

## <a name="luis-and-speech"></a>LUIS 和語音

LUIS 會與語音服務整合以從語音辨識意圖。 您不需要語音服務訂用帳戶，只要有 LUIS 即可。

LUIS 會使用三種金鑰：

|金鑰類型|目的|
|--------|-------|
|編寫|可讓您透過程式設計建立和修改 LUIS 應用程式|
|入門|可讓您只使用文字來測試 LUIS 應用程式|
|端點 |可授權特定 LUIS 應用程式的存取權|

在本教學課程中，您需要端點金鑰類型。 本教學課程使用「家庭自動化」LUIS 應用程式範例，遵循[使用預先建置的家庭自動化應用程式](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app)快速入門即可建立。 如果您已建立自有 LUIS 應用程式，則可改用此應用程式。

當您建立 LUIS 應用程式時，LUIS 會自動產生入門金鑰，讓您可以使用文字查詢來測試應用程式。 此金鑰不會啟用語音服務整合，且無法用於本教學課程。 在 Azure 儀表板中建立 LUIS 資源，並將它指派給 LUIS 應用程式。 在本教學課程中，您可以使用免費訂用帳戶層。

在 Azure 儀表板中建立 LUIS 資源之後，登入 [LUIS 入口網站](https://www.luis.ai/home)，在 [我的應用程式]  頁面上選擇您的應用程式，然後切換至應用程式的 [管理]  頁面。 最後，選取提要欄位中的 [金鑰和端點]  。

![LUIS 入口網站的金鑰和端點設定](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

在 [金鑰和端點設定]  頁面上：

1. 向下捲動至 [資源與金鑰]  區段，然後選取 [指派資源]  。
1. 在 [對應用程式指派金鑰]  對話方塊中，進行下列變更：

   * 在 [租用戶]  下，選擇 [Microsoft]  。
   * 在 [訂用帳戶名稱]  下，選擇包含所要使用 LUIS 資源的 Azure 訂用帳戶。
   * 在 [金鑰]  下，選擇要與應用程式搭配使用的 LUIS 資源。

   稍等一會兒，新的訂用帳戶就會出現在頁面底部的資料表中。

1. 選取金鑰旁邊的圖示，將它複製到剪貼簿。 (您可以使用任一金鑰。)

![LUIS 應用程式 (app) 訂用帳戶金鑰](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [辨識意圖](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)
