---
title: 在 Azure Sentinel 中設定客戶管理的金鑰 |Microsoft Docs
description: 瞭解如何在 Azure Sentinel 中設定客戶管理的金鑰 (CMK)。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2020
ms.author: yelevin
ms.openlocfilehash: ecb4203c822f5b72068e11d0ad4b988d294aab0d
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427049"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>設定 Azure Sentinel 客戶管理的金鑰

本文提供 Azure Sentinel 的背景資訊和設定客戶管理的金鑰 (CMK) 步驟。 CMK 可讓所有儲存或傳送至 Azure Sentinel 的資料加密，這些資料放在所有相關的儲存體資源中，並使用您所建立或擁有的 Azure Key Vault 金鑰加密。

> [!NOTE]
> - Azure Sentinel 的 CMK 功能僅提供給 **新客戶** 。
>
> - 這項功能的存取權是由 Azure 功能註冊所控制。 您可以藉由聯絡來要求存取權 azuresentinelCMK@microsoft.com 。 擱置的要求將會根據可用容量核准。
>
> - CMK 功能僅適用於每天傳送 1TB 或以上的客戶。 當您向 Microsoft 申請在您的 Azure 訂用帳戶佈建 CMK 時，您會收到額外價格的相關資訊。 深入了解 [Log Analytics 價格](../azure-monitor/platform/manage-cost-storage.md#log-analytics-dedicated-clusters)。

## <a name="how-cmk-works"></a>CMK 的運作方式 

Azure Sentinel 解決方案會針對記錄收集和功能使用數個儲存體資源，包括 Log Analytics 和其他。 在 Azure Sentinel CMK 設定的過程中，您也必須在相關的儲存體資源上設定 CMK 設定。 儲存在 Log Analytics 以外的儲存體資源中的資料也會一併加密。

進一步了解 [CMK](../azure-monitor/platform/customer-managed-keys.md#customer-managed-key-overview)。

> [!NOTE]
> 如果您在 Azure Sentinel 上啟用 CMK，並不會啟用任何不支援 CMK 的公開預覽功能。

## <a name="enable-cmk"></a>啟用 CMK 

若要佈建 CMK，請遵循下列步驟： 

1.  建立 Azure Key Vault 並儲存金鑰。

2.  在您的 Log Analytics 工作區啟用 CMK。

3.  註冊 Cosmos DB。

4.  將存取原則新增至您的 Azure Key Vault 執行個體。

5.  在 Azure Sentinel 中啟用 CMK。

6.  啟用 Azure Sentinel。

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>步驟 1：建立 Azure Key Vault 並儲存金鑰

1.  [建立 Azure Key Vault 資源](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal)，然後產生或匯入將用於資料加密的金鑰。
    > [!NOTE]
    >  必須將 Azure Key Vault 設定為可復原，才能保護您的金鑰和存取權。

1.  [開啟復原選項：](../key-vault/general/best-practices.md#turn-on-recovery-options)

    -   請確定已開啟[虛刪除](../key-vault/general/soft-delete-overview.md)。

    -   開啟[清除保護](../key-vault/general/soft-delete-overview.md#purge-protection)，以防止強制刪除秘密/保存庫，即使在虛刪除之後也一樣。

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>步驟 2：在您的 Log Analytics 工作區啟用 CMK

依照 [Azure 監視器客戶管理的金鑰設定](../azure-monitor/platform/customer-managed-keys.md)中的指示，建立在下列步驟中將做為 Azure Sentinel 工作區使用的 CMK 工作區。

### <a name="step-3-register-for-cosmos-db"></a>步驟 3：註冊 Cosmos DB

Azure Sentinel 與 Cosmos DB 搭配可以做為額外的儲存體資源。 請務必註冊 Cosmos DB。

遵循 Cosmos DB 指示，為您的 Azure 訂用帳戶[註冊 Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider)。

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>步驟4：將存取原則新增至您的 Azure Key Vault 執行個體

請務必將 Cosmos DB 中的存取權新增至您的 Azure Key Vault 執行個體。 遵循 Cosmos DB 指示，使用 Azure Cosmos DB 主體[將存取原則新增至 Azure Key Vault 執行個體](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)。

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>步驟 5：在 Azure Sentinel 中啟用 CMK

新客戶只有在直接從 Azure 產品群組收到存取權之後，才會獲得 Azure Sentinel CMK 功能。 請透過您的 Microsoft 連絡人接收 Azure Sentinel 小組的核准，以在您的解決方案中啟用 CMK。

在您取得核准之後，系統會要求您提供下列資訊，以啟用 CMK 功能。

-  您要啟用 CMK 的工作區識別碼

-  Key Vault URL：將金鑰的「金鑰識別碼」複製到最後一個正斜線：  
    

    ![金鑰識別碼](./media/customer-managed-keys/key-identifier.png)

    Azure Sentinel 小組會為您提供的工作區啟用 Azure Sentinel CMK 功能。

-  來自 Azure Sentinel 產品小組的確認，確認已核准您使用這項功能。 您必須獲得此確認才能繼續。

### <a name="step-6-enable-azure-sentinel"></a>步驟 6：啟用 Azure Sentinel


移至 Azure 入口網站，在您設定 CMK 的工作區上啟用 Azure Sentinel。 如需詳細資訊，請參閱 [Azure Sentinel 上線](quickstart-onboard.md)。

## <a name="key-encryption-key-revocation-or-deletion"></a>金鑰加密金鑰的撤銷或刪除


如果使用者撤銷金鑰加密金鑰 (不管是藉由刪除或移除 Azure Sentinel 的存取權)，在一小時內，Azure Sentinel 會接受此變更，而且其行為會如同資料已無法再使用一樣。 此時，將會防止任何使用持續性儲存體資源的作業執行，例如資料擷取、持續性設定變更、事件建立。 先前儲存的資料不會被刪除，但仍將無法存取。 無法存取的資料是由資料保留原則所控管，並會根據該原則加以清除。

撤銷或刪除加密金鑰之後，唯一可能的作業就是刪除帳戶。

如果在撤銷之後還原存取，Azure Sentinel 會在一小時內還原資料的存取權。

若要深入瞭解撤銷在 Azure 監視器中如何運作，請參閱 [Azure 監視器 CMK 撤銷](../azure-monitor/platform/customer-managed-keys.md#key-revocation)。

## <a name="key-encryption-key-rotation"></a>金鑰加密金鑰輪替


Azure Sentinel 和 Log Analytics 支援金鑰輪替。 當使用者在 Key Vault 中執行金鑰輪替時，Azure Sentinel 會在一小時內支援新的金鑰。

在 Key Vault 中，您可以藉由建立新版本的金鑰來執行金鑰輪替：

![金鑰輪替](./media/customer-managed-keys/key-rotation.png)

您可以在 24 小時後停用舊版本的金鑰，或在 Azure Key Vault 稽核記錄不再顯示任何使用舊版金鑰的活動之後停用。

如果您在 Azure Sentinel 和 Log Analytics 中使用相同的金鑰，則必須執行金鑰輪替，您必須使用新的 Azure Key Vault 金鑰版本，在 Log Analytics 中明確更新叢集資源。 如需詳細資訊，請參閱 [Azure 監視器 CMK 輪替](../azure-monitor/platform/customer-managed-keys.md#key-rotation)。

## <a name="next-steps"></a>後續步驟
在本文件中，您已瞭解如何在 Azure Sentinel 中設定客戶管理的金鑰。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

