---
title: Azure 中的 Red Hat Enterprise Linux 映像 | Microsoft Docs
description: 了解 Microsoft Azure 中的 Red Hat Enterprise Linux 映像
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
ms.openlocfilehash: 5ed5d9337dd4e7acdbba25c4cb66d2690793f250
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134399"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux 映射的總覽
本文說明 Azure Marketplace 中所提供的 Red Hat Enterprise Linux (RHEL) 映像，以及與其命名和保留期相關的原則。

如需所有 RHEL 版本的 Red Hat 支援原則資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)頁面。 如需定價詳細資料，請造訪[Azure 定價計算機](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。

>[!IMPORTANT]
> 目前可在 Azure Marketplace 中取得的 RHEL 映像支援自備訂用帳戶 (BYOS) 或隨用隨付 (PAYG) 授權模型。 不支援 [Azure Hybrid Use Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)和 BYOS 與 PAYG 之間的動態切換。 切換授權模式時，需要從對應的映像重新部署 VM。

>[!NOTE]
> 如需 Azure marketplace 中 RHEL 映射的任何相關問題，請向 Microsoft 提出支援票證。

## <a name="viewing-images-available-in-azure"></a>在 Azure 中查看可用的映射
當您搜尋 Marketplace 中的「Red Hat」，或在 Azure 入口網站 UI 中建立資源時，您只會看到所有可用 RHEL 映射的子集。 您隨時可以使用 Azure CLI/PowerShell/API 取得可用 VM 映像的完整集合。

若要查看 Azure 中可用 Red Hat 映像的完整集合，請執行下列命令

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>命名慣例
Azure 中的 VM 映像是依發行者、供應項目、SKU 及版本進行組織。 「發行者:供應項目:SKU:版本」的組合便是映像 URN，並能唯一識別要使用的映像。

例如，`RedHat:RHEL:7-LVM:7.6.2018103108` 指的是 RHEL 7.6 LVM-分割的映射，建置於2018年10月31日。

下面顯示如何建立 RHEL 7.6 VM 的範例。
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>"Latest" (最新) Moniker
Azure REST API 允許針對版本使用「最新」的「名字」，而不是特定版本。 使用 "latest" 將會佈建針對指定發行者、供應項目和 SKU 的最新可用映像。

例如，`RedHat:RHEL:7-LVM:latest` 指的是最新的 RHEL 7 系列 LVM-可用的資料分割映射。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:latest --no-wait
```

>[!NOTE]
> 一般而言，比較版本以決定最新版本的程序會遵循 [CompareTo 方法](https://msdn.microsoft.com/library/a5ts8tb6.aspx)來進行。
此映射版本比較是藉由比較值做為[版本](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8)物件（不是字串）來完成。

## <a name="rhel-6-image-types"></a>RHEL 6 映射類型
若為 RHEL 6.x 映射，映射類型如下所示：

|發行者 | 供應項目 | SKU 值 | 版本 | 詳細資料
|----------|-------|-----------|---------|--------
|RedHat | RHEL | 次要版本（例如6.9） | RHEL 次要版本和發行日期的串連值（例如6.9.2018010506） | 所有標準的 RHEL 6.x 映射都會遵循此慣例
|RedHat | rhel-byos | rhel-raw69 | RHEL 次要版本和發行日期的串連值（例如6.9.20181023） | 此映射是 RHEL 6.9 BYOS 映射。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 次要版本和發行日期的串連值（例如6.8.2017053118） | 這是適用于 SAP 應用程式的 RHEL 6.8 映射。 其有權存取 SAP 應用程式存放庫，以及基礎 RHEL 存放庫。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 次要版本和發行日期的串連值（例如6.7.2017053121） | 這是適用于 SAP Hana 映射的 RHEL 6.7。 其有權存取 SAP Hana 存放庫，以及基礎 RHEL 存放庫。

## <a name="rhel-7-image-types"></a>RHEL 7 映射類型
RHEL 7.x 映射有幾種不同的映射類型。 下表顯示我們所提供的不同影像集。 您可以使用 Az CLI 命令 `az vm image list --publisher redhat --all`來查看完整清單。

>[!NOTE]
> 除非另有指示，否則所有映射都是 LVM 資料分割，且會連線到一般 RHEL 存放庫（也就是未 EUS，而非 E4S）。 接下來，我們將移至只發佈 LVM 分割的映射，但已開放給您對此決策的意見反應。 如需 SAP 的擴充更新支援和更新服務的詳細資料，請[Red Hat Enterprise Linux 生命週期頁面](https://access.redhat.com/support/policy/updates/errata)。

|發行者 | 供應項目 | SKU 值 | 版本 | 詳細資料
|----------|-------|------------|---------|--------
|RedHat | RHEL | 次要版本（例如7.6） | RHEL 次要版本和發行日期的串連值（例如7.6.2019102813） | 2019年4月之前發佈的映射將會附加至標準 RHEL 存放庫。 2019年4月之後發佈的映射將會附加至 Red Hat 的擴充更新支援（EUS）存放庫，以允許特定次要版本的版本鎖定。 需要一般存放庫的客戶應該使用 SKU 值中包含 7-LVM 或 7-RAW 的映射（下面的詳細資料）。 RHEL 7.7 和更新版本的映射將會分割為 LVM。 此類別中的所有其他影像都是原始資料分割。
|RedHat | RHEL | 7-RAW | RHEL 次要版本和發行日期的串連值（例如7.6.2019102813） | 這些映射是未經處理的資料分割（亦即，尚未新增任何邏輯磁片區）。
|RedHat | RHEL | 7-RAW-CI | RHEL 次要版本和發行日期的串連值（例如7.6.2019072418） | 這些映射是未經處理的資料分割（也就是未新增邏輯磁片區），而且會使用雲端 init 進行布建。
|RedHat | RHEL | 7-LVM | RHEL 次要版本和發行日期的串連值（例如7.6.2019062414） | 這些映射是 LVM 分割的。
|RedHat | rhel-byos | rhel-{lvm，raw} | RHEL 次要版本和發行日期的串連值（例如7.7.20190819） | 這些映射是 RHEL 7 BYOS 映射。 它們不會附加至任何存放庫，也不會收取 RHEL premium 費用。 如果您對 RHEL BYOS 映射感興趣，請[要求存取權](https://aka.ms/rhel-byos)。 SKU 值的結尾是次要版本，並指出映射是原始或 LVM 分割的。 例如，lvm77 的 SKU 值表示 LVM 分割的 RHEL 7.7 映射。
|RedHat | RHEL | RHEL-SAP | RHEL 次要版本和發行日期的串連值（例如7.6.2019071300） | 這些映射是 RHEL for SAP 映射。 他們有權存取 SAP Hana 和應用程式存放庫，以及 RHEL E4S 存放庫。 計費包括以基本計算費用為基礎的 RHEL premium 和 SAP premium。
|RedHat | RHEL | RHEL-SAP-HA | RHEL 次要版本和發行日期的串連值（例如7.6.2019062320） | 這些映射是 RHEL for SAP，具有高可用性和更新服務映射。 他們有權存取 SAP Hana 和應用程式存放庫，以及高可用性存放庫以及 RHEL E4S 存放庫。 計費會在基礎計算費用上包含 RHEL premium、SAP premium 和 HA premium。
|RedHat | RHEL | RHEL-HA | RHEL 次要版本和發行日期的串連值（例如7.6.2019062019） | 這些是也有權存取高可用性附加元件的 RHEL 映射。 因為 HA 附加元件 premium，它們會在 RHEL 和基礎計算費用上稍微額外收費。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 次要版本和發行日期的串連值（例如7.3.2017053118） | 這些映射已過期，因為 SAP 應用程式和 SAP Hana 存放庫已合併至 SAP 存放庫。 這些是適用于 SAP 應用程式映射的 RHEL。 他們有權存取 SAP 應用程式存放庫，以及基礎 RHEL 存放庫。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 次要版本和發行日期的串連值（例如7.3.2018051421） | 這些映射已過期，因為 SAP 應用程式和 SAP Hana 存放庫已合併至 SAP 存放庫。 這些是適用于 SAP Hana 映射的 RHEL。 他們有權存取 SAP Hana 存放庫，以及基礎 RHEL 存放庫。

## <a name="rhel-8-image-types"></a>RHEL 8 映射類型
RHEL 8 映射類型的詳細資料如下。

|發行者 | 供應項目 | SKU 值 | 版本 | 詳細資料
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | RHEL 次要版本和發行日期的串連值（例如8.0.20191023） | 這些映射是連接到標準 Red Hat 存放庫的 RHEL 8.0 LVM 分割映射。
|RedHat | RHEL | 8-gen2 | RHEL 次要版本和發行日期的串連值（例如8.0.20191024） | 這些映射是連接到標準 Red Hat 存放庫的 Hyper-v 第2代 RHEL 8.0 LVM 分割的映射。 如需 Azure 中第2代 Vm 的詳細資訊，請參閱[這裡](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)。

## <a name="rhel-longer-support-add-ons"></a>RHEL 長期支援附加元件

### <a name="extended-update-support-eus"></a>擴充更新支援（EUS）
從2019年4月起，預設會附加至延伸更新支援（EUS）存放庫的 RHEL 映射。 如需 RHEL EUS 的更多詳細資料，請[參閱 Red Hat 檔](https://access.redhat.com/articles/rhel-eus)。

您可以切換到 EUS 存放庫，而且受到支援。 [這裡](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)提供有關如何將您的 VM 切換至 EUS 的指示，以及有關 EUS 支援結束日期的詳細資訊。

>[!NOTE]
> RHEL 額外專案不支援 EUS。 這表示如果您要安裝的套件通常可從 RHEL 額外通道取得，則在 EUS 時將無法這麼做。 Red Hat 額外產品生命週期詳述于[這裡](https://access.redhat.com/support/policy/updates/extras/)。

#### <a name="differentiating-between-regular-and-eus-images"></a>區分一般和 EUS 的影像。
若客戶想要使用附加至 EUS 存放庫的映射，則應使用 SKU 中包含 RHEL 次要版本號碼的 RHEL 映射。

例如，您可能會看到下列兩個 RHEL 7.4 映射可供使用：
```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```
在此情況下，預設會將 `RedHat:RHEL:7.6:7.6.2019102813` 附加至 EUS 存放庫（SKU 值為7.4），而 `RedHat:RHEL:7-LVM:7.6.2019062414` 預設會附加至非 EUS 存放庫（SKU 值為 7-LVM）。

如果您想要使用一般（非 EUS）存放庫，請使用不包含 SKU 中次要版本號碼的映射進行部署。

#### <a name="rhel-images-with-eus"></a>使用 EUS 的 RHEL 映射
下表將適用于連接到 EUS 存放庫的 RHEL 映射。

>[!NOTE]
> 在撰寫本文時，只有 RHEL 7.4 和更新版本的次要版本具有 EUS 支援。 RHEL < = 7.3 已不再支援 EUS。
>
> 如需 RHEL EUS 可用性的詳細資訊，請參閱[這裡](https://access.redhat.com/support/policy/updates/errata)。

次要版本 |EUS 影像範例              |EUS 狀態                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7。4      |RedHat： RHEL：7.4：7.4.2019041718 | 預設會 EUS 2019 年4月發行的映射|
RHEL 7.5      |RedHat： RHEL：7.5：7.5.2019060305 | 預設會 EUS 在2019年6月發佈的映射 |
RHEL 7。6      |RedHat： RHEL：7.6：7.6.2019052206 | 預設會 EUS 已發佈的映射2019和更新版本  |
RHEL 8。0      |N/A                            | Red Hat 中沒有可用的 EUS                               |

### <a name="update-services-for-sap-e4s"></a>更新 SAP 的服務（E4S）
最新的 RHEL for SAP 映射將會連接到 SAP 解決方案訂閱的更新服務（E4S）。 如需 E4S 的詳細資訊，請參閱 Red Hat[檔](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)。

#### <a name="rhel-images-with-e4s"></a>使用 E4S 的 RHEL 映射
從2019年12月之後建立的下列供應專案所提供的映射，將會連線到 E4S 存放庫。

* RHEL-SAP （RHEL for SAP）
* RHEL-SAP-HA （RHEL for SAP with HA 和 Update Services）

## <a name="other-available-offers-and-skus"></a>其他可用的優惠和 Sku
可用供應項目和 SKU 的完整清單可能會包含上述表格所列內容以外的其他映像，例如 `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`。 這些供應項目可能是用來為特定 Marketplace 解決方案提供支援，也可能是基於預覽和測試目的而發行。 它們隨時都有可能在不發出警告的情況下被變更或移除。 除非它們的存在已由 Microsoft 或 Red Hat 公開記錄，否則請不要使用它們。

## <a name="publishing-policy"></a>發行原則
Microsoft 和 Red Hat 會在發行新的次要版本時更新映像以解決特定的 CVE，或是進行偶爾的設定變更/更新。 我們致力於以最快的速度提供已更新的映像，也就是在 CVE 修正發行或可供使用後的三個工作天之內。

我們只會更新特定映像系列中的最新次要版本。 在發行較新的次要版本之後，我們將會停止更新較舊的次要版本。 例如，在發行 RHEL 7.6 之後，便不會再次更新 RHEL 7.5 映像。

>[!NOTE]
> 從 RHEL 隨用隨付映像佈建的作用中 Azure VM 會連線至 Azure RHUI，並可以在 Red Hat 發行更新和修正，並將它們複寫至 Azure RHUI (這通常會在 Red Hat 正式發行後的 24 小時內發生) 後立即接收到它們。 這些 VM 不需要新的已發行映像以取得更新，且客戶能完全控制該更新的起始時機。

## <a name="image-retention-policy"></a>映像保留原則
我們目前的原則是保留先前所發行的所有映像。 我們保留移除已知會造成任何類型問題之映像的權利。 例如，我們可能會移除因後續平台或元件更新而具有不正確設定的映像。 可能會被移除的映像將會遵循目前的 Marketplace 原則，並在移除映像之前，於最多 30 天前提供通知。

## <a name="next-steps"></a>後續步驟
* [在 Azure 中查看 RHEL 映射](./redhat-imagelist.md)的完整清單。
* 請[在這裡](https://aka.ms/rhui-update)了解 Azure Red Hat Update Infrastructure。
* 深入瞭解[RHEL BYOS 供應](./byos.md)專案。
* 如需所有 RHEL 版本的 Red Hat 支援原則資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)頁面。
