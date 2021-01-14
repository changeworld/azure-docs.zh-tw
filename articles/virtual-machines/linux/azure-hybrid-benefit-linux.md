---
title: Azure Hybrid Benefit 和 Linux Vm
description: 瞭解 Azure Hybrid Benefit 如何協助您在 Azure 上執行的 Linux 虛擬機器上節省成本。
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 990c396c859477c83e0ef421659a299920ee0c35
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209129"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>Linux 虛擬機器的 Azure Hybrid Benefit 套用方式

Azure Hybrid Benefit 是一項授權權益，可協助您大幅降低執行 Red Hat Enterprise Linux (RHEL) 和 SUSE Linux Enterprise Server (在雲端中) Vm (虛擬機器的成本。 有了這項權益，您只需支付 VM 的基礎結構成本，因為您的 RHEL 或 SLES 訂用帳戶涵蓋了軟體費用。 此權益適用于所有 RHEL 和 SLES Marketplace 隨用隨付 (PAYG) 映射。

適用于 Linux Vm 的 Azure Hybrid Benefit 現已公開推出。

## <a name="benefit-description"></a>權益描述

透過 Azure Hybrid Benefit，您可以藉由將 Azure 上的現有 RHEL 和 SLES PAYG Vm 轉換成自備訂用帳戶 (BYOS) 帳單，將內部部署 RHEL 和 SLES 伺服器遷移至 Azure。 一般而言，從 Azure 上的 PAYG 映射部署的 Vm 將會收取基礎結構費用和軟體費用。 使用 Azure Hybrid Benefit，PAYG Vm 可以轉換成 BYOS 計費模式，而不需要重新部署，因此您可以避免任何停機風險。

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="在 Linux Vm 上 Azure Hybrid Benefit 成本視覺效果。":::

在 RHEL 或 SLES VM 上啟用權益之後，您將不再需要支付 PAYG VM 上通常產生的額外軟體費用。 相反地，您的 VM 會開始產生 BYOS 費用，其中只包含計算硬體費用和無軟體費用。

您也可以選擇將已啟用權益的 VM 轉換回 PAYG 計費模型。

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Linux Vm 的 Azure Hybrid Benefit 資格範圍

Azure Hybrid Benefit 適用于 Azure Marketplace 的所有 RHEL 和 SLES PAYG 映射。 Azure Marketplace 的 RHEL 或 SLES BYOS 映射或自訂映射尚未提供權益。

如果您已經使用 Linux Vm 的權益，保留實例、Azure 專用主機實例和 SQL 混合式權益不符合 Azure Hybrid Benefit 資格。

## <a name="get-started"></a>開始使用

### <a name="red-hat-customers"></a>Red Hat 客戶

適用于 RHEL 的 Azure Hybrid Benefit 適用于符合這兩個準則的 Red Hat 客戶：

- 具有適用于 Azure 的有效或未使用 RHEL 訂用帳戶
- 已啟用一或多個訂用帳戶，以在 Azure 中搭配 [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 方案使用

> [!IMPORTANT]
> 確定已在 [雲端存取](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 程式上啟用正確的訂用帳戶。

若要開始使用 Red Hat 的權益：

1. 使用 [Red Hat Cloud Access 客戶介面](https://access.redhat.com/management/cloud)啟用一或多個合格的 RHEL 訂用帳戶，以便在 Azure 中使用。

   接著，您在 Red Hat Cloud Access 啟用程式中提供的 Azure 訂用帳戶將允許使用 Azure Hybrid Benefit 功能。
1. 將 Azure Hybrid Benefit 套用至任何現有的 RHEL PAYG Vm，以及您從 Azure Marketplace PAYG 映射部署的任何新 RHEL Vm。 您可以使用 Azure 入口網站或 Azure CLI 來啟用權益。
1. 遵循建議的 [後續步驟](https://access.redhat.com/articles/5419341) ，以設定 rhel vm 的更新來源和 rhel 訂用帳戶合規性指導方針。


### <a name="suse-customers"></a>SUSE 客戶

若要開始使用 SUSE 的權益：

1. 向 SUSE Public Cloud 方案註冊。
1. 透過 Azure 入口網站或 Azure CLI，將權益套用至新建立或現有的 Vm。
1. 使用個別的更新來源來註冊接收權益的 Vm。

## <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>在 Azure 入口網站中啟用和停用權益

您可以流覽左側的 [設定] 選項，並遵循 **此處的步驟** ，在現有的 vm 上啟用權益。 在 VM 建立體驗期間，您可能會在新的 Vm 上啟用權益。

### <a name="azure-portal-example-to-enable-the-benefit-for-an-existing-vm"></a>為現有的 VM 啟用權益的 Azure 入口網站範例：
1. 造訪 [Microsoft Azure 入口網站](https://portal.azure.com/)
1. 移至入口網站上的 [建立虛擬機器] 頁面。
 ![建立 VM 時 AHB](./media/azure-hybrid-benefit/create-vm-ahb.png)
1. 按一下核取方塊以啟用 AHB 轉換，並使用雲端存取授權。
 ![建立 VM 時 AHB 核取方塊](./media/azure-hybrid-benefit/create-vm-ahb-checkbox.png)
1. 遵循下一組指示來建立虛擬機器
1. 檢查設定 **分頁，您** 將會看到已啟用的選項。 
![建立之後的 AHB 設定 blade](./media/azure-hybrid-benefit/create-configuration-blade.png)

### <a name="azure-portal-example-to-enable-the-benefit-during-creation-of-vm"></a>在 VM 建立期間啟用權益的 Azure 入口網站範例：
1. 造訪 [Microsoft Azure 入口網站](https://portal.azure.com/)
1. 開啟您想要套用轉換的虛擬機器頁面。
1. 移至左側的 [設定 **] 選項。** 您將會看到 [授權] 區段。 若要啟用 AHB 轉換，請核取 [是] 選項按鈕，然後核取 [確認] 核取方塊。
![建立之後的 AHB 設定 blade](./media/azure-hybrid-benefit/create-configuration-blade.png)


>[!NOTE]
> 如果您已 (RHEL 或 SLES PAYG Marketplace 映射的 SIG) 建立 **自訂快照** 或 **共用映射** ，則只能使用 Azure CLI 來啟用 Azure Hybrid Benefit。 這是已知的限制，而且目前沒有可在 Azure 入口網站上提供這項功能的時程表。



## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>在 Azure CLI 中啟用和停用權益

您可以使用 `az vm update` 命令來更新現有的 vm。 針對 RHEL Vm，請使用的參數執行命令 `--license-type` `RHEL_BYOS` 。 針對 SLES Vm，請使用的參數執行命令 `--license-type` `SLES_BYOS` 。

### <a name="cli-example-to-enable-the-benefit"></a>啟用權益的 CLI 範例
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>停用權益的 CLI 範例
若要停用此權益，請使用 `--license-type` `None` 下列值：

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>在大量 Vm 上啟用權益的 CLI 範例
若要在大量的 Vm 上啟用權益，您可以 `--ids` 在 Azure CLI 中使用參數：

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

下列範例示範兩種取得資源識別碼清單的方法：一個在資源群組層級，另一個是訂用帳戶層級。

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>在 VM 建立時間套用 Azure Hybrid Benefit
除了將 Azure Hybrid Benefit 套用至現有的隨用隨付 Vm，您也可以在建立 VM 時加以叫用。 這樣做的優點如下 threefold：
- 您可以使用相同的映射和處理常式來布建 PAYG 和 BYOS Vm。
- 它可讓未來的授權模式變更、未提供 BYOS 映射的內容，或您自備 VM。
- 根據預設，VM 會連線到 Red Hat 更新基礎結構 (RHUI) ，以確保其維持在最新狀態且安全。 您可以隨時在部署後變更更新的機制。

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>檢查 VM 的 Azure Hybrid Benefit 狀態
您可以使用 Azure CLI 或使用 Azure Instance Metadata Service 來查看 VM 的 Azure Hybrid Benefit 狀態。

### <a name="azure-cli"></a>Azure CLI

您可以 `az vm get-instance-view` 針對此用途使用命令。 尋找 `licenseType` 回應中的欄位。 如果 `licenseType` 欄位存在且值為 `RHEL_BYOS` 或，則 `SLES_BYOS` 您的 VM 已啟用權益。

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure 執行個體中繼資料服務

從 VM 本身中，您可以在 Azure Instance Metadata Service 中查詢證明中繼資料，以判斷 VM 的 `licenseType` 值。 `licenseType`或的值 `RHEL_BYOS` `SLES_BYOS` 會指出您的 VM 已啟用權益。 [深入瞭解證明中繼資料](./instance-metadata-service.md#attested-data)。

## <a name="compliance"></a>合規性

### <a name="red-hat"></a>Red Hat

使用 RHEL Azure Hybrid Benefit 的客戶，同意與 Azure Marketplace RHEL 供應專案相關聯的標準 [法律條款](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) 和 [隱私權聲明](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) 。

使用 RHEL Azure Hybrid Benefit 的客戶有三個選項可將軟體更新和修補程式提供給這些 Vm：

- [Red Hat 更新基礎結構](../workloads/redhat/redhat-rhui.md) (預設選項) 
- Red Hat 附屬伺服器
- Red Hat 訂用帳戶管理員

選擇 RHUI 選項的客戶可以繼續使用 RHUI 作為其 Azure Hybrid Benefit RHEL Vm 的主要更新來源，而不需要將 RHEL 訂用帳戶附加至這些 Vm。 選擇 RHUI 選項的客戶必須負責確保 RHEL 訂用帳戶合規性。

選擇 Red Hat 附屬伺服器或 Red Hat 訂用帳戶管理員的客戶應該移除 RHUI 設定，然後將已啟用雲端存取的 RHEL 訂用帳戶附加至其 Azure Hybrid Benefit RHEL Vm。  

如需有關 Azure Hybrid Benefit RHEL Vm 的 Red Hat 訂用帳戶合規性、軟體更新和來源的詳細資訊，請參閱 [關於使用 RHEL 訂用帳戶搭配 Azure Hybrid Benefit 的 Red hat 文章](https://access.redhat.com/articles/5419341)。

### <a name="suse"></a>SUSE

若要針對 SLES Vm 使用 Azure Hybrid Benefit，以及從 SLES PAYG 移至 BYOS 或從 SLES BYOS 移至 PAYG 的相關資訊，請參閱 [SUSE Linux Enterprise 和 Azure Hybrid Benefit](https://www.suse.com/c/suse-linux-enterprise-and-azure-hybrid-benefit/)。 

## <a name="frequently-asked-questions"></a>常見問題集
*問：我是否可以使用 `RHEL_BYOS` 具有 SLES 映射的授權類型，反之亦然？*

答：否，您不可以。 嘗試輸入不正確符合您 VM 上執行之散發套件的授權類型，將不會更新任何帳單中繼資料。 但是，如果您不小心輸入了錯誤的授權類型，則再次將 VM 更新為正確的授權類型仍會啟用權益。

*問：我已向 Red Hat Cloud Access 註冊，但仍無法在 RHEL Vm 上啟用權益。我該怎麼做？*

答：您的 Red Hat Cloud Access 訂用帳戶註冊可能需要一些時間才能從 Red Hat 傳播至 Azure。 如果您在一個工作天之後仍看到錯誤，請洽詢 Microsoft 支援服務。

*問：我已使用 RHEL BYOS 「黃金映射」部署 VM。我可以將這些映射的帳單從 BYOS 轉換成 PAYG 嗎？*

答：否，您不可以。 Azure Hybrid Benefit 僅支援隨用隨付映射的轉換。

*問：我已經從內部部署 (透過 Azure Migrate、Azure Site Recovery 或) 將自己的 RHEL 映射上傳至 Azure。我可以將這些映射的帳單從 BYOS 轉換成 PAYG 嗎？*

答：否，您不可以。 Azure Hybrid Benefit 功能目前僅適用于 Azure Marketplace 中的 RHEL 和 SLES 映射。 

*問：我已經從內部部署 (透過 Azure Migrate、Azure Site Recovery 或) 將自己的 RHEL 映射上傳至 Azure。我是否需要進行任何動作，以 Azure Hybrid Benefit 的好處？*

答：否，您沒有。 您上傳的 RHEL 映射已被視為 BYOS，而您只需支付 Azure 基礎結構成本的費用。 您必須負責 RHEL 訂用帳戶成本，就像您在內部部署環境中所做的一樣。 

*問：我可以在部署自 Azure Marketplace RHEL 和 SLES SAP 映射的 Vm 上使用 Azure Hybrid Benefit 嗎？*

答：可以。 您可以使用 RHEL Vm 的授權類型 `RHEL_BYOS` ，以及 `SLES_BYOS` 從 Azure Marketplace RHEL 和 SLES SAP 映射部署的 vm 的轉換。

*問：我可以在 RHEL 和 SLES 的虛擬機器擴展集上使用 Azure Hybrid Benefit 嗎？*

答：否，您不可以。 虛擬機器擴展集目前不在 RHEL 和 SLES 的 Azure Hybrid Benefit 範圍內。

*問：我可以在 RHEL 和 SLES 的保留實例上使用 Azure Hybrid Benefit 嗎？*

答：否，您不可以。 保留實例目前不在 RHEL 和 SLES 的 Azure Hybrid Benefit 範圍內。

*問：是否可以在針對 RHEL 映射部署 SQL Server 的虛擬機器上使用 Azure Hybrid Benefit？*

答：否，您不可以。 沒有任何計畫可支援這些虛擬機器。

*問：我可以在 RHEL 虛擬資料中心訂用帳戶上使用 Azure Hybrid Benefit 嗎？*

答：否，您無法。 Azure 上不支援 VDC （包括 AHB）。  
 

## <a name="common-problems"></a>常見問題
此區段會列出您可能會遇到的常見問題以及緩和措施的步驟。

| 錯誤 | 降低 |
| ----- | ---------- |
| 「無法完成動作，因為我們的記錄顯示您尚未成功啟用 Azure 訂用帳戶上的 Red Hat Cloud 存取 ...」。 | 若要使用 RHEL Vm 的優點，您必須先 [向 Red Hat Cloud Access 註冊您的 Azure 訂用](https://access.redhat.com/management/cloud)帳戶。

## <a name="next-steps"></a>後續步驟
* [瞭解如何使用 Azure Hybrid Benefit 來建立和更新 Vm，以及新增授權類型 (RHEL_BYOS、SLES_BYOS) Azure CLI](/cli/azure/vm?preserve-view=true&view=azure-cli-latest)