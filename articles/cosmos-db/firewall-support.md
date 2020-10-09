---
title: Azure Cosmos 帳戶的 IP 防火牆
description: 了解如何將 IP 存取控制原則使用於防火牆支援，以保護 Azure Cosmos DB 資料庫。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "66241086"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>Azure Cosmos DB 的 IP 防火牆

為了保護您帳戶中所儲存的資料，Azure Cosmos DB 支援利用強式雜湊式訊息驗證碼 (HMAC) 的祕密型授權模型。 此外，Azure Cosmos DB 支援 IP 型存取控制，以提供輸入防火牆支援。 此模型與傳統資料庫系統的防火牆規則相類似，並可為您的帳戶提供額外的安全性層級。 有了防火牆，您可以設定只能從一組核准的電腦和 (或) 雲端服務存取 Azure Cosmos 帳戶。 經由這些核准的電腦和服務組合來存取 Azure Cosmos 資料庫中儲存的資料，仍然需要呼叫者出示有效的授權權杖。

## <a name="ip-access-control-overview"></a><a id="ip-access-control-overview"></a>IP 存取控制概觀

根據預設，只要要求伴隨有效的授權權杖，就可以從網際網路存取 Azure Cosmos 帳戶。 若要設定 IP 原則型存取控制，使用者必須以 CIDR (無類別網域間路由) 形式提供這組 IP 位址或 IP 位址範圍，以作為允許存取指定 Azure Cosmos 帳戶的用戶端 IP 清單。 套用此組態之後，任何源自此允許清單外部機器的要求都會收到 403 (禁止) 回應。 使用 IP 防火牆時，建議允許 Azure 入口網站存取您的帳戶。 需具備存取權，才能使用資料總管，以及為您的帳戶擷取 Azure 入口網站上顯示的計量。 使用資料瀏覽器時，除了允許 Azure 入口網站存取您的帳戶之外，您還需要更新防火牆設定，以將目前的 IP 位址新增至防火牆規則。 請注意，防火牆變更可能需要15分鐘才能傳播。 

您可以結合具有子網路的 IP 型防火牆與 VNET 存取控制。 結合它們，即可將存取權限制為任何具有公用 IP 和/或來自 VNET 內特定子網路的來源。 若要深入了解如何使用子網路和 VNET 型存取控制，請參閱[從虛擬網路存取 Azure Cosmos DB 資源](vnet-service-endpoint.md)。

總括起來，一律需有授權權杖才能存取 Azure Cosmos 帳戶。 如果未設定 IP 防火牆和 VNET 存取控制清單 (ACL)，則可使用授權權杖來存取 Azure Cosmos 帳戶。 在 IP 防火牆或 VNET ACL 或兩者皆已設定於 Azure Cosmos 帳戶之後，只有源自您所指定來源 (且具有授權權杖) 的要求會得到有效回應。 

## <a name="next-steps"></a>後續步驟

接下來，您可以使用下列文件為您的帳戶設定 IP 防火牆或 VNET 服務端點：

* [如何設定 Azure Cosmos 帳戶的 IP 防火牆](how-to-configure-firewall.md)
* [從虛擬網路存取 Azure Cosmos DB 資源](vnet-service-endpoint.md)
* [如何設定 Azure Cosmos DB 帳戶的虛擬網路服務端點](how-to-configure-vnet-service-endpoint.md)




