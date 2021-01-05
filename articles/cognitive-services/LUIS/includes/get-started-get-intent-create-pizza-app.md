---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.date: 12/29/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: aahi
ms.reviewer: roy-har
ms.openlocfilehash: 7aa2fba6ef551a745ccaf5b00f36021b9d8680ce
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97820483"
---
1. 選取 [pizza-app-for-luis-v6.json](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) 以顯示 `pizza-app-for-luis.json` 檔案的 GitHub 頁面。
1. 以滑鼠右鍵按一下或長點選 [原始] 按鈕，然後選取 [另存連結] 將 `pizza-app-for-luis.json` 儲存到您的電腦。
1. 登入 [LUIS 入口網站](https://www.luis.ai)。
1. 選取[我的應用程式](https://www.luis.ai/applications)。
1. 在 [我的應用程式] 頁面中，選取 [+ 新增對話應用程式]。
1. 選取 [匯入為 JSON]。
1. 在 [匯入新的應用程式] 對話方塊中，選取 [選擇檔案] 按鈕。
1. 選取您下載的 `pizza-app-for-luis.json` 檔案，然後選取 [開啟]。
1. 在 [匯入新的應用程式] 對話方塊的 [名稱] 欄位中，輸入您 Pizza 應用程式的名稱，然後選取 [完成] 按鈕。

應用程式將會匯入。

如果您看到對話方塊 [如何建立有效的 LUIS 應用程式]，請關閉對話方塊。

## <a name="train-and-publish-the-pizza-app"></a>將 Pizza 應用程式定型並發佈

您應該會看到 [意圖] 頁面，其中包含 Pizza 應用程式中的意圖清單。

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

您的 Pizza 應用程式現在已準備好可以使用。

## <a name="record-the-app-id-prediction-key-and-prediction-endpoint-of-your-pizza-app"></a>記錄您 Pizza 應用程式的應用程式識別碼、預測金鑰和預測端點

若要使用新 Pizza 應用程式，您將需要自己的 Pizza 應用程式的應用程式識別碼、預測金鑰和預測端點。

若要尋找這些值：

1. 從 [意圖] 頁面上，選取 [管理]。
1. 從 [應用程式設定] 頁面上，記錄 [應用程式識別碼]。
1. 選取 [Azure 資源]。
1. 從 [Azure 資源] 頁面上，記錄 [主索引鍵]。 此值是您的預測金鑰。
1. 記錄 [端點 URL]。 此值是您的預測端點。
