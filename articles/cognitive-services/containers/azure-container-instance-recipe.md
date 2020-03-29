---
title: Azure 容器實例配方
titleSuffix: Azure Cognitive Services
description: 瞭解如何在 Azure 容器實例上部署認知服務容器
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 78f35042678aa7c30cebf73796df3e5d564b4502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76716993"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>在 Azure 容器執行個體上部署及執行容器

通過以下步驟，使用 Azure[容器實例](https://docs.microsoft.com/azure/container-instances/)輕鬆在雲中擴展 Azure 認知服務應用程式。 容器化可説明您專注于構建應用程式，而不是管理基礎結構。 有關使用容器的詳細資訊，請參閱[功能和優點](../cognitive-services-container-support.md#features-and-benefits)。

## <a name="prerequisites"></a>Prerequisites

該配方適用于任何認知服務容器。 在使用配方之前，必須在 Azure 門戶中創建認知服務資源。 每個支援容器的認知服務都有一個"如何安裝"文檔，專門用於安裝和配置容器的服務。 某些服務需要檔或檔集作為容器的輸入，請務必瞭解並成功使用此解決方案之前使用該容器。

* 在 Azure 門戶中創建的認知服務資源。
* 認知服務**終結點 URL** - 查看特定服務的容器的"如何安裝"，查找終結點 URL 在 Azure 門戶中的位置，以及 URL 的正確示例是什麼樣子。 確切的格式可以從服務更改為服務。
* 認知服務**金鑰**- 鍵位於 Azure 資源的 **"金鑰"** 頁上。 您只需要兩個金鑰中的其中一個。 該鍵是一個 32 個字母數位字元的字串。
* 本地主機（電腦）上的單個認知服務容器。 確保您可以：
  * 使用`docker pull`命令向下拉圖像。
  * 使用`docker run`命令使用所有必需的配置設置成功運行本地容器。
  * 調用容器的終結點，獲取 HTTP 2xx 的回應和 JSON 回應。

角括弧`<>`中的所有變數都需要用您自己的值替換。 此更換包括角括弧。

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>使用容器實例

1. 選擇 **"概述"** 並複製 IP 位址。 它將是一個數位 IP 位址，`55.55.55.55`如 。
1. 打開新的瀏覽器選項卡並使用 IP 位址，例如 。 `http://<IP-address>:5000 (http://55.55.55.55:5000` 您將看到容器的主頁，讓您知道容器正在運行。

1. 選擇 **"服務 API 描述"** 以查看容器的搖曳頁。

1. 選擇任何**POST** API 並選擇 **"試用"。** 將顯示參數，包括輸入。 填寫參數。

1. 選擇 **"執行"** 以將要求傳送到容器實例。

    您已成功在 Azure 容器實例中創建和使用認知服務容器。
