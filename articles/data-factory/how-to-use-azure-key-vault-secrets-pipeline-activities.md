---
title: 在管線活動中使用 Azure Key Vault 祕密
description: 瞭解如何從 Azure key vault 提取已儲存的認證，並在 data factory 管線執行期間使用它們。
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 5a662119d9ccf95eac23785c5fe9a787da882531
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537390"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>在管線活動中使用 Azure Key Vault 祕密

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

您可以將認證或秘密值儲存在 Azure Key Vault 中，並在管線執行期間使用它們來傳遞至您的活動。

## <a name="prerequisites"></a>必要條件

這項功能依賴 data factory 受控識別。  瞭解 [Data Factory 的受控識別](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) 如何運作，並確定您的 Data Factory 有相關聯的資料處理站。

## <a name="steps"></a>步驟

1. 開啟資料處理站的屬性，並複製受控識別應用程式識別碼值。

    ![受控識別值](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. 開啟金鑰保存庫存取原則，並新增受控識別許可權以取得和列出秘密。

    ![顯示 [存取原則] 頁面的螢幕擷取畫面，其中已醒目提示 [新增存取原則] 動作。](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Key Vault 存取原則](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    按一下 [ **新增**]，然後按一下 [ **儲存**]。

3. 流覽至您的 Key Vault 秘密，並複製秘密識別碼。

    ![秘密識別碼](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    記下您想要在 data factory 管線執行期間取得的密碼 URI。

4. 在您的 Data Factory 管線中，加入新的 Web 活動並設定它，如下所示。  

    |屬性  |值  |
    |---------|---------|
    |安全輸出     |True         |
    |URL     |[您的秘密 URI 值]？ api-版本 = 7。0         |
    |方法     |GET         |
    |驗證     |MSI         |
    |資源        |https://vault.azure.net       |

    ![Web 活動](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > 您必須在秘密 URI 的結尾新增 **？ api-version = 7.0** 。  

    > [!CAUTION]
    > 將安全輸出選項設定為 true，防止秘密值以純文字記錄。  任何進一步使用此值的活動都應該將其 [安全輸入] 選項設定為 [是]。

5. 若要使用其他活動中的值，請使用下列程式碼運算式** @activity ( ' Web1 ' ) . output. value**。

    ![程式碼運算式](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>後續步驟

若要瞭解如何使用 Azure Key Vault 儲存資料存放區和計算的認證，請參閱 [將認證儲存在 Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
