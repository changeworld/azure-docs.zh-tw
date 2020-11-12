---
title: 部署 ISO 27001 共用服務藍圖範例
description: ISO 27001 共用服務藍圖範例的部署步驟，其中包括藍圖成品參數的詳細資料。
ms.date: 11/02/2020
ms.topic: sample
ms.openlocfilehash: acce9ecf906430047fa1e687f719fd4e04daf756
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420270"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>部署 ISO 27001 共用服務藍圖範例

若要部署 Azure 藍圖 ISO 27001 共用服務藍圖範例，必須採取下列步驟：

> [!div class="checklist"]
> - 從範例建立新的藍圖
> - 將您的範例複本標記為 **已發佈**
> - 將您的藍圖複本指派給現有的訂用帳戶

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free)。

## <a name="create-blueprint-from-sample"></a>從範例建立藍圖

首先，若要實作藍圖範例，請使用範例作為起點，在您的環境中建立新藍圖。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 在左側的 [快速入門] 頁面上，選取 [建立藍圖] **下方的 [建立]** 按鈕。

1. 尋找 **ISO 27001：共用服務** 藍圖範例 (位在 [其他範例] 底下)，然後選取 [使用此範例]。

1. 輸入藍圖範例的 [基本資料]：

   - **藍圖名稱** ：為您的 ISO 27001 共用服務藍本範例複本提供名稱。
   - **定義位置** ：使用省略符號，然後選取要作為範例複本儲存位置的管理群組。

1. 在頁面頂端選取 [成品]索引標籤，或在頁面底部選取 **[下一步：成品]** 。

1. 檢閱構成此藍圖範例的成品清單。 許多成品都具有我們稍後會定義的參數。 當您檢閱完藍圖範例時，請選取 [儲存草稿]。

## <a name="publish-the-sample-copy"></a>發佈範例複本

您的環境中現已建立了藍圖範例複本。 該複本會以 **草稿** 模式建立，而且必須先 **發佈** ，才能進行指派和部署。 您可以根據環境和需求來自訂藍圖範例複本，但是這樣的修改可能會使其不符合 ISO 27001 標準。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 選取左側的 [藍圖定義] 頁面。 使用篩選來尋找您的藍圖範例複本，然後將其選取。

1. 選取頁面頂端的 [發佈藍圖]。 在右側的新窗格中，提供藍圖範例複本的 **版本** 。 如果您稍後會進行修改，此屬性十分實用。 提供 [變更附註]，例如「First version published from the ISO 27001 blueprint sample」。 然後選取頁面底部的 [發佈]。

## <a name="assign-the-sample-copy"></a>指派範例複本

成功 **發佈** 藍圖範例複本後，可以將藍圖定義指派給其所在管理群組中的訂用帳戶。 此步驟用於提供參數，以建立每個專屬的藍圖範例複本部署。

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 選取左側的 [藍圖定義] 頁面。 使用篩選來尋找您的藍圖範例複本，然後將其選取。

1. 選取藍圖定義頁面頂端的 [指派藍圖]。

1. 提供用於指派藍圖的參數值：

   - 基本概念

     - **訂用帳戶** ：在用來儲存藍圖範例複本的管理群組中，選取一或多個訂用帳戶。 如果您選取多個訂用帳戶，輸入的參數就會用來為每個訂用帳戶建立指派。
     - **指派名稱** ：名稱會根據藍圖名稱預先填入。
       視需要變更，或保持原狀。
     - **位置** ：選取要在其中建立受控識別的區域。 Azure 藍圖會使用此受控識別來部署指派的藍圖之中的所有成品。 若要深入了解，請參閱[適用於 Azure 資源的受控識別](../../../../active-directory/managed-identities-azure-resources/overview.md)。
     - **藍圖定義版本** ：挑選 **已發佈** 版本的藍圖範例複本。

   - 鎖定指派

     為您的環境選取藍圖鎖定設定。 如需詳細資訊，請參閱[藍圖資源鎖定](../../concepts/resource-locking.md)。

   - 受控識別

     保留預設的 [系統指派] 受控識別選項。

   - 藍圖參數

     許多藍圖定義中的成品都會使用這一節定義的參數，藉此提供一致性。

     - **組織名稱** ：輸入您組織的簡短名稱。 此屬性主要用於命名資源。
     - **共用服務的子網路位址首碼** ：提供 CIDR 標記法的值，透過網路將所部署的資源連結在一起。
     - **共用服務位置** ：決定要部署成品的位置。 某些位置無法使用部分服務。 成品部署之類的服務，可為要部署成品的位置提供參數選項。
     - **允許的位置 (原則：ISO 27001 的藍圖方案)** ：指出資源群組和資源可用的位置值。
     - **VM 代理程式的 Log Analytics 工作區 (原則：ISO 27001 的藍圖方案)** ：指定工作區的資源識別碼。 此參數會使用 `concat` 函式來建構資源識別碼。

   - 構件參數

     本節中定義的參數會套用至其定義所屬的成品。 這些參數是[動態參數](../../concepts/parameters.md#dynamic-parameters)，因為定義於藍圖指派期間。 如需完整清單或成品參數及其說明，請參閱[成品參數資料表](#artifact-parameters-table)。

1. 輸入所有參數後，選取頁面底部的 [指派]。 藍圖指派會隨即建立，並且開始部署成品。 部署需要大約一小時的時間。 若要檢查部署的狀態，請開啟藍圖指派。

> [!WARNING]
> Azure 藍圖服務和內建藍圖範例皆是 **免費** 項目。 Azure 資源會[依據產品計價](https://azure.microsoft.com/pricing/)。 請使用[定價計算機](https://azure.microsoft.com/pricing/calculator/)來預估此藍圖範例所部署資源的執行成本。

## <a name="artifact-parameters-table"></a>成品參數資料表

下表提供藍圖成品參數的清單：

|成品名稱|成品類型|參數名稱|描述|
|-|-|-|-|
|\[預覽\]：為 Linux VM 擴展集 (VMSS) 部署 Log Analytics 代理程式|原則指派|選擇性：要新增至範圍的 VM 映像清單，這些映像具有支援的 Linux OS|(選用) 預設值為 _["none"]_ 。|
|\[預覽\]：部署適用於 Linux VM 的 Log Analytics 代理程式|原則指派|選擇性：要新增至範圍的 VM 映像清單，這些映像具有支援的 Linux OS|(選用) 預設值為 _["none"]_ 。|
|\[預覽\]：為 Windows VM 擴展集 (VMSS) 部署 Log Analytics 代理程式|原則指派|選擇性：要新增至範圍的 VM 映像清單，這些映像具有支援的 Windows OS|(選用) 預設值為 _["none"]_ 。|
|\[預覽\]：部署適用於 Windows VM 的 Log Analytics 代理程式|原則指派|選擇性：要新增至範圍的 VM 映像清單，這些映像具有支援的 Windows OS|(選用) 預設值為 _["none"]_ 。|
|允許的資源類型|原則指派|允許的資源類型|可部署的資源類型清單。 這份清單包含部署在共用服務中的所有資源類型。|
|允許的儲存體帳戶 SKU|原則指派|允許的儲存體 SKU|允許的診斷記錄儲存體帳戶 SKU 清單。 預設值是 ["Standard_LRS"]。|
|允許的虛擬機器 SKU|原則指派|允許部署的虛擬機器 SKU 清單。 預設值是 ["Standard_DS1_v2", "Standard_DS2_v2"]。|
|ISO 27001 的藍圖方案|原則指派|用於稽核診斷記錄的資源類型|診斷記錄設定未啟用時，可稽核的資源類型清單。 您可以在 [Azure 監視器診斷記錄結構描述](../../../../azure-monitor/platform/resource-logs-schema.md#service-specific-schemas)中找到可用值。|
|Log Analytics 資源群組|資源群組|名稱|**已鎖定** - 串連 **組織名稱** 與 `-sharedsvsc-log-rg` 來形成唯一的資源群組。|
|Log Analytics 資源群組|資源群組|Location|**已鎖定** - 使用藍圖參數。|
|Log Analytics 範本|Resource Manager 範本|服務層|設定 Log Analytics 工作區的服務層級。 預設值為「PerNode」。|
|Log Analytics 範本|Resource Manager 範本|記錄保留天數|資料保留天數。 預設值為「365」。|
|Log Analytics 範本|Resource Manager 範本|Location|用來建立 Log Analytics 工作區的區域。 預設值為「美國西部 2」。|
|網路資源群組|資源群組|名稱|**已鎖定** - 串連 **組織名稱** 與 `-sharedsvcs-net-rg` 來形成唯一的資源群組。|
|網路資源群組|資源群組|Location|**已鎖定** - 使用藍圖參數。|
|Azure 防火牆範本|Resource Manager 範本|Azure 防火牆私人 IP|設定 [Azure 防火牆](../../../../firewall/overview.md)的私人 IP。 此值也會用來當作共用服務子網路上的預設路由表。 應該是 **Azure 防火牆子網路位址首碼** 中所定義 CIDR 標記法的一部分。 預設值為 10.0.4.4。|
|Azure 防火牆範本|Resource Manager 範本|記錄保留天數|資料保留天數。 預設值為「365」。|
|網路安全性群組範本|Resource Manager 範本|記錄保留天數|資料保留天數。 預設值為「365」。|
|虛擬網路與路由表範本|Resource Manager 範本|虛擬網路位址首碼|虛擬網路的 CIDR 標記法。 預設值為 10.0.0.0/16。|
|虛擬網路與路由表範本|Resource Manager 範本|啟用虛擬網路 DDoS 保護|為虛擬網路設定 DDoS 保護。 預設值為 true。|
|虛擬網路與路由表範本|Resource Manager 範本|共用服務的子網路位址首碼|共用服務子網路的 CIDR 標記法。 預設值為 10.0.0.0/24。|
|虛擬網路與路由表範本|Resource Manager 範本|DMZ 子網路位址首碼|DMZ 子網路的 CIDR 標記法。 預設值為 10.0.1.0/24。|
|虛擬網路與路由表範本|Resource Manager 範本|應用程式閘道子網路位址首碼|應用程式閘道子網路的 CIDR 標記法。 預設值為 10.0.2.0/24。|
|虛擬網路與路由表範本|Resource Manager 範本|虛擬網路閘道子網路位址首碼|虛擬網路閘道子網路的 CIDR 標記法。 預設值為 10.0.3.0/24。|
|虛擬網路與路由表範本|Resource Manager 範本|Azure 防火牆子網路位址首碼|[Azure 防火牆](../../../../firewall/overview.md)子網路的 CIDR 標記法。 應該包含 **Azure 防火牆私人 IP** 參數。|
|金鑰保存庫資源群組|資源群組|名稱|**已鎖定** - 串連 **組織名稱** 與 `-sharedsvcs-kv-rg` 來形成唯一的資源群組。|
|金鑰保存庫資源群組|資源群組|Location|**已鎖定** - 使用藍圖參數。|
|Key Vault 範本|Resource Manager 範本|Jumpbox 管理使用者名稱|Jumpbox 的使用者名稱。 必須符合 **Jumpbox 範本** 中的相同屬性值。 預設值是 jb-admin-user。|
|Key Vault 範本|Resource Manager 範本|Jumpbox 管理員的 SSH 金鑰或密碼|Jumpbox 上的帳戶金鑰或密碼。 必須符合 **Jumpbox 範本** 中的相同屬性值。 沒有預設值，且不能空白。|
|Key Vault 範本|Resource Manager 範本|網域管理使用者名稱|用來存取 Active Directory VM，以及將其他 VM 加入網域的使用者名稱。 必須符合 **Active Directory 網域服務範本** 中的 **網域管理使用者** 屬性值。 預設值是 domain-admin-user。|
|Key Vault 範本|Resource Manager 範本|網域管理員密碼|網域管理使用者的密碼。 沒有預設值，且不能空白。|
|Key Vault 範本|Resource Manager 範本|AAD 物件識別碼|需要 Key Vault 執行個體存取權的帳戶所具有的 AAD 物件識別碼。 沒有預設值，且不能空白。 若要從 Azure 入口網站尋找此值，請在 [服務] 底下搜尋並選取 [使用者]。 使用 [名稱] 方塊來篩選帳戶名稱並選取該帳戶。 在 [使用者設定檔] 頁面上，選取 [物件識別碼] 旁的 [按一下以複製] 圖示。  |
|Key Vault 範本|Resource Manager 範本|記錄保留天數|資料保留天數。 預設值為「365」。|
|Key Vault 範本|Resource Manager 範本|Key Vault SKU|指定所建立 Key Vault 的 SKU。 預設值為「進階」。|
|Jumpbox 資源群組|資源群組|名稱|**已鎖定** - 串連 **組織名稱** 與 `-sharedsvcs-jb-rg` 來形成唯一的資源群組。|
|Jumpbox 資源群組|資源群組|Location|**已鎖定** - 使用藍圖參數。|
|Jumpbox 範本|Resource Manager 範本|Jumpbox 管理使用者名稱|用以存取 jumpbox VM 的使用者名稱。 必須符合 **Key Vault 範本** 中的相同屬性值。 預設值是 jb-admin-user。|
|Jumpbox 範本|Resource Manager 範本|Jumpbox 管理員密碼 (Key Vault 資源識別碼)|Key Vault 的資源識別碼。 使用 "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv"，並將 `{subscriptionId}` 取代為訂用帳戶識別碼以及將 `{orgName}` 取代為 **組織名稱** 藍圖參數。|
|Jumpbox 範本|Resource Manager 範本|Jumpbox 管理員密碼 (Key Vault 祕密名稱)|Jumpbox 管理員的使用者名稱。必須符合 **Key Vault 範本** 屬性 **Jumpbox 管理使用者名稱** 中的值。|
|Jumpbox 範本|Resource Manager 範本|Jumpbox 作業系統|判斷 Jumpbox VM 的作業系統。 預設值為 Windows。|
|Active Directory Domain Services 資源群組|資源群組|名稱|**已鎖定** - 串連 **組織名稱** 與 `-sharedsvcs-adds-rg` 來形成唯一的資源群組。|
|Active Directory Domain Services 資源群組|資源群組|Location|**已鎖定** - 使用藍圖參數。|
|Active Directory Domain Services 範本|Resource Manager 範本|網域管理使用者名稱|ADDS Jumpbox 的使用者名稱。 必須符合 **Key Vault 範本** 中的相同屬性值。 預設值是 adds-admin-user。|
|Active Directory Domain Services 範本|Resource Manager 範本|網域管理員密碼 (Key Vault 資源識別碼)|Key Vault 的資源識別碼。 使用 "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv"，並將 `{subscriptionId}` 取代為訂用帳戶識別碼以及將 `{orgName}` 取代為 **組織名稱** 藍圖參數。|
|Active Directory Domain Services 範本|Resource Manager 範本|網域管理員密碼 (Key Vault 祕密名稱)|網域管理員的使用者名稱。必須符合 **Key Vault 範本** 屬性 **網域管理使用者名稱** 中的值。|
|Active Directory Domain Services 範本|Resource Manager 範本|網域名稱|範例所建立 Active Directory 的名稱。 預設值為「contoso.com」。|
|Active Directory Domain Services 範本|Resource Manager 範本|網域管理使用者|用於管理員 AD 帳戶，以及用於將裝置加入 AD 網域的使用者名稱。 必須符合 **Key Vault 範本** 中的 **AD 管理使用者名稱** 屬性。 預設值是 domain-admin-user。|
|Active Directory Domain Services 範本|Resource Manager 範本|網域管理員密碼|設定用來儲存密碼的 Key Vault 詳細資料。 沒有預設值，且不能空白。|

## <a name="next-steps"></a>後續步驟

您已檢閱過 ISO 27001 共用服務藍圖範例的部署步驟，接下來請瀏覽下列文章以了解架構和控制對應：

> [!div class="nextstepaction"]
> [ISO 27001 共用服務藍圖 - 概觀](./index.md)
> [ISO 27001 共用服務藍圖 - 控制對應](./control-mapping.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。
