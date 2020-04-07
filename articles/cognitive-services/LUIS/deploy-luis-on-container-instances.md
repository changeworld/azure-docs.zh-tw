---
title: 在 Azure 容器實體上部署 LUIS 容器
titleSuffix: Azure Cognitive Services
description: 將 LUIS 容器部署到 Azure 容器實例,並在 Web 瀏覽器中測試它。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dapine
ms.openlocfilehash: f1a0a08351a03e46d6c3a1e82b68ecea6e36c015
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757256"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>將語言理解 (LUIS) 容器部署到 Azure 容器實體

瞭解如何將認知服務[LUIS](luis-container-howto.md)容器部署到 Azure[容器實體 。](https://docs.microsoft.com/azure/container-instances/) 此過程演示了異常檢測器資源的創建。 然後,我們討論拉關聯的容器映射。 最後,我們強調從瀏覽器執行兩者業務流程的能力。 使用容器可以將開發人員的注意力從管理基礎結構轉移到專注於應用程式開發上。

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用

LUIS 容器需要`.gz`在運行時拉取的模型檔。 容器必須能夠通過容器實例的卷裝載存取此模型檔。 有關建立 Azure 檔案的共享的資訊,請參考[檔案分享](../../storage/files/storage-how-to-create-file-share.md)。 記下 Azure 儲存帳戶名稱、密鑰和檔共享名稱,因為稍後將需要它們。

### <a name="export-and-upload-packaged-luis-app"></a>匯出與上傳的 LUIS 應用程式

要將 LUIS 模型(打包套用)上傳到 Azure 檔分享,您需要<a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">首先<span class="docon docon-navigate-external x-hidden-focus"></span>從 LUIS 門戶匯出它</a>。 從 Azure 門戶導航到儲存帳戶資源的 **「概述」** 頁,然後選擇 **「檔案共用**」。。 選擇您最近創建的檔案共享名稱,然後選擇 **「上傳**」按鈕。

> [!div class="mx-imgBorder"]
> ![上傳到檔案分享](media/luis-how-to-deploy-to-aci/upload-file-share.png)

上傳 LUIS 模型檔。

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
