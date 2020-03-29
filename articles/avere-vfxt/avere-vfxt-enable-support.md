---
title: 啟用對 Avere vFXT 的支援 - Azure
description: 如何啟用支援從「適用於 Azure 的 Avere vFXT」上傳的功能
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: d12bbd1708ceb948aea982f9ed1ab36879e3751c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75415358"
---
# <a name="enable-support-uploads"></a>啟用支援上傳的功能

Azure 的 Avere vFXT 可以自動上載有關群集的支援資料。 這些上傳項目可讓支援人員提供可能的最佳服務。

## <a name="steps-to-enable-uploads"></a>啟用上傳的步驟

請依照這些來自「Avere 控制台」的步驟來啟用支援。 （讀取[訪問 vFXT 群集](avere-vfxt-cluster-gui.md)以瞭解如何打開控制台。

1. 瀏覽至頂端的 [Settings] \(設定\)**** 索引標籤。
1. 按一下左側的 [Support] \(支援\)**** 連結，然後接受隱私權原則。

   ![顯示 Avere Control Panel 的螢幕擷取畫面，以及具有 [Confirm] \(確認\) 按鈕以接受隱私權原則的快顯視窗](media/avere-vfxt-privacy-policy.png)

1. 在支援配置頁上，通過按一下左側的三角形打開 **"客戶資訊**"部分。
1. 按一下 [Revalidate upload information] \(重新驗證上傳資訊\)**** 按鈕。
1. 在**唯一群集名稱**中設置群集的支援名稱。 確保此名稱唯一標識群集以支援員工。
1. 勾選 [Statistics Monitoring] \(統計資料監視\)****、[General Information Upload] \(一般資訊上傳\)**** 及 [Crash Information Upload] \(當機資訊上傳\)**** 方塊。
1. 按一下 [提交]****。

   ![包含支援設定頁面上已完成的客戶資訊區段的螢幕擷取畫面](media/avere-vfxt-support-info.png)

1. 按一下 [Secure Proactive Support (SPS)] \(安全的主動式支援 (SPS)\)**** 左側的三角形以展開該區段。
1. 勾選 [Enable SPS Link] \(啟用 SPS 連結\)**** 方塊。
1. 按一下 [提交]****。

   ![包含支援設定頁面上已完成的安全主動式支援區段的螢幕擷取畫面](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>後續步驟

如果需要向群集添加本地或現有雲存儲系統，請按照[配置存儲](avere-vfxt-add-storage.md)中的說明操作。

如果您已做好開始將用戶端連結至叢集的準備，請參閱[掛接 Avere vFXT 叢集](avere-vfxt-mount-clients.md)。
