---
title: Azure 資料共用的安全性總覽
description: Azure 資料共用的安全性總覽
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: e299f63777847c557fc13afb4d36d01c12b3b52a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84637047"
---
# <a name="security-overview-for-azure-data-share"></a>Azure 資料共用的安全性總覽

本文提供 Azure 資料共用服務的安全性總覽。

## <a name="security-overview"></a>安全性概觀

Azure Data Share 會利用 Azure 提供的基礎安全性來保護待用資料和傳輸中資料。 資料在待用情況下會加密，基礎資料存放區有這方面的支援。 傳輸中的資料也會加密。 資料共用的相關中繼資料也會在待用情況下和傳輸過程中加密。 

您可以在 Azure Data Share 資源層級設定存取控制，以確保資料存取者有獲得授權。 

Azure 資料共用會利用受控識別（先前稱為 MSI）來存取用於資料共用的資料存放區。 資料提供者與資料取用者之間不會交換認證。 如需受控識別的詳細資訊，請參閱[適用于 Azure 資源的受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)識別。 如需共用資料所需之角色和許可權的詳細資訊，請參閱[角色和需求](concepts-roles-permissions.md)。

## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。




