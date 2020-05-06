---
title: 什麼是 Azure Blockchain Tokens
description: Azure Blockchain Tokens 是一項可用於權杖發行和管理的平台即服務 (PaaS)。
ms.date: 11/04/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: e7c106ffee8ea06c7c0ce738aa8be0aba96a38ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "79215086"
---
# <a name="what-is-azure-blockchain-tokens"></a>什麼是 Azure 區塊鏈服務權杖？

[!INCLUDE [Preview note](./includes/preview.md)]

Azure Blockchain Tokens 是一項平台即服務 (PaaS)，可在 Azure 中進行跨區塊鏈總帳的標準化權杖發行和管理。

使用 Azure Blockchain Tokens，您可以利用預先建立的權杖範本，為您的區塊鏈解決方案建立標準化權杖。 您也可以使用此服務來撰寫自己的權杖範本。 建立後，請使用 Azure Blockchain Tokens 來連線和發行區塊鏈上的權杖。 發行後，您就可以管理跨多個區塊鏈網路的權杖。

## <a name="templates"></a>範本

使用 Azure Blockchain Tokens 來選取預先建立的權杖範本，或建立自己的權杖範本。 Azure Blockchain Tokens 支援權杖範本可組合性，可讓您根據支援的行為建立自己的權杖範本。 權杖範本可用於大部分的區塊鏈解決方案，因為其會對應至最常使用的權杖。 您可以從範本著手，加以個人化，並為您的解決方案部署權杖。

如需 Azure Blockchain Tokens 範本的詳細資訊，請參閱 [Azure Blockchain Tokens 範本](templates.md)。

## <a name="management"></a>管理性

Azure Blockchain Tokens 提供 Azure 入口網站管理和 API，以連線到現有的區塊鏈網路。 目前，您可以連線到 [Azure 區塊鏈服務](../service/overview.md)或另一個 Ethereum 系列區塊鏈。

連線至一或多個區塊鏈網路之後，您可使用 Azure Blockchain Tokens API 來發行和管理權杖，以便在您的區塊鏈解決方案中使用。 使用 API，您可以在您的商務應用程式和邏輯中整合權杖管理。 例如，您可以使用 REST API 來管理權杖，而不是直接在區塊鏈上管理權杖。

## <a name="blockchains-and-accounts"></a>區塊鏈和帳戶

Azure Blockchain Tokens 提供 Azure 入口網站管理和 API，以便可在已連線的區塊鏈網路上建立新的群組和新的區塊鏈帳戶。 您可以直接在已連線的網路上建立新的帳戶，而 Azure Blockchain Tokens 會代表您管理帳戶私密金鑰。 使用群組，您可以將來自多個網路的不同區塊鏈帳戶群組在一起，並透過群組來管理存取控制。

如需 Azure Blockchain Tokens 帳戶管理的詳細資訊，請參閱 [Azure Blockchain Tokens 帳戶管理](account-management.md)。

## <a name="token-taxonomy-framework"></a>Token Taxonomy Framework

Azure Blockchain Tokens 建置於名為 Token Taxonomy Framework (TTF) 的標準本位基礎之上。 TTF 是從 [Token Taxonomy Initiative](https://entethalliance.org/participate/token-taxonomy-initiative/) (TTI) 權杖工作群組建立的一組交付項目。 TTI 工作群組會定義權杖的商務分類法，以及可套用於所有主要總帳 (包括 Ethereum、 Quorum、Corda 和 Hyperledger Fabric) 的行為。 此工作群組的目標是要建立一個架構，以便從商務觀點將權杖的使用標準化，進而簡化和推廣權杖型開發。 讓產業定義這些權杖及其在商務層級的行為，就會從操作權杖的商務邏輯中抽離權杖的詳細實作。

## <a name="support-and-feedback"></a>支援與意見反應

如需 Azure 區塊鏈服務消息，請瀏覽 [Azure 區塊鏈服務部落格](https://azure.microsoft.com/blog/topics/blockchain/)，即時掌握 Azure 區塊鏈服務工程小組提供的區塊鏈服務供應項目和最新資訊。

若要取得 Azure Blockchain Tokens 預覽版的存取權，請[聯絡 Azure Blockchain Tokens 小組](https://aka.ms/PreviewForm)。

若要提供產品意見反應或要求新功能，請透過 [Azure 區塊鏈服務的意見反應論壇](https://aka.ms/blockchainuservoice)，張貼票選想法。

## <a name="next-steps"></a>後續步驟

深入了解可用的 [Azure Blockchain Tokens 範本](templates.md)。
