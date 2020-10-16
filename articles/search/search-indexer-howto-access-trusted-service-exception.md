---
title: 使用信任的服務例外狀況存取 Azure 儲存體的索引子
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋中的索引子啟用資料存取，以安全地儲存在 Azure 儲存體中的資料。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e139c15ef6de00376a4e1a88000d263c3486994b
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92101370"
---
# <a name="indexer-access-to-azure-storage-using-the-trusted-service-exception-azure-cognitive-search"></a>使用受信任服務例外狀況的 Azure 儲存體索引子存取 (Azure 認知搜尋) 

Azure 認知搜尋服務中的索引子可存取 Azure 儲存體帳戶中的資料，可以利用 [受信任的服務例外](../storage/common/storage-network-security.md#exceptions) 功能來安全地存取資料。 這項機制提供的客戶無法 [使用 IP 防火牆規則來授與索引子存取存取](search-indexer-howto-access-ip-restricted.md) 儲存體帳戶中資料的簡單、安全且免費的替代方案。

> [!NOTE]
> 支援透過受信任的服務例外狀況存取儲存體帳戶中的資料，僅限於 Azure Blob 儲存體和 Azure Data Lake Gen2 儲存體。 不支援 Azure 表格儲存體。

## <a name="step-1-configure-a-connection-using-a-managed-identity"></a>步驟1：使用受控識別設定連接

遵循 [使用受控識別設定 Azure 儲存體帳戶](search-howto-managed-identities-storage.md)的連線中的指示。 當您完成時，您將會以信任的服務向 Azure Active Directory 註冊您的搜尋服務，而且您會在 Azure 儲存體中授與許可權，以存取資料或資訊。

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>步驟2：允許信任的 Microsoft 服務存取儲存體帳戶

在 Azure 入口網站中，流覽至儲存體帳戶的 [ **防火牆和虛擬網路** ] 索引標籤。 確定已核取 [ **允許信任的 Microsoft 服務存取此儲存體帳戶** ] 選項。 此選項只允許特定的搜尋服務實例具有儲存體帳戶的適當以角色為基礎的存取 (增強式驗證) 存取儲存體帳戶中的資料，即使是由 IP 防火牆規則所保護。

![受信任的服務例外狀況](media\search-indexer-howto-secure-access\exception.png "受信任的服務例外狀況")

索引子現在可以存取儲存體帳戶中的資料，即使是透過 IP 防火牆規則來保護帳戶。

## <a name="next-steps"></a>後續步驟

深入了解 Azure 儲存體索引子：

- [Azure Blob 索引子](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 索引子](search-howto-index-azure-data-lake-storage.md)
- [Azure 資料表索引子](search-howto-indexing-azure-tables.md)