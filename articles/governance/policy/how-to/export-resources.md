---
title: 匯出 Azure 原則資源
description: 瞭解如何將 Azure 原則資源匯出到 GitHub，例如原則定義和原則指派。
ms.date: 09/30/2020
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 691e0a026c5f4f1a0a68c744ee81b1da8da9e70b
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777083"
---
# <a name="export-azure-policy-resources"></a>匯出 Azure 原則資源

本文提供如何匯出現有 Azure 原則資源的相關資訊。 匯出您的資源對備份很實用且建議使用，但也是您在進行雲端治理和將 [原則視為程式碼](../concepts/policy-as-code.md)的旅程中的重要步驟。 Azure 原則資源可以透過 [Azure 入口網站](#export-with-azure-portal)、 [Azure CLI](#export-with-azure-cli)、 [Azure PowerShell](#export-with-azure-powershell)及每個支援的 sdk 來匯出。

## <a name="export-with-azure-portal"></a>使用 Azure 入口網站匯出

若要從 Azure 入口網站匯出原則定義，請遵循下列步驟：

1. 藉由按一下 [所有服務] 然後搜尋並選取 [原則]，在 Azure 入口網站中啟動 Azure 原則服務。

1. 選取 Azure 原則頁面左側的 **定義** 。

1. 使用 [ **匯出定義** ] 按鈕，或選取原則定義資料列上的省略號，然後選取 [ **匯出定義**]。

1. 選取 [ **使用 GitHub 登入** ] 按鈕。 如果您尚未使用 GitHub 進行驗證以授權 Azure 原則匯出資源，請在開啟的新視窗中查看存取 [Github 動作](https://github.com/features/actions) 的需求，然後選取 [ **授權 AzureGitHubActions** ] 以繼續進行匯出流程。 完成後，新的視窗就會自行關閉。

1. 在 [ **基本** ] 索引標籤上，設定下列選項，然後選取頁面底部的 [ **原則** ] 索引標籤或 [ **下一步：原則** ] 按鈕。

   - 存放**庫篩選**：設定為_我的存放庫_，只查看您擁有的存放庫或_所有存放庫_，以查看您授與 GitHub 動作存取權的所有存放庫。
   - 存放**庫**：設定為您想要匯出 Azure 原則資源的存放庫。
   - **分支**：設定存放庫中的分支。 使用預設的分支是驗證更新的好方法，然後再合併到您的原始程式碼。
   - **目錄**：要匯出 Azure 原則資源的 _根層級資料夾_ 。 此目錄下的子資料夾會根據所匯出的資源來建立。

1. 在 [ **原則** ] 索引標籤上，選取省略號並挑選管理群組、訂用帳戶或資源群組的組合，以設定要搜尋的範圍。
   
1. 使用 [ **新增原則定義 (s]) ** 按鈕，在範圍中搜尋要匯出的物件。 在開啟的側邊視窗中，選取要匯出的每個物件。 依搜尋方塊或類型篩選選取範圍。 選取所有要匯出的物件之後，請使用頁面底部的 [ **新增** ] 按鈕。

1. 針對每個選取的物件，選取所需的匯出選項，例如 [ _僅限定義_ ] 或 [ _定義] 和 [指派] (s) _ 作為原則定義。 然後選取頁面底部的 [ **審核 + 匯出** ] 索引標籤或 [ **下一步：檢查 + 匯出** ] 按鈕。

   > [!NOTE]
   > 如果選擇 [選項 _定義] 和 [指派] (s) _ ，則只會匯出在加入原則定義時由篩選準則所設定之範圍內的原則指派。

1. 在 [ **審核 + 匯出** ] 索引標籤上，檢查詳細資料是否相符，然後使用頁面底部的 [ **匯出** ] 按鈕。

1. 檢查您的 GitHub 存放庫、分支和 _根層級資料夾_ ，以查看選取的資源現在已匯出至您的原始檔控制。

Azure 原則資源會匯出至所選 GitHub 存放庫和 _根層級資料夾_內的下列結構：

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

Azure 原則定義、計畫和指派可以使用 [Azure CLI](/cli/azure/install-azure-cli)匯出為 JSON。 每個命令都使用 **name** 參數來指定要取得 JSON 的物件。 **Name**屬性通常是_GUID_ ，而不是物件的**displayName** 。

- 定義- [az 原則定義顯示](/cli/azure/policy/definition#az-policy-definition-show)
- 方案- [az 原則集-定義顯示](/cli/azure/policy/set-definition#az-policy-set-definition-show)
- 指派- [az policy 指派 show](/cli/azure/policy/assignment#az-policy-assignment-show)

以下是取得 **名稱** 為 _VirtualMachineStorage_之原則定義的 JSON 範例：

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>使用 Azure PowerShell 匯出

Azure 原則定義、計畫和指派可以使用 [Azure PowerShell](/powershell/azure/)匯出為 JSON。 這些 Cmdlet 都使用 **Name** 參數來指定要取得 JSON 的物件。 **Name**屬性通常是_GUID_ ，而不是物件的**displayName** 。

- 定義- [取得->new-azpolicydefinition](/powershell/module/az.resources/get-azpolicydefinition)
- 計畫- [AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- 指派- [取得->new-azpolicyassignment](/powershell/module/az.resources/get-azpolicyassignment)

以下是取得 **名稱** 為 _VirtualMachineStorage_之原則定義的 JSON 範例：

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage'
```

## <a name="next-steps"></a>後續步驟

- 在 [Azure 原則範例](../samples/index.md)檢閱範例。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。
- 了解如何[以程式設計方式建立原則](programmatically-create.md)。
- 了解如何[補救不符合規範的資源](remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。
