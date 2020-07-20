---
title: 部署自訂語音語音服務的模型
titleSuffix: Azure Cognitive Services
description: 在本檔中，您將瞭解如何使用自訂語音入口網站來建立和部署端點。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: c7f03027abf7f3c5e330e5cd95075cce1152a7d9
ms.sourcegitcommit: 666303748238dfdf9da30d49d89b915af73b0468
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85130410"
---
# <a name="deploy-a-custom-model"></a>部署自訂模型

在您上傳和檢查資料、評估精確度，以及定型自訂模型之後，您可以部署自訂端點，以與您的應用程式、工具和產品搭配使用。 在本檔中，您將瞭解如何使用[自訂語音入口網站](https://speech.microsoft.com/customspeech)來建立和部署端點。

## <a name="create-a-custom-endpoint"></a>建立自訂端點

若要建立新的自訂端點，請登入[自訂語音入口網站](https://speech.microsoft.com/customspeech)，然後從頁面頂端的 [自訂語音] 功能表中選取 [**部署**]。 如果這是您第一次執行，您會發現資料表中沒有列出任何端點。 建立端點之後，您將使用此頁面來追蹤每個已部署的端點。

接下來，選取 [**新增端點**]，然後輸入自訂端點的**名稱**和**描述**。 然後選取您想要與此端點產生關聯的自訂模型。 在此頁面中，您也可以啟用記錄功能。 記錄可讓您監視端點流量。 若已停用，則不會儲存流量。

![如何部署模型](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> 別忘了接受使用條款和定價詳細資料。

接下來，選取 [建立]****。 此動作會將您返回 [**部署**] 頁面。 資料表現在包含一個對應至您自訂端點的專案。 端點的狀態會顯示其目前的狀態。 最多可能需要30分鐘的時間，才能使用您的自訂模型來具現化新端點。 當部署的狀態變更為 [**完成**] 時，端點就可供使用。

部署端點之後，端點名稱會顯示為連結。 按一下連結以顯示端點的特定資訊，例如端點金鑰、端點 URL 和範例程式碼。

## <a name="view-logging-data"></a>查看記錄資料

您可以在 [**端點 > 詳細**資料] 底下下載記錄資料。
> [!NOTE]
>記錄資料可在 Microsoft 擁有的儲存體30天內使用，之後將會移除。 如果客戶擁有的儲存體帳戶已連結到認知服務訂用帳戶，則不會自動刪除記錄資料。

## <a name="next-steps"></a>後續步驟

* [在這裡](how-to-specify-source-language.md)瞭解如何使用您的自訂模型。

## <a name="additional-resources"></a>其他資源

* [準備和測試您的資料](how-to-custom-speech-test-data.md)
* [檢查您的資料](how-to-custom-speech-inspect-data.md)
* [評估您的資料](how-to-custom-speech-evaluate-data.md)
* [定型模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)
