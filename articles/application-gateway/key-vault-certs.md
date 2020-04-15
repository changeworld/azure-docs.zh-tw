---
title: 使用 Azure 金鑰保存庫憑證終止 TLS
description: 瞭解如何將 Azure 應用程式閘道與連接到啟用 HTTPS 的偵聽器的伺服器證書的密鑰保管庫整合。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 26093d051da8f2182a40f80837acbd9ef7dd008f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312079"
---
# <a name="tls-termination-with-key-vault-certificates"></a>使用金鑰保存憑證的 TLS 終止

[Azure 密鑰保管庫](../key-vault/key-vault-overview.md)是一個平臺管理的秘密存儲,可用於保護機密、密鑰和 TLS/SSL 證書。 Azure 應用程式閘道支援與連接到啟用 HTTPS 的偵聽器的伺服器證書的密鑰保管庫整合。 此支援僅限於應用程式閘道的 v2 SKU。

金鑰保管庫集成為 TLS 端接提供了兩種模型:

- 您可以顯示式提供附加到偵聽器的 TLS/SSL 憑證。 此模型是將 TLS/SSL 憑證傳遞到應用程式閘道以進行 TLS 終止的傳統方法。
- 在創建啟用 HHHS 的偵聽器時,可以選擇提供對現有密鑰保管庫證書或機密的引用。

與金鑰保管庫的應用程式閘道整合提供許多好處,包括:

- 更強的安全性,因為 TLS/SSL 證書不是由應用程式開發團隊直接處理的。 整合讓單獨的安全團隊:
  * 設置應用程式閘道。
  * 控制應用程式閘道生命週期。
  * 向選定的應用程序閘道授予存取存儲在金鑰保管庫中的證書的許可權。
- 支援將現有證書導入金鑰保管庫。 或者使用密鑰保管庫 API 與任何受信任的密鑰保管庫合作夥伴創建和管理新證書。
- 支援自動續訂存儲在密鑰保管庫中的證書。

應用程式閘道目前僅支援軟體驗證的證書。 不支援硬體安全模組 (HSM) 驗證的證書。 將應用程式閘道配置為使用金鑰保管庫證書後,其實例將從密鑰保管庫檢索證書,並在本地安裝以進行 TLS 終止。 實例還每隔 24 小時輪詢 Key Vault,以檢索證書的更新版本(如果存在)。 如果找到更新的證書,則當前與 HTTPS 偵聽器關聯的 TLS/SSL 證書將自動旋轉。

> [!NOTE]
> Azure 門戶僅支援密鑰庫證書,不支援機密。 應用程式閘道仍然支援引用 KeyVault 的機密,但只能透過非門戶資源(如 PowerShell、CLI、API、ARM 樣本等)來引用機密。 

## <a name="how-integration-works"></a>整合的運作方式

與金鑰保存庫的應用程式閘道整合需要三步設定程序:

1. **建立使用者配置的託管識別**

   創建或重用現有使用者分配的託管標識,應用程式閘道使用該標識代表您從密鑰保管庫檢索證書。 有關詳細資訊,請參閱[Azure 資源的託管標識是什麼?](../active-directory/managed-identities-azure-resources/overview.md) 此步驟在 Azure 活動目錄租戶中創建新標識。 標識受用於創建標識的訂閱的信任。

1. **設定金鑰保存庫**

   然後,導入現有證書或在密鑰保管庫中創建新證書。 證書將由通過應用程式閘道運行的應用程式使用。 在此步驟中,您還可以使用密鑰保管庫密鑰保管庫密鑰,該密鑰保管庫密鑰金鑰存儲為無密碼、基於 64 編碼的 PFX 檔。 我們建議使用證書類型,因為密鑰保管庫中的證書類型物件可以使用自動續訂功能。 創建證書或機密後,在密鑰保管庫中定義訪問策略,以允許授予標識*以訪問*該機密。

1. **設定應用程式閘道**

   完成上述兩個步驟后,可以設置或修改現有應用程序閘道以使用使用者分配的託管標識。 您還可以設定 HTTP 偵聽器的 TLS/SSL 憑證,以指向金鑰保管庫證書或密鑰 ID 的完整 URI。

   ![金鑰保存庫憑證](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>後續步驟

[使用 Azure PowerShell 使用金鑰保管庫憑證設定 TLS 終止](configure-keyvault-ps.md)
