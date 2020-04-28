---
title: 虛擬機器上的 Azure 機密計算解決方案
description: 瞭解虛擬機器上的 Azure 機密計算解決方案。
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: e574ac33e5f7da814c4bd813fc1c083c7cb4c2c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187880"
---
# <a name="solutions-on-azure-virtual-machines"></a>Azure 虛擬機器上的解決方案

本文說明如何部署 Azure 機密運算虛擬機器（Vm），以執行 intel[軟體防護延伸](https://software.intel.com/sgx)模組（intel SGX）支援的 intel 處理器。 

## <a name="azure-confidential-computing-vm-sizes"></a>Azure 機密計算 VM 大小

Azure 機密計算虛擬機器的設計，是為了在雲端中處理資料和程式碼時，保護其機密性和完整性 

[DCsv2 系列](../virtual-machines/dcv2-series.md)Vm 是最新且最新的機密計算大小系列。 這些 Vm 支援較大範圍的部署功能，相較于我們的 DC 系列 Vm，具有2倍的記憶體保護區頁面快取（EPC）和較大的大小選擇。 [DC 系列](../virtual-machines/sizes-previous-gen.md#preview-dc-series)vm 目前為預覽狀態，將會淘汰，而且不會包含在正式運作中。

遵循[快速入門教學](quick-create-marketplace.md)課程，開始透過 Microsoft 商業市場部署 DCSV2 系列 VM。

### <a name="current-available-sizes-and-regions"></a>目前可用的大小和區域

若要取得可用區域和可用性區域中所有正式推出之機密計算 VM 大小的清單，請在[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)中執行下列命令：

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" 
    --all 
    --output table
```

從2020年4月起，這些 Sku 適用于下欄區域和可用性區域：

```output
Name              Locations      AZ_a
----------------  -------------  ------
Standard_DC8_v2   eastus         2
Standard_DC1s_v2  eastus         2
Standard_DC2s_v2  eastus         2
Standard_DC4s_v2  eastus         2
Standard_DC8_v2   CanadaCentral
Standard_DC1s_v2  CanadaCentral
Standard_DC2s_v2  CanadaCentral
Standard_DC4s_v2  CanadaCentral
Standard_DC8_v2   uksouth        3
Standard_DC1s_v2  uksouth        3
Standard_DC2s_v2  uksouth        3
Standard_DC4s_v2  uksouth        3
Standard_DC8_v2   CentralUSEUAP
Standard_DC1s_v2  CentralUSEUAP
Standard_DC2s_v2  CentralUSEUAP
Standard_DC4s_v2  CentralUSEUAP
```

如需上述大小的詳細資訊，請執行下列命令：

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family']"
```

## <a name="deployment-considerations"></a>部署考量因素

遵循快速入門教學課程，在10分鐘內部署 DCsv2 系列虛擬機器。 

- **Azure 訂**用帳戶–若要部署機密計算 VM 實例，請考慮隨用隨付訂用帳戶或其他購買選項。 如果您使用[azure 免費帳戶](https://azure.microsoft.com/free/)，您將不會有適當數量的 azure 計算核心配額。

- **定價和區域可用性**-在[虛擬機器定價頁面](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)上尋找 DCsv2 系列 vm 的定價。 如需了解 Azure 區域中的可用性，請查看 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) 。


- **核心配額** – 您可能需要從預設值增加 Azure 訂用帳戶的核心配額。 您的訂用帳戶也可能會限制您可以在特定 VM 大小系列（包括 DCsv2 系列）中部署的核心數目。 若要要求增加配額，可免費[開啟線上客戶支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)。 請注意，預設限制會根據您的訂用帳戶類別而有所不同。

  > [!NOTE]
  > 如果您有大規模的容量需求，請連絡 Azure 支援。 Azure 配額為信用額度，而不是容量保證。 無論您的配額有多少，您只需針對您使用的核心付費。
  
- 重設**大小**–因為其專門的硬體，您只能調整相同大小系列內的機密計算實例大小。 例如，您只能將 DCsv2 系列 VM 的大小從一個 DCsv2 系列調整為另一個。 不支援從非機密計算大小調整為機密計算大小。  

- **映射**–若要在機密計算實例上提供 Intel 軟體防護延伸模組（intel SGX）支援，所有部署都必須在第2代映射上執行。 Azure 機密計算支援在 Ubuntu 18.04 Gen 2、Ubuntu 16.04 Gen 2 和 Windows Server 2016 Gen 2 上執行的工作負載。 請參閱[Azure 上第2代 vm 的支援](../virtual-machines/linux/generation-2.md)，以深入瞭解支援和不支援的案例。 

- **儲存體**-Azure 機密運算虛擬機器資料磁片和我們的暫時性 OS 磁片都在 NVMe 磁片上。 實例僅支援進階 SSD 和標準 SSD 磁片，而非 Ultra SSD 或標準 HDD。 虛擬機器大小**DC8_v2**不支援 Premium 儲存體。 

- **磁片加密**–機密計算實例目前不支援磁片加密。 

## <a name="high-availability-and-disaster-recovery-considerations"></a>高可用性和災害復原考量

使用 Azure 中的虛擬機器時，您必須負責執行高可用性和嚴重損壞修復解決方案，以避免任何停機時間。 

Azure 機密運算目前不支援透過可用性區域的區域冗余。 若要取得機密運算的最高可用性和冗余，請使用[可用性設定組](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)。 由於硬體限制，機密計算實例的可用性設定組最多隻能有10個更新網域。 

## <a name="deploying-via-an-azure-resource-manager-template"></a>透過 Azure Resource Manager 範本進行部署 

Azure Resource Manager 是 Azure 的部署和管理服務。 其提供管理層，可讓您建立、更新和刪除您 Azure 訂用帳戶中的資源。 您可以使用存取控制、鎖定和標記等管理功能，在部署後保護及組織您的資源。

若要了解 Azure Resource Manager 範本，請參閱[範本部署概觀](../azure-resource-manager/templates/overview.md)。

若要在 ARM 範本中部署 DCsv2 系列 VM，您將使用[虛擬機器資源](../virtual-machines/windows/template-description.md)。 您必須確保為**vmSize**和**imageReference**指定正確的屬性。

### <a name="vm-size"></a>VM 大小

在虛擬機器資源的 ARM 範本中指定下列其中一種大小。 這個字串會放在**屬性**中做為**vmSize** 。

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Gen2 OS 映射

在 [**屬性**] 下，您也必須參考**storageProfile**下的影像。 您的**imageReference**只能使用下列*其中一個*影像。

```json
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "16_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "16_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>後續步驟 

在本文中，您已瞭解建立機密計算虛擬機器時所需的資格和設定。 您現在可以前往 Azure Marketplace 來部署 DCsv2 系列 VM。

> [!div class="nextstepaction"]
> [在 Azure Marketplace 中部署 DCsv2 系列虛擬機器](quick-create-marketplace.md)