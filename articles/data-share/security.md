---
title: Azure Data Share 的安全性概觀
description: Azure Data Share 的安全性概觀
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 06/05/2020
ms.openlocfilehash: 10f31b74b461941b15f13e45f90b5fbc408c90fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86108408"
---
# <a name="security-overview-for-azure-data-share"></a>Azure Data Share 的安全性概觀

本文提供 Azure Data Share 服務的安全性總覽。

## <a name="security-overview"></a>安全性概觀

Azure Data Share 會利用 Azure 提供的基礎安全性來保護待用資料和傳輸中資料。 資料在待用情況下會加密，基礎資料存放區有這方面的支援。 傳輸中的資料也會加密。 資料共用的相關中繼資料也會在待用情況下和傳輸過程中加密。 

您可以在 Azure Data Share 資源層級設定存取控制，以確保資料存取者有獲得授權。 

Azure Data Share 會利用受控識別 (之前稱為 MSI) ，來存取用於資料共用的資料存放區。 資料提供者與資料取用者之間不會交換認證。 如需受控識別的詳細資訊，請參閱 [適用于 Azure 資源的受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)識別。 如需共用資料所需的角色和許可權的詳細資訊，請參閱 [角色和需求](concepts-roles-permissions.md)。

## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。




