---
title: 使用 Azure 門戶配置客戶託管金鑰
description: 本文介紹如何在 Azure 資料資源管理器中配置客戶管理的資料金鑰加密。
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: c014ed2c25711677617d3bf8ff5d2f0f968a3b14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80301040"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>使用 Azure 門戶配置客戶管理的金鑰

> [!div class="op_single_selector"]
> * [入口網站](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure 資源管理器範本](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>在 Azure 門戶中使用客戶管理的金鑰進行加密

本文介紹如何使用 Azure 門戶啟用客戶管理金鑰加密。 預設情況下，Azure 資料資源管理器加密使用 Microsoft 管理的金鑰。 將 Azure 資料資源管理器群集配置為使用客戶管理的金鑰，並指定要與群集關聯的金鑰。

1. 在[Azure 門戶](https://portal.azure.com/)中，轉到[Azure 資料資源管理器群集](create-cluster-database-portal.md#create-a-cluster)資源。 
1. 在門戶的左側窗格中選擇 > **"設置加密**"。 **Settings**
1. 在 **"加密**"窗格中，為**客戶管理的金鑰**設置選擇 **"打開**"。
1. 按一下 **"選擇鍵**"。

    ![設定客戶管理的金鑰](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. 在"**從 Azure 金鑰保存庫中選擇金鑰**"視窗中，從下拉清單中選擇現有的**金鑰保存庫**。 如果選擇 **"創建新**"[以創建新的金鑰保存庫](/azure/key-vault/quick-create-portal#create-a-vault)，則將路由到 **"創建金鑰保存庫"** 螢幕。

1. 選擇**鍵**。
1. 選擇**版本**。
1. 按一下 **"選擇**"。

    ![從 Azure 金鑰保存庫中選擇金鑰](media/customer-managed-keys-portal/cmk-key-vault.png)

1. 在"**加密**"窗格中，現在包含金鑰，選擇 **"保存**"。 當 CMK 創建成功時，您將在**通知**中看到一條成功消息。

    ![保存客戶管理的金鑰](media/customer-managed-keys-portal/cmk-encryption-setting.png)

通過為 Azure 資料資源管理器群集啟用客戶管理的金鑰，如果群集不存在，則將創建系統分配的標識。 此外，您將向所選金鑰保存庫上的 Azure 資料資源管理器群集提供所需的視圖許可權，並獲取金鑰保存庫屬性。 

> [!NOTE]
> 選擇 **"關閉**"以在創建客戶管理的金鑰後將其刪除。

## <a name="next-steps"></a>後續步驟

* [在 Azure 中保護 Azure 資料資源管理器群集](security.md)
* 通過在靜態啟用加密來保護[Azure 資料資源管理器 - Azure 門戶中的群集](manage-cluster-security.md)。
* [使用 Azure 資源管理器範本配置客戶託管金鑰](customer-managed-keys-resource-manager.md)
* [使用 C 配置客戶託管金鑰#](customer-managed-keys-csharp.md)



