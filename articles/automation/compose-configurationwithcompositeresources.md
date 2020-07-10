---
title: 撰寫 DSC 設定
description: 本文說明如何在 Azure 自動化狀態設定中使用複合資源撰寫設定。
keywords: powershell dsc, desired state configuration, powershell dsc azure, composite resources, 需要的狀態設定, 複合資源
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 9c5524d2847f59dda1f2c24f67e1e18f18d49b1f
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185563"
---
# <a name="compose-dsc-configurations"></a>撰寫 DSC 設定

當您需要以多個預期狀態設定 (DSC) 來管理資源時，使用[複合資源](/powershell/scripting/dsc/resources/authoringresourcecomposite)將是最佳途徑。 複合資源是巢狀且參數化的設定，用來在另一個設定中當作 DSC 資源。 使用複合資源可讓您建立複雜的設定，同時允許以個別方式管理和建置基礎複合資源。

Azure 自動化能夠[匯入和編譯複合資源](automation-dsc-compile.md)。 將複合資源匯入到您的自動化帳戶之後，您就可以透過) 中的**狀態設定 (DSC Azure 入口網站**功能來使用 Azure 自動化狀態設定。

## <a name="compose-a-configuration"></a>撰寫設定

您必須先撰寫設定，才能在 Azure 入口網站中指派來自複合資源的設定。 在 [設定] 或 [已編譯設定] 索引標籤上，複合會使用 [狀態設定 (DSC)] 頁面上的 [撰寫設定]。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 依序按一下左側的 [所有資源] 和您的自動化帳戶名稱。
1. 在 [自動化帳戶] 頁面上，選取 [組態管理] 下方的 [State Configuration (DSC)]。
1. 在 [狀態設定 (DSC)] 頁面上，按一下 [設定] 或 [已編譯設定] 索引標籤，然後按一下頁面頂端功能表中的 [撰寫設定]。
1. 在 [基本] 步驟中，提供新的設定名稱 (必要)，然後在您要納入新設定之每個複合資源列上的任意位置按一下，然後按一下 [下一步] 或按一下 [原始程式碼] 步驟。 針對下列步驟，我們選取了 `PSExecutionPolicy` 和 `RenameAndDomainJoin` 複合資源。
   ![撰寫設定頁面之基本步驟的螢幕擷取畫面](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. **原始程式碼**步驟會顯示所選取之複合資源的已撰寫設定看起來會像怎樣。 您可以看到所有參數的合併，和它們如何傳遞給複合資源。 當您檢閱完新的原始程式碼之後，按一下 [下一步] 或按一下 [參數] 步驟。
   ![撰寫設定頁面之原始程式碼步驟的螢幕擷取畫面](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. 在 [參數] 步驟中，會公開每個複合資源的參數，以便提供值。 如果參數具有描述，則會顯示在參數欄位旁邊。 如果參數屬於 `PSCredential` 類型，則下拉式清單會提供目前自動化帳戶中的 [認證] 物件清單。 此外也可使用 [+ 新增認證] 選項。 一旦已提供所有必要參數，按一下 [儲存並編譯]。
   ![撰寫設定頁面之參數步驟的螢幕擷取畫面](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

## <a name="submit-the-configuration-for-compilation"></a>提交設定以進行編譯

新的設定儲存之後，便會提交以進行編譯。 您可以檢視編譯作業的狀態，就像已任何匯入的設定一樣。 如需詳細資訊，請參閱[檢視編譯作業](automation-dsc-getting-started.md#view-a-compilation-job)。

當編譯已順利完成時，新的設定會顯示在 [已編譯設定] 索引標籤中。接著，您可以使用[將節點重新指派至不同的節點組態](automation-dsc-getting-started.md#reassign-a-node-to-a-different-node-configuration)中的步驟，將設定指派給受控節點。

## <a name="next-steps"></a>後續步驟

- 若要了解如何啟用節點，請參閱[啟用 Azure Automation State Configuration](automation-dsc-onboarding.md)。
- 若要了解如何編譯 DSC 組態，以便可將其指派給目標節點，請參閱[編譯 Azure Automation State Configuration 中的 DSC 組態](automation-dsc-compile.md)。
- 如需在持續部署管道中使用 Azure Automation State Configuration 的範例，請參閱[使用 Chocolatey 設定持續部署](automation-dsc-cd-chocolatey.md)。
- 如需定價資訊，請參閱 [Azure Automation State Configuration 定價](https://azure.microsoft.com/pricing/details/automation/)。
- 如需 PowerShell Cmdlet 參考，請參閱 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
