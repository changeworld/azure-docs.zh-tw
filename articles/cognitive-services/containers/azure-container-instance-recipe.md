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
ms.openlocfilehash: f247465c7e2c0a212df2821ebc7165d3ee5b15f3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876652"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>在 Azure 容器執行個體上部署及執行容器

在下列步驟中，您可以使用 Azure[容器實例](https://docs.microsoft.com/azure/container-instances/)，輕鬆地調整雲端中的 azure 認知服務應用程式。 容器化可協助您專注于建立應用程式，而不是管理基礎結構。 如需使用容器的詳細資訊，請參閱[功能與優點](../cognitive-services-container-support.md#features-and-benefits)。

## <a name="prerequisites"></a>先決條件

配方適用于任何認知服務的容器。 您必須先在 Azure 入口網站中建立認知服務資源，才能使用此配方。 每個支援容器的認知服務都有「如何安裝」檔，專門用來安裝和設定容器的服務。 某些服務需要檔案或一組檔案作為容器的輸入，請務必先瞭解並成功使用容器，然後再使用此解決方案。

* 在 Azure 入口網站中建立的認知服務資源。
* 認知服務**端點 URL** -審查您特定服務的容器「如何安裝」，以找出端點 URL 在 Azure 入口網站內的位置，以及正確的 URL 範例。 確切的格式可以從服務變更為服務。
* 認知服務**金鑰**-金鑰位於 Azure 資源的 [**金鑰**] 頁面。 您只需要兩個金鑰中的其中一個。 索引鍵是32個英數位元的字串。
* 本機主機（您的電腦）上的單一認知服務容器。 請確定您可以：
  * 使用`docker pull`命令提取映射。
  * 使用`docker run`命令，以所有必要的設定來順利執行本機容器。
  * 呼叫容器的端點，取得 HTTP 2xx 和 JSON 回應的回應。

以角括弧括住`<>`的所有變數都必須以您自己的值取代。 這種取代包括角括弧。

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>使用容器實例

1. 選取 [**總覽**] 並複製 [IP 位址]。 它將會是數位 IP 位址，例如`55.55.55.55`。
1. 開啟新的瀏覽器索引標籤，並使用 IP 位址， `http://<IP-address>:5000 (http://55.55.55.55:5000`例如）。 您會看到容器的首頁，讓您知道容器正在執行。

1. 選取 [**服務 API 描述**] 以查看容器的 swagger 頁面。

1. 選取任何一個**POST** api，然後選取 [立即**試用**]。 系統會顯示參數，包括輸入。 填寫參數。

1. 選取 [**執行**]，將要求傳送至您的容器實例。

    您已成功在 Azure 容器實例中建立並使用認知服務容器。
