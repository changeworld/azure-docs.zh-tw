---
title: Azure 中的 Red Hat Enterprise Linux 映像 | Microsoft Docs
description: 瞭解 Microsoft Azure 中 Red Hat Enterprise Linux 映射。
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 7913cb888e1799efae0f3ecdf3391d19736cc273
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970158"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux 映射的總覽

本文說明 Azure Marketplace 中的可用 Red Hat Enterprise Linux （RHEL）映射，以及其命名和保留期的原則。

如需所有 RHEL 版本的 Red Hat 支援原則資訊，請參閱[Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)。 如需定價詳細資料，請參閱[Azure 定價計算機](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。

>[!IMPORTANT]
> Azure Marketplace 中目前提供的 RHEL 映射支援自備訂用帳戶（BYOS）或隨用隨付授權模型。 不支援 BYOS 與隨用隨付授權之間的[Azure 混合式使用權益](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)和動態切換。 若要切換授權模式，您必須從對應的映射重新部署 VM。

>[!NOTE]
> 如有任何與 Azure Marketplace 中 RHEL 映射相關的問題，請向 Microsoft 提出支援票證。

## <a name="view-images-available-in-azure"></a>查看 Azure 中可用的映射

當您在 Azure Marketplace 中搜尋「Red Hat」，或在 Azure 入口網站 UI 中建立資源時，您只會看到所有可用 RHEL 映射的子集。 您一律可以使用 Azure CLI、PowerShell 和 API，取得一組完整的可用 VM 映射。

若要查看 Azure 中的一組完整可用 Red Hat 映射，請執行下列命令：

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>命名慣例

Azure 中的 VM 映射是依發行者、供應專案、SKU 和版本進行組織。 「發行者:供應項目:SKU:版本」的組合便是映像 URN，並能唯一識別要使用的映像。

例如，`RedHat:RHEL:7-LVM:7.6.2018103108` 指的是 RHEL 7.6 LVM-分割的映射，建置於2018年10月31日。

如需如何建立 RHEL 7.6 VM 的範例，請參閱這裡。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>"Latest" (最新) Moniker

Azure REST API 允許針對版本使用「最新」的「名字」，而不是特定版本。 使用「最新」會為指定的發行者、供應專案和 SKU 布建最新的可用映射。

例如，`RedHat:RHEL:7-LVM:latest` 指的是最新的 RHEL 7 系列 LVM-可用的資料分割映射。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:latest --no-wait
```

>[!NOTE]
> 一般而言，比較版本以決定最新版本的程序會遵循 [CompareTo 方法](https://msdn.microsoft.com/library/a5ts8tb6.aspx)來進行。
這個映射版本比較是藉由比較值為[版本](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8)物件（而不是字串）來完成。

## <a name="rhel-6-image-types"></a>RHEL 6 映射類型

針對 RHEL 6.x 映射，映射類型如下表所示。

|發行者 | 供應項目 | SKU 值 | 版本 | 詳細資料
|----------|-------|-----------|---------|--------
|RedHat | RHEL | 次要版本（例如，6.9） | RHEL 次要版本的串連值和發佈的日期（例如，6.9.2018010506） | 所有標準的 RHEL 6.x 映射都會遵循此慣例。
|RedHat | rhel-byos | rhel-raw69 | RHEL 次要版本的串連值和發佈的日期（例如，6.9.20181023） | 此映射是 RHEL 6.9 BYOS 映射。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 次要版本的串連值和發佈的日期（例如，6.8.2017053118） | 此映射是適用于 SAP 應用程式的 RHEL 6.8 映射。 其有權存取 SAP 應用程式存放庫和基礎 RHEL 存放庫。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 次要版本的串連值和發佈的日期（例如，6.7.2017053121） | 此映射是適用于 SAP Hana 映射的 RHEL 6.7。 其有權存取 SAP Hana 存放庫和基礎 RHEL 存放庫。

## <a name="rhel-7-image-types"></a>RHEL 7 映射類型

RHEL 7.x 映射有幾種不同的映射類型。 下表顯示我們所提供的不同影像集。 若要查看完整清單，請使用 Azure CLI 命令 `az vm image list --publisher redhat --all`。

>[!NOTE]
> 除非另有指示，否則所有映射都是 LVM 分割並聯機到一般 RHEL 存放庫。 也就是說，存放庫不是擴充更新支援（EUS），也不是適用于 SAP 的更新服務（E4S）。 接下來，我們只是為了發佈 LVM 分割的映射，而是開啟以提供有關此決策的意見反應。 如需有關適用于 SAP 的擴充更新支援和更新服務的詳細資訊，請參閱[Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)。

|發行者 | 供應項目 | SKU 值 | 版本 | 詳細資料
|----------|-------|------------|---------|--------
|RedHat | RHEL | 次要版本（例如，7.6） | RHEL 次要版本的串連值和發佈的日期（例如，7.6.2019102813） | 2019年4月之前發佈的映射會附加至標準 RHEL 存放庫。 2019年4月之後發佈的映射會附加至 Red Hat 的 EUS 存放庫，以允許特定次要版本的版本鎖定。 需要一般存放庫的客戶應該使用 SKU 值中包含 7-LVM 或7個原始的映射（詳細資料請遵循）。 RHEL 7.7 和更新版本的映射為 LVM 資料分割。 此類別中的所有其他影像都是原始資料分割。
|RedHat | RHEL | 7-RAW | RHEL 次要版本的串連值和發佈的日期（例如，7.6.2019102813） | 這些映射是未經處理的資料分割（例如，未新增任何邏輯磁片區）。
|RedHat | RHEL | 7-RAW-CI | RHEL 次要版本的串連值和發佈的日期（例如，7.6.2019072418） | 這些映射是未經處理的資料分割（例如，未新增邏輯磁片區），並使用雲端 init 進行布建。
|RedHat | RHEL | 7-LVM | RHEL 次要版本的串連值和發佈的日期（例如，7.6.2019062414） | 這些映射是已分割的 LVM。
|RedHat | rhel-byos | rhel-{lvm，raw} | RHEL 次要版本的串連值和發佈的日期（例如，7.7.20190819） | 這些映射是 RHEL 7 BYOS 映射。 它們不會附加至任何存放庫，也不會收取 RHEL premium 費用。 如果您對 RHEL BYOS 映射感興趣，請[要求存取權](https://aka.ms/rhel-byos)。 SKU 值的結尾是次要版本，並指出映射是未經處理的還是 LVM 分割的。 例如，lvm77 的 SKU 值表示 LVM 分割的 RHEL 7.7 映射。
|RedHat | RHEL | RHEL-SAP | RHEL 次要版本的串連值和發佈的日期（例如，7.6.2019071300） | 這些映射是 RHEL for SAP 映射。 他們有權存取 SAP Hana 和應用程式存放庫，以及 RHEL E4S 存放庫。 計費包括以基本計算費用為基礎的 RHEL premium 和 SAP premium。
|RedHat | RHEL | RHEL-SAP-HA | RHEL 次要版本的串連值和發佈的日期（例如，7.6.2019062320） | 這些映射是 RHEL for SAP，具有高可用性和更新服務映射。 他們有權存取 SAP Hana 和應用程式存放庫，以及高可用性存放庫，以及 RHEL E4S 存放庫。 計費會在基礎計算費用上包含 RHEL premium、SAP premium 和高可用性 premium。
|RedHat | RHEL | RHEL-HA | RHEL 次要版本的串連值和發佈的日期（例如，7.6.2019062019） | 這些映射是也有權存取高可用性附加元件的 RHEL 映射。 因為高可用性附加元件 premium，它們會在 RHEL 和基礎計算費用上稍微額外收費。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 次要版本的串連值和發佈的日期（例如，7.3.2017053118） | 這些映射已過期，因為 SAP 應用程式和 SAP Hana 存放庫已合併至 SAP 存放庫。 這些映射是 RHEL for SAP Applications 映射。 他們有權存取 SAP 應用程式存放庫和基礎 RHEL 存放庫。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 次要版本的串連值和發佈的日期（例如，7.3.2018051421） | 這些映射已過期，因為 SAP 應用程式和 SAP Hana 存放庫已合併至 SAP 存放庫。 這些映射是 RHEL，用於 SAP Hana 映射。 他們有權存取 SAP Hana 存放庫和基礎 RHEL 存放庫。

## <a name="rhel-8-image-types"></a>RHEL 8 映射類型

RHEL 8 映射類型的詳細資料如下。

|發行者 | 供應項目 | SKU 值 | 版本 | 詳細資料
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | RHEL 次要版本的串連值和發佈的日期（例如，8.0.20191023） | 這些映射是連接到標準 Red Hat 存放庫的 RHEL 8.0 LVM 分割映射。
|RedHat | RHEL | 8-gen2 | RHEL 次要版本的串連值和發佈的日期（例如，8.0.20191024） | 這些映射是連接到標準 Red Hat 存放庫的 Hyper-v 第2代 RHEL 8.0 LVM 分割的映射。 如需 Azure 中第2代 Vm 的詳細資訊，請參閱[azure 上第2代 vm 的支援](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)。

## <a name="rhel-longer-support-add-ons"></a>RHEL 長期支援附加元件

### <a name="extended-update-support"></a>擴充更新支援

從2019年4月起，預設會附加至 EUS 存放庫的 RHEL 映射可用。 有關 RHEL EUS 的詳細資訊可在[Red Hat 檔](https://access.redhat.com/articles/rhel-eus)中取得。

您可以切換到 EUS 存放庫，而且受到支援。 如需有關如何將您的 VM 切換至 EUS 的指示，以及有關 EUS 支援生命循環結束日期的詳細資訊，請參閱[RHEL EUS 和版本鎖定的 Rhel vm](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)。

>[!NOTE]
> RHEL 額外專案不支援 EUS。 這表示如果您安裝的套件通常可從 RHEL 額外通道取得，則在 EUS 時將無法執行此動作。 如需 Red Hat 額外產品生命週期的詳細資訊，請參閱[Red Hat Enterprise Linux 額外的生命週期](https://access.redhat.com/support/policy/updates/extras/)。

#### <a name="differentiate-between-regular-and-eus-images"></a>區分一般和 EUS 影像

若客戶想要使用附加至 EUS 存放庫的映射，則應使用 SKU 中包含 RHEL 次要版本號碼的 RHEL 映射。

例如，您可能會看到下列兩個 RHEL 7.4 映射可供使用。

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

在此情況下，預設會將 `RedHat:RHEL:7.6:7.6.2019102813` 附加至 EUS 存放庫。 SKU 值為7.4。 根據預設，和 `RedHat:RHEL:7-LVM:7.6.2019062414` 會附加至非 EUS 存放庫。 SKU 值為 7-LVM。

若要使用一般（非 EUS）存放庫，請使用 SKU 中未包含次要版本號碼的映射。

#### <a name="rhel-images-with-eus"></a>使用 EUS 的 RHEL 映射

下表中的資訊適用于已連線至 EUS 存放庫的 RHEL 映射。

>[!NOTE]
> 在撰寫本文時，只有 RHEL 7.4 和更新版本的次要版本具有 EUS 支援。 RHEL < = 7.3 已不再支援 EUS。
>
> 如需 RHEL EUS 可用性的詳細資訊，請參閱[Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)。

次要版本 |EUS 影像範例              |EUS 狀態                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7。4      |RedHat： RHEL：7.4：7.4.2019041718 | 預設會 EUS 4 月2019和更新版本所發行的映射。|
RHEL 7.5      |RedHat： RHEL：7.5：7.5.2019060305 | 預設會 EUS 2019 年6月發佈的映射。 |
RHEL 7。6      |RedHat： RHEL：7.6：7.6.2019052206 | 預設會 EUS 已發佈的映射2019和更新版本。 |
RHEL 8。0      |N/A                            | Red Hat 不提供任何 EUS。                               |

### <a name="update-services-for-sap"></a>更新 SAP 服務

最新的 RHEL for SAP 映射將會連接到 SAP 解決方案訂閱的更新服務（E4S）。 如需 E4S 的詳細資訊，請參閱 Red Hat[檔](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)。

#### <a name="rhel-images-with-e4s"></a>使用 E4S 的 RHEL 映射

從2019年12月之後建立的下列供應專案所提供的映射，會連線到 E4S 存放庫：

* RHEL-SAP （RHEL for SAP）
* RHEL-SAP-HA （RHEL for SAP 含高可用性和更新服務）

## <a name="other-available-offers-and-skus"></a>其他可用的優惠和 Sku

可用供應專案和 Sku 的完整清單可能包括上表所列內容以外的其他映射。 例如 `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`。 這些供應專案可能用來提供特定 marketplace 解決方案的支援。 或者，它們也可以發行以供預覽和測試之用。 它們可能會隨時變更或移除，而不會發出警告。 除非 Microsoft 或 Red Hat 已公開記載其目前狀態，否則請勿使用它們。

## <a name="publishing-policy"></a>發行原則

Microsoft 和 Red Hat 更新映射作為新的次要版本時，會視需要解決特定的常見弱點和暴露（Cve），或是偶爾設定變更或更新。 我們會在三個工作天內，盡速提供更新的映射，並遵循 CVE 修正程式的發行或可用性。

我們只會更新指定映射系列中的目前次要版本。 在發行較新的次要版本之後，我們將會停止更新較舊的次要版本。 例如，隨著 RHEL 7.6 的發行，RHEL 7.5 映射將不再更新。

>[!NOTE]
> 從 RHEL 隨用隨付映射布建的作用中 Azure Vm 會連線到 Azure RHUI，並可在 Red Hat 發行更新和修正並複寫至 Azure RHUI 時立即接收。 在 Red Hat 正式發行之後，時間通常小於24小時。 這些 Vm 不需要新的已發佈映射來取得更新。 客戶可以完全掌控何時起始更新。

## <a name="image-retention-policy"></a>映像保留原則

目前的原則是保留所有先前發佈的映射。 我們保留移除已知會造成任何類型問題之映像的權利。 例如，因為後續平臺或元件更新而導致設定不正確的映射，可能會遭到移除。 可能移除的映射會遵循目前的 Azure Marketplace 原則，以提供最多30天的通知，然後才移除映射。

## <a name="next-steps"></a>後續步驟

* 若要在 Azure 中查看 RHEL 映射的完整清單，請參閱[azure 中提供的 Red Hat Enterprise Linux （RHEL）映射](./redhat-imagelist.md)。
* 若要深入瞭解 Azure Red Hat 更新基礎結構，請參閱[azure 中隨選 RHEL vm 的 Red Hat 更新基礎結構](https://aka.ms/rhui-update)。
* 若要深入瞭解 RHEL BYOS 供應專案，請參閱[在 Azure 中 Red Hat Enterprise Linux 自備訂](./byos.md)用帳戶金級映射。
* 如需所有 RHEL 版本的 Red Hat 支援原則資訊，請參閱[Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)。
