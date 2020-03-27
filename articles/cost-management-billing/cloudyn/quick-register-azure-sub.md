---
title: 向 Cloudyn 註冊您的 Azure 訂用帳戶
description: 本快速入門詳細說明建立 Cloudyn 試用訂用帳戶並登入 Cloudyn 入口網站所需的註冊程序。
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: f3b529d3dbe0fe41fb13fabfe32dfad208ef5012
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474623"
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>註冊個人 Azure 訂用帳戶並檢視成本資料

您可使用您的 Azure 訂用帳戶向 Cloudyn 進行註冊。 註冊將可提供 Cloudyn 入口網站的存取權。 本快速入門詳細說明建立 Cloudyn 試用訂用帳戶並登入 Cloudyn 入口網站所需的註冊程序。 它也會示範如何立即開始檢視成本資料。

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="sign-in-to-azure"></a>登入 Azure

- 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="register-with-cloudyn"></a>向 Cloudyn 註冊

1. 在 Azure 入口網站中，按一下服務清單中的 [成本管理與帳單]  。
2. 在 [概觀]  下，按一下 [Cloudyn]   
    ![在 Azure 入口網站中顯示的 Cloudyn 頁面](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. 在 [成本管理]  頁面上，按一下 [移至 Cloudyn]  以在新視窗開啟 Cloudyn 註冊頁面。
4. 在 Cloudyn 入口網站試用註冊頁面上，輸入您的公司名稱並選取 [Azure 個人訂用帳戶擁有者]  ，然後按一下 [下一步]  。 您的帳戶名稱和租用戶識別碼便會自動加入表單。  
    ![輸入註冊資訊所在的試用版註冊頁面](./media/quick-register-azure-sub/trial-reg-ind.png)
5. 選取與您的訂用帳戶相關聯的 [供應項目識別碼 - 名稱]  。 如果不確定訂用帳戶的「費率識別碼」，可以檢視您的 Azure 帳單，並尋找 [供應項目識別碼]  。
6. 同意使用條款，然後驗證您的資訊並按一下 [下一步]  。
7. 在 [收集其他資料]  頁面中，按一下 [下一步]  以授權 Cloudyn 收集 Azure 資源資料。 收集的資料包括您訂用帳戶的使用量、效能、計費和標籤資料。  
    ![收集授權 Cloudyn 所在的其他資料頁面](./media/quick-register-azure-sub/gather-additional.png)
8. 您的瀏覽器會進入 Cloudyn 的登入頁面。 使用您的 Azure 訂用帳戶認證來登入。
9. 按一下 [移至 Cloudyn]  以開啟 Cloudyn 入口網站，然後就會在 [帳戶管理]  頁面上看見您的 Azure 訂用帳戶資訊。  
    ![顯示 Azure 訂用帳戶資訊的 [帳戶管理] 頁面](./media/quick-register-azure-sub/accounts-mgt.png)

若要觀看有關註冊 Azure 訂用帳戶的教學課程影片，請觀看[使用 Cloudyn 尋找您的 Directory GUID 和費率識別碼](https://youtu.be/PaRjnyaNGMI)。

[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>後續步驟

在此快速入門中，您使用 Azure 訂用帳戶資訊向 Cloudyn 進行註冊。 您也登入了 Cloudyn 入口網站，並開始檢視成本資料。 若要深入了解 Cloudyn，請繼續 Cloudyn 的教學課程。

> [!div class="nextstepaction"]
> [檢閱使用量和成本](tutorial-review-usage.md)
