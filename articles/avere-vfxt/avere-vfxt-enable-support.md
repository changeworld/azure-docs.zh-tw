---
title: 啟用對 Avere vFXT 的支援 - Azure
description: 瞭解如何從 Avere vFXT for Azure 啟用自動上傳您叢集的支援資料，以協助支援提供客戶服務。
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: 93b99aa624a21d9312297e4279b1dcf053c79ae3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88272723"
---
# <a name="enable-support-uploads"></a>啟用支援上傳的功能

Avere vFXT for Azure 可以自動上傳有關您叢集的支援資料。 這些上傳項目可讓支援人員提供可能的最佳服務。

## <a name="steps-to-enable-uploads"></a>啟用上傳的步驟

請依照這些來自「Avere 控制台」的步驟來啟用支援。  (Read [vFXT](avere-vfxt-cluster-gui.md) 叢集的存取權，以瞭解如何開啟 [控制台]。 ) 

1. 瀏覽至頂端的 [Settings] \(設定\)**** 索引標籤。
1. 按一下左側的 [Support] \(支援\)**** 連結，然後接受隱私權原則。

   ![顯示 Avere Control Panel 的螢幕擷取畫面，以及具有 [Confirm] \(確認\) 按鈕以接受隱私權原則的快顯視窗](media/avere-vfxt-privacy-policy.png)

1. 在 [支援設定] 頁面上，按一下左側的三角形以開啟 [ **客戶資訊** ] 區段。
1. 按一下 [Revalidate upload information] \(重新驗證上傳資訊\)  按鈕。
1. 將叢集的支援名稱設定為 **唯一**的叢集名稱。 請確定此名稱可唯一識別您的叢集，以支援人員。
1. 勾選 [Statistics Monitoring] \(統計資料監視\)  、[General Information Upload] \(一般資訊上傳\)  及 [Crash Information Upload] \(當機資訊上傳\)  方塊。
1. 按一下 [提交]  。

   ![包含支援設定頁面上已完成的客戶資訊區段的螢幕擷取畫面](media/avere-vfxt-support-info.png)

1. 按一下 [Secure Proactive Support (SPS)] \(安全的主動式支援 (SPS)\)  左側的三角形以展開該區段。
1. 勾選 [Enable SPS Link] \(啟用 SPS 連結\)  方塊。
1. 按一下 [提交]  。

   ![包含支援設定頁面上已完成的安全主動式支援區段的螢幕擷取畫面](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>後續步驟

如果您需要將內部部署或現有的雲端存放裝置系統新增至叢集，請依照 [設定存放裝置](avere-vfxt-add-storage.md)中的指示進行。

如果您已做好開始將用戶端連結至叢集的準備，請參閱[掛接 Avere vFXT 叢集](avere-vfxt-mount-clients.md)。
