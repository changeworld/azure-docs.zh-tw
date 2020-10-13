---
title: 允許透過受信任的服務存取儲存體例外狀況
titleSuffix: Azure Cognitive Search
description: 說明如何設定信任的服務例外狀況以安全地存取儲存體帳戶中資料的操作指南。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 4fbffaa7bc68bb32bd07b657f4b769e5af4302bf
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950019"
---
# <a name="accessing-data-in-storage-accounts-securely-via-trusted-service-exception"></a>透過信任的服務例外狀況安全地存取儲存體帳戶中的資料

存取儲存體帳戶中資料的索引子可以利用 [受信任的服務例外](../storage/common/storage-network-security.md#exceptions) 狀況功能來安全地存取資料。 這項機制提供的客戶無法透過 [IP 防火牆規則來授與索引子存取](search-indexer-howto-access-ip-restricted.md) 存取儲存體帳戶中資料的簡單、安全且免費的替代方案。

> [!NOTE]
> 支援透過受信任的服務例外狀況存取儲存體帳戶中的資料，僅限於 Azure Blob 儲存體和 Azure Data Lake Gen2 儲存體。 不支援 Azure 表格儲存體。

## <a name="step-1-configure-connection-to-the-storage-account-via-identity"></a>步驟1：透過身分識別設定儲存體帳戶的連接

遵循 [受控識別存取指南](search-howto-managed-identities-storage.md) 中所述的詳細資料，將索引子設定為透過搜尋服務的受控識別來存取儲存體帳戶。

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>步驟2：允許信任的 Microsoft 服務存取儲存體帳戶

在 Azure 入口網站中，流覽至儲存體帳戶的 [防火牆和虛擬網路] 索引標籤。 確定已核取 [允許信任的 Microsoft 服務存取此儲存體帳戶] 選項。 此選項只允許特定的搜尋服務實例具有儲存體帳戶的適當以角色為基礎的存取 (增強式驗證) 存取儲存體帳戶中的資料，即使是由 IP 防火牆規則所保護。

![受信任的服務例外狀況](media\search-indexer-howto-secure-access\exception.png "受信任的服務例外狀況")

索引子現在可以存取儲存體帳戶中的資料，即使是透過 IP 防火牆規則來保護帳戶。

## <a name="next-steps"></a>後續步驟

深入了解 Azure 儲存體索引子：

- [Azure Blob 索引子](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 索引子](search-howto-index-azure-data-lake-storage.md)
- [Azure 資料表索引子](search-howto-indexing-azure-tables.md)