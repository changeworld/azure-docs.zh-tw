---
title: 在 Azure 容器實體執行例外例外偵測器容器
titleSuffix: Azure Cognitive Services
description: 將異常檢測器容器部署到 Azure 容器實例,並在 Web 瀏覽器中測試它。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a4973254c8a53917d99daf38b0d11e5dbecbb731
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875099"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>將例外偵測器容器部署到 Azure 容器實體

瞭解如何將認知服務[例外偵測器](../anomaly-detector-container-howto.md)容器部署到 Azure[容器實體 。](https://docs.microsoft.com/azure/container-instances/) 此過程演示了異常檢測器資源的創建。 然後,我們討論拉關聯的容器映射。 最後,我們強調從瀏覽器執行兩者業務流程的能力。 使用容器可以將開發人員的注意力從管理基礎結構轉移到專注於應用程式開發上。

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>要求私人容器登錄的存取

您必須首先填寫並提交[異常檢測器容器請求表單](https://aka.ms/adcontainer),以請求訪問容器。

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>後續步驟

* 檢視[安裝並執行容器](../anomaly-detector-container-configuration.md)來以拉取容器映像並執行容器
* 檢閱[設定容器](../anomaly-detector-container-configuration.md)以了解組態設定
* [瞭解有關例外偵測器 API 服務的更多資訊](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
