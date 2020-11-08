---
title: 在 Azure 容器實例中執行異常偵測器容器
titleSuffix: Azure Cognitive Services
description: 將異常偵測器容器部署至 Azure 容器實例，並在網頁瀏覽器中進行測試。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mbullwin
ms.openlocfilehash: 3979bb82bfa055cc2a134bf3119097c452ffb855
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94364117"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>將異常偵測器容器部署至 Azure 容器實例

瞭解如何將認知服務 [異常](../anomaly-detector-container-howto.md) 偵測器容器部署至 Azure [容器實例](../../../container-instances/index.yml)。 此程式示範如何建立異常偵測器資源。 然後我們會討論如何提取相關聯的容器映射。 最後，我們強調了從瀏覽器執行兩者的協調流程的能力。 使用容器可以將開發人員的注意力轉移到管理基礎結構之外，改為專注于應用程式開發。

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>後續步驟

* 查看 [安裝和執行](../anomaly-detector-container-configuration.md) 容器以提取容器映射並執行容器
* 檢閱[設定容器](../anomaly-detector-container-configuration.md)以了解組態設定
* [深入瞭解異常偵測器 API 服務](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)