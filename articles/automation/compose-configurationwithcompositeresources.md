---
title: 使用複合資源在 Azure 自動化狀態設定中組合 DSC 設定
description: 瞭解如何在 Azure 自動化狀態配置中使用複合資源組合配置配置配置。
keywords: powershell dsc, desired state configuration, powershell dsc azure, composite resources, 需要的狀態設定, 複合資源
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 7e7625ae95d5355ae1122a16ea4828eed5f78c73
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682933"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-using-composite-resources"></a>使用複合資源在 Azure 自動化狀態設定中組合 DSC 設定

當您需要使用多個所需的狀態設定 (DSC) 管理資源時,最佳路徑是使用[複合資源](/powershell/scripting/dsc/resources/authoringresourcecomposite)。 複合資源是巢狀且參數化的設定，用來在另一個設定中當作 DSC 資源。 使用複合資源可以創建複雜的配置,同時允許單獨管理和生成基礎復合資源。

Azure 自動化能夠[匯入和編譯複合資源](automation-dsc-compile.md)。 將複合資源導入自動化帳戶後,可以通過**狀態配置(Azure**門戶中的 DSC 功能)使用 Azure 自動化狀態配置。

## <a name="composing-a-configuration-from-composite-resources"></a>從複合資源撰寫設定

在分配由 Azure 門戶中的複合資源組成的配置之前,必須編寫配置。 合成在「**設定**」或 **「已編譯設定**」選項卡上使用狀態配置 (DSC) 頁上的**合成**配置。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 依序按一下左側的 [所有資源]**** 和您的自動化帳戶名稱。
1. 在 [自動化帳戶] 頁面上，選取 [組態管理]**** 之下的 [State Configuration (DSC)]****。
1. 在「狀態配置 」(DSC)」頁上,按下「**設定**」或 **「已編譯設定」** 選項卡,然後單擊頁面頂部功能表中的 **「撰寫配置**」 。
1. 在 [基本]**** 步驟中，提供新的設定名稱 (必要)，然後在您要納入新設定之每個複合資源列上的任意位置按一下，然後按一下 [下一步]**** 或按一下 [原始程式碼]**** 步驟。 對於以下步驟,我們選擇了`PSExecutionPolicy``RenameAndDomainJoin`和復合資源。
   ![撰寫設定頁面之基本步驟的螢幕擷取畫面](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. **原始程式碼**步驟會顯示所選取之複合資源的已撰寫設定看起來會像怎樣。 您可以看到所有參數的合併，和它們如何傳遞給複合資源。 當您檢閱完新的原始程式碼之後，按一下 [下一步]**** 或按一下 [參數]**** 步驟。
   ![撰寫設定頁面之原始程式碼步驟的螢幕擷取畫面](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. 在**參數**步驟上,將公開每個複合資源的參數,以便提供值。 如果參數具有描述，則會顯示在參數欄位旁邊。 如果參數的類型`PSCredential`,下拉清單提供當前自動化帳戶中的**憑據**物件清單。 A **= 添加認證的**選項也可用。 一旦已提供所有必要參數，按一下 [儲存並編譯]****。
   ![撰寫設定頁面之參數步驟的螢幕擷取畫面](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

新的設定儲存之後，便會提交以進行編譯。 就像任何已匯入的設定，您可以檢視編譯作業狀態。 如需詳細資訊，請參閱[檢視編譯作業](automation-dsc-getting-started.md#viewing-a-compilation-job)。

成功編譯後,新設定將顯示在 **「已編譯設定」選項卡中**。然後,您可以使用將[節點重新分配給其他節點配置](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration)中的步驟將配置分配給託管節點。

## <a name="next-steps"></a>後續步驟

- 要開始,請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)。
- 要瞭解如何載入節點,請參閱[載入電腦來進行 Azure 自動化狀態設定進行管理](automation-dsc-onboarding.md)。
- 要瞭解如何編譯 DSC 設定以便將它們分配給目標節點,請參閱[在 Azure 自動化狀態設定中編譯設定](automation-dsc-compile.md)。
- 有關 PowerShell cmdlet 引用,請參閱[Azure 自動化狀態設定 cmdlet](/powershell/module/azurerm.automation/#automation)。
- 有關定價資訊,請參閱[Azure 自動化狀態設定定價](https://azure.microsoft.com/pricing/details/automation/)。
- 要檢視在連續部署導管中使用 Azure 自動化狀態設定的範例,請參考[Azure 自動化狀態設定進行持續部署和巧克力](automation-dsc-cd-chocolatey.md)。
