---
title: 圍繞存儲防火牆設置進行操作
description: 在 Azure HPC 緩存中創建 Azure Blob 存儲目標時，存儲帳戶網路防火牆設置可能會導致失敗。 本文給出了在軟體修復到位之前限制的解決方法。
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/7/2019
ms.author: rohogue
ms.openlocfilehash: 6643662d498db8cbcffcb120a9ceabc46cfc04cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74174404"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>解決 Blob 儲存體帳戶防火牆設定問題

存儲帳戶防火牆中使用的特定設置可能會導致 Blob 存儲目標創建失敗。 Azure HPC 緩存團隊正在處理此問題的軟體修復程式，但您可以按照本文中的說明解決此問題。

僅允許從"選定網路"訪問的防火牆設置可以阻止緩存創建 Blob 存儲目標。 此配置位於存儲帳戶的**防火牆和虛擬網路**設置頁中。

問題是快取服務使用獨立于客戶環境的隱藏服務虛擬網路。 無法顯式授權此網路訪問您的存儲帳戶。

創建 Blob 存儲目標時，快取服務使用此網路檢查容器是否為空。 如果防火牆不允許從隱藏網路進行訪問，則檢查失敗，存儲目標創建失敗。

要解決此問題，請臨時更改防火牆設置，同時創建存儲目標：

1. 轉到存儲帳戶**防火牆和虛擬網路**頁面，並將"允許訪問"設置更改為**所有網路**。
1. 在 Azure HPC 緩存中創建 Blob 存儲目標。
1. 成功創建存儲目標後，將帳戶的防火牆設置更改回 **"選定網路**"。

Azure HPC 緩存不使用服務虛擬網路訪問已完成的存儲目標。

有關此解決方法的説明，[請與 Microsoft 服務和支援 。](hpc-cache-support-ticket.md)
