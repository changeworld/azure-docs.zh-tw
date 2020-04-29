---
title: 在 Azure 儀表板上使用自訂 Markdown 磚
description: 了解如何將 Markdown 磚新增至 Azure 儀表板以顯示靜態內容
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/08/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 74102423461a56bb6fc19c2eb9874f96a76e34e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76310708"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>在 Azure 儀表板上使用 Markdown 磚以顯示自訂內容

您可以將 Markdown 磚新增至 Azure 儀表板以顯示自訂的靜態內容。 例如，您可以在 markdown 磚上顯示基本指示、影像或一組超連結。

## <a name="add-a-markdown-tile-to-your-dashboard"></a>將 Markdown 磚新增至您的儀表板

1. 從 Azure 入口網站資訊看板選取 [儀表板]****。

   ![顯示入口網站提要欄位的螢幕擷取畫面](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. 如果您已建立任何自訂儀表板，請在儀表板檢視中，使用下拉式清單來選取應該出現自訂 Markdown 磚的儀表板。 選取 [編輯] 圖示以開啟 [磚庫]****。

   ![顯示儀表板編輯檢視的螢幕擷取畫面](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. 在 [**磚庫**] 中，找出名為**Markdown**的磚，然後選取 [**新增**]。 磚會新增至儀表板，且 [編輯 Markdown]**** 窗格隨即開啟。

1. 輸入 [**標題**] 和 [**副標題**] 的值，這會在您移至另一個欄位之後顯示在磚上。

   ![顯示輸入標題和副標題之結果的螢幕擷取畫面](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. 選取其中一個選項來包含 markdown 內容： [**內嵌編輯**] 或 [**使用 URL 插入內容**]。

   - 如果您想要直接輸入 markdown，請選取 [**內嵌編輯**]。

      ![顯示輸入內嵌內容的螢幕擷取畫面](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - 如果您想要使用線上託管的現有 markdown 內容，請選取 [**使用 URL 插入內容**]。

      ![顯示輸入 URL 的螢幕擷取畫面](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > 為了增加安全性，您可以建立 markdown 檔案，並將它儲存在[已啟用加密的 Azure 儲存體帳戶 blob](../storage/common/storage-service-encryption.md)中，然後使用 [URL] 選項指向該檔案。 Markdown 內容會透過儲存體帳戶的加密選項進行加密。 只有具備檔案許可權的使用者才能在儀表板上看到 markdown 內容。

1. 選取 [完成]**** 以關閉 [編輯 Markdown]**** 窗格。 您的內容會出現在 [Markdown] 圖格上，您可以拖曳右下角的控點來調整大小。

   ![顯示自訂 Markdown 磚的螢幕擷取畫面](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Markdown 內容功能和限制

您可以在 Markdown 磚上使用純文字、Markdown 語法及 HTML 內容的任意組合。 Azure 入口網站會使用名為 _marked_ 的開放原始碼程式庫，將您的內容轉換成磚上顯示的 HTML。 _marked_ 所產生的 HTML 會由入口網站進行前處理後才顯示。 此步驟有助於確保您的自訂不會影響入口網站的安全性或版面配置。 在進行該前處理的期間，系統會移除可能造成威脅的任何 HTML 部分。 以下是入口網站不允許的內容類型：

* JavaScript – 將會移除 `<script>` 標籤和內嵌的 JavaScript 評估。
* iframe - 將會移除 `<iframe>` 標籤。
* Style - 將會移除 `<style>` 標籤。 HTML 元素上的內嵌樣式屬性並未受到正式支援。 您可能會發現有些內嵌樣式元素對您而言可以運作，但如果它們干擾到入口網站的版面配置，則可能隨時停止運作。 Markdown 磚適用於使用入口網站預設樣式的基本、靜態內容。

## <a name="next-steps"></a>後續步驟

* 若要建立自訂儀表板，請參閱[在 Azure 入口網站中建立和共用儀表板](../azure-portal/azure-portal-dashboards.md)
