---
title: 資料&存儲建議 - Azure 安全中心
description: 本文件說明「Azure 資訊安全中心」中的建議，這些建議可協助您保護您的資料和 Azure SQL 服務，以及遵守安全性原則。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 74ed55e1d460495bfa8d3d4c00bd37bb7f05260e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552860"
---
# <a name="protect-azure-data-and-storage-services"></a>保護 Azure 資料和存儲服務
當 Azure 安全中心識別潛在的安全性漏洞時，它會創建建議，指導您完成配置所需控制項以強化和保護資源的過程。

本文介紹了安全中心資源安全部分**的資料安全頁面**。

有關您可能在此頁上看到的建議的完整清單，請參閱[資料和存儲建議](recommendations-reference.md#recs-datastorage)。


## <a name="view-your-data-security-information"></a>查看資料安全資訊

1. 在 **"資源安全衛生**"部分中，按一下 **"資料和存儲資源**"。

    ![資料&存儲資源](./media/security-center-monitoring/click-data.png)

    "**資料安全**"頁將打開，並包含資料資源的建議。

    [![資料資源](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    您可以從這個頁面執行下列動作：

    * 按一下"**概述"** 選項卡列出要修復的所有資料資源建議。 
    * 按一下每個選項卡，並按資源類型查看建議。

    > [!NOTE]
    > 有關存儲加密的詳細資訊，請參閱[靜態資料的 Azure 存儲加密](../storage/common/storage-service-encryption.md)。


## <a name="remediate-a-recommendation-on-a-data-resource"></a>修正對資料資源的建議

1. 從任何資源選項卡中，按一下資源。 將打開資訊頁，列出要修復的建議。

    ![資源資訊](./media/security-center-monitoring/sql-recommendations.png)

2. 按一下建議。 "建議"頁將打開並顯示實施建議的 **"修正"步驟**。

   ![補救步驟](./media/security-center-monitoring/remediate1.png)

3. 按一下 **"執行操作**"。 將顯示資源設置頁。

    ![啟用建議](./media/security-center-monitoring/remediate2.png)

4. 按照**修復步驟**，然後按一下"**保存**"。


## <a name="next-steps"></a>後續步驟

要瞭解有關適用于其他 Azure 資源類型的建議，請參閱以下主題：

* [Azure 安全中心安全建議的完整參考清單](recommendations-reference.md)
* [保護 Azure 資訊安全中心內的機器和應用程式](security-center-virtual-machine-protection.md)
* [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)