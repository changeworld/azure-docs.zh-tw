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
ms.openlocfilehash: 60752cf1b3c05ab7817083e70310ba7b40227dec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129870"
---
1. 按一下要為對等互連服務啟用的對等互連連接，然後按一下 **...** > **編輯連接**按鈕。
    > [!div class="mx-imgBorder"]
    > ![對等連接編輯](../media/setup-direct-modify-editconnection.png)
1. 在"***對等服務使用"*** 一節中，按一下"**已啟用"** 並**保存**。
    > [!div class="mx-imgBorder"]
    > ![對等互連連接啟用對等互連服務](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. 在"概述"螢幕中，您將看到部署詳細資訊。 部署完成後，按一下"**轉到資源**"。
    > [!div class="mx-imgBorder"]
    > ![您的部署已完成](../media/setup-direct-modify-overview-deployment-complete.png)

1. 然後，您將在"設置**已註冊首碼"** 下看到 。 按一下 **"添加已註冊首碼**"。
    > [!div class="mx-imgBorder"]
    > ![已註冊的首碼和連接](../media/setup-direct-modify-add-registered-prefix.png)
1. 通過選擇**名稱**和首碼註冊前**綴**，然後按一下"**保存"**
    > [!div class="mx-imgBorder"]
    >  ![註冊首碼](../media/setup-direct-modify-register-a-prefix.png) 

1. 創建首碼後，您將在"已註冊首碼"清單中看到它。 按一下首碼**的名稱**以查看更多詳細資訊。
    > [!div class="mx-imgBorder"]
    > ![已註冊的首碼和連接](../media/setup-direct-modify-registered-prefixes.png)
1. 在已註冊的首碼頁中，您將看到包含每個**首碼的首碼鍵**的完整詳細資訊。 此金鑰需要提供給從其供應商 ISP 分配此首碼的客戶。 然後，客戶可以使用此金鑰在其訂閱中註冊其首碼。
    > [!div class="mx-imgBorder"]
    > ![帶首碼鍵的首碼](../media/setup-direct-modify-registered-prefix-detail.png)