---
title: 使用入口網站建立 Internet Analyzer 測試 | Microsoft Docs
description: 在此文章中，了解如何建立您的第一個 Internet Analyzer 測試。
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "73509832"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>使用入口網站建立 Internet Analyzer 測試 (預覽)

有兩種方式可以建立 Internet Analyzer 資源，分別是使用 Azure 入口網站或使用 [CLI](internet-analyzer-cli.md)。 此節可協助您使用我們的入口網站體驗，建立新的 Azure Internet Analyzer 資源。

> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="before-you-begin"></a>開始之前

公開預覽可供全球各區域使用，不過，在預覽期間，資料儲存體限制為*美國西部 2*。

## <a name="basics"></a>基本概念

1. 依照 [Azure Internet Analyzer 常見問題集](internet-analyzer-faq.md)中的**如何參與預覽？** 指示，取得 Internet Analyzer 預覽存取。
2. 從 [Azure 入口網站](https://preview.portal.azure.com)中的首頁，按一下 [+ 建立資源]  。 Internet Analyzer 目前僅適用於 Azure 入口網站的預覽版本。
3. 在 [新增]  頁面上，在 [搜尋 Marketplace]  欄位中搜尋「Internet Analyzer」。
4. 按一下 [Internet Analyzer (預覽)]  。 確定發行者為 *Microsoft*，且類別 [網路]  。
5. 在 [Internet Analyzer (預覽)]  頁面上，按一下 [建立]  以開啟 [建立 Internet Analyzer]  頁面。
6. 針對您的 Internet Analyzer 資源，指定下列組態設定：

    * **訂用帳戶︰** 裝載新 Internet Analyzer 資源的 Azure 訂用帳戶。 ***使用要求預覽存取所使用的相同訂用帳戶識別碼。***
    * **資源群組：** 將在其中建立新的 Internet Analyzer 資源的 Azure 資源群組。 如果您沒有現有資源群組，可以建立新的資源群組。
    * **名稱：** 新 Internet Analyzer 資源設定檔的名稱。
    * **區域：** 將在其中建立資源的 Azure 公用區域。 在預覽期間，只有 [美國西部 2]  可供使用。

7. 當您完成指定設定檔設定後，請按一下 [檢閱 + 建立]  。

## <a name="configuration"></a>組態

完成基本步驟是設定測試及內嵌 JavaScript 用戶端的先決條件。 建立設定檔之後，請移至 [設定] > [組態]  以設定您的第一個測試。

1. 在 [測試名稱]  方塊中，為測試指定名稱。
2. 在 [描述]  欄位中，新增測試的描述。
3. 按一下 [設定端點]  ，右側將會出現一個索引標籤。 選取您要設定之端點的類型：單一 Azure 區域、多個 Azure 區域或自訂端點。

    >
    ***預先設定的端點：單一與多個 Azure 區域組合***
    * 從[預先設定的 Azure 端點清單](internet-analyzer-faq.md)中選取一個或一組區域。
    * 接下來，選取您要評估之應用程式或內容傳遞架構的類型。
        * 單一 Azure 區域：網站加速 ([Azure Front Door](https://azure.microsoft.com/services/frontdoor/))、靜態內容快取 ([Azure CDN for Microsoft](https://azure.microsoft.com/services/cdn/)) 或無。
        * 多個 Azure 區域：網站加速 ([Azure Front Door](https://azure.microsoft.com/services/frontdoor/))、DNS 控制 ([Azure 流量管理員](https://azure.microsoft.com/services/traffic-manager/))  

    ***自訂端點***
    * 依照[建立自訂端點](internet-analyzer-custom-endpoint.md)頁面上的指示進行。
    * 在入口網站中貼上單一像素影像的 HTTPS URL 位置。
    >

4. 按一下 [新增]  ，將端點新增到測試中。
5. 重複步驟 1-4 來設定您的第二個端點。 端點 B 一律會相對於端點 A 來測量。設定端點時，請考慮哪個端點應該是您的測試對照組。
6. 按一下 [儲存]  按鈕以儲存測試。 儲存測試之後，您就無法再編輯特定測試的端點。
7. 選取您要啟動的測試，然後按一下 [開始測試]  。 這會將測試的 [狀態] 變更為 [執行中]。 您可以隨時開始測試，但必須內嵌 JavaScript 用戶端，測試才能開始收集測量。
8. 隨時新增更多測試。 請注意，在建立一個測試之前，將不會產生唯一的 JavaScript 用戶端。

## <a name="embed-client"></a>內嵌用戶端

若要開始任何測試，JavaScript 用戶端必須內嵌在您的 Web 應用程式中。 至少設定一個測試之後，按一下 [檢閱 + 建立]  、移至 [設定] > [組態]  ，然後複製 JavaScript 用戶端。 您可以在[內嵌 Internet Analyzer 用戶端](internet-analyzer-embed-client.md)頁面上找到特定的指示。  

## <a name="next-steps"></a>後續步驟

* 閱讀 [Internet Analyzer 常見問題集](internet-analyzer-faq.md)
* 深入了解如何內嵌 [Internet Analyzer 用戶端](internet-analyzer-embed-client.md)以及如何建立[自訂端點](internet-analyzer-custom-endpoint.md)。
