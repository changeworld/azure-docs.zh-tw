---
title: 使用入口網站將對等 ASN 與 Azure 訂用帳戶互連
titleSuffix: Azure
description: 使用入口網站將對等 ASN 與 Azure 訂用帳戶互連
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: cee548aff49cd5e4a57eed994b8ade2d157c6313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912174"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>使用入口網站將對等 ASN 與 Azure 訂用帳戶互連

在提交對等請求之前，應首先使用以下步驟將 ASN 與 Azure 訂閱相關聯。

如果您願意，您可以使用[PowerShell](howto-subscription-association-powershell.md)完成本指南。

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>創建對等 Asn 以將 ASN 與 Azure 訂閱關聯

### <a name="sign-in-to-the-portal"></a>登錄到門戶
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>註冊對等資來源提供者
按照以下步驟註冊訂閱中的對等資來源提供者。 如果不執行此操作，則無法訪問設置對等互連所需的 Azure 資源。

1. 按一下門戶左上角的**訂閱**。 如果您沒有看到它，請按一下 **"更多服務"** 並搜索它。

    > [!div class="mx-imgBorder"]
    > ![打開訂閱](./media/rp-subscriptions-open.png)

1. 按一下要用於對等互連的訂閱。

    > [!div class="mx-imgBorder"]
    > ![啟動訂閱](./media/rp-subscriptions-launch.png)

1. 訂閱打開後，在左側，按一下**資來源提供者**。 然後，在右側窗格中，在搜索視窗中搜索*對等互連*，或使用滾動欄查找**Status****Microsoft。** 如果狀態為 ***"已註冊"，*** 請跳過以下步驟，然後繼續創建**對等 Asn**節。 如果狀態為 ***"未註冊***"，請選擇 **"Microsoft.對等"，** 然後按一下"**註冊**"。

    > [!div class="mx-imgBorder"]
    > ![註冊開始](./media/rp-register-start.png)

1. 觀察狀態更改為***註冊***。

    > [!div class="mx-imgBorder"]
    > ![註冊正在進行中](./media/rp-register-progress.png)

1. 等待一分鐘左右，等待它完成註冊。 然後，按一下 **"刷新"** 並驗證狀態是否***已註冊***。

    > [!div class="mx-imgBorder"]
    > ![註冊完成](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>創建對等 Asn
您可以創建用於將自治系統號 （ASN） 與 Azure 訂閱關聯的新的 PeerAsn 資源。 您可以通過為需要關聯的每個 ASN 創建一個**PeerAsn**將多個 ASN 與訂閱相關聯。

1. 按一下 **"創建資源** > **查看所有**"。

    > [!div class="mx-imgBorder"]
    > ![搜索對等 Asn](./media/peerasn-seeall.png)

1. 在搜索框中搜索*PeerAsn，* 然後按鍵盤上的 *"輸入*"。 從結果中，按一下**PeerAsn**資源。

    > [!div class="mx-imgBorder"]
    > ![啟動對等 Asn](./media/peerasn-launch.png)

1. 啟動**對等操作後**，按一下"**創建**"。

    > [!div class="mx-imgBorder"]
    > ![創建對等 Asn](./media/peerasn-create.png)

1. 在 **"關聯對等 ASN"** 頁上，在 **"基本"** 選項卡下填寫欄位，如下所示。

    > [!div class="mx-imgBorder"]
    > ![對等 Asn 基礎知識選項卡](./media/peerasn-basics-tab.png)

    * **名稱**對應于資源名稱，可以是您選擇的任何名稱。  
    * 選擇關聯 ASN 所需的**訂閱**。
    * **對等名稱**對應于公司的名稱，並且需要盡可能接近您的對等資料庫設定檔。 請注意，該值僅支援字元 a-z、A-Z 和空格
    * 在 **"對等 ASN"欄位中輸入 ASN。**
    * 按一下 **"創建新**"並輸入網路運營中心 （NOC）**的電子郵件地址**和**電話號碼**
1. 然後，按一下 **"審閱 + 創建**"，並觀察門戶對輸入的資訊進行基本驗證。 這顯示在頂部的功能區中，如*正在運行最終驗證...*

    > [!div class="mx-imgBorder"]
    > ![對等 Asn 審閱選項卡](./media/peerasn-review-tab-validation.png)

1. 功能區中的消息變為 *"驗證已通過"* 後，請驗證您的資訊，並通過按一下"**創建**"提交請求。 如果驗證未通過，請按一下 **"上一步"** 並重複上述步驟以修改請求並確保輸入的值沒有錯誤。

    > [!div class="mx-imgBorder"]
    > ![對等 Asn 審閱選項卡](./media/peerasn-review-tab.png)

1. 提交請求後，請等待它完成部署。 如果部署失敗，請與[微軟對等互連](mailto:peering@microsoft.com)。 成功部署將顯示如下。

    > [!div class="mx-imgBorder"]
    > ![同行成功](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>查看對等 Asn 的狀態
成功部署 PeerAsn 資源後，您需要等待 Microsoft 批准關聯請求。 可能需要長達 12 小時才能獲得批准。 獲得批准後，您將收到上述部分中輸入的電子郵件地址的通知。

> [!IMPORTANT]
> 等待驗證狀態在提交對等請求之前將"已批准"變為"已批准"。 此批准可能需要長達 12 小時。

## <a name="modify-peerasn"></a>修改對等Asn
當前不支援修改對等 Asn。 如果需要修改，請與[Microsoft 對等互連](mailto:peering@microsoft.com)。

## <a name="delete-peerasn"></a>刪除對等 Asn
當前不支援刪除對等 Asn。 如果需要刪除對等器，請與[微軟對等互連](mailto:peering@microsoft.com)。

## <a name="next-steps"></a>後續步驟

* [使用入口網站建立或修改直接對等互連](howto-direct-portal.md)
* [使用入口網站將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-portal.md)
* [使用門戶創建或修改 Exchange 對等互連](howto-exchange-portal.md)
* [使用入口網站將舊版 Exchange 對等互連轉換成 Azure 資源](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>其他資源

有關詳細資訊，請訪問[互聯網對等常見問題解答](faqs.md)