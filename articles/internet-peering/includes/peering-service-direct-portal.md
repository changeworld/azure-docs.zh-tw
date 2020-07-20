---
title: 包含檔案
titleSuffix: Azure
description: 包含檔案
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81687066"
---
1. 選取您想要為 Azure 對等服務啟用的對等互連連線。 然後選取 [ **...**  >  ]**編輯連接**。
    > [!div class="mx-imgBorder"]
    > ![對等互連連線編輯連接](../media/setup-direct-modify-editconnection.png)
1. 在 [**用於對等互連服務**] 底下選取 [**已啟用**]，然後選取 [**儲存**]。
    > [!div class="mx-imgBorder"]
    > ![對等互連連接啟用對等互連服務](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. 在 [**總覽**] 畫面上，您會看到部署詳細資料。 部署完成之後，請選取 [**移至資源**]。
    > [!div class="mx-imgBorder"]
    > ![您的部署已完成](../media/setup-direct-modify-overview-deployment-complete.png)

1. 在 [**註冊的首碼**] 窗格中，選取 [**新增已註冊的前置**詞]。
    > [!div class="mx-imgBorder"]
    > ![新增已註冊的前置詞](../media/setup-direct-modify-add-registered-prefix.png)
1. 選取**名稱**和**前置**詞，然後選取 [**儲存**]，以註冊前置詞。
    > [!div class="mx-imgBorder"]
    >  ![註冊前置詞](../media/setup-direct-modify-register-a-prefix.png) 

1. 建立前置詞之後，您會在**已註冊的首碼**清單中看到它。 選取前置詞的**名稱**以查看更多詳細資料。
    > [!div class="mx-imgBorder"]
    > ![已註冊的首碼和連接](../media/setup-direct-modify-registered-prefixes.png)
1. 在 [註冊的首碼] 頁面上，您會看到完整的詳細資料，其中包含每個前置詞的**首碼索引鍵**。 此金鑰必須提供給從其提供者 ISP 配置此首碼的客戶。 客戶可以使用此金鑰，在其訂用帳戶內註冊其前置詞。
    > [!div class="mx-imgBorder"]
    > ![前置詞索引鍵](../media/setup-direct-modify-registered-prefix-detail.png)