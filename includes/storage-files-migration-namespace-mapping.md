---
title: 將資料夾結構對應至 Azure 檔案同步拓撲
description: 將現有的檔案和資料夾結構對應至 Azure 檔案共用，以與 Azure 檔案同步搭配使用。共同的文字區塊，在遷移檔之間共用。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 44fb95de88cc86c802e5ba72f0b5379b8708c506
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209554"
---
在此步驟中，您會評估您需要多少個 Azure 檔案共用。 單一 Windows 伺服器（或叢集）最多可以同步30個 Azure 檔案共用。

您的 StorSimple 上可能會有更多的資料夾，而您目前已在本機上以 SMB 共用的形式與您的使用者和應用程式共用。 最簡單的方式是預見內部部署共用將1:1 對應到 Azure 檔案共用。 如果這個數位是管理 small，在單一 Windows 伺服器上的意義低於30，或您計畫有兩部 Windows 伺服器（60）等等，則建議使用1:1 對應。

如果您的共用數超過30個，通常不需要將內部部署共用1:1 對應到 Azure 檔案共用。
請考慮下列選項：

#### <a name="share-grouping"></a>共用群組

比方說，如果您的人力資源部門總共有15個共用，則您可以考慮將所有 HR 資料儲存在單一 Azure 檔案共用中。 將多個內部部署共用儲存在一個 Azure 檔案共用中，並不會讓您在本機 Windows 伺服器上建立平常的15個 SMB 共用。 這只表示您將這15個共用的根資料夾組織成一個通用資料夾下的子資料夾。 接著，您會將此通用資料夾同步至 Azure 檔案共用。 如此一來，此內部部署共用群組就只需要雲端中的單一 Azure 檔案共用。

#### <a name="volume-sync"></a>磁片區同步

Azure 檔案同步支援將磁片區的根目錄同步至 Azure 檔案共用。
如果您同步處理根資料夾，則所有子資料夾和檔案最後會在相同的 Azure 檔案共用中。

#### <a name="other-best-practices-to-consider"></a>其他應考慮的最佳做法

除了每一伺服器30個 Azure 檔案共用同步處理限制以外，首要考慮是同步處理的效率。

當您的伺服器上有多個共用會同步至自己的 Azure 檔案共用時，同步處理就能以平行方式對全部執行。 尺規向量不是同步處理範圍中所有檔案的大小。 這是需要處理的專案（檔案和資料夾）數目。

單一 Azure 檔案共用最多可以保存 100 TiB。
Azure 檔案同步支援每個 Azure 檔案共用同步處理最多100000000個專案。

同步根磁片區並不一定是最佳的答案。 同步處理多個位置有一些好處，這麼做有助於讓每個同步範圍的專案數降低。 以較少的專案來設定 Azure 檔案同步，並不只是同步處理的重要之處，同時也會受益于從備份進行雲端端還原，以及協助嚴重損壞修復的速度，以防您的伺服器遺失，並布建連接到相同 Azure fil 的新資源e 共用。

使用上述概念的組合，以協助判斷您需要多少個 Azure 檔案共用，而現有 StorSimple 資料的哪些部分將會在哪個 Azure 檔案共用中結束。

建立一份清單來記錄您的想法，讓您可以在下一個步驟中參考它。 這裡的組織非常重要，因為當您一次布建許多資源時，可能會很容易遺失對應計畫的詳細資料。
