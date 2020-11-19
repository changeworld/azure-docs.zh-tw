---
title: 匯出 Azure 原則資源
description: 瞭解如何將 Azure 原則資源匯出到 GitHub，例如原則定義和原則指派。
ms.date: 10/29/2020
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 923b063244f6f47def1c3e6a63d6e4d6b3b88083
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94919560"
---
# <a name="export-azure-policy-resources"></a>匯出 Azure 原則資源

本文提供如何匯出現有 Azure 原則資源的相關資訊。 匯出您的資源對備份很實用且建議使用，但也是您在進行雲端治理和將 [原則視為程式碼](../concepts/policy-as-code.md)的旅程中的重要步驟。 Azure 原則資源可以透過 [Azure 入口網站](#export-with-azure-portal)、 [Azure CLI](#export-with-azure-cli)、 [Azure PowerShell](#export-with-azure-powershell)及每個支援的 sdk 來匯出。

## <a name="export-with-azure-portal"></a>使用 Azure 入口網站匯出

若要從 Azure 入口網站匯出原則定義，請遵循下列步驟：

1. 藉由按一下 [所有服務] 然後搜尋並選取 [原則]，在 Azure 入口網站中啟動 Azure 原則服務。

1. 選取 Azure 原則分頁左側的 [定義]。

1. 使用 [匯出定義] 按鈕，或選取原則定義資料列上的省略符號，然後選取 [匯出定義]。

1. 選取 [以 GitHub 登入] 按鈕。 如果您尚未向 GitHub 進行驗證以授權 Azure 原則來匯出資源，請在開啟的新視窗中，檢查 [GitHub 動作](https://github.com/features/actions)需要的存取權，然後選取 [授權 AzureGitHubActions] 以繼續匯出程序。 完成之後，新視窗會自行關閉。

1. 在 [基本資料] 索引標籤上設定下列選項，然後選取 [原則] 索引標籤或 [下一步：原則] 按鈕 (位於頁面底部)。

   - **存放庫篩選器**：設定為 [我的存放庫] 來僅查看您擁有的存放庫，或設定為 [所有存放庫] 來查看您授與 GitHub 動作存取權的所有存放庫。
   - **存放庫：** 將設定為您想要匯出 Azure 原則資源的存放庫。
   - **分支**：設定存放庫中的分支。 使用預設以外的分支，是再進一步合併至原始程式碼之前，用來驗證更新的好方法。
   - **目錄**：將 Azure 原則資源匯出到其中的「根層級資料夾」。 此目錄下的子資料夾會根據匯出的資源來建立。

1. 在 [原則] 索引標籤上，選取省略符號並挑選管理群組、訂用帳戶或資源群組的組合，以設定要搜尋的範圍。
   
1. 使用 [新增原則定義] 按鈕，搜尋要匯出的物件範圍。 在開啟的側邊視窗中，選取要匯出的每個物件。 依據搜尋方塊或類型來篩選選取範圍。 選取要匯出的所有物件之後，請使用頁面底部的 [新增] 按鈕。

1. 針對每個選取的物件，選取原則定義所需的匯出選項，例如 [僅定義] 或 [定義和指派]。 然後選取 [檢閱 + 匯出] 索引標籤或 [下一步：檢閱 + 匯出] 按鈕 (位於頁面底部)。

   > [!NOTE]
   > 如果選擇 [定義和指派] 選項，則只會匯出在新增原則定義時，篩選準則所設範圍內的原則指派。

1. 在 [檢閱 + 匯出] 索引標籤上，檢查詳細資料是否相符，然後使用頁面底部的 [匯出] 按鈕。

1. 檢查您的 GitHub 存放庫、分支和「根層級資料夾」，您會看到選取的資源現在已匯出至您的原始檔控制。

Azure 原則資源會匯出到所選 GitHub 存放庫和「根層級資料夾」內的下列結構：

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

## <a name="export-with-azure-cli"></a>使用 Azure CLI 匯出

Azure 原則定義、計畫和指派可以使用 [Azure CLI](/cli/azure/install-azure-cli)匯出為 JSON。 每個命令都使用 **name** 參數來指定要取得 JSON 的物件。 **Name** 屬性通常是 _GUID_ ，而不是物件的 **displayName** 。

- 定義- [az 原則定義顯示](/cli/azure/policy/definition#az_policy_definition_show)
- 方案- [az 原則集-定義顯示](/cli/azure/policy/set-definition#az_policy_set_definition_show)
- 指派- [az policy 指派 show](/cli/azure/policy/assignment#az_policy_assignment_show)

以下是取得 **名稱** 為 _VirtualMachineStorage_ 之原則定義的 JSON 範例：

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>使用 Azure PowerShell 匯出

Azure 原則定義、計畫和指派可以使用 [Azure PowerShell](/powershell/azure/)匯出為 JSON。 這些 Cmdlet 都使用 **Name** 參數來指定要取得 JSON 的物件。 **Name** 屬性通常是 _GUID_ ，而不是物件的 **displayName** 。

- 定義- [取得->new-azpolicydefinition](/powershell/module/az.resources/get-azpolicydefinition)
- 計畫- [AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- 指派- [取得->new-azpolicyassignment](/powershell/module/az.resources/get-azpolicyassignment)

以下是取得 **名稱** 為 _VirtualMachineStorage_ 之原則定義的 JSON 範例：

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage' | ConvertTo-Json -Depth 10
```

## <a name="next-steps"></a>後續步驟

- 在 [Azure 原則範例](../samples/index.md)檢閱範例。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。
- 了解如何[以程式設計方式建立原則](programmatically-create.md)。
- 了解如何[補救不符合規範的資源](remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。
