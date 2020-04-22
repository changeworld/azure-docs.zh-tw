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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678846"
---
1. 在「**建立對等」** 頁上的 **「設定」** 選項卡上,填寫如下所示的框。

    > [!div class="mx-imgBorder"]
    > ![建立對等頁設定選項卡](../media/setup-direct-conf-tab.png)

    * 對於**對等式**,選擇 **"直接**"。
    * 對於**微軟網路**,請選擇**AS8075**。 不要選擇 ASN 8069。 它保留給特殊應用程序,並且只由[微軟對等互連](mailto:peering@microsoft.com)使用。
    * 選擇**SKU**做**為基本免費**。 不要選擇"免費高級",因為它是為特殊應用程式保留的。
    * 選擇要將對等互連轉換為 Azure 資源的**Metro**位置。 如果在選擇的**Metro**位置中具有未轉換為 Azure 資源的對等連接,則這些連接將列在 **「對等連接**」部分中,如圖所示。 現在,您可以將這些對等連接轉換為 Azure 資源。

        > [!div class="mx-imgBorder"]
        > ![對等互連連線清單](../media/setup-directlegacy-conf-tab.png)

1. 如果需要更新頻寬,請選擇線路的編輯按鈕以修改連接設置。

    > [!div class="mx-imgBorder"]
    > ![[編輯] 按鈕](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > 如果要在所選**的 Metro**位置添加與 Microsoft 的其他對等互連連接,請選擇「**創建新**」。 有關詳細資訊,請參閱[使用門戶 創建或修改直接對等互連](../howto-direct-portal.md)。
    >

1. 選取 [檢閱 + 建立]  。 請注意,門戶運行您輸入的資訊的基本驗證。 頂部的功能區顯示消息 *「正在運行最終驗證..."*

    > [!div class="mx-imgBorder"]
    > ![對等驗證選項卡](../media/setup-direct-review-tab-validation.png)

1. 消息更改為*驗證后*,驗證您的資訊。 通過選擇 **「創建**」提交請求。 要修改請求,請選擇 **「上一步」** 並重複這些步驟。

    > [!div class="mx-imgBorder"]
    > ![對等提交](../media/setup-direct-review-tab-submit.png)

1. 提交請求後,等待部署完成。 如果部署失敗,請與[微軟對等互連](mailto:peering@microsoft.com)。 成功部署如下所示。

    > [!div class="mx-imgBorder"]
    > ![窺視成功](../media/setup-direct-success.png)
