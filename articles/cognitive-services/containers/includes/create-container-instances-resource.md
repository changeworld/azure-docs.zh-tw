---
title: 容器支援
titleSuffix: Azure Cognitive Services
description: 瞭解如何創建 Azure 容器實例資源。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 09957461fd92114d1181a570550ee1a189edd8ea
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876397"
---
## <a name="create-an-azure-container-instance-resource"></a>建立 Azure 容器實體資源

1. 跳到容器實例的["創建](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances)"頁。

2. 在 **'基礎知識'** 選項卡上,輸入以下詳細資訊:

    |設定|值|
    |--|--|
    |訂用帳戶|選取您的訂用帳戶。|
    |資源群組|選擇可用資源群組或建立新的資源群組,如`cognitive-services`。|
    |容器名稱|輸入名稱,如`cognitive-container-instance`。 名稱必須位於下蓋中。|
    |Location|選擇要部署的區域。|
    |映像類型|如果您的容器映像儲存在不需要認證的容器註冊表中, 請選擇`Public`。 如果存取容器映像需要認證,請選擇`Private`。 有關容器映像是容器映像是`Public``Private`還是 ("公共預覽")的詳細資訊,請參閱[容器儲存庫和影像](../../cognitive-services-container-support.md#container-repositories-and-images)。 |
    |映像名稱|輸入認知服務容器位置。 位置是用作指令參數的內容`docker pull`。 有關可用映像名稱及其儲存儲存函式庫,請參考[容器儲存函式庫及映像](../../cognitive-services-container-support.md#container-repositories-and-images)。<br><br>指定三個部分的圖像名稱必須完全限定。 首先,容器註冊表,然後是儲存庫,最後是映像名稱: `<container-registry>/<repository>/<image-name>`。<br><br>下面是一個範例,`mcr.microsoft.com/azure-cognitive-services/keyphrase`表示 Azure 認知服務儲存庫下的 Microsoft 容器註冊表中的關鍵短語提取映射。 另一個示例`containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`是,它將表示容器預覽容器註冊表的 Microsoft 儲存庫中的「語音到文本」映射。 |
    |OS 類型|`Linux`|
    |大小|將大小變更為特定認知服務容器的建議:<br>2 個 CPU 核心<br>4 GB

3. 在 **「網路」** 選項卡上,輸入以下詳細資訊:

    |設定|值|
    |--|--|
    |連接埠|將 TCP`5000`連接埠 設定為 。 在埠 5000 上公開容器。|

4. 在 **「進階」** 選項卡上,輸入 Azure 容器實體資源的容器計費設定所需的**環境變數**:

    | Key | 值 |
    |--|--|
    |`apikey`|從資源的 **「密鑰」** 頁複製。 它是一個 32 個字母數位字元字串,沒有空格或破`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`折 號。|
    |`billing`|從資源的 **「概述」** 頁複製。|
    |`eula`|`accept`|

5. 按下 **"審閱並創建"**
6. 驗證通過後,按下 **「建立」** 以完成建立過程
7. 成功部署資源後,它已準備就緒
