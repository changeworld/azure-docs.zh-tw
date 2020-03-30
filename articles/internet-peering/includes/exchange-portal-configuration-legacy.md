---
title: 包含檔案
titleSuffix: Azure
description: 包含檔案
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 7d7b9f847cdcc4ab4b1ff065425eebe07fb4d888
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775208"
---
1. 在"**創建對等"** 頁上，**在"配置"** 選項卡下填寫欄位，如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等配置 - 交換](../media/setup-exchange-conf-tab.png)

    * 對於**對等式**，選擇 *"交換*"。
    * 選擇**SKU**作為*基本免費*。
    * 選擇要將對等互連轉換為 Azure 資源的 **"地鐵"** 位置。 如果在選定的**Metro**位置中具有未轉換為 Azure 資源的對等互連連接，則此類連接將顯示在 **"對等互連連接**"部分中，如下所示。 現在，您可以將這些對等互連連接轉換為 Azure 資源。

        > [!div class="mx-imgBorder"]
        > ![對等配置 - 交換 - 舊連接](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > 不能修改舊對等互連連接的設置。 如果要在所選**的 Metro**位置添加與 Microsoft 的其他對等互連連接，可以通過按一下"**創建新**按鈕"來執行此操作。 有關詳細資訊[，請參閱使用門戶創建或修改 Exchange 對等互連](../howto-exchange-portal.md)。
        >

1. 按一下 **"審閱 + 創建**"。 請注意，門戶運行您輸入的資訊的基本驗證。 這顯示在頂部的功能區中，如*正在運行最終驗證...*

    > [!div class="mx-imgBorder"]
    > ![對等驗證選項卡](../media/setup-direct-review-tab-validation.png)

1. 在它變為*驗證通過*後，驗證您的資訊，並通過按一下"**創建**"提交請求。 如果需要修改請求，請按一下 **"上一步"** 並重複上述步驟。

    > [!div class="mx-imgBorder"]
    > ![對等提交](../media/setup-exchange-review-tab-submit.png)

1. 提交請求後，請等待它完成部署。 如果部署失敗，請與[微軟對等互連](mailto:peering@microsoft.com)。 成功部署將顯示如下。

    > [!div class="mx-imgBorder"]
    > ![窺視成功](../media/setup-direct-success.png)
