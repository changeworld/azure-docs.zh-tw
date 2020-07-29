---
title: 資料 & 儲存體建議-Azure 資訊安全中心
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75552860"
---
# <a name="protect-azure-data-and-storage-services"></a>保護 Azure 資料和儲存體服務
當 Azure 資訊安全中心識別潛在的安全性弱點時，它會建立建議，引導您完成設定所需控制項來強化和保護資源的程式。

本文說明資訊安全中心的 [資源安全性] 區段中的 [**資料安全性] 頁面**。

如需您可能會在此頁面上看到之建議的完整清單，請參閱[資料和儲存體建議](recommendations-reference.md#recs-datastorage)。


## <a name="view-your-data-security-information"></a>查看您的資料安全性資訊

1. 在 [**資源安全性**防護] 區段中，按一下 [**資料和儲存體資源**]。

    ![資料 & 儲存體資源](./media/security-center-monitoring/click-data.png)

    [**資料安全性**] 頁面隨即開啟，並提供資料資源的建議。

    [![資料資源](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    您可以從這個頁面執行下列動作：

    * 按一下 [**總覽**] 索引標籤，列出要補救的所有資料資源建議。 
    * 按一下每個索引標籤，並依資源類型查看建議。

    > [!NOTE]
    > 如需儲存體加密的詳細資訊，請參閱待用[資料的加密 Azure 儲存體](../storage/common/storage-service-encryption.md)。


## <a name="remediate-a-recommendation-on-a-data-resource"></a>補救資料資源的建議

1. 從任何 [資源] 索引標籤中，按一下資源。 [資訊] 頁面隨即開啟，列出要補救的建議。

    ![資源資訊](./media/security-center-monitoring/sql-recommendations.png)

2. 按一下 [建議]。 [建議] 頁面隨即開啟，並顯示執行建議的**補救步驟**。

   ![補救步驟](./media/security-center-monitoring/remediate1.png)

3. 按一下 [**採取動作**]。 [資源設定] 頁面隨即出現。

    ![啟用建議](./media/security-center-monitoring/remediate2.png)

4. 遵循**補救步驟**，然後按一下 [**儲存**]。


## <a name="next-steps"></a>後續步驟

若要深入瞭解適用于其他 Azure 資源類型的建議，請參閱下列主題：

* [Azure 資訊安全中心安全性建議的完整參考清單](recommendations-reference.md)
* [保護 Azure 資訊安全中心內的機器和應用程式](security-center-virtual-machine-protection.md)
* [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)