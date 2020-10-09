---
title: 在 Azure 容器實例上部署 LUIS 容器
titleSuffix: Azure Cognitive Services
description: 將 LUIS 容器部署至 Azure 容器實例，並在網頁瀏覽器中進行測試。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: 08af17106846a0f5f7a0ccc2b01da1b2e15c1143
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80879191"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>將 Language Understanding (LUIS) 容器部署至 Azure 容器實例

瞭解如何將認知服務 [LUIS](luis-container-howto.md) 容器部署至 Azure [容器實例](https://docs.microsoft.com/azure/container-instances/)。 此程式示範如何建立異常偵測器資源。 然後我們會討論如何提取相關聯的容器映射。 最後，我們強調了從瀏覽器執行兩者的協調流程的能力。 使用容器可以將開發人員的注意力轉移到管理基礎結構之外，改為專注于應用程式開發。

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用

LUIS 容器需要在 `.gz` 執行時間提取的模型檔案。 容器必須能夠透過容器實例的磁片區掛接來存取此模型檔案。 如需建立 Azure 檔案共用的詳細資訊，請參閱 [建立檔案共用](../../storage/files/storage-how-to-create-file-share.md)。 請記下 Azure 儲存體的帳戶名稱、金鑰和檔案共用名稱，因為您稍後將會用到。

### <a name="export-and-upload-packaged-luis-app"></a>匯出和上傳已封裝的 LUIS 應用程式

為了將 LUIS 模型 (封裝的應用程式) 上傳至 Azure 檔案共用，您必須<a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">先 <span class="docon docon-navigate-external x-hidden-focus"></span> 從 LUIS 入口網站匯出</a>。 在 Azure 入口網站中，流覽至儲存體帳戶資源的 [ **總覽** ] 頁面，然後選取 [檔案 **共用**]。 選取您最近建立的檔案共用名稱，然後選取 [ **上傳** ] 按鈕。

> [!div class="mx-imgBorder"]
> ![上傳至檔案共用](media/luis-how-to-deploy-to-aci/upload-file-share.png)

上傳 LUIS 模型檔案。

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
