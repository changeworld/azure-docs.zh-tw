---
title: Azure 中的 Red Hat Enterprise Linux 映射總覽
description: 深入瞭解 Microsoft Azure 中的 Red Hat Enterprise Linux 映射。
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
ms.openlocfilehash: 07444e32d1808f928de418dd898aa1fa30703c90
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977624"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux 映射總覽

本文說明 Azure Marketplace 中 (RHEL) 映射的可用 Red Hat Enterprise Linux，以及它們的命名和保留期原則。

如需所有 RHEL 版本的 Red Hat 支援原則的相關資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)。 如需定價詳細資料，請參閱 [Azure 定價計算機](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。

>[!IMPORTANT]
> Azure Marketplace 支援自備訂用帳戶 (BYOS) 或隨用隨付授權模型，目前提供的 RHEL 映射可供使用。 不支援 BYOS 與隨用隨付授權之間的 [Azure 混合式使用權益](../../windows/hybrid-use-benefit-licensing.md) 和動態切換。 若要切換授權模式，您必須從對應的映射重新部署 VM。

>[!NOTE]
> 針對 Azure Marketplace 中 RHEL 映射的任何相關問題，請向 Microsoft 提出支援票證。

## <a name="view-images-available-in-azure"></a>查看 Azure 中可用的映射

當您在 Azure Marketplace 中搜尋「Red Hat」時，或當您在 Azure 入口網站 UI 中建立資源時，您只會看到所有可用 RHEL 映射的子集。 您一律可以使用 Azure CLI、PowerShell 和 API 取得一組完整的可用 VM 映射。

若要在 Azure 中查看一組完整的可用 Red Hat 映射，請執行下列命令：

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>命名慣例

Azure 中的 VM 映射會依發行者、供應專案、SKU 和版本進行組織。 「發行者:供應項目:SKU:版本」的組合便是映像 URN，並能唯一識別要使用的映像。

例如， `RedHat:RHEL:8-LVM:8.1.20200318` 指的是建立在2020年3月18日的 RHEL 8.1 LVM 分割映射。

這裡顯示如何建立 RHEL 8.1 VM 的範例。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>"Latest" (最新) Moniker

Azure REST API 允許針對版本（而非特定版本）使用「最新」標記。 使用「最新」會為指定的發行者、供應專案和 SKU 布建最新的可用映射。

例如， `RedHat:RHEL:8-LVM:latest` 指的是最新的 RHEL 8 系列 LVM-資料分割映射可用。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> 一般而言，比較版本以決定最新版本的程序會遵循 [CompareTo 方法](/dotnet/api/system.version.compareto?view=netcore-3.1#system_version_compareto_system_version_)來進行。
此映射版本比較是藉由比較這些值做為 [版本](/dotnet/api/system.version.-ctor?view=netframework-4.8) 物件（而不是字串）來完成。

## <a name="rhel-6-image-types"></a>RHEL 6 映射類型

在 RHEL 6.x 映射中，映射類型如下表所示。

|發行者 | 供應項目 | SKU 值 | 版本 | 詳細資料
|----------|-------|-----------|---------|--------
|RedHat | RHEL | 次要版本 (例如 6.9)  | RHEL 次要版本的串連值和發佈的日期 (例如 6.9.2018010506)  | 所有標準 RHEL 6.x 映射都會遵循此慣例。
|RedHat | rhel-byos | rhel-raw69 | RHEL 次要版本的串連值和發佈的日期 (例如 6.9.20181023)  | 此映射是 RHEL 6.9 BYOS 映射。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 次要版本的串連值和發佈的日期 (例如 6.8.2017053118)  | 此映射是適用于 SAP 應用程式映射的 RHEL 6.8。 它有權存取 SAP 應用程式存放庫和基礎 RHEL 存放庫。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 次要版本的串連值和發佈的日期 (例如 6.7.2017053121)  | 此映射是 SAP Hana 映射的 RHEL 6.7。 它有權存取 SAP Hana 存放庫和 base RHEL 存放庫。

## <a name="rhel-7-image-types"></a>RHEL 7 映射類型

RHEL 7.x 映射有幾種不同的影像類型。 下表顯示我們所提供的不同映射集。 若要查看完整清單，請使用 Azure CLI 命令 `az vm image list --publisher redhat --all` 。

>[!NOTE]
> 除非另有指示，否則所有映射都會進行資料分割，並連接到一般 RHEL 存放庫。 也就是說，存放庫不 (EUS) 擴充更新支援，也不是 SAP (E4S) 的更新服務。 接下來，我們將移至只發佈 LVM 分割的影像，但會開啟以提供有關此決策的意見反應。 如需有關 SAP 的擴充更新支援和更新服務的詳細資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)。

|發行者 | 供應項目 | SKU 值 | 版本 | 詳細資料
|----------|-------|------------|---------|--------
|RedHat | RHEL | 次要版本 (例如 7.6)  | RHEL 次要版本的串連值和發佈的日期 (例如 7.6.2019102813)  | 在2019年4月之前發佈的映射會附加至標準 RHEL 存放庫。 在2019年4月之後發佈的映射會附加至 Red Hat 的 EUS 存放庫，以允許特定次要版本的版本鎖定。 如果客戶想要定期存放庫，請在 SKU 值中使用包含 7-LVM 或 7-RAW 的映射 (詳細資料) 。 RHEL 7.7 和更新版本的映射為 LVM 分割。 此類別中的所有其他映射都是原始資料分割。
|RedHat | RHEL | 7-RAW | RHEL 次要版本的串連值和發佈的日期 (例如 7.6.2019102813)  | 這些映射是未經處理的資料分割 (例如，) 未新增任何邏輯磁片區。
|RedHat | RHEL | 7-RAW-CI | RHEL 次要版本的串連值和發佈的日期 (例如 7.6.2019072418)  | 這些映射是未經處理的資料分割 (例如，未新增任何邏輯磁片區) 並使用雲端初始化進行布建。
|RedHat | RHEL | 7-LVM | RHEL 次要版本的串連值和發佈的日期 (例如 7.6.2019062414)  | 這些映射是 LVM 資料分割。
|RedHat | rhel-byos | rhel-{lvm，raw} | RHEL 次要版本的串連值和發佈的日期 (例如 7.7.20190819)  | 這些映射是 RHEL 7 BYOS 映射。 它們不會附加至任何存放庫，也不會收取 RHEL premium 費用。 如果您對 RHEL BYOS 映射有興趣，請 [要求存取權](https://aka.ms/rhel-byos)。 SKU 值的結尾是次要版本，表示映射為原始或 LVM 資料分割。 例如，lvm77 的 SKU 值表示 LVM 資料分割的 RHEL 7.7 映射。
|RedHat | RHEL | RHEL-SAP | RHEL 次要版本的串連值和發佈的日期 (例如 7.6.2019071300)  | 這些映射是 RHEL for SAP 映射。 他們有權存取 SAP Hana 和應用程式存放庫，以及 RHEL E4S 存放庫。 帳單會在基礎計算費用之上包含 RHEL premium 和 SAP premium。
|RedHat | RHEL | RHEL-SAP-HA | RHEL 次要版本的串連值和發佈的日期 (例如 7.6.2019062320)  | 這些映射是 RHEL for SAP，具有高可用性和更新服務映射。 他們有權存取 SAP Hana 和應用程式存放庫，以及高可用性存放庫以及 RHEL E4S 存放庫。 根據基本計算費用，計費包含 RHEL premium、SAP premium 和高可用性 premium。
|RedHat | RHEL | RHEL-HA | RHEL 次要版本的串連值和發佈的日期 (例如 7.6.2019062019)  | 這些映射是也有權存取高可用性附加元件的 RHEL 映射。 由於高可用性附加元件 premium，它們會在 RHEL 和基礎計算費用上稍微增加一些費用。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 次要版本的串連值和發佈的日期 (例如 7.3.2017053118)  | 這些映射已過期，因為 SAP 應用程式和 SAP Hana 存放庫已合併至 SAP 存放庫。 這些映射是 RHEL for SAP 應用程式映射。 他們有權存取 SAP 應用程式存放庫和基礎 RHEL 存放庫。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 次要版本的串連值和發佈的日期 (例如 7.3.2018051421)  | 這些映射已過期，因為 SAP 應用程式和 SAP Hana 存放庫已合併至 SAP 存放庫。 這些映射是 RHEL 的 SAP Hana 映射。 他們有權存取 SAP Hana 存放庫和基礎 RHEL 存放庫。

## <a name="rhel-8-image-types"></a>RHEL 8 映射類型

>[!NOTE]
> Red Hat 建議使用 Grubby 來設定 RHEL 8 + 中的核心命令列參數。 您可以在 [這裡](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel)找到更多詳細資料。

RHEL 8 映射類型的詳細資料如下。

|發行者 | 供應項目 | SKU 值 | 版本 | 詳細資料
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | RHEL 次要版本的串連值和發佈的日期 (例如 8.0.20191023)  | 這些映射是連接到標準 Red Hat 存放庫的 RHEL 8 LVM 資料分割映射。
|RedHat | RHEL | 8-gen2 | RHEL 次要版本的串連值和發佈的日期 (例如 8.0.20191024)  | 這些映射是連接到標準 Red Hat 存放庫的 Hyper-v 第2代 RHEL 8 LVM 資料分割映射。 如需 Azure 中第2代 Vm 的詳細資訊，請參閱 [azure 上的第2代 Vm 支援](../../generation-2.md)。

## <a name="rhel-longer-support-add-ons"></a>RHEL 較長支援附加元件

### <a name="extended-update-support"></a>擴充更新支援

從2019年4月起，預設會將 RHEL 映射附加至 EUS 存放庫。 有關 RHEL EUS 的詳細資訊，可在 [Red Hat 的檔](https://access.redhat.com/articles/rhel-eus)中取得。

您可以切換至 EUS 存放庫，並支援此功能。 如需有關如何將您的 VM 切換至 EUS 的指示，以及 EUS 支援結束日期的詳細資訊，請參閱 [RHEL EUS 和版本鎖定 Rhel vm](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)。

>[!NOTE]
> RHEL 額外專案不支援 EUS。 這表示，如果您安裝的封裝通常可從 RHEL 額外的通道中取得，您將無法在 EUS 時這麼做。 如需 Red Hat 額外產品生命週期的詳細資訊，請參閱 [Red Hat Enterprise Linux 額外的生命週期](https://access.redhat.com/support/policy/updates/extras/)。

#### <a name="differentiate-between-regular-and-eus-images"></a>區分一般和 EUS 的影像

如果客戶想要使用附加至 EUS 存放庫的映射，則應使用 SKU 中包含 RHEL 次要版本號碼的 RHEL 映射。

例如，您可能會看到下列兩個 RHEL 7.4 映射可供使用。

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

在此情況下， `RedHat:RHEL:7.6:7.6.2019102813` 預設會附加至 EUS 存放庫。 SKU 值為7.4。 和 `RedHat:RHEL:7-LVM:7.6.2019062414` 預設會附加至非 EUS 存放庫。 SKU 值為 7-LVM。

若要使用標準 (非 EUS) 存放庫，請使用 SKU 中不含次要版本號碼的映射。

#### <a name="rhel-images-with-eus"></a>使用 EUS 的 RHEL 映射

下表中的資訊適用于連接至 EUS 存放庫的 RHEL 映射。

>[!NOTE]
> 在撰寫本文時，只有 RHEL 7.4 和更新版本的次要版本具有 EUS 支援。 RHEL <= 7.3 已不再支援 EUS。
>
> 如需 RHEL EUS 可用性的詳細資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)。

次要版本 |EUS 映射範例              |EUS 狀態                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat： RHEL：7.4：7.4.2019041718 | 預設會 EUS 2019 年4月和之後發行的映射。|
RHEL 7.5      |RedHat： RHEL：7.5：7.5.2019060305 | 預設會 EUS 在2019年6月和之後發佈的映射。 |
RHEL 7.6      |RedHat： RHEL：7.6：7.6.2019052206 | 預設會 EUS 發佈的映射（2019和更新版本）。 |
RHEL 8。0      |N/A                            | Red Hat 沒有任何可用的 EUS。                               |

### <a name="update-services-for-sap"></a>適用于 SAP 的更新服務

最新的 RHEL for SAP 映射將會連線至適用于 SAP 解決方案的更新服務 (E4S) 。 如需 E4S 的詳細資訊，請參閱 Red Hat [檔](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)。

#### <a name="rhel-images-with-e4s"></a>使用 E4S 的 RHEL 映射

從2019年12月之後建立的下列供應專案中的映射，會連線到 E4S 存放庫：

* RHEL-SAP (RHEL for SAP) 
* RHEL-SAP-HA (RHEL for SAP 搭配高可用性和更新服務) 

## <a name="other-available-offers-and-skus"></a>其他可用的供應專案和 Sku

可用供應專案和 Sku 的完整清單可能包含上表所列專案以外的其他映射。 例如 `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`。 這些供應專案可能會用來提供特定 marketplace 解決方案的支援。 或者，也可以針對預覽和測試目的進行發佈。 您可以隨時變更或移除它們，而不會發出警告。 除非 Microsoft 或 Red Hat 公開記錄其目前狀態，否則請不要使用它們。

## <a name="publishing-policy"></a>發行原則

Microsoft 和 Red Hat 會在發行新的次要版本時，視需要解決特定的常見弱點和漏洞 (Cve) 或偶爾的設定變更或更新。 我們致力於在發行或推出 CVE 修正之後，在三個工作天內儘快提供更新的映射。

我們只會更新指定映射系列中的目前次要版本。 在發行較新的次要版本之後，我們將會停止更新較舊的次要版本。 例如，在發行 RHEL 7.6 之後，RHEL 7.5 映射將不再更新。

>[!NOTE]
> 從 RHEL 隨用隨付映射布建的有效 Azure Vm 會連線到 Azure RHUI，並可在 Red Hat 釋出更新和修正，並複寫至 Azure RHUI 時立即收到。 此時間通常會低於 Red Hat 正式發行後的24小時。 這些 Vm 不需要新的已發佈映射來取得更新。 客戶可以完全掌控何時開始更新。

## <a name="image-retention-policy"></a>映像保留原則

目前的原則是保留所有先前發佈的映射。 我們保留移除已知會造成任何類型問題之映像的權利。 例如，可能會移除由於後續平臺或元件更新而設定不正確的映射。 可能移除的映射會遵循目前的 Azure Marketplace 原則，提供最多30天的通知，然後再移除映射。

## <a name="next-steps"></a>後續步驟

* 若要在 Azure 中查看 RHEL 映射的完整清單，請參閱 [Red Hat Enterprise Linux (azure 中提供的 rhel) 映射](./redhat-imagelist.md)。
* 若要深入瞭解 Azure Red Hat 更新基礎結構，請參閱 [適用于 azure 中隨選 RHEL vm 的 Red Hat 更新基礎結構](./redhat-rhui.md)。
* 若要深入瞭解 RHEL BYOS 供應專案，請參閱 [在 Azure 中 Red Hat Enterprise Linux 自備訂用帳戶黃金映射](./byos.md)。
* 如需所有 RHEL 版本的 Red Hat 支援原則的相關資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)。