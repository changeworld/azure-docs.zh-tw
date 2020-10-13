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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85130410"
---
# <a name="deploy-a-custom-model"></a>部署自訂模型

上傳和檢查資料、評估精確度，以及定型自訂模型之後，您可以部署自訂端點，以搭配您的應用程式、工具和產品使用。 在本檔中，您將瞭解如何使用 [自訂語音入口網站](https://speech.microsoft.com/customspeech)來建立和部署端點。

## <a name="create-a-custom-endpoint"></a>建立自訂端點

若要建立新的自訂端點，請登入 [自訂語音入口網站](https://speech.microsoft.com/customspeech) ，然後從頁面頂端的自訂語音功能表中選取 [ **部署** ]。 如果這是您第一次執行，您會發現資料表中沒有列出任何端點。 建立端點之後，您將使用此頁面來追蹤每個已部署的端點。

接下來，選取 [ **新增端點** ]，並輸入自訂端點的 **名稱** 和 **描述** 。 然後選取您想要與此端點建立關聯的自訂模型。 您也可以從這個頁面啟用記錄功能。 記錄可讓您監視端點的流量。 如果停用，則不會儲存流量。

![如何部署模型](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> 別忘了接受使用條款和定價詳細資料。

接下來，選取 [建立]****。 此動作會將您返回 [ **部署** ] 頁面。 資料表現在包含對應至您自訂端點的專案。 端點的狀態會顯示其目前的狀態。 使用您的自訂模型來具現化新端點最多可能需要30分鐘的時間。 當部署的狀態變更為 [ **完成**] 時，端點就可供使用。

部署端點之後，端點名稱會顯示為連結。 按一下連結以顯示您端點的特定資訊，例如端點金鑰、端點 URL 及範例程式碼。

## <a name="view-logging-data"></a>查看記錄資料

您可以在 **端點 > 詳細**資料中下載記錄資料。
> [!NOTE]
>記錄資料可供 Microsoft 擁有的儲存體使用30天，之後將會移除。 如果客戶擁有的儲存體帳戶連結到認知服務訂用帳戶，記錄資料將不會自動刪除。

## <a name="next-steps"></a>後續步驟

* 在 [此](how-to-specify-source-language.md)瞭解如何使用您的自訂模型。

## <a name="additional-resources"></a>其他資源

* [準備及測試您的資料](how-to-custom-speech-test-data.md)
* [檢查您的資料](how-to-custom-speech-inspect-data.md)
* [評估您的資料](how-to-custom-speech-evaluate-data.md)
* [定型模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)
