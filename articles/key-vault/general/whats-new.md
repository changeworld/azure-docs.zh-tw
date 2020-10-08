---
title: Azure Key Vault 的新功能
description: Azure Key Vault 的近期更新
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 164ba4767e0154154e5b3dc864265ba1505859d0
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653140"
---
# <a name="whats-new-for-azure-key-vault"></a>Azure Key Vault 的新功能

以下是 Azure Key Vault 的新功能。 您也可以在 [Azure 更新 Key Vault 通道](https://azure.microsoft.com/updates/?category=security&query=Key%20vault)上宣佈新功能和改善項目。

## <a name="july-2020"></a>2020 年 7 月

> [!WARNING]
> 這兩項更新可能會影響到 Azure Key Vault 的實作。

### <a name="soft-delete-on-by-default"></a>預設的虛刪除

在 2020 年底之前，**所有金鑰保存庫預設都會開啟虛刪除功能** (包括新的和預先存在的金鑰保存庫)。 如需這項可能重大變更的完整詳細資料，以及尋找受影響的金鑰保存庫，並事先加以更新的步驟，請參閱[將在所有金鑰保存庫上啟用虛刪除](soft-delete-change.md)的一文。 

### <a name="azure-tls-certificate-changes"></a>Azure TLS 憑證變更  

Microsoft 正在更新 Azure 服務，以使用來自一組不同根憑證授權單位 (CA) 的 TLS 憑證。 由於目前的 CA 憑證不符合其中一個 CA/瀏覽器論壇基準需求，因此需要進行此項變更。  如需完整的詳細資料，請參閱 [Azure TLS 憑證變更](../../security/fundamentals/tls-certificate-changes.md)。

## <a name="june-2020"></a>2020 年 6 月

適用於 Key Vault 的 Azure 監視器目前為預覽狀態。  Azure 監視器可針對您的 Key Vault 要求、效能、失敗和延遲提供統合檢視，讓您能夠全面監視金鑰保存庫。 如需詳細資訊，請參閱[適用於 Key Vault 的 Azure 監視器 (預覽)](../../azure-monitor/insights/key-vault-insights-overview.md)。

## <a name="may-2020"></a>2020 年 5 月

Key Vault「攜帶您自己的金鑰」 (BYOK) 現已正式發行。 請參閱 [Azure Key Vault BYOK 規格](../keys/byok-specification.md)，並了解如何[將受 HSM 保護的金鑰匯入至 Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md)。

## <a name="march-2020"></a>2020 年 3 月

私人端點現已提供預覽版本。 Azure Private Link 服務可讓您透過虛擬網路中的私人端點存取 Azure Key Vault 和 Azure 裝載的客戶/合作夥伴服務。  了解如何[整合 Key Vault 與 Azure Private Link](private-link-service.md)。

## <a name="2019"></a>2019

- 下一代 Azure Key Vault SDK 發行版本。 如需用法範例，請參閱適用於 [Python](../secrets/quick-create-python.md)、[.NET](../secrets/quick-create-net.md)、[Java](../secrets/quick-create-java.md) 和 [Node.js](../secrets/quick-create-node.md) 的 Azure Key Vault 祕密快速入門
- 管理金鑰保存庫憑證的新 Azure 原則。 請參閱[適用於 Key Vault 的 Azure 原則內建定義](../policy-samples.md)。
- Azure Key Vault 虛擬機器擴充功能現已正式發行。  請參閱[適用於 Linux 的 Key Vault 虛擬機器擴充功能](../../virtual-machines/extensions/key-vault-linux.md)和[適用於 Windows 的 Key Vault 虛擬機器擴充功能](../../virtual-machines/extensions/key-vault-windows.md)。
- Azure 事件方格現已提供 Azure Key Vault 事件驅動的祕密管理。 如需詳細資訊，請參閱 [Azure Key Vault 中的事件方格結構描述](../../event-grid/event-schema-key-vault.md]，並了解如何[使用 Azure 事件方格接收和回應金鑰保存庫通知](event-grid-tutorial.md)。

## <a name="2018"></a>2018

今年發行的新功能與整合項目：

- 與 Azure Functions 整合。 如需利用 [Azure Functions](../../azure-functions/index.yml) 進行金鑰保存庫作業的範例案例，請參閱[祕密自動輪替](../secrets/tutorial-rotation.md)。 
- [與 Azure Databricks 整合](/azure/databricks/scenarios/store-secrets-azure-key-vault)。 如此一來，Azure Databricks 現在支援兩種類型的祕密範圍：Azure Key Vault 支援和 Databricks 支援。 如需詳細資訊，請參閱[建立 Azure Key Vault 支援的祕密範圍](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Azure Key Vault 的虛擬網路服務端點](overview-vnet-service-endpoints.md)。

## <a name="2016"></a>2016

今年發行的新功能：

- 受控儲存體帳戶金鑰。 儲存體帳戶金鑰功能已新增可與 Azure 儲存體更輕鬆地整合。 如需詳細資訊，請參閱概觀主題：[受控儲存體帳戶金鑰概觀](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys)。
- 虛刪除。 虛刪除功能，改善金鑰保存庫和金鑰保存庫物件的資料保護。 如需詳細資訊，請參閱概觀主題：[虛刪除概觀](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)。

## <a name="2015"></a>2015

今年發行的新功能：
- 憑證管理。 在 2016 年 9 月 26 日，已作為功能新增至正式發行版本 2015-06-01。

於 2015 年 6 月 24 日宣佈正式發行 (版本 2015-06-01)。 此版本已進行下列變更： 
- 刪除金鑰 - 已移除 "use" 欄位。
- 取得金鑰的相關資訊 - 已移除 "use" 欄位。
- 將金鑰匯入保存庫 - 已移除 "use" 欄位。
- 還原金鑰 - 已移除 "use" 欄位。     
- 針對 RSA 演算法，已將 "RSA_OAEP" 變更為 "RSA-OAEP"。 請參閱[關於金鑰、祕密與憑證](about-keys-secrets-certificates.md)。    
 
於 2015 年 4 月 20 日宣佈第二個預覽版本 (版本 2015-02-01-preview)。 如需詳細資訊，請參閱 [REST API 更新](https://docs.microsoft.com/archive/blogs/kv/rest-api-update)部落格文章。 已更新下列工作：
 
- 列出保存庫中的工作 - 已將分頁支援新增至作業。
- 列出金鑰的版本 - 已新增作業，來列出金鑰的版本。  
- 列出保存庫中的祕密 - 已新增分頁支援。
- 列出祕密的版本 - 新增作業，來列出祕密的版本。  
- 所有作業 - 已將建立/更新時間戳記新增至屬性。  
- 建立祕密 - 已將 Content-Type 新增至祕密。
- 建立金鑰 - 已將標籤新增為選用資訊。
- 建立祕密 - 已將標籤新增為選用資訊。
- 更新金鑰 - 已將標籤新增為選用資訊。
- 更新祕密 - 已將標籤新增為選用資訊。
- 已將祕密的大小上限從 10 K 變更為 25 K 位元組。 請參閱[關於金鑰、祕密與憑證](about-keys-secrets-certificates.md)。    

## <a name="2014"></a>2014
 
於 2015 年 1 月 8 日宣佈第一個預覽版本 (版本 2014-12-08-preview)。  

## <a name="next-steps"></a>後續步驟

如果您有其他問題，請透過[支援](https://azure.microsoft.com/support/options/)與我們連絡。  
