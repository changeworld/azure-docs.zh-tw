---
title: 在 Azure 容器實例上部署 LUIS 容器
titleSuffix: Azure Cognitive Services
description: 將 LUIS 容器部署到 Azure 容器實例，並在 Web 瀏覽器中測試它。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 30fd19634f6054b8b636dabcb4ef83b118554468
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75689462"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>將語言理解 （LUIS） 容器部署到 Azure 容器實例

瞭解如何將認知服務[LUIS](luis-container-howto.md)容器部署到 Azure[容器實例](https://docs.microsoft.com/azure/container-instances/)。 此過程演示了異常檢測器資源的創建。 然後，我們討論拉關聯的容器映射。 最後，我們強調從瀏覽器執行兩者業務流程的能力。 使用容器可以將開發人員的注意力從管理基礎結構轉移到專注于應用程式開發上。

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用

LUIS 容器需要在`.gz`運行時拉取的模型檔。 容器必須能夠通過容器實例的卷裝載訪問此模型檔。 有關創建 Azure 檔共用的資訊，請參閱[創建檔共用](../../storage/files/storage-how-to-create-file-share.md)。 記下 Azure 存儲帳戶名稱、金鑰和檔共用名稱稱，因為稍後將需要它們。

### <a name="export-and-upload-packaged-luis-app"></a>匯出和上傳打包的 LUIS 應用程式

要將 LUIS 模型（打包應用）上載到 Azure 檔共用，您需要<a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">首先<span class="docon docon-navigate-external x-hidden-focus"></span>從 LUIS 門戶匯出它</a>。 從 Azure 門戶導航到存儲帳戶資源的 **"概述"** 頁，然後選擇 **"檔共用**"。 選擇您最近創建的檔共用名稱稱，然後選擇 **"上傳**"按鈕。

> [!div class="mx-imgBorder"]
> ![上載到檔共用](media/luis-how-to-deploy-to-aci/upload-file-share.png)

上傳 LUIS 模型檔。

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
