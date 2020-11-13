---
title: Azure Data Share 的安全性概觀
description: Azure Data Share 的安全性概觀
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 1fdf026e9271ef6eb30c2b4ca96a04880b65be75
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578088"
---
# <a name="security-overview-for-azure-data-share"></a>Azure Data Share 的安全性概觀

本文提供 Azure Data Share 服務的安全性總覽。

## <a name="security-overview"></a>安全性概觀

Azure Data Share 會利用 Azure 提供的基礎安全性來保護待用資料和傳輸中資料。 資料在待用情況下會加密，基礎資料存放區有這方面的支援。 資料在傳輸期間也會使用 TLS 1.2 進行加密。 資料共用的相關中繼資料也會在待用情況下和傳輸過程中加密。 Azure Data Share 不會儲存正在共用之客戶資料的內容。

Azure Data Share 會利用受控識別 (之前稱為 MSI) ，來存取用於資料共用的資料存放區。 資料提供者與資料取用者之間不會交換認證。 如需受控識別的詳細資訊，請參閱 [適用于 Azure 資源的受控](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)識別。 如需共用資料所需的角色和許可權的詳細資訊，請參閱 [角色和需求](concepts-roles-permissions.md)。

您可以在 Data Share 資源層級上設定 Azure Data Share 的存取控制，以確保已獲授權的存取權。 

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>從已啟用防火牆的資料存放區共用資料或共用資料
若要從已開啟防火牆的儲存體帳戶共用資料，您必須啟用儲存體帳戶中的 [ **允許信任的 Microsoft 服務** ]。 如需詳細資訊，請參閱 [設定 Azure 儲存體防火牆和虛擬網路](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) 。


## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。
