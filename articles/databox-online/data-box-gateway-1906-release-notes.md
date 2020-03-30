---
title: Azure 資料框閘道& Azure 資料框邊緣 1906 版本資訊*微軟文檔
description: 介紹運行 1906 版本的 Azure 資料框閘道和 Azure 資料框邊緣的關鍵打開問題和解決方法。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 69c905176a5beb1c7d442cd5f1a69161cd520c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099482"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Azure 資料框邊緣和 Azure 資料框閘道 1906 版本資訊

以下版本資訊標識 Azure 資料框邊緣和 Azure 資料框閘道的 1906 版本的關鍵未決問題和已解決的問題。

版本資訊會持續更新，並在發現需要提出因應措施的重大問題時有所增補。 在部署資料框邊緣/資料盒閘道之前，請仔細查看版本資訊中包含的資訊。

此版本對應于軟體版本：

- **資料盒閘道 1906 （1.6.978.743）**
- **資料框邊緣 1906 （1.6.978.743）**

> [!NOTE]
> 更新 1906 只能應用於運行軟體的通用版 （GA） 或 1905 版本的資料盒邊緣設備。

## <a name="whats-new"></a>新功能

- **修復金鑰管理工作流中的 Bug 修復**- 在早期版本中，由於未應用修復金鑰，出現了一個 Bug。 此版本已經修正這個錯誤。 我們強烈建議您應用此更新，因為修復金鑰允許您在設備未啟動的情況下恢復設備上的資料。 有關詳細資訊，請參閱在[部署資料框邊緣或資料框閘道時如何保存修復金鑰](data-box-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device)。
- **現場可程式設計閘陣列 （FPGA） 日誌記錄改進**- 從 1905 年開始發佈，進行了與 FPGA 相關的日誌記錄和警報增強。 如果將 Edge 計算功能與 FPGA 一起使用，則這仍然是資料框邊緣所需的更新。 有關詳細資訊，請參閱如何在[資料框邊緣上使用 Edge 計算轉換資料](data-box-edge-deploy-configure-compute-advanced.md)。

## <a name="known-issues-in-ga-release"></a>GA 版本中的已知問題

此版本未注意到任何新問題。 所有發佈的注意到的問題都從以前的版本中延續過來。 要查看已知問題的清單，請訪問[GA 版本中的已知問題](data-box-gateway-release-notes.md#known-issues-in-ga-release)。


## <a name="next-steps"></a>後續步驟

- [準備部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [準備部署 Azure Data Box Edge](data-box-edge-deploy-prep.md)
