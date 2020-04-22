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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681039"
---
1. 在「**建立對等」** 頁上的 **「設定」** 選項卡上,填寫如下所示的框。

    * 對於**對等式**,選擇 **"直接**"。
    * 對於**微軟網路**,請選擇**AS8075**。 不要使用 ASN 8069 創建對等互連。 它保留給特殊應用程序,並且只由[微軟對等互連](mailto:peering@microsoft.com)使用。
    * 選擇**SKU**做**為基本免費**。 不要選擇"免費高級",因為它是為特殊應用程式保留的。
    * 選擇要設置對等互連的**地鐵**位置。

        > [!NOTE]
        > 如果在所選**的 Metro**位置中已與 Microsoft 建立了對等互連連接,並且首次使用 Azure 門戶在該位置設置對等互連,則現有的對等互連連接將列在 **「對等互連連接**」部分中,如圖所示。 Microsoft 將自動將這些對等連接轉換為 Azure 資源,以便您可以在一個位置管理所有這些連接以及新連接。 有關詳細資訊,請參閱[通過使用門戶將舊版直接對等互連轉換為 Azure 資源](../howto-legacy-direct-portal.md)。
        >

1. 在 **"對等互連連接**"下,選擇 **"新建**"以為要設置的每個新連接添加一條線。

    * 要配置或修改連接設置,請選擇線路的編輯按鈕。

        > [!div class="mx-imgBorder"]
        > ![[編輯] 按鈕](../media/setup-direct-conf-tab-edit.png)
    
    * 要刪除列,請選擇 **...** > **刪除**。

        > [!div class="mx-imgBorder"]
        > ![刪除按鈕](../media/setup-direct-conf-tab-delete.png)

    * 您需要提供連接的所有設置,如下所示。

         > [!div class="mx-imgBorder"]
         > ![直接對等連線頁面](../media/setup-direct-conf-tab-connection.png)

        1. 選擇需要設定連線的**對等設施**。
        1. **工作階段位址提供者**用於確定誰提供了在網路和 Microsoft 之間設置 BGP 工作階段所需的子網。 如果可以提供子網路,請選擇**對等項目**。 否則,選擇**Microsoft**和[Microsoft 對等互連](mailto:peering@microsoft.com)將與您聯繫。 選擇此選項需要較長的時間,Microsoft 來處理對等互連請求。 在某些情況下,Microsoft 可能無法提供子網,這將導致請求拒絕。
        1. 如果選擇**工作階段位址提供程式**選項為**對等,** 則分別輸入 IPv4 和 IPv6 位址以及**工作階段 IPv4 首**碼 和前置**碼**的前置碼。
        1. 輸入您將在 **「最大通告的 IPv4 位址」** 和 **「最大通告的 IPv6 位址**」框中通告的 IPv4 和 IPv6 首碼的數量。
        1. 調整**總頻寬**滑塊以反映連接的頻寬。
        1. 選擇 **「儲存」** 以儲存連接設定。

1. 重複上一步,在 Microsoft 與您的網路共存的任何設施中,在之前選擇的**Metro**中添加更多連接。

1. 添加所有必需的連接後,選擇 **「查看 + 創建**」。。

    > [!div class="mx-imgBorder"]
    > ![對等設定選項卡決賽](../media/setup-direct-conf-tab-final.png)

1. 請注意,門戶運行您輸入的資訊的基本驗證。 頂部的功能區顯示消息 *「正在運行最終驗證..."*

    > [!div class="mx-imgBorder"]
    > ![對等驗證選項卡](../media/setup-direct-review-tab-validation.png)

1. 消息更改為*驗證后*,驗證您的資訊。 通過選擇 **「創建**」提交請求。 要修改請求,請選擇 **「上一步」** 並重複這些步驟。

    > [!div class="mx-imgBorder"]
    > ![對等提交](../media/setup-direct-review-tab-submit.png)

1. 提交請求後,等待部署完成。 如果部署失敗,請與[微軟對等互連](mailto:peering@microsoft.com)。 成功部署如下所示。

    > [!div class="mx-imgBorder"]
    > ![窺視成功](../media/setup-direct-success.png)
