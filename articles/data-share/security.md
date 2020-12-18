---
title: Azure Data Share 的安全性概觀
description: Azure Data Share 的安全性概觀
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: 4e62645dd5a7a8336df4fccf12daebc730a91168
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678423"
---
# <a name="security-overview-for-azure-data-share"></a>Azure Data Share 的安全性概觀

本文提供 Azure Data Share 服務的安全性總覽。

## <a name="security-overview"></a>安全性概觀

Azure Data Share 會利用 Azure 提供的基礎安全性來保護待用資料和傳輸中資料。 資料在待用情況下會加密，基礎資料存放區有這方面的支援。 資料在傳輸期間也會使用 TLS 1.2 進行加密。 資料共用的相關中繼資料也會在待用情況下和傳輸過程中加密。 Azure Data Share 不會儲存正在共用之客戶資料的內容。

Azure Data Share 會利用受控識別 (之前稱為 MSI) ，來存取用於資料共用的資料存放區。 資料提供者與資料取用者之間不會交換認證。 如需受控識別的詳細資訊，請參閱 [適用于 Azure 資源的受控](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)識別。 如需共用資料所需的角色和許可權的詳細資訊，請參閱 [角色和需求](concepts-roles-permissions.md)。

## <a name="access-control"></a>存取控制

您可以在 Data Share 資源層級上設定 Azure Data Share 的存取控制，以確保已獲授權的存取權。 Data Share 資源的擁有者和參與者可以共用資料、接收共用，以及對現有的共用進行變更。 Data Share 資源的讀者可以查看共用，但無法進行變更。 

建立或接收共用之後，具有 Data Share 資源之適當許可權的使用者就可以進行變更。 當建立或接收共用的使用者離開組織時，不會終止共用或停止資料流程。 具有 Data Share 資源適當許可權的其他使用者可以繼續管理共用。

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>從已啟用防火牆的資料存放區共用資料或共用資料
若要從已開啟防火牆的儲存體帳戶共用資料，您必須啟用儲存體帳戶中的 [ **允許信任的 Microsoft 服務** ]。 如需詳細資訊，請參閱 [設定 Azure 儲存體防火牆和虛擬網路](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) 。


## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。
