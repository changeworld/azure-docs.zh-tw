---
title: Azure 資源移動器的相關常見問題
description: 取得有關 Azure 資源移動器的常見問題解答
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 520c2d4fd258bfab5a5a1e0abf890d58bb98fbdc
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652852"
---
# <a name="common-questions"></a>常見問題

本文將回答有關 [Azure 資源移動器](overview.md)的常見問題。

## <a name="general"></a>一般

### <a name="is-resource-mover-generally-available"></a>資源移動器已正式推出嗎？

資源移動器目前處於公開預覽狀態。 支援生產工作負載。

## <a name="region-move"></a>區域移動

### <a name="what-resources-can-i-move-across-regions-using-resource-mover"></a>使用資源移動器可以跨區域移動哪些資源？

使用資源移動器，您目前可以跨區域移動下列資源：

- Azure Vm 和相關聯的磁片
- NIC
- 可用性設定組 
- Azure 虛擬網路 
- 公用 IP 位址 (Nsg) 的網路安全性群組
- 內部和公用負載平衡器 
- Azure SQL 資料庫和彈性集區

### <a name="where-is-the-metadata-about-a-region-move-stored"></a>區域的相關中繼資料會儲存在哪裡？

它會儲存在 [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) 資料庫中，以及 [azure Blob 儲存體](../storage/common/storage-service-encryption.md)的 Microsoft 訂用帳戶中。

### <a name="is-the-collected-metadata-encrypted"></a>收集的中繼資料是否已加密？

是，同時傳輸和待用。
- 在傳輸期間，中繼資料會透過網際網路使用 HTTPS 安全地傳送至資源移動器服務。
- 在儲存體中，中繼資料會加密。

### <a name="how-is-managed-identity-used-in-resource-mover"></a>資源移動器中使用受控識別的方式為何？

[受控識別](../active-directory/managed-identities-azure-resources/overview.md) (先前稱為「受控服務識別 (錯誤) # A3 會在 Azure AD 中為 Azure 服務提供自動管理的身分識別。
- 資源移動器會使用受控識別，讓它可以存取 Azure 訂用帳戶來跨區域移動資源。
- 移動集合需要系統指派的身分識別，並可存取包含您要移動之資源的訂用帳戶。

- 如果您在入口網站中跨區域移動資源，則會自動執行此程式。
- 如果您使用 PowerShell 移動資源，請執行 Cmdlet 以將系統指派的身分識別指派給集合，然後將具有正確訂用帳戶許可權的角色指派給身分識別主體。 

### <a name="what-managed-identity-permissions-does-resource-mover-need"></a>資源移動器需要哪些受控識別許可權？

Azure 資源移動器受控識別至少需要下列許可權： 

- 在使用者訂用帳戶中寫入/建立資源的許可權，可透過「 *參與者* 」角色使用。 
- 建立角色指派的許可權。 通常適用 *于擁有者* 或 *使用者存取系統管理員* 角色，或具有已獲指派 *Microsoft 授權/角色指派/寫入權限* 的自訂角色。 如果資料共用資源的受控識別已獲得 Azure 資料存放區的存取權，則不需要此許可權。 
 
當您在入口網站的資源移動器中樞內新增資源時，只要使用者具有上述許可權，就會自動處理許可權。 如果您使用 PowerShell 新增資源，您可以手動指派許可權。

> [!IMPORTANT]
> 強烈建議您不要修改或移除身分識別角色指派。 

### <a name="what-should-i-do-if-i-dont-have-permissions-to-assign-role-identity"></a>如果我沒有指派角色身分識別的許可權，該怎麼辦？

**可能的原因** | **建議**
--- | ---
當您第一次新增資源時，您不是「 *參與者* 」和「 *使用者存取系統管理員* 」 (或「 *擁有* 者」) 。 | 使用具有 *參與者* 和 *使用者存取系統管理員* (的帳戶，或訂用帳戶的 *擁有* 者) 許可權。
資源移動器受控識別沒有必要的角色。 | 新增「參與者」和「使用者存取系統管理員」角色。
資源移動器受控識別已重設為 *無*。 | 在 > 身分 **識別**的移動集合中重新啟用系統指派的身分識別。 或者，在 [ **新增資源**] 中再次新增資源，這會執行相同的工作。  
訂用帳戶已移至不同的租使用者。 | 停用並啟用移動集合的受控識別。


## <a name="next-steps"></a>接下來的步驟

[深入瞭解](about-move-process.md) 資源移動器元件和移動流程。
