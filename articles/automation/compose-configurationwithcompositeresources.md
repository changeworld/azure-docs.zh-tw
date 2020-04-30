---
title: 使用複合資源在 Azure 自動化狀態設定中撰寫 DSC 設定
description: 瞭解如何在 Azure 自動化狀態設定中使用複合資源撰寫設定。
keywords: powershell dsc, desired state configuration, powershell dsc azure, composite resources, 需要的狀態設定, 複合資源
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 7e7625ae95d5355ae1122a16ea4828eed5f78c73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682933"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-using-composite-resources"></a>使用複合資源在 Azure 自動化狀態設定中撰寫 DSC 設定

當您需要使用一個以上的預期狀態設定（DSC）來管理資源時，最佳的路徑是使用[複合資源](/powershell/scripting/dsc/resources/authoringresourcecomposite)。 複合資源是巢狀且參數化的設定，用來在另一個設定中當作 DSC 資源。 使用複合資源可讓您建立複雜的設定，同時允許個別管理和建立基礎複合資源。

Azure 自動化能夠[匯入和編譯複合資源](automation-dsc-compile.md)。 將複合資源匯入到您的自動化帳戶之後，您就可以透過 Azure 入口網站中的**狀態設定（DSC**功能）來使用 Azure 自動化狀態設定。

## <a name="composing-a-configuration-from-composite-resources"></a>從複合資源撰寫設定

您必須先撰寫設定，才可以從 Azure 入口網站中的複合資源指派設定。 撰寫會**在 [設定] 或 [** **已編譯**的設定] 索引標籤上使用 [狀態設定（DSC）] 頁面上的 [**撰寫**設定]。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 依序按一下左側的 [所有資源]**** 和您的自動化帳戶名稱。
1. 在 [自動化帳戶] 頁面上，選取 [組態管理]**** 之下的 [State Configuration (DSC)]****。
1. 在 [State configuration （DSC）] 頁面上，按一下 [設定 **] 或 [** **已編譯**的設定] 索引標籤，然後按一下頁面頂端功能表中的 [**撰寫**設定]。
1. 在 [基本]**** 步驟中，提供新的設定名稱 (必要)，然後在您要納入新設定之每個複合資源列上的任意位置按一下，然後按一下 [下一步]**** 或按一下 [原始程式碼]**** 步驟。 在下列步驟中，我們已`PSExecutionPolicy`選取`RenameAndDomainJoin`和複合資源。
   ![撰寫設定頁面之基本步驟的螢幕擷取畫面](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. **原始程式碼**步驟會顯示所選取之複合資源的已撰寫設定看起來會像怎樣。 您可以看到所有參數的合併，和它們如何傳遞給複合資源。 當您檢閱完新的原始程式碼之後，按一下 [下一步]**** 或按一下 [參數]**** 步驟。
   ![撰寫設定頁面之原始程式碼步驟的螢幕擷取畫面](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. 在**參數**步驟中，會公開每個複合資源的參數，以便提供值。 如果參數具有描述，則會顯示在參數欄位旁邊。 如果參數的`PSCredential`類型是，則下拉式清單會提供目前自動化帳戶中的**認證**物件清單。 也可以使用 [ **+ 新增認證**] 選項。 一旦已提供所有必要參數，按一下 [儲存並編譯]****。
   ![撰寫設定頁面之參數步驟的螢幕擷取畫面](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

新的設定儲存之後，便會提交以進行編譯。 就像任何已匯入的設定，您可以檢視編譯作業狀態。 如需詳細資訊，請參閱[檢視編譯作業](automation-dsc-getting-started.md#viewing-a-compilation-job)。

成功完成編譯時，新的設定會出現在 [**已編譯**的設定] 索引標籤中。接著，您可以使用將[節點重新指派至不同的節點](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration)設定中的步驟，將設定指派給受管理的節點。

## <a name="next-steps"></a>後續步驟

- 若要開始使用，請參閱[Azure 自動化狀態設定](automation-dsc-getting-started.md)使用者入門。
- 若要瞭解如何將節點上架，請參閱將[機器上架以進行管理，方法是 Azure 自動化狀態設定](automation-dsc-onboarding.md)。
- 若要瞭解如何編譯 DSC 設定，讓您可以將它們指派給目標節點，請參閱[在 Azure 自動化狀態設定中編譯](automation-dsc-compile.md)設定。
- 如需 PowerShell Cmdlet 參考，請參閱[Azure 自動化狀態設定 Cmdlet](/powershell/module/azurerm.automation/#automation)。
- 如需定價資訊，請參閱[Azure 自動化狀態設定定價](https://azure.microsoft.com/pricing/details/automation/)。
- 若要查看在持續部署管線中使用 Azure 自動化狀態設定的範例，請參閱[使用 Azure 自動化狀態設定和 Chocolatey 的持續部署](automation-dsc-cd-chocolatey.md)。
