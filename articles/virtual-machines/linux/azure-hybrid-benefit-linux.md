---
title: Azure Hybrid Benefit 和 Linux Vm
description: 此 Azure Hybrid Benefit 可讓您在 Azure 上執行的 Linux 虛擬機器上節省成本。
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 35bed58f95deebb78d8e787c8bc3f522ce7004df
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326964"
---
# <a name="azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Azure Hybrid Benefit –適用于 Linux 虛擬機器的方式

## <a name="overview"></a>概觀

Azure Hybrid Benefit 可讓您使用自己既有的 Red Hat 或 SUSE software 訂用帳戶，更輕鬆地將您的內部部署 Red Hat Enterprise Linux (RHEL) 和 SUSE Linux Enterprise Server (的 SLES) 虛擬機器 (Vm) 至 Azure。 有了這項權益，您只需支付 VM 的基礎結構成本，因為您的 RHEL 或 SLES 訂用帳戶涵蓋了軟體費用。 此權益適用于所有 RHEL 和 SLES Marketplace 隨用隨付 (PAYG) 映射。

> [!IMPORTANT]
> 適用于 Linux Vm 的 Azure Hybrid Benefit 現已開放公開使用


## <a name="benefit-description"></a>權益描述

透過 Azure Hybrid Benefit，您可以藉由將 Azure 上的現有 RHEL 和 SLES PAYG Vm 轉換成自備訂用帳戶 (BYOS) 帳單，更輕鬆地將內部部署 RHEL 和 SLES 伺服器遷移至 Azure。 一般而言，從 Azure 上的 PAYG 映射部署的 Vm 將會收取基礎結構費用和軟體費用。 使用 Azure Hybrid Benefit，PAYG Vm 可以轉換成 BYOS 計費模式，而不需重新部署，以避免任何停機風險。

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="在 Linux Vm 上 Azure Hybrid Benefit 成本視覺效果。":::

在 RHEL 或 SLES VM 上啟用權益時，您將不再需要支付 PAYG VM 上通常產生的額外軟體費用。 相反地，您的 VM 會開始發出 BYOS 費用，其中只包含計算硬體費用和無軟體費用。

如果您想要的話，也可以將已啟用權益的 VM 轉換回 PAYG 計費模型。

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Linux Vm 的 Azure Hybrid Benefit 資格範圍

Azure Hybrid Benefit 適用于所有 RHEL 和 SLES Marketplace PAYG 映射。 RHEL 或 SLES Marketplace BYOS 映射或自訂映射尚未提供此權益。

如果您已經使用 Linux Vm 的權益，保留實例、專用主機和 SQL 混合式權益不符合 Azure Hybrid Benefit 資格。

## <a name="how-to-get-started"></a>如何開始使用

### <a name="red-hat-customers"></a>Red Hat 客戶

適用于 RHEL 的 Azure Hybrid Benefit 適用于具有使用中/未使用的 RHEL 訂用帳戶，且該訂用帳戶可在 Azure 中使用，以及已啟用一或多個訂用帳戶，以在 Azure 中搭配 [Red Hat Cloud 存取](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 程式使用的客戶。 

1.  使用 [Red Hat Cloud Access 客戶介面](https://access.redhat.com/management/cloud)，啟用一或多個合格的 RHEL 訂用帳戶，以便在 Azure 中使用。
1.  您在 Red Hat Cloud Access 啟用程式期間提供的 Azure 訂用帳戶 () ，接著將允許使用 Azure Hybrid Benefit 功能。
1.  將 Azure Hybrid Benefit 套用至任何現有的 RHEL PAYG Vm，以及您從 Azure Marketplace PAYG 映射部署的任何新 RHEL Vm。
1.  遵循建議的 [後續步驟](https://access.redhat.com/articles/5419341) ，以設定 rhel vm 的更新來源和 rhel 訂用帳戶合規性指導方針。


### <a name="suse-customers"></a>SUSE 客戶

1.    向 SUSE Public Cloud 方案註冊
1.    透過 Azure CLI 將權益套用至現有的 Vm
1.    使用個別的更新來源來註冊您的 Vm 以接收權益


### <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>在 Azure CLI 中啟用和停用權益

您可以使用 ' az vm update ' 命令來更新現有的 Vm。 針對 RHEL Vm，請以 "RHEL_BYOS" 的--授權類型參數執行命令。 針對 SLES Vm，請使用 "SLES_BYOS" 的--授權類型參數執行命令。

#### <a name="cli-example-to-enable-the-benefit"></a>啟用權益的 CLI 範例：
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
#### <a name="cli-example-to-disable-the-benefit"></a>停用權益的 CLI 範例：
若要停用此權益，請使用「無」授權類型值
```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

#### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>在大量 Vm 上啟用權益的 CLI 範例
若要在大量的 Vm 上啟用權益，您可以 `--ids` 在 Azure CLI 中使用參數。

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file containing a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

下列範例顯示兩個取得資源識別碼清單的方法：一個在資源群組層級，一個在訂用帳戶層級。
```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="check-ahb-status-of-a-vm"></a>檢查 VM 的 AHB 狀態
您可以透過兩種方式來查看 VM 的 AHB 狀態：使用 Azure CLI，或使用 Azure Instance Metadata Service (Azure IMDS) 。


### <a name="azure-cli"></a>Azure CLI

`az vm get-instance-view`此命令可用於此用途。 在回應中尋找 licenseType 欄位。 如果 licenseType 欄位存在且值為「RHEL_BYOS」或「SLES_BYOS」，則您的 VM 已啟用權益。

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure 執行個體中繼資料服務

從 VM 本身中，您可以查詢 IMDS 證明中繼資料來判斷 VM 的 licenseType。 LicenseType 值 ' RHEL_BYOS ' 或 ' SLES_BYOS ' 會指出您的 VM 已啟用權益。 [在此](./instance-metadata-service.md#attested-data)深入瞭解證明中繼資料

## <a name="compliance"></a>法規遵循

### <a name="red-hat"></a>Red Hat

使用 RHEL Azure Hybrid Benefit 的客戶同意與 Azure Marketplace RHEL 供應專案相關聯的標準 [法律條款](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) 和 [隱私權聲明](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) 。

使用 RHEL Azure Hybrid Benefit 的客戶有三個選項可將軟體更新和修補程式提供給這些 Vm：

1.  [Red Hat 更新基礎結構 (RHUI) ](../workloads/redhat/redhat-rhui.md) (預設選項) 
1.  Red Hat 附屬伺服器
1.  Red Hat 訂用帳戶管理員

選擇 RHUI 選項的客戶可以繼續使用 RHUI 作為其 AHB RHEL Vm 的主要更新來源，而不需要將 RHEL 訂用帳戶附加至這些 Vm。  選擇 RHUI 選項的客戶必須負責確保 RHEL 訂用帳戶合規性。

選擇 Red Hat 附屬伺服器或 Red Hat 訂用帳戶管理員的客戶應該移除 RHUI 設定，然後將已啟用雲端存取的 RHEL 訂用帳戶附加至其 AHB RHEL VM (s) 。  

您可以在 [這裡](https://access.redhat.com/articles/5419341)找到有關 Red Hat 訂用帳戶合規性、軟體更新和 AHB RHEL vm 來源的詳細資訊。

### <a name="suse"></a>SUSE

若要將 Azure Hybrid Benefit 用於 SLES Vm，您必須先向 SUSE Public Cloud 方案註冊。 若要深入瞭解此程式，請參閱。 購買 SUSE 訂用帳戶之後，您必須使用 SUSE 客戶中心、訂用帳戶管理工具伺服器或 SUSE Manager，在您自己的更新來源使用這些訂用帳戶註冊您的 Vm。

## <a name="frequently-asked-questions"></a>常見問題集
*問：我是否可以使用「RHEL_BYOS」授權類型搭配 SLES 映射，反之亦然？*

答：不可以。 如果嘗試輸入不正確符合您 VM 上執行之發行版本的授權類型，將不會更新任何帳單中繼資料。 不過，如果您不小心輸入了錯誤的授權類型，則再次將 VM 更新為正確的授權類型仍會啟用權益。

*問：我已向 Red Hat Cloud Access 註冊，但仍無法在 RHEL Vm 上啟用權益。我該怎麼辦？*

答：您的 Red Hat Cloud Access 訂用帳戶註冊可能需要一些時間才能從 Red Hat 傳播至 Azure。 如果您在一個工作天之後仍看到錯誤，請洽詢 Microsoft 支援服務。

*問：我已使用 RHEL BYOS 「黃金映射」部署 VM。我可以將這些映射的帳單從 BYOS 轉換成 PAYG 嗎？*

答：不可以。 Azure Hybrid Benefit 僅支援隨用隨付映射的轉換。

*問：我已使用 RHEL BYOS 「黃金映射」部署 VM。我可以將這些映射的帳單從 BYOS 轉換成 PAYG 嗎？*

答：不可以。 Azure Hybrid Benefit 僅支援隨用隨付映射的轉換。

*問：我已從內部內部部署 (透過 Azure Migrate、ASR 或) 至 Azure，上傳了自己的 RHEL 映射。我可以將這些映射的帳單從 BYOS 轉換成 PAYG 嗎？*

答：不可以。 Azure Hybrid Benefit 功能目前僅適用于 RHEL 和 SLES Marketplace 映射。 

*問：我已從內部內部部署 (透過 Azure Migrate、ASR 或) 至 Azure，上傳了自己的 RHEL 映射。我是否需要進行任何動作，以 Azure Hybrid Benefit 的好處？*

答：否。 您上傳的 RHEL 映射已被視為 BYOS，而您只需支付 Azure 基礎結構成本。 您必須負責 RHEL 訂用帳戶的成本，就像您在內部部署環境中所做的一樣。 

*問：我可以在從 Marketplace RHEL 和 SLES SAP 映射部署的 Vm 上使用 Azure Hybrid Benefit 嗎？*

答：可以。 您可以使用 RHEL Vm 的「RHEL_BYOS」授權類型和「SLES_BYOS」來轉換從 Marketplace RHEL 和 SLES SAP 映射部署的 Vm。

*問：我可以在 Azure Hybrid Benefit 的虛擬機器擴展集上使用 (VMSS) 適用于 RHEL 和 SLES 嗎？*

答：否，您無法。 VMSS 目前不在 RHEL 和 SLES 的 Azure Hybrid Benefit 範圍內。

*問：我可以在適用于 RHEL 和 SLES (RIs) 的保留實例上使用 Azure Hybrid Benefit 嗎？*

答：否，您無法。 RIs 目前不在目前的 RHEL 和 SLES Azure Hybrid Benefit 範圍內。

*問：是否可以在針對 RHEL 映射部署 SQL Server 的虛擬機器上使用 Azure Hybrid Benefit？*

答：否，您無法。 沒有任何支援的方案。
 

## <a name="common-issues"></a>常見問題
本節包含可能遇到的常見問題清單，以及緩和措施的步驟。

| 錯誤 | 降低 |
| ----- | ---------- |
| 「無法完成動作，因為我們的記錄顯示您尚未成功啟用 Azure 訂用帳戶上的 Red Hat Cloud 存取 ...」。 | 若要使用 RHEL Vm 的優點，您必須先向 Red Hat Cloud Access 註冊您的 Azure 訂用帳戶 () 。 請流覽此連結，以深入瞭解如何註冊適用于 Red Hat Cloud Access 的 Azure 訂用帳戶

## <a name="next-steps"></a>後續步驟
* 瞭解如何建立和更新 Vm，並 (RHEL_BYOS、SLES_BYOS) Azure Hybrid Benefit[在這裡](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest&preserve-view=true)使用 Azure CLI 來新增授權類型。
