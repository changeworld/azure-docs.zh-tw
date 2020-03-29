---
title: 容器支援
titleSuffix: Azure Cognitive Services
description: 瞭解如何創建 Azure 容器實例資源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 18f4edf5cc63a448779423cc1b302130b4b80724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75692524"
---
## <a name="create-an-azure-container-instance-resource"></a>創建 Azure 容器實例資源

1. 轉到容器實例的["創建](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances)"頁。

2. 在 **"基礎知識"** 選項卡上，輸入以下詳細資訊：

    |設定|值|
    |--|--|
    |訂用帳戶|選取您的訂用帳戶。|
    |資源群組|選擇可用資源組或創建新的資源組，如`cognitive-services`。|
    |容器名稱|輸入名稱，如`cognitive-container-instance`。 名稱必須位於下蓋中。|
    |Location|選擇要部署的區域。|
    |映像類型|如果您的容器映射存儲在不需要憑據的容器註冊表中，請選擇`Public`。 如果訪問容器映射需要憑據，請選擇`Private`。 有關容器映射是容器映射是`Public`還是`Private`（"公共預覽"）的詳細資訊，請參閱[容器存儲庫和圖像](../../cognitive-services-container-support.md#container-repositories-and-images)。 |
    |映像名稱|輸入認知服務容器位置。 位置是用作命令參數的內容`docker pull`。 有關可用映射名稱及其相應的存儲庫，請參閱[容器存儲庫和映射](../../cognitive-services-container-support.md#container-repositories-and-images)。<br><br>指定三個部分的圖像名稱必須完全限定。 首先，容器註冊表，然後是存儲庫，最後是映射名稱： `<container-registry>/<repository>/<image-name>`。<br><br>下面是一個示例，`mcr.microsoft.com/azure-cognitive-services/keyphrase`表示 Azure 認知服務存儲庫下的 Microsoft 容器註冊表中的關鍵短語提取映射。 另一個示例`containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`是，它將表示容器預覽容器註冊表的 Microsoft 存儲庫中的"語音到文本"映射。 |
    |OS 類型|`Linux`|
    |大小|將大小更改為特定認知服務容器的建議：<br>2 個 CPU 內核<br>4 GB

3. 在 **"網路"** 選項卡上，輸入以下詳細資訊：

    |設定|值|
    |--|--|
    |連接埠|將 TCP 埠`5000`設置為 。 在埠 5000 上公開容器。|

4. 在 **"高級"** 選項卡上，輸入 Azure 容器實例資源的容器計費設置所需的**環境變數**：

    | Key | 值 |
    |--|--|
    |`apikey`|從資源的 **"金鑰"** 頁複製。 它是一個 32 個字母數位字元字串，沒有空格或破折`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`號。|
    |`billing`|從資源的 **"概述"** 頁複製。|
    |`eula`|`accept`|

5. 按一下 **"審閱並創建"**
6. 驗證通過後，按一下 **"創建"** 以完成創建過程
7. 成功部署資源後，它已準備就緒
