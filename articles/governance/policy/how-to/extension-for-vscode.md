---
title: Visual Studio Code 的 Azure 原則延伸模組
description: 瞭解如何使用 Visual Studio Code 的 Azure 原則延伸模組來查閱 Resource Manager 別名。
ms.date: 03/07/2020
ms.topic: how-to
ms.openlocfilehash: 0c4e04cc352744fed1c7c2965f8096f0f05c2a50
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82182560"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>使用 Visual Studio Code 的 Azure 原則延伸模組

> 適用于 Azure 原則延伸模組版本**0.0.21**和更新版本

瞭解如何使用適用于 Visual Studio Code 的 Azure 原則延伸模組來查閱[別名](../concepts/definition-structure.md#aliases)，以及審查資源和原則。 首先，我們將說明如何在 Visual Studio Code 中安裝 Azure 原則延伸模組。 然後，我們將逐步解說如何查詢別名。

Visual Studio Code 的 Azure 原則擴充功能可以安裝在 Visual Studio Code 支援的所有平臺上。 這種支援包括 Windows、Linux 和 macOS。

> [!NOTE]
> 在 Azure 原則延伸模組中針對 Visual Studio Code 所看到的原則所做的變更，不會同步至 Azure。

## <a name="prerequisites"></a>Prerequisites

若要完成此文章中的步驟，將會需要下列項目：

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- [Visual Studio Code](https://code.visualstudio.com) \(英文\)。

## <a name="install-azure-policy-extension"></a>安裝 Azure 原則擴充功能

符合必要條件之後，您可以遵循下列步驟來安裝適用于 Visual Studio Code 的 Azure 原則延伸模組：

1. 開啟 Visual Studio Code。

1. 從功能表列中，移至 [**視圖** > **延伸**模組]。

1. 在 [搜尋] 方塊中，輸入**Azure 原則**。

1. 從搜尋結果中選取 [ **Azure 原則**]，然後選取 [**安裝**]。

1. 視需要選取 [**重載**]。

## <a name="set-the-azure-environment"></a>設定 Azure 環境

若是國家雲端使用者，請遵循下列步驟來先設定 Azure 環境：

1. 選取 [ **File\Preferences\Settings**]。

1. 搜尋下列字串： _Azure： Cloud_

1. 從清單中選取 [全國] 雲端：

   :::image type="content" source="../media/extension-for-vscode/set-default-azure-cloud-sign-in.png" alt-text="設定 Visual Studio Code 的預設 Azure 雲端登入" border="false":::

## <a name="connect-to-an-azure-account"></a>連接到 Azure 帳戶

若要評估資源和查閱別名，您必須連接到您的 Azure 帳戶。 請遵循下列步驟，從 Visual Studio Code 連線至 Azure：

1. 從 Azure 原則延伸模組或命令選擇區登入 Azure。

   - Azure 原則延伸模組

     從 Azure 原則延伸模組中，選取 [登**入 Azure**]。

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png" alt-text="Azure 原則延伸模組中 Visual Studio Code 的 Azure 雲端登入" border="false":::

   - 命令選擇區

     從功能表列中，移至 [**查看** > ] [**命令**選擇區]，然後輸入**Azure： Sign In**。

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png" alt-text="從命令選擇區進行 Visual Studio Code 的 Azure 雲端登入" border="false":::

1. 遵循登入指示來登入 Azure。 連線之後，您的 Azure 帳戶名稱會顯示在 [Visual Studio Code] 視窗底部的狀態列上。

## <a name="select-subscriptions"></a>選取訂用帳戶

當您第一次登入時，Azure 原則延伸模組只會載入預設的訂用帳戶資源和原則。 若要新增或移除訂閱以顯示資源和原則，請遵循下列步驟：

1. 從命令選擇區或視窗頁尾啟動 [訂用帳戶] 命令。

   - 命令選擇區： 

     從功能表列中，移至 [**查看** > ] [**命令**選擇區]，然後輸入**Azure： Select 訂閱**。

   - 視窗頁尾

     在畫面底部的視窗頁尾中，選取符合 Azure 的區段** \<：您的帳戶\>**。

1. 使用 [篩選] 方塊，依名稱快速尋找訂閱。 然後，檢查或移除每個訂用帳戶的檢查，以設定 Azure 原則擴充功能所顯示的訂閱。 完成新增或移除要顯示的訂閱時，請選取 **[確定]**。

## <a name="search-for-and-view-resources"></a>搜尋及查看資源

[Azure 原則] 延伸模組會依資源提供者和資源群組，**在 [資源] 窗格**中列出所選訂用帳戶中的資源。 Treeview 包含所選訂用帳戶或訂用帳戶層級內的下列資源群組：

- **資源提供者**
  - 具有資源的每個已註冊資源提供者，以及具有原則別名的相關子資源
- **資源群組**
  - 依其所在資源群組的所有資源

根據預設，此延伸模組會依據現有的資源和具有原則別名的資源來篩選「資源提供者」部分。 在 [**設定** > **延伸** > 模組]**Azure 原則**中變更此行為，以查看所有資源提供者而不進行篩選。

在單一訂用帳戶中具有數百或數千個資源的客戶，可能會偏好可搜尋的方式來尋找其資源。 Azure 原則延伸模組可讓您使用下列步驟來搜尋特定的資源：

1. 從 Azure 原則延伸模組或命令選擇區啟動搜尋介面。

   - Azure 原則延伸模組

     在 [Azure 原則] 延伸模組中，將滑鼠停留在 [**資源**] 面板上並選取省略號，然後選取 [**搜尋資源**]。

   - 命令選擇區：

     從功能表列中，移至 [**流覽** > ] [**命令**選擇區]，然後輸入**資源：搜尋資源**。

1. 如果選取了多個訂用帳戶以供顯示，請使用篩選來選取要搜尋的訂用帳戶。

1. 使用篩選器來選取要搜尋的資源群組，這是先前所選訂用帳戶的一部分。

1. 使用篩選器來選取要顯示的資源。 此篩選準則適用于資源名稱和資源類型。

## <a name="discover-aliases-for-resource-properties"></a>探索資源屬性的別名

選取資源時，不論是透過搜尋介面或在 treeview 中選取，Azure 原則延伸模組都會開啟代表該資源及其所有 Resource Manager 屬性值的 JSON 檔案。

資源開啟後，將滑鼠停留在 Resource Manager 屬性名稱或值時，就會顯示 Azure 原則別名（如果有的話）。 在此範例中，資源是`Microsoft.Compute/virtualMachines`資源類型，而**storageProfile. imageReference. 供應**專案屬性會停留在上方。 暫留會顯示相符的別名。

:::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Azure 原則延伸模組停留顯示 Resource Manager 屬性別名" border="false":::

## <a name="search-for-and-view-policies-and-assignments"></a>搜尋及查看原則和指派

Azure 原則延伸模組會列出原則類型和原則指派，做為選取要顯示在 [**原則**] 窗格中之訂用帳戶的 treeview。 在單一訂用帳戶中具有數百或數千個原則或指派的客戶，可能會偏好可搜尋的方式來尋找其原則或指派。 Azure 原則延伸模組可讓您使用下列步驟來搜尋特定的原則或指派：

1. 從 Azure 原則延伸模組或命令選擇區啟動搜尋介面。

   - Azure 原則延伸模組

     從 Azure 原則延伸模組，將滑鼠停留在 [**原則**] 面板上並選取省略號，然後選取 [**搜尋原則**]。

   - 命令選擇區：

     從功能表列中，移至 [**流覽** > ] [**命令**選擇區]，然後輸入**原則：搜尋原則**。

1. 如果選取了多個訂用帳戶以供顯示，請使用篩選來選取要搜尋的訂用帳戶。

1. 使用篩選器來選取要搜尋的原則類型或指派，這是先前所選訂用帳戶的一部分。

1. 使用篩選器來選取要顯示的原則。 篩選適用于原則定義或原則指派的_displayName_ 。

選取原則或指派時（不論是透過搜尋介面，還是在 treeview 中選取），Azure 原則延伸模組會開啟代表原則或指派及其所有 Resource Manager 屬性值的 JSON。 延伸模組可以驗證已開啟的 Azure 原則 JSON 架構。

## <a name="sign-out"></a>登出

從功能表列中，移至 [**查看** > ] [**命令**選擇區]，然後輸入**Azure： Sign Out**。

## <a name="next-steps"></a>後續步驟

- 如[Azure 原則範例](../samples/index.md)，請參閱範例。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。
- 瞭解如何以程式設計[方式建立原則](programmatically-create.md)。
- 瞭解如何[補救不符合規範的資源](remediate-resources.md)。
- 請參閱使用[Azure 管理群組來組織資源](../../management-groups/overview.md)的管理群組。