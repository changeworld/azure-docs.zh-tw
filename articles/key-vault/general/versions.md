---
title: Azure Key Vault 版本
description: 各種 Azure Key Vault 版本
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: mbaldwin
ms.openlocfilehash: 4959d530e1f7a3c777aa3db21fe577c853fbca13
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85856007"
---
# <a name="key-vault-versions"></a>Key Vault 版本

以下是 Azure Key Vault 的新這是什麼。 [Azure updates Key Vault 通道](https://azure.microsoft.com/updates/?category=security&query=Key%20vault)也會宣佈新功能和改進。

## <a name="june-2020"></a>2020 年 6 月

Key Vault 的 Azure 監視器現在為預覽狀態。  Azure 監視器藉由提供您的 Key Vault 要求、效能、失敗和延遲的統一觀點，全面監視您的金鑰保存庫。 如需詳細資訊，請參閱[Key Vault 的 Azure 監視器（預覽）。](../../azure-monitor/insights/key-vault-insights-overview.md)

## <a name="may-2020"></a>2020 年 5 月

Key Vault 「攜帶您自己的金鑰」（BYOK）現已正式推出。 請參閱[AZURE KEY VAULT BYOK 規格](../keys/byok-specification.md)，並瞭解如何將[受 HSM 保護的金鑰匯入至 Key Vault （BYOK）](../keys/hsm-protected-keys-byok.md)。

## <a name="march-2020"></a>2020 年 3 月

私人端點現已提供預覽。 Azure 私人連結服務可讓您透過虛擬網路中的私人端點，存取 Azure Key Vault 和 Azure 託管的客戶/合作夥伴服務。  瞭解如何將[Key Vault 與 Azure 私人連結整合](private-link-service.md)。

## <a name="2019"></a>2019

- 發行下一代 Azure Key Vault Sdk。 如需其用法的範例，請參閱適用于[Python](../secrets/quick-create-python.md)、 [.net](../secrets/quick-create-net.md)、 [JAVA](../secrets/quick-create-java.md)和[Node.js](../secrets/quick-create-node.md)的 Azure Key Vault 秘密快速入門
- 用來管理金鑰保存庫憑證的新 Azure 原則。 請參閱[Key Vault 的 Azure 原則內建定義](../policy-samples.md)。
- Azure Key Vault 虛擬機器擴充功能現已正式運作。  請參閱[適用于 Linux 的 Key Vault 虛擬機器擴充](../../virtual-machines/extensions/key-vault-linux.md)功能和[適用于 Windows 的 Key Vault 虛擬機器擴充](../../virtual-machines/extensions/key-vault-windows.md)功能。
- Azure 事件方格現已提供 Azure Key Vault 事件驅動的秘密管理。 如需詳細資訊，請參閱 [Azure Key Vault 中的事件的事件方格架構] （.。/../event-grid/event-schema-key-vault.md]，並瞭解如何[使用 Azure 事件方格來接收和回應金鑰保存庫通知](event-grid-tutorial.md)。

## <a name="2018"></a>2018

今年發行的新功能與整合：

- 與 Azure Functions 整合。 如需運用金鑰保存庫作業[Azure Functions](../../azure-functions/index.yml)的範例案例，請參閱[自動化密碼輪替](../secrets/tutorial-rotation.md)。 
- [與 Azure Databricks 整合](/azure/databricks/scenarios/store-secrets-azure-key-vault)。 如此一來，Azure Databricks 現在支援兩種類型的秘密範圍： Azure Key Vault 支援和 Databricks。 如需詳細資訊，請參閱[建立 Azure Key Vault 支援的秘密範圍](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Azure Key Vault 的虛擬網路服務端點](overview-vnet-service-endpoints.md)。

## <a name="2016"></a>2016

今年發行的新功能：

- 受控儲存體帳戶金鑰。 儲存體帳戶金鑰功能新增與 Azure 儲存體的整合更容易。 如需詳細資訊，請參閱概觀主題：[受控儲存體帳戶金鑰概觀](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys)。
- 虛刪除。 虛刪除功能可改善金鑰保存庫和金鑰保存庫物件的資料保護。 如需詳細資訊，請參閱概觀主題：[虛刪除概觀](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)。

## <a name="2015"></a>2015

今年發行的新功能：
- 憑證管理。 已在2016年9月26日新增為 GA 版本2015-06-01 的功能。

公開上市（版本2015-06-01）已于2015年6月24日宣佈。 此版本已進行下列變更： 
- 刪除金鑰 - 已移除 [使用] 欄位。
- 取得金鑰的相關資訊 - 已移除 [使用] 欄位。
- 將金鑰匯入保存庫 - 已移除 [使用] 欄位。
- 還原金鑰 - 已移除 [使用] 欄位。     
- 針對 RSA 演算法，已將 "RSA_OAEP" 變更為 "RSA-OAEP"。 請參閱[關於金鑰、秘密和憑證](about-keys-secrets-certificates.md)。    
 
第二個預覽版本（版本 2015-02-01-preview）已于2015年4月20日宣佈。 如需詳細資訊，請參閱 [REST API 更新](https://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx)部落格文章。 已更新下列工作：
 
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
 
第一個預覽版本（版本 2014-12-08-preview）已于2015年1月8日宣佈。  
 
## <a name="next-steps"></a>後續步驟

- [關於金鑰、密碼與憑證](about-keys-secrets-certificates.md)
- [[索引鍵]](../keys/index.yml)
- [密碼](../secrets/index.yml)
- [憑證](../certificates/index.yml)
