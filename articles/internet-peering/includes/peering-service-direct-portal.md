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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687066"
---
1. 選擇要為 Azure 對等服務啟用的對等互連連接。 然後選擇 **...** > **編輯連線**。
    > [!div class="mx-imgBorder"]
    > ![連接連結](../media/setup-direct-modify-editconnection.png)
1. 在 **"對等服務使用**"下,選擇 **"已啟用",** 然後選擇"**儲存**"。
    > [!div class="mx-imgBorder"]
    > ![對等互連連線啟用對等互連服務](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. 在 **「概述」** 螢幕上,您將看到部署詳細資訊。 部署完成後,選擇 **「轉到資源**」 。
    > [!div class="mx-imgBorder"]
    > ![您的部署已完成](../media/setup-direct-modify-overview-deployment-complete.png)

1. 在 **「已註冊前置字」** 窗格中,選擇 **「添加已註冊前置字**串」 。
    > [!div class="mx-imgBorder"]
    > ![新增已註冊的前置字串](../media/setup-direct-modify-add-registered-prefix.png)
1. 選擇**名稱**與前置前置碼並選擇 **「儲存」** 來註冊前置**字串 。**
    > [!div class="mx-imgBorder"]
    >  ![註冊前置字串](../media/setup-direct-modify-register-a-prefix.png) 

1. 建立前置字尾後,您將在 **「已註冊前置**字串」清單中看到它。 選擇首碼**的名稱**以查看更多詳細資訊。
    > [!div class="mx-imgBorder"]
    > ![已註冊的前置字串和連線](../media/setup-direct-modify-registered-prefixes.png)
1. 在已註冊的前置碼上,您會看到完整的詳細資訊,其中包括每個前置字**元 。** 此密鑰必須提供給從其供應商 ISP 分配此前綴的客戶。 然後,客戶可以使用此密鑰在其訂閱中註冊其首碼。
    > [!div class="mx-imgBorder"]
    > ![帶前置字尾鍵的前置字串](../media/setup-direct-modify-registered-prefix-detail.png)