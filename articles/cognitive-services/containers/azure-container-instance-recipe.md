---
title: Azure 容器實例配方
titleSuffix: Azure Cognitive Services
description: 瞭解如何在 Azure 容器實例上部署認知服務容器
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8ddaed181d017e3167694a9d7edf53c7c09fd5e9
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968514"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>在 Azure 容器執行個體上部署及執行容器

在下列步驟中，您可以使用 Azure [容器實例](../../container-instances/index.yml)，輕鬆地調整雲端中的 Azure 認知服務應用程式。 容器化可協助您專注于建立應用程式，而不是管理基礎結構。 如需使用容器的詳細資訊，請參閱 [功能和優點](../cognitive-services-container-support.md#features-and-benefits)。

## <a name="prerequisites"></a>Prerequisites

配方適用于任何認知服務容器。 使用配方之前，必須先在 Azure 入口網站中建立認知服務資源。 每項支援容器的認知服務都有「如何安裝」檔，專門用來安裝及設定容器的服務。 某些服務需要檔案或檔案集合做為容器的輸入，您必須先瞭解並成功使用容器，再使用此解決方案。

* 在 Azure 入口網站中建立的認知服務資源。
* 認知服務 **端點 URL** -查看容器的特定服務「如何安裝」，以找出端點 URL 從 Azure 入口網站內的位置，以及正確的 URL 範例看起來是什麼樣子。 確切的格式可以從服務變更為「服務」。
* 認知服務 **金鑰** -金鑰位於 Azure 資源的 [ **金鑰** ] 頁面上。 您只需要兩個金鑰中的其中一個。 索引鍵是32英數位元的字串。
* 本機主機上的單一認知服務容器 (您的電腦) 。 請確定您可以：
  * 使用命令來提取映射 `docker pull` 。
  * 使用命令執行所有必要的設定，以順利執行本機容器 `docker run` 。
  * 呼叫容器的端點，取得 HTTP 2xx 的回應和傳回的 JSON 回應。

角括弧中的所有變數都 `<>` 必須以您自己的值取代。 這種取代包含角括弧。

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>使用容器實例

1. 選取 **總覽** 並複製 IP 位址。 它會是數值 IP 位址，例如 `55.55.55.55` 。
1. 開啟新的瀏覽器索引標籤，並使用 IP 位址，例如 `http://<IP-address>:5000 (http://55.55.55.55:5000`) 。 您會看到容器的首頁，讓您知道容器正在執行。

1. 選取 [ **服務 API 描述** ] 以查看容器的 swagger 頁面。

1. 選取任何 **貼** 文 api，然後選取 [立即 **試用**]。 這些參數隨即顯示，包括輸入。 填入參數。

1. 選取 [ **執行** ] 將要求傳送至您的容器實例。

    您已成功在 Azure 容器實例中建立並使用認知服務容器。