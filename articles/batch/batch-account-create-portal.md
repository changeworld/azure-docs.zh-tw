---
title: 在 Azure 入口網站中建立帳戶
description: 了解如何在 Azure 入口網站中建立 Azure Batch 帳戶，以在雲端中執行大規模的平行工作負載
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 943fd145dbd4964e0d43d91f726ea5a79fda59de
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895985"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>使用 Azure 入口網站建立 Batch 帳戶

本主題說明如何在[Azure 入口網站](https://portal.azure.com)中建立[Azure Batch 帳戶](accounts.md)，選擇符合您計算案例的帳戶屬性。 您也將瞭解何處可以找到重要的帳戶屬性，例如存取金鑰與帳戶 Url。

如需 Batch 帳戶和案例的相關背景，請參閱 [Batch 服務工作流程和資源](batch-service-workflow-features.md)。

## <a name="create-a-batch-account"></a>建立批次帳戶：

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 從首頁選取 [ **建立資源**]。

1. 在搜尋方塊中，輸入 **Batch 服務**。 從結果中選取 [ **Batch 服務** ]，然後選取 [ **建立**]。

1. 輸入下列詳細資料。

    :::image type="content" source="media/batch-account-create-portal/batch-account-portal.png" alt-text="[新增 Batch 帳戶] 畫面的螢幕擷取畫面。":::

    a. 訂用帳戶：要在其中建立 Batch 帳戶的訂用帳戶。 如果您只有一個訂用帳戶，則預設會選取此項目。

    b. **資源群組**：為新的 Batch 帳戶選取一個現有的資源群組，或視需要建立一個新的資源群組。

    c. **帳戶名稱**：您選擇的名稱在建立帳戶的 Azure 區域內必須是唯一的 (請參閱下面的「位置」)。 帳戶名稱只能包含小寫字元或數字，且長度必須為 3-24 個字元。

    d. **位置**：要在其中建立 Batch 帳戶的 Azure 區域。 只有您的訂用帳戶和資源群組所支援的區域會顯示為選項。

    e. **儲存體帳戶**：選用的 [Azure 儲存體帳戶](accounts.md#azure-storage-accounts) ，與您的 Batch 帳戶相關聯。 您可以選取現有的儲存體帳戶，或建立一個新帳戶。 建議使用一般用途 v2 儲存體帳戶，以達到最佳效能。

    :::image type="content" source="media/batch-account-create-portal/storage_account.png" alt-text="建立儲存體帳戶時的選項螢幕擷取畫面。":::

1. 如有需要，請選取 [ **Advanced** ] 來指定 **識別類型**、 **公用網路存取** 或 **集區配置模式**。 在大部分的情況下，預設選項是正常的。

1. 選取 [ **審核 + 建立**]，然後選取 [ **建立** ] 以建立帳戶。

## <a name="view-batch-account-properties"></a>檢視 Batch 帳戶屬性

一旦建立帳戶後，選取帳戶以存取其設定和屬性。 您可以使用左側功能表來存取所有的帳戶設定和屬性。

> [!NOTE]
> Batch 帳戶的名稱就是其識別碼，無法變更。 如果您需要變更 Batch 帳戶的名稱，則必須刪除帳戶，再以想要的名稱建立新帳戶。

:::image type="content" source="media/batch-account-create-portal/batch_blade.png" alt-text="Azure 入口網站中 Batch 帳戶頁面的螢幕擷取畫面。":::

當您使用 [Batch API](batch-apis-tools.md#azure-accounts-for-batch-development) 來開發應用程式時，需要有帳戶 URL 和金鑰，才能存取 Batch 資源。  (Batch 也支援 Azure Active Directory authentication。 ) 若要查看 Batch 帳戶存取訊號，請選取 [ **金鑰**]。

:::image type="content" source="media/batch-account-create-portal/batch-account-keys.png" alt-text="Azure 入口網站中 Batch 帳戶金鑰的螢幕擷取畫面。":::

若要檢視與 Batch 帳戶相關聯的儲存體帳戶名稱和金鑰，請選取 [儲存體帳戶]。

若要查看適用于 Batch 帳戶的 [資源配額](batch-quota-limit.md) ，請選取 [ **配額**]。

## <a name="additional-configuration-for-user-subscription-mode"></a>使用者訂用帳戶模式的其他組態

如果您選擇在使用者訂用帳戶模式中建立 Batch 帳戶，請在建立帳戶之前執行下列其他的步驟。

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>允許 Azure Batch 存取訂用帳戶 (一次性作業)

當您在使用者訂用帳戶模式中建立第一個 Batch 帳戶時，您必須向 Batch 註冊訂用帳戶。  (如果您已經這麼做，請跳到下一節。 ) 

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 [更多服務] > [訂用帳戶]，然後選取您想要用於 Batch 帳戶的訂用帳戶。

1. 在 [訂用帳戶] 頁面中，選取 [資源提供者]，並搜尋 **Microsoft.Batch**。 請檢查 **Microsoft.Batch** 資源提供者是否已在訂用帳戶中註冊。 如果未選取，請選取靠近畫面頂端的 [ **註冊** ] 連結。

    :::image type="content" source="media/batch-account-create-portal/register_provider.png" alt-text="顯示 Microsoft.Batch 資源提供者的螢幕擷取畫面。":::

1. 返回 [**訂** 用帳戶] 頁面，然後選取 [**存取控制] (IAM)**  >  **角色指派**  >  **新增**[  >  **新增角色指派**]。

    :::image type="content" source="media/batch-account-create-portal/subscription_iam.png" alt-text="訂用帳戶之 [角色指派] 頁面的螢幕擷取畫面。":::

1. 在 [ **新增角色指派** ] 頁面上，選取 [ **參與者** ] 或 [ **擁有** 者] 角色，然後搜尋 Batch API。 搜尋 **Microsoft Azure Batch** 或 **MICROSOFTAZUREBATCH** 以尋找 API。  (**ddbf3205-c6bd-46ae-8127-60eb93363864** 是 Batch API 的應用程式識別碼。 ) 

1. 一旦您找到 Batch API 之後，請選取它，然後選取 [儲存]。

### <a name="create-a-key-vault"></a>建立金鑰保存庫

在使用者訂用帳戶模式中，需要 [Azure Key Vault](../key-vault/general/overview.md) 。 Key Vault 必須與要建立的 Batch 帳戶位於相同的訂用帳戶和區域中。

1. 從 [Azure 入口網站](https://portal.azure.com)的 [首頁] 中，選取 [ **建立資源**]。
1. 在 [搜尋] 方塊中輸入 **Key Vault**。 從結果中選取 **Key Vault** ，然後選取 [ **建立**]。
1. 在 [ **建立金鑰保存庫** ] 頁面中，輸入 Key Vault 的名稱，並在您想要用於 Batch 帳戶的相同區域中建立新的資源群組。 將其餘設定保留為預設值，然後選取 [建立]。

在使用者訂用帳戶模式中建立 Batch 帳戶時，請將 **使用者訂** 用帳戶指定為集區配置模式、選取您建立的 Key Vault，然後選取該方塊以授與 Key Vault Azure Batch 存取權。

如果您想要以手動方式授與 Key Vault 的存取權，請移至 Key Vault 的 [ **存取原則** ] 區段，然後選取 [ **新增存取原則**]。 選取 [ **選取主體** ] 旁的連結，然後搜尋 **Microsoft Azure Batch** (應用程式識別碼 **ddbf3205-c6bd-46ae-8127-60eb93363864**) 。 選取該主體，然後使用下拉式功能表來設定 **秘密許可權** 。 至少必須將 [取得]、[列出]、[設定] 和 [刪除] 權限授與 Azure Batch。

:::image type="content" source="media/batch-account-create-portal/secret-permissions.png" alt-text="Azure Batch 的秘密許可權選項螢幕擷取畫面":::

選取 **[新增**]，然後確定已針對連結 **Key Vault** 資源選取 [**用於部署的 Azure 虛擬機器**] 和 [**針對範本部署 Azure Resource Manager** ] 核取方塊。 選取 [ **儲存** ] 以認可您的變更。

:::image type="content" source="media/batch-account-create-portal/key-vault-access-policy.png" alt-text="[存取原則] 畫面的螢幕擷取畫面。":::

### <a name="configure-subscription-quotas"></a>設定訂用帳戶配額

針對使用者訂用帳戶的 Batch 帳戶，必須手動設定 [核心配額](batch-quota-limit.md) 。 Standard Batch 核心配額不適用於使用者訂用帳戶模式中的帳戶，且 [您訂](../azure-resource-manager/management/azure-subscription-service-limits.md) 用帳戶中的區域計算核心、每個系列計算核心和其他資源的配額會被使用並強制執行。

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取使用者訂用帳戶模式 Batch 帳戶，以顯示其設定和屬性。
1. 從左側功能表中選取 [配額]，以檢視和設定與您的 Batch 帳戶相關聯的核心配額。

## <a name="other-batch-account-management-options"></a>其他 Batch 帳戶管理選項

除了使用 Azure 入口網站以外，您也可以使用下列工具來建立及管理 Batch 帳戶︰

- [Batch PowerShell Cmdlet](batch-powershell-cmdlets-get-started.md)
- [Azure CLI](batch-cli-get-started.md)
- [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>後續步驟

- 深入了解 [Batch 服務工作流程和主要資源](batch-service-workflow-features.md)，例如集區、節點、作業和工作。
- 了解使用 [Batch .NET 用戶端程式庫](quick-run-dotnet.md)或 [Python](quick-run-python.md) 開發啟用 Batch 之應用程式的基本概念。 這些快速入門會引導您使用 Batch 服務在多個計算節點上執行工作負載的範例應用程式，並使用 Azure 儲存體來進行工作負載檔案預備和抓取。