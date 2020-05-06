---
title: 部署 ISO 27001 ASE/SQL 工作負載藍圖範例
description: ISO 27001 App Service 環境/SQL Database 工作負載藍圖範例的部署步驟，包括藍圖成品參數的詳細資料。
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: 6b8f3b753f1dd8cfbc247a77f2004e3c4d3423bb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "75922569"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>部署 ISO 27001 App Service 環境/SQL Database 工作負載藍圖範例

若要部署 Azure 藍圖的 ISO 27001 App Service 環境/SQL Database 工作負載藍圖範例，您必須採取下列步驟：

> [!div class="checklist"]
> - 部署 [ISO 27001 共用服務](../iso27001-shared/index.md)藍圖範例
> - 從範例建立新的藍圖
> - 將您的範例複本標記為**已發佈**
> - 將您的藍圖複本指派給現有的訂用帳戶

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free)。

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>部署 ISO 27001 共用服務藍圖範例

必須先將 [ISO 27001 共用服務](../iso27001-shared/index.md)藍圖範例部署到目標訂用帳戶，才能部署此藍圖範例。 若未成功部署 ISO 27001 共用服務藍圖範例，此藍圖範例將會遺失基礎結構相依性，並在部署期間失敗。

> [!IMPORTANT]
> 此藍圖範例必須指派到與 [ISO 27001 共用服務](../iso27001-shared/index.md)藍圖範例相同的訂用帳戶內。

## <a name="create-blueprint-from-sample"></a>從範例建立藍圖

首先，若要實作藍圖範例，請使用範例作為起點，在您的環境中建立新藍圖。

1. 在左側窗格中選取 [所有服務]  。 搜尋並選取 [藍圖]  。

1. 在左側的 [快速入門]  頁面上，選取 [建立藍圖]  **下方的 [建立]** 按鈕。

1. 尋找 **ISO 27001：ASE/SQL 工作負載**藍圖範例 (位在 [其他範例]  底下)，然後選取 [使用此範例]  。

1. 輸入藍圖範例的 [基本資料]  ：

   - **藍圖名稱**：為您的 ISO 27001 ASE/SQL 工作負載藍圖範例複本提供名稱。
   - **定義位置**：使用省略符號，然後選取要作為範例複本儲存位置的管理群組。

1. 在頁面頂端選取 [成品]  索引標籤，或在頁面底部選取 **[下一步：成品]** 。

1. 檢閱構成此藍圖範例的成品清單。 許多成品都具有我們稍後會定義的參數。 當您檢閱完藍圖範例時，請選取 [儲存草稿]  。

## <a name="publish-the-sample-copy"></a>發佈範例複本

您的環境中現已建立了藍圖範例複本。 該複本會以**草稿**模式建立，而且必須先**發佈**，才能進行指派和部署。 您可以根據環境和需求來自訂藍圖範例複本，但是這樣的修改可能會使其不符合 ISO 27001 標準。

1. 在左側窗格中選取 [所有服務]  。 搜尋並選取 [藍圖]  。

1. 選取左側的 [藍圖定義]  頁面。 使用篩選來尋找您的藍圖範例複本，然後將其選取。

1. 選取頁面頂端的 [發佈藍圖]  。 在右側的新窗格中，提供藍圖範例複本的**版本**。 如果您稍後會進行修改，此屬性十分實用。 提供 [變更附註]  ，例如「First version published from the ISO 27001 blueprint sample」。 然後選取頁面底部的 [發佈]  。

## <a name="assign-the-sample-copy"></a>指派範例複本

成功**發佈**藍圖範例複本後，可以將藍圖定義指派給其所在管理群組中的訂用帳戶。 此步驟用於提供參數，以建立每個專屬的藍圖範例複本部署。

1. 在左側窗格中選取 [所有服務]  。 搜尋並選取 [藍圖]  。

1. 選取左側的 [藍圖定義]  頁面。 使用篩選來尋找您的藍圖範例複本，然後將其選取。

1. 選取藍圖定義頁面頂端的 [指派藍圖]  。

1. 提供用於指派藍圖的參數值：

   - 基本概念

     - **訂用帳戶**：在用來儲存藍圖範例複本的管理群組中，選取一或多個訂用帳戶。 如果您選取多個訂用帳戶，輸入的參數就會用來為每個訂用帳戶建立指派。
     - **指派名稱**：名稱會根據藍圖名稱預先填入。
       視需要變更，或保持原狀。
     - **位置**：選取要在其中建立受控識別的區域。 Azure 藍圖會使用此受控識別來部署指派的藍圖之中的所有成品。 若要深入了解，請參閱[適用於 Azure 資源的受控識別](../../../../active-directory/managed-identities-azure-resources/overview.md)。
     - **藍圖定義版本**：挑選**已發佈**版本的藍圖範例複本。

   - 鎖定指派

     為您的環境選取藍圖鎖定設定。 如需詳細資訊，請參閱[藍圖資源鎖定](../../concepts/resource-locking.md)。

   - 受控識別

     保留預設的 [系統指派]  受控識別選項。

   - 藍圖參數

     許多藍圖定義中的成品都會使用這一節定義的參數，藉此提供一致性。

     - **組織名稱**：輸入您組織的簡短名稱。 此屬性主要用於命名資源。
     - **共用服務訂用帳戶識別碼**：[ISO 27001 共用服務](../iso27001-shared/index.md)藍圖範例指派所在的訂用帳戶識別碼。
     - **預設子網路位址首碼**：虛擬網路預設子網路的 CIDR 標記法。
       預設值為 10.1.0.0/16  。
     - **工作負載位置**：決定要部署成品的位置。 某些位置無法使用部分服務。 成品部署之類的服務，可為要部署成品的位置提供參數選項。

   - 構件參數

     本節中定義的參數會套用至其定義所屬的成品。 這些參數是[動態參數](../../concepts/parameters.md#dynamic-parameters)，因為定義於藍圖指派期間。 如需完整清單或成品參數及其說明，請參閱[成品參數資料表](#artifact-parameters-table)。

1. 輸入所有參數後，選取頁面底部的 [指派]  。 藍圖指派會隨即建立，並且開始部署成品。 部署需要大約一小時的時間。 若要檢查部署的狀態，請開啟藍圖指派。

> [!WARNING]
> Azure 藍圖服務和內建藍圖範例皆是**免費**項目。 Azure 資源會[依據產品計價](https://azure.microsoft.com/pricing/)。 請使用[定價計算機](https://azure.microsoft.com/pricing/calculator/)來預估此藍圖範例所部署資源的執行成本。

## <a name="artifact-parameters-table"></a>成品參數資料表

下表提供藍圖成品參數的清單：

|成品名稱|成品類型|參數名稱|描述|
|-|-|-|-|
|Log Analytics 資源群組|資源群組|名稱|**已鎖定** - 串連**組織名稱**與 `-workload-log-rg` 來形成唯一的資源群組。|
|Log Analytics 資源群組|資源群組|Location|**已鎖定** - 使用藍圖參數。|
|Log Analytics 範本|Resource Manager 範本|服務層|設定 Log Analytics 工作區的服務層級。 預設值為「PerNode」  。|
|Log Analytics 範本|Resource Manager 範本|記錄保留天數|資料保留天數。 預設值為「365」  。|
|Log Analytics 範本|Resource Manager 範本|Location|用來建立 Log Analytics 工作區的區域。 預設值為「美國西部 2」  。|
|網路資源群組|資源群組|名稱|**已鎖定** - 串連**組織名稱**與 `-workload-net-rg` 來形成唯一的資源群組。|
|網路資源群組|資源群組|Location|**已鎖定** - 使用藍圖參數。|
|網路安全性群組範本|Resource Manager 範本|記錄保留天數|資料保留天數。 預設值為「365」  。|
|虛擬網路與路由表範本|Resource Manager 範本|Azure 防火牆私人 IP|設定 [Azure 防火牆](../../../../firewall/overview.md)的私人 IP。 應是 CIDR 標記法的一部份，定義於 _ISO 27001：共用服務_成品參數 **Azure 防火牆子網路位址首碼**。 預設值為 10.0.4.4  。|
|虛擬網路與路由表範本|Resource Manager 範本|共用服務訂用帳戶識別碼|用來啟用工作負載和共用服務間 VNET 對等互連的值。|
|虛擬網路與路由表範本|Resource Manager 範本|虛擬網路位址首碼|虛擬網路的 CIDR 標記法。 預設值為 10.1.0.0/16  。|
|虛擬網路與路由表範本|Resource Manager 範本|預設子網路位址首碼|虛擬網路預設子網路的 CIDR 標記法。 預設值為 10.1.0.0/16  。|
|虛擬網路與路由表範本|Resource Manager 範本|ADDS IP 位址|第一個 ADDS VM 的 IP 位址。 此值會作為自訂 VNET DNS。|
|金鑰保存庫資源群組|資源群組|名稱|**已鎖定** - 串連**組織名稱**與 `-workload-kv-rg` 來形成唯一的資源群組。|
|金鑰保存庫資源群組|資源群組|Location|**已鎖定** - 使用藍圖參數。|
|Key Vault 範本|Resource Manager 範本|AAD 物件識別碼|需要 Key Vault 執行個體存取權的帳戶所具有的 AAD 物件識別碼。 沒有預設值，且不能空白。 若要從 Azure 入口網站尋找此值，請在 [服務]  底下搜尋並選取 [使用者]。 使用 [名稱]  方塊來篩選帳戶名稱並選取該帳戶。 在 [使用者設定檔]  頁面上，選取 [物件識別碼]  旁的 [按一下以複製] 圖示。|
|Key Vault 範本|Resource Manager 範本|記錄保留天數|資料保留天數。 預設值為「365」  。|
|Key Vault 範本|Resource Manager 範本|Key Vault SKU|指定所建立 Key Vault 的 SKU。 預設值為「進階」  。|
|Key Vault 範本|Resource Manager 範本|Azure SQL Server 管理員使用者名稱|用於存取 Azure SQL Server 的使用者名稱。 必須符合 **Azure SQL Database 範本**中的相同屬性值。 預設值為「sql-admin-user」  。|
|Azure SQL Database 資源群組|資源群組|名稱|**已鎖定** - 串連**組織名稱**與 `-workload-azsql-rg` 來形成唯一的資源群組。|
|Azure SQL Database 資源群組|資源群組|Location|**已鎖定** - 使用藍圖參數。|
|Azure SQL Database 範本|Resource Manager 範本|Azure SQL Server 管理員使用者名稱|Azure SQL Server 的使用者名稱。 必須符合 **Key Vault 範本**中的相同屬性值。 預設值為「sql-admin-user」  。|
|Azure SQL Database 範本|Resource Manager 範本|Azure SQL Server 管理員密碼 (Key Vault 資源識別碼)|Key Vault 的資源識別碼。 使用「/subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv」，並將 `{subscriptionId}` 取代為訂用帳戶識別碼以及將 `{orgName}` 取代為**組織名稱**藍圖參數。|
|Azure SQL Database 範本|Resource Manager 範本|Azure SQL Server 管理員密碼 (Key Vault 祕密名稱)|SQL Server 管理員的使用者名稱。必須符合 **Key Vault 範本**屬性 **Azure SQL Server 管理員使用者名稱**中的值。|
|Azure SQL Database 範本|Resource Manager 範本|記錄保留天數|資料保留天數。 預設值為「365」  。|
|Azure SQL Database 範本|Resource Manager 範本|AAD 管理員物件識別碼|會指派為 Active Directory 管理員的使用者所具有的 AAD 物件識別碼。沒有預設值，且不能空白。 若要從 Azure 入口網站尋找此值，請在 [服務]  底下搜尋並選取 [使用者]。 使用 [名稱]  方塊來篩選帳戶名稱並選取該帳戶。 在 [使用者設定檔]  頁面上，選取 [物件識別碼]  旁的 [按一下以複製] 圖示。|
|Azure SQL Database 範本|Resource Manager 範本|AAD 管理員登入|Microsoft 帳戶 (例如 live.com 或 outlook.com) 目前不能設定為管理員。只有組織內的使用者和安全性群組可以設定為管理員。沒有預設值，且不能空白。 若要從 Azure 入口網站尋找此值，請在 [服務]  底下搜尋並選取 [使用者]。 使用 [名稱]  方塊來篩選帳戶名稱並選取該帳戶。 在 [使用者設定檔]  頁面上，複製 [使用者名稱]  。|
|App Service 環境資源群組|資源群組|名稱|**已鎖定** - 串連**組織名稱**與 `-workload-ase-rg` 來形成唯一的資源群組。|
|App Service 環境資源群組|資源群組|Location|**已鎖定** - 使用藍圖參數。|
|App Service 環境範本|Resource Manager 範本|網域名稱|範例所建立 Active Directory 的名稱。 預設值為「contoso.com」  。|
|App Service 環境範本|Resource Manager 範本|ASE 位置|App Service 環境位置。 預設值為「美國西部 2」  。|
|App Service 環境範本|Resource Manager 範本|應用程式閘道記錄保留天數|資料保留天數。 預設值為「365」  。|

## <a name="next-steps"></a>後續步驟

您已檢閱過 ISO 27001 App Service 環境/SQL Database 工作負載藍圖範例的部署步驟，接下來請瀏覽下列文章以了解架構和控制對應：

> [!div class="nextstepaction"]
> [ISO 27001 App Service 環境/SQL Database 工作負載藍圖 - 概觀](./index.md)
> [ISO 27001 App Service 環境/SQL Database 工作負載藍圖 - 控制項對應](./control-mapping.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。