---
title: 在管線活動中使用 Azure Key Vault 祕密
description: 瞭解如何從 Azure 金鑰保存庫獲取存儲的憑據，並在資料工廠管道運行期間使用它們。
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 09051ad3633ddc720cb34d3d145ccf649fa9cb08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77200107"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>在管線活動中使用 Azure Key Vault 祕密

您可以將憑據或機密值存儲在 Azure 金鑰保存庫中，並在管道執行期間使用它們傳遞到活動。

## <a name="prerequisites"></a>Prerequisites

此功能依賴于資料工廠託管標識。  瞭解資料[工廠的託管標識的工作原理，](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)並確保資料工廠具有關聯資料。

## <a name="steps"></a>步驟

1. 打開資料工廠的屬性並複製託管標識應用程式 ID 值。

    ![託管標識值](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. 打開金鑰保存庫訪問策略，並將託管標識許可權添加到獲取和列出機密。

    ![Key Vault 存取原則](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Key Vault 存取原則](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    按一下"**添加**"，然後按一下"**保存**"。

3. 導航到金鑰保存庫金鑰庫，並複製機密識別碼。

    ![秘密識別碼](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    記下在資料工廠管道運行期間要獲取的秘密 URI。

4. 在資料工廠管道中，添加新的 Web 活動並將其配置如下。  

    |屬性  |值  |
    |---------|---------|
    |安全輸出     |True         |
    |URL     |[您的機密 URI 值]？api 版本=7.0         |
    |方法     |GET         |
    |驗證     |MSI         |
    |資源        |https://vault.azure.net       |

    ![Web 活動](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > 您必須在機密 URI 的末尾添加 **？api 版本=7.0。**  

    > [!CAUTION]
    > 將"安全輸出"選項設置為 true，以防止以純文字記錄機密值。  使用此值的任何進一步活動都應將其安全輸入選項設置為 true。

5. 要在另一個活動中使用該值，請使用以下代碼運算式**@activity（"Web1"）。輸出.value**。

    ![代碼運算式](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>後續步驟

要瞭解如何使用 Azure 金鑰保存庫存儲資料存儲和計算的憑據，請參閱在[Azure 金鑰保存庫中存儲憑據](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
