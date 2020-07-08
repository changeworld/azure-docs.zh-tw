---
title: 使用 Azure 入口網站部署資源
description: 使用 Azure 入口網站和 Azure 資源管理，將您的資源部署至訂用帳戶中的資源群組。
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 31f80eb617820def871633dac1541c7dc3bed691
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255257"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>使用 ARM 範本和 Azure 入口網站部署資源

瞭解如何使用[Azure 入口網站](https://portal.azure.com)搭配[Azure Resource Manager （ARM）範本](overview.md)來部署您的 Azure 資源。 若要瞭解如何管理您的資源，請參閱[使用 Azure 入口網站來管理 Azure 資源](../management/manage-resources-portal.md)。

使用 Azure 入口網站部署 Azure 資源通常包含兩個步驟：

- 建立資源群組。
- 將資源部署至資源群組。

此外，您也可以部署 ARM 範本來建立 Azure 資源。

本文章示範這兩種方法。

## <a name="create-a-resource-group"></a>建立資源群組

1. 若要建立新的資源群組，請從 [ [Azure 入口網站](https://portal.azure.com)中選取 [**資源群組**]。

   ![選取資源群組](./media/deploy-portal/select-resource-groups.png)

1. 在 [資源群組] 底下，選取 [**新增**]。

   ![新增資源群組](./media/deploy-portal/add-resource-group.png)

1. 選取或輸入下列屬性值：

    - 訂用帳戶：選取 Azure 訂用帳戶。
    - **資源群組**：為資源群組指定名稱。
    - **區域**：指定 Azure 位置。 這是資源群組用來儲存資源相關中繼資料的位置。 為了符合法規，您可能會想要指定中繼資料的儲存位置。 一般情況下，我們建議您指定大部分資源所在的位置。 使用相同位置可簡化範本。

   ![設定群組值](./media/deploy-portal/set-group-properties.png)

1. 選取 [檢閱 + 建立]。
1. 檢查值，然後選取 [**建立**]。
1. 選取 **[** 重新整理]，您才可以在清單中看到新的資源群組。

## <a name="deploy-resources-to-a-resource-group"></a>將資源部署至資源群組

建立資源群組之後，您可以從 Marketplace 將資源部署到群組。 Marketplace 針對常見的案例提供預先定義的解決方案。

1. 若要開始部署，請從 [ [Azure 入口網站](https://portal.azure.com)中選取 [**建立資源**]。

   ![新增資源](./media/deploy-portal/new-resources.png)

1. 尋找您想要部署的資源類型。 這些資源會組織成類別目錄。 如果沒有看到要部署的特定解決方案，您可以在 Marketplace 搜尋。 下列螢幕擷取畫面顯示已選取 [Ubuntu Server]。

   ![選取 [資源類型]](./media/deploy-portal/select-resource-type.png)

1. 根據所選資源的類型，您必須在部署前設定相關的屬性集合。 對於所有類型，您必須選取目的地資源群組。 下圖顯示如何建立 Linux 虛擬機器，並將其部署至您所建立的資源群組。

   ![建立資源群組](./media/deploy-portal/select-existing-group.png)

   或者，您也可以決定在部署資源時建立資源群組。 選取 [新建] **** 並提供資源群組的名稱。

1. 您的部署隨即開始。 部署可能需要幾分鐘的時間。 有些資源比其他資源需要更長的時間。 部署完成後，您就會看到通知。 選取 [**前往資源**] 以開啟

   ![檢視通知](./media/deploy-portal/view-notification.png)

1. 部署您的資源之後，您可以選取 [新增]****，將更多資源新增至資源群組。

   ![新增資源](./media/deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>從自訂範本部署資源

如果您想要執行部署，但不使用 Marketplace 中的任何範本，您可建立自訂範本以定義您的解決方案的基礎結構。 若要瞭解如何建立範本，請參閱[瞭解 ARM 範本的結構和語法](template-syntax.md)。

> [!NOTE]
> 入口網站介面不支援參考 [Key Vault 中的祕密](key-vault-parameter.md)。 請改用 [PowerShell](deploy-powershell.md) 或 [Azure CLI](deploy-cli.md)，在本機或從外部 URI 部署您的範本。

1. 若要透過入口網站部署自訂範本，請選取 [**建立資源**]、[搜尋**範本**]。 然後選取 [**範本部署**]。

   ![搜尋範本部署](./media/deploy-portal/search-template.png)

1. 選取 [建立]。
1. 您會看到數個建立範本的選項：

    - **在編輯器中建立您自己的範本**：使用入口網站範本編輯器建立範本。  編輯器能夠加入資源範本架構。
    - **通用範本**：有四個常用的範本，用來建立 Linux 虛擬機器、Windows 虛擬機器、web 應用程式，以及 Azure SQL Database 中的資料庫。
    - **載入 GitHub 快速入門範本**：使用現有的[快速入門範本](https://azure.microsoft.com/resources/templates/)。

   ![檢視選項](./media/deploy-portal/see-options.png)

    本教學課程提供載入快速入門範本的指示。

1. 在 [**載入 GitHub 快速入門範本**] 底下，輸入或選取 [ **101-儲存體-帳戶-建立**]。

    您有兩個選擇：

    - **選取範本**：部署範本。
    - **編輯範本**：在部署快速入門範本之前，先加以編輯。

1. 選取 [**編輯範本**] 以流覽入口網站範本編輯器。 範本會在編輯器中載入。 請注意，有兩個參數： [ **storageAccountType** ] 和 [ **location**]。

   ![建立範本](./media/deploy-portal/show-json.png)

1. 對範本進行較小的變更。 例如，將**storageAccountName**變數更新為：

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. 選取 [儲存]。 現在您會看到入口網站範本部署介面。 請注意您在範本中定義的兩個參數。
1. 輸入或選取屬性值：

    - 訂用帳戶：選取 Azure 訂用帳戶。
    - **資源群組**：選取 [**新建**] 並指定名稱。
    - **位置**：選取 Azure 位置。
    - **儲存體帳戶類型**：使用預設值。
    - **位置**：使用預設值。
    - **我同意上方所述的條款及條件**：（選取）

1. 選取 [購買]。

## <a name="next-steps"></a>後續步驟

- 若要檢視稽核記錄，請參閱 [使用 Resource Manager 來稽核作業](../management/view-activity-logs.md)。
- 若要針對部署錯誤進行疑難排解，請參閱[檢視部署作業](deployment-history.md)。
- 若要從部署或資源群組匯出範本，請參閱[匯出 ARM 範本](export-template-portal.md)。
- 若要安全地在多個區域推出您的服務，請參閱[Azure Deployment Manager](deployment-manager-overview.md)。
