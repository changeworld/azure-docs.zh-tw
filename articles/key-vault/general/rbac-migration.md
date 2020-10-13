---
title: 遷移至 Azure 角色型存取控制 |Microsoft Docs
description: 瞭解如何從保存庫存取原則遷移至 Azure 角色。
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: e06a7a759c712b47f3a725a3c49a660226da6a09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90064117"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-preview-permission-model"></a>從保存庫存取原則遷移至 Azure 角色型存取控制 (預覽) 許可權模型

保存庫存取原則模型是內建 Key Vault 的現有授權系統，可提供金鑰、秘密和憑證的存取權。 您可以藉由將個別許可權指派給安全性主體 (使用者、群組、服務主體、Key Vault 範圍) 的受控識別，來控制存取權。 

Azure 角色型存取控制 (Azure RBAC) 是以 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 為基礎的授權系統，可提供更細緻的 Azure 資源存取管理。 適用于 Key Vault 金鑰、秘密和憑證存取管理的 Azure RBAC 目前處於公開預覽狀態。 您可以使用 Azure RBAC 來控制資源的存取權，方法是建立角色指派，其中包含三個元素：安全性主體、角色定義 (預先定義的一組許可權) ，以及 (資源群組或個別資源) 的範圍。 如需詳細資訊，請參閱 [azure 角色型存取控制 (AZURE RBAC) ](https://docs.microsoft.com/azure/role-based-access-control/overview)。

在遷移至 Azure RBAC 之前，請務必瞭解其優點與限制。

Azure RBAC 對保存庫存取原則的優勢：
- 為 Azure 資源提供統一的存取控制模型-Azure 服務之間的相同 API
- 系統管理員的集中式存取管理-單一視圖中管理所有 Azure 資源
- 與適用于時間型存取控制的 [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) 整合
- 拒絕指派-在特定範圍內排除安全性主體的能力。 如需詳細資訊，請參閱 [瞭解 Azure 拒絕指派](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments)

Azure RBAC 的缺點：
- 角色指派的延遲-套用角色指派可能需要幾分鐘的時間。 系統會立即指派保存庫存取原則。
- 有限的角色指派數-每個訂用帳戶2000個角色指派與每個 Key Vault 的1024個存取原則

## <a name="access-policies-to-azure-roles-mapping"></a>存取 Azure 角色對應的原則

Azure RBAC 有數個 Azure 內建角色，可供您指派給使用者、群組、服務主體和受控識別。 如果內建的角色無法滿足您組織的特定需求，您可以建立自己的 [Azure 自訂角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)。

Key Vault 金鑰、憑證和秘密存取管理的內建角色：
- Key Vault 系統管理員 (預覽) 
- Key Vault 讀者 (預覽) 
- Key Vault 憑證長 (預覽) 
- Key Vault 加密長 (預覽) 
- Key Vault 加密使用者 (預覽) 
- Key Vault 秘密長 (預覽) 
- Key Vault 秘密使用者 (預覽) 

如需現有內建角色的詳細資訊，請參閱[Azure 內建](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)角色

您可以使用個別選取的許可權或預先定義的權限範本，來指派保存庫存取原則。

存取原則預先定義的權限範本：
- 金鑰、秘密、憑證管理
- 金鑰 & 秘密管理
- 秘密 & 憑證管理
- 金鑰管理
- 秘密管理
- 憑證管理
- SQL Server 連接器
- Azure Data Lake Storage 或 Azure 儲存體
- Azure 備份
- Exchange Online 客戶金鑰
- SharePoint Online 客戶金鑰
- Azure 資訊 BYOK

### <a name="access-policies-templates-to-azure-roles-mapping"></a>存取 Azure 角色對應的原則範本
| 存取原則範本 | 作業 | Azure 角色 |
| --- | --- | --- |
| 金鑰、秘密、憑證管理 | 金鑰：所有作業 <br>憑證：所有作業<br>祕密：所有作業 | Key Vault 系統管理員 (預覽)  |
| 金鑰 & 秘密管理 | 金鑰：所有作業 <br>祕密：所有作業| Key Vault 加密長 (預覽) <br> Key Vault 秘密長 (預覽) |
| 秘密 & 憑證管理 | 憑證：所有作業 <br>祕密：所有作業| Key Vault 憑證官員 (preview) <br> Key Vault 秘密長 (預覽) |
| 金鑰管理 | 金鑰：所有作業| Key Vault 加密長 (預覽) |
| 秘密管理 | 祕密：所有作業| Key Vault 秘密長 (預覽) |
| 憑證管理 | 憑證：所有作業 | Key Vault 憑證官員 (preview) |
| SQL Server 連接器 | 索引鍵： get、list、wrap key、解除包裝金鑰 | Key Vault 加密服務加密 (預覽) |
| Azure Data Lake Storage 或 Azure 儲存體 | 金鑰：取得、列出、解除包裝金鑰 | N/A<br> 需要自訂角色|
| Azure 備份 | 金鑰：取得、列出、備份<br> 憑證：取得、列出、備份 | N/A<br> 需要自訂角色|
| Exchange Online 客戶金鑰 | 索引鍵： get、list、wrap key、解除包裝金鑰 | Key Vault 加密服務加密 (預覽) |
| Exchange Online 客戶金鑰 | 索引鍵： get、list、wrap key、解除包裝金鑰 | Key Vault 加密服務加密 (預覽) |
| Azure 資訊 BYOK | 金鑰：取得、解密、簽署 | N/A<br>需要自訂角色|


## <a name="assignment-scopes-mapping"></a>指派範圍對應  

適用于 Key Vault 的 Azure RBAC 允許在下列範圍指派角色：
- 管理群組
- 訂用帳戶
- 資源群組
- Key Vault 資源
- 個別的金鑰、秘密和憑證

保存庫存取原則許可權模型僅限於在 Key Vault 資源層級指派原則， 

一般情況下，最佳作法是在每個應用程式中都有一個金鑰保存庫，並在金鑰保存庫層級管理存取權。 在其他範圍管理存取權時，有一些案例可以簡化存取管理。

- * * 基礎結構、安全性系統管理員和操作員：使用保存庫存取原則管理群組、訂用帳戶或資源群組層級的金鑰保存庫群組需要維護每個金鑰保存庫的原則。 Azure RBAC 可讓您在管理群組、訂用帳戶或資源群組上建立一個角色指派。 該指派會套用至在相同範圍內建立的任何新金鑰保存庫。 在此案例中，建議使用 Privileged Identity Management 搭配即時存取，以提供永久存取權。
 
- * * 應用程式：在某些情況下，應用程式必須與其他應用程式共用秘密。 使用保存庫存取原則時，必須建立不同的金鑰保存庫，以避免提供所有秘密的存取權。 Azure RBAC 可讓您改為使用單一金鑰保存庫將角色指派給個別秘密的範圍。

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>Azure RBAC 遷移步驟的保存庫存取原則
Azure RBAC 和保存庫存取原則許可權模型之間有許多差異。 為了避免在遷移期間發生中斷，建議您遵循下列步驟。
 
1. **識別並指派角色**：根據上面的對應表識別內建角色，並在需要時建立自訂角色。 根據範圍對應指引，在範圍指派角色。 如需如何將角色指派給 key vault 的詳細資訊，請參閱 [使用 Azure 角色型存取控制提供 Key Vault 的存取 (preview) ](rbac-guide.md)
1. **驗證角色指派**： Azure RBAC 中的角色指派可能需要幾分鐘的時間才能傳播。 如需如何檢查角色指派的指引，請參閱 [列出範圍中的角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope)
1. **設定金鑰保存庫的監視和警示**：對於拒絕存取例外狀況，請務必啟用記錄和設定警示。 如需詳細資訊，請參閱 [Azure Key Vault 的監視和警示](https://docs.microsoft.com/azure/key-vault/general/alert)
1. **在 Key Vault 上設定 azure 角色型存取控制許可權模型**：啟用 azure RBAC 許可權模型將會使所有現有的存取原則失效。 如果發生錯誤，您可以切換回許可權模型，而且所有現有的存取原則都不會保持不變。

> [!NOTE]
> 當 Azure RBAC 許可權模型啟用時，嘗試更新存取原則的所有腳本都將會失敗。 請務必將這些腳本更新為使用 Azure RBAC。

## <a name="troubleshooting"></a>疑難排解
-  角色指派在數分鐘後無法運作-在某些情況下，角色指派可能需要較長的時間。 請務必在程式碼中撰寫重試邏輯，以涵蓋這些案例。
- 刪除 Key Vault 時，角色指派會消失， (虛刪除) 和已復原-這目前在所有 Azure 服務之間都有虛刪除功能的限制。 您必須在復原後重新建立所有角色指派。    

## <a name="learn-more"></a>深入了解

- [Azure RBAC 總覽](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [自訂角色教學課程](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-cli)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
