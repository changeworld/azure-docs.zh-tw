---
title: Azure 中的 Red Hat Enterprise Linux 映像 | Microsoft Docs
description: 瞭解 Microsoft Azure 中的紅帽企業 Linux 映射。
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
ms.openlocfilehash: f06c4304be67fbc2f3116375dae33b10228723a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239865"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>紅帽企業Linux映射概述

本文介紹了 Azure 應用商店中的可用紅帽企業 Linux （RHEL） 映射及其命名和保留原則。

有關所有版本的 RHEL 的紅帽支援策略的資訊，請參閱[紅帽企業 Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)。 有關定價詳細資訊，請參閱[Azure 定價計算機](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。

>[!IMPORTANT]
> Azure 應用商店中當前可用的 RHEL 映射支援自帶訂閱 （BYOS） 或即用即付許可模型。 不支援在 BYOS 和即用即付許可之間使用[Azure 混合使用優勢](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)和動態切換。 要切換許可模式，必須從相應的映射重新部署 VM。

>[!NOTE]
> 對於 Azure 應用商店中與 RHEL 映射相關的任何問題，向 Microsoft 提交支援票證。

## <a name="view-images-available-in-azure"></a>查看 Azure 中可用的圖像

在 Azure 應用商店中搜索"紅帽"或在 Azure 門戶 UI 中創建資源時，將僅看到所有可用 RHEL 映射的子集。 始終可以使用 Azure CLI、PowerShell 和 API 獲取完整的可用 VM 映射集。

要查看 Azure 中完整的可用紅帽映射集，運行以下命令：

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>命名慣例

Azure 中的 VM 映射按發行者、產品/服務、SKU 和版本組織。 「發行者:供應項目:SKU:版本」的組合便是映像 URN，並能唯一識別要使用的映像。

例如，`RedHat:RHEL:8-LVM:8.1.20200318`是指 2020 年 3 月 18 日構建的 RHEL 8.1 LVM 分區映射。

此處顯示了如何創建 RHEL 8.1 VM 的示例。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>"Latest" (最新) Moniker

Azure REST API 允許對版本使用"最新"名字，而不是特定版本。 使用"最新"規定給定發佈商、產品/服務/服務/SKU 的最新可用映射。

例如，`RedHat:RHEL:8-LVM:latest`引用最新的 RHEL 8 系列 LVM 分區映射可用。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> 一般而言，比較版本以決定最新版本的程序會遵循 [CompareTo 方法](https://msdn.microsoft.com/library/a5ts8tb6.aspx)來進行。
此圖像版本比較是通過將值作為[Version](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8)物件而不是字串進行比較來完成的。

## <a name="rhel-6-image-types"></a>RHEL 6 圖像類型

對於 RHEL 6.x 圖像，下圖顯示了圖像類型。

|發行者 | 供應項目 | SKU 值 | 版本 | 詳細資料
|----------|-------|-----------|---------|--------
|RedHat | RHEL | 次要版本（例如 6.9） | RHEL 次要版本的連體值和發佈日期（例如，6.9.2018010506） | 所有標準 RHEL 6.x 圖像都遵循此約定。
|RedHat | 雷爾-比奧斯 | rhel-raw69 | RHEL 次要版本的連體值和發佈日期（例如，6.9.20181023） | 此映射是 RHEL 6.9 BYOS 映射。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 次要版本的連體值和發佈日期（例如，6.8.2017053118） | 此映射是 SAP 應用程式映射的 RHEL 6.8。 它有權訪問 SAP 應用程式存儲庫和基礎 RHEL 存儲庫。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 次要版本的連體值和發佈日期（例如，6.7.2017053121） | 此圖像是 SAP HANA 圖像的 RHEL 6.7。 它有權訪問 SAP HANA 存儲庫和基礎 RHEL 存儲庫。

## <a name="rhel-7-image-types"></a>RHEL 7 圖像類型

對於 RHEL 7.x 圖像，有幾種不同的圖像類型。 下表顯示了我們提供的不同圖像集。 要查看完整清單，請使用 Azure CLI 命令`az vm image list --publisher redhat --all`。

>[!NOTE]
> 除非另有說明，否則所有圖像均被 LVM 分區並連接到常規 RHEL 存儲庫。 也就是說，存儲庫不是擴展更新支援 （EUS），也不是 SAP 的更新服務 （E4S）。 展望未來，我們將只發佈 LVM 分區映射，但可以收到有關此決策的回饋。 有關 SAP 擴展更新支援和更新服務的詳細資訊，請參閱[紅帽企業 Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)。

|發行者 | 供應項目 | SKU 值 | 版本 | 詳細資料
|----------|-------|------------|---------|--------
|RedHat | RHEL | 次要版本（例如，7.6） | RHEL 次要版本的連體值和發佈日期（例如，7.6.2019102813） | 2019 年 4 月之前發佈的圖像將附加到標準 RHEL 存儲庫。 2019 年 4 月之後發佈的圖像將附加到紅帽的 EUS 存儲庫，以允許特定次要版本的版本鎖定。 需要常規存儲庫的客戶應使用 SKU 值中包含 7-LVM 或 7-RAW 的圖像（詳情如下）。 RHEL 7.7 和更高版本的圖像被 LVM 分區。 此類別中的所有其他映射都是原始分區的。
|RedHat | RHEL | 7-RAW | RHEL 次要版本的連體值和發佈日期（例如，7.6.2019102813） | 這些映射是原始分區的（例如，未添加邏輯卷）。
|RedHat | RHEL | 7-RAW-CI | RHEL 次要版本的連體值和發佈日期（例如，7.6.2019072418） | 這些映射是原始分區的（例如，未添加邏輯卷），並使用雲 init 進行預配。
|RedHat | RHEL | 7-LVM | RHEL 次要版本的連體值和發佈日期（例如，7.6.2019062414） | 這些映射已分區 LVM。
|RedHat | 雷爾-比奧斯 | rhel-lvm，原始* | RHEL 次要版本的連體值和發佈日期（例如，7.7.20190819） | 這些圖像是 RHEL 7 BYOS 圖像。 它們不附加到任何存儲庫，也不會收取 RHEL 保費。 如果您對 RHEL BYOS 映射感興趣，[請請求訪問](https://aka.ms/rhel-byos)。 SKU 值以次要版本結尾，表示映射是生的還是對 LVM 分區的。 例如，rhel-lvm77 的 SKU 值表示 LVM 分區的 RHEL 7.7 圖像。
|RedHat | RHEL | RHEL-SAP | RHEL 次要版本的連體值和發佈日期（例如，7.6.2019071300） | 這些映射是 SAP 映射的 RHEL。 他們有權訪問 SAP HANA 和應用程式存儲庫以及 RHEL E4S 存儲庫。 計費包括 RHEL 保費和 SAP 保費，以及基本計算費用。
|RedHat | RHEL | 雷爾-薩普哈 | RHEL 次要版本的連體值和發佈日期（例如，7.6.2019062320） | 這些映射是具有高可用性和更新服務映射的 SAP 的 RHEL。 他們有權訪問 SAP HANA 和應用程式存儲庫、高可用性存儲庫以及 RHEL E4S 存儲庫。 計費包括 RHEL 保費、SAP 保費和高可用性溢價，以及基本計算費用。
|RedHat | RHEL | 雷爾-哈 | RHEL 次要版本的連體值和發佈日期（例如，7.6.2019062019） | 這些圖像是 RHEL 圖像，也有權訪問高可用性載入項。 由於高可用性附加溢價，它們在 RHEL 和基本計算費用上收取稍多的費用。
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 次要版本的連體值和發佈日期（例如，7.3.2017053118） | 這些映射已過期，因為 SAP 應用程式和 SAP HANA 存儲庫已合併到 SAP 存儲庫中。 這些映射是 SAP 應用程式映射的 RHEL。 他們有權訪問 SAP 應用程式存儲庫和基礎 RHEL 存儲庫。
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 次要版本的連體值和發佈日期（例如，7.3.2018051421） | 這些映射已過期，因為 SAP 應用程式和 SAP HANA 存儲庫已合併到 SAP 存儲庫中。 這些圖像是 SAP HANA 圖像的 RHEL。 他們有權訪問 SAP HANA 存儲庫和基礎 RHEL 存儲庫。

## <a name="rhel-8-image-types"></a>RHEL 8 圖像類型

RHEL 8 圖像類型的詳細資訊如下。

|發行者 | 供應項目 | SKU 值 | 版本 | 詳細資料
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | RHEL 次要版本的連體值和發佈日期（例如，8.0.20191023） | 這些圖像是 RHEL 8 LVM 分區的圖像，連接到標準紅帽存儲庫。
|RedHat | RHEL | 8 代2 | RHEL 次要版本的連體值和發佈日期（例如，8.0.20191024） | 這些圖像是 Hyper-V 第 2 代 RHEL 8 LVM 分區的圖像，連接到標準紅帽存儲庫。 有關 Azure 中第 2 代 VM 的詳細資訊，請參閱[Azure 上對第 2 代 VM 的支援](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)。

## <a name="rhel-longer-support-add-ons"></a>RHEL 長支援附加元件

### <a name="extended-update-support"></a>擴展更新支援

自 2019 年 4 月起，RHEL 圖像預設連接到 EUS 存儲庫。 有關 RHEL EUS 的更多資訊，請參閱[紅帽的文檔](https://access.redhat.com/articles/rhel-eus)。

可以切換到 EUS 存儲庫，並且支援。 有關如何將 VM 切換到 EUS 的說明以及有關 EUS 支援壽命終止日期的詳細資訊，請參閱[RHEL EUS 和版本鎖定 RHEL VM。](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)

>[!NOTE]
> RHEL 附加服務不支援 EUS。 這意味著，如果您安裝的包通常可從 RHEL Extras 通道獲得，則在 EUS 上時將無法執行此操作。 有關紅帽額外產品生命週期的詳細資訊，請參閱[紅帽企業 Linux 附加生命週期](https://access.redhat.com/support/policy/updates/extras/)。

#### <a name="differentiate-between-regular-and-eus-images"></a>區分常規圖像和 EUS 圖像

希望使用附加到 EUS 存儲庫的圖像的客戶應使用 SKU 中包含 RHEL 次要版本號的 RHEL 映射。

例如，您可能會看到以下兩個 RHEL 7.4 圖像可用。

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

在這種情況下，`RedHat:RHEL:7.6:7.6.2019102813`預設情況下附加到 EUS 存儲庫。 SKU 值為 7.4。 預設情況下`RedHat:RHEL:7-LVM:7.6.2019062414`，它附加到非 EUS 存儲庫。 SKU 值為 7-LVM。

要使用常規（非 EUS）存儲庫，請使用 SKU 中不包含次要版本號的圖像。

#### <a name="rhel-images-with-eus"></a>帶 EUS 的 RHEL 圖像

下表中的資訊適用于連接到 EUS 存儲庫的 RHEL 映射。

>[!NOTE]
> 在編寫本文時，只有 RHEL 7.4 和更高版本的次要版本才支援 EUS。 RHEL <+7.3 不再支援 EUS。
>
> 有關 RHEL EUS 可用性的詳細資訊，請參閱[紅帽企業 Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)。

次要版本 |EUS 圖像示例              |EUS 狀態                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |紅帽：RHEL：7.4：4.2019041718 | 預設情況下，2019 年 4 月及更高版本發佈的圖像為 EUS。|
RHEL 7.5      |紅帽：RHEL：7.5：7.5.2019060305 | 預設情況下，2019 年 6 月及更高版本發佈的圖像為 EUS。 |
RHEL 7.6      |紅帽：RHEL：7.6：7.6.2019052206 | 預設情況下，2019 年 5 月及更高版本發佈的圖像為 EUS。 |
RHEL 8.0      |N/A                            | 紅帽沒有EUS。                               |

### <a name="update-services-for-sap"></a>更新 SAP 服務

SAP 映射的最新 RHEL 將連接到 SAP 解決方案訂閱的更新服務 （E4S）。 有關 E4S 的詳細資訊，請參閱紅帽[文檔](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)。

#### <a name="rhel-images-with-e4s"></a>帶 E4S 的 RHEL 圖像

2019 年 12 月以後創建的以下優惠中的圖像已連接到 E4S 存儲庫：

* RHEL-SAP（用於 SAP 的 RHEL）
* RHEL-SAP-HA（用於高可用性和更新服務的 SAP RHEL）

## <a name="other-available-offers-and-skus"></a>其他可用優惠和 SKU

可用優惠和 SKU 的完整清單可能包括超出上表中列出的其他映射。 例如 `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`。 這些優惠可用於為特定市場解決方案提供支援。 或者，它們可以發佈用於預覽和測試目的。 它們可能隨時更改或刪除，恕不經警告。 除非 Microsoft 或紅帽公開記錄它們的存在，否則不要使用它們。

## <a name="publishing-policy"></a>發行原則

Microsoft 和紅帽會根據解決特定常見漏洞和暴露 （CVE） 或偶爾更改或更新的配置更新，以發佈新的次要版本來更新映射。 我們努力在 CVE 修復程式發佈或可用後的三個工作日內儘快提供更新的圖像。

我們僅更新給定映射系列中的當前次要版本。 在發行較新的次要版本之後，我們將會停止更新較舊的次要版本。 例如，隨著 RHEL 7.6 的發佈，RHEL 7.5 圖像將不再更新。

>[!NOTE]
> 從 RHEL 即付即用映射預配的活動 Azure VM 連接到 Azure RHUI，一旦由紅帽發佈並複製到 Azure RHUI，即可接收更新和修復。 時間通常不到24小時後，紅帽正式釋放。 這些 VM 不需要新的已發佈映射來獲取更新。 客戶可以完全控制何時啟動更新。

## <a name="image-retention-policy"></a>映像保留原則

當前策略是保留以前發佈的所有圖像。 我們保留移除已知會造成任何類型問題之映像的權利。 例如，由於後續平臺或元件更新而配置不正確的映射可能會被刪除。 可能刪除的圖像遵循當前的 Azure 應用商店策略，在刪除映射前 30 天提供通知。

## <a name="next-steps"></a>後續步驟

* 要查看 Azure 中 RHEL 映射的完整清單，請參閱[Azure 中可用的紅帽企業 Linux （RHEL） 映射](./redhat-imagelist.md)。
* 要瞭解有關 Azure 紅帽更新基礎結構的詳細資訊，請參閱[紅帽更新基礎結構，瞭解 Azure 中的按需 RHEL VM。](https://aka.ms/rhui-update)
* 要瞭解有關 RHEL BYOS 產品/服務詳細資訊，請參閱[Azure 中的紅帽企業 Linux 自編自辦黃金映射](./byos.md)。
* 有關所有版本的 RHEL 的紅帽支援策略的資訊，請參閱[紅帽企業 Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)。
