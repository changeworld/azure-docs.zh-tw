---
title: Azure 中可用的 Red Hat Enterprise Linux 映射
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
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 01c9c4389e2c950fe68c8be171b4c50d3d6271c1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87052025"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Azure 中可用的 Red Hat Enterprise Linux （RHEL）映射
Azure 針對不同的使用案例提供各種 RHEL 映射。

> [!NOTE]
> 所有 RHEL 映射都可以在 Azure 公用和 Azure Government 雲端中使用。 它們無法在 Azure 中國雲端中使用。

## <a name="list-of-rhel-images"></a>RHEL 映射清單
這是 Azure 中可用的 RHEL 映射清單。 除非另有說明，否則所有映射都會分割並附加至一般 RHEL 存放庫（不是 EUS，而非 E4S）。 以下是目前可供一般使用的映射：

> [!NOTE]
> 不會再以 LVM 分割的映射來產生原始影像。 LVM 與較舊的原始（非 LVM）資料分割配置相比，提供了數個優點，包括大幅彈性的資料分割調整大小選項。

供應項目| SKU | 資料分割 | 正在佈建 | 附註
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Linux 代理程式 |
|             | 6.8      | RAW    | Linux 代理程式 |
|             | 6.9      | RAW    | Linux 代理程式 |
|             | 6.10     | RAW    | Linux 代理程式 |
|             | 7-RAW    | RAW    | Linux 代理程式 | RHEL 7.x 系列映射。 <br> 預設會附加至一般存放庫（非 EUS）。
|             | 7-LVM    | LVM    | Linux 代理程式 | RHEL 7.x 系列映射。 <br> 預設會附加至一般存放庫（非 EUS）。 如果您要尋找要部署的標準 RHEL 映射，請使用這組映射及/或其第2代對應。
|             | 7lvm-gen2| LVM    | Linux 代理程式 | 第2代、RHEL 7. x 系列映射。 <br> 預設會附加至一般存放庫（非 EUS）。 如果您要尋找要部署的標準 RHEL 映射，請使用這組映射及/或其第1代對應。
|             | 7-RAW-CI | RAW-CI | Cloud-init  | RHEL 7.x 系列映射。 <br> 預設會附加至一般存放庫（非 EUS）。
|             | 7.2      | RAW    | Linux 代理程式 |
|             | 7.3      | RAW    | Linux 代理程式 |
|             | 7.4      | RAW    | Linux 代理程式 | 預設會附加至 EUS 存放庫，從2019年4月起。
|             | 74-gen2  | RAW    | Linux 代理程式 | 預設會附加至 EUS 存放庫。
|             | 7.5      | RAW    | Linux 代理程式 | 預設會附加至 EUS 存放庫，從2019年6月起。
|             | 75-gen2  | RAW    | Linux 代理程式 | 預設會附加至 EUS 存放庫。
|             | 7.6      | RAW    | Linux 代理程式 | 預設會附加至 EUS 存放庫，從5月2019。
|             | 76-gen2  | RAW    | Linux 代理程式 | 預設會附加至 EUS 存放庫。
|             | 7.7      | LVM    | Linux 代理程式 | 預設會附加至 EUS 存放庫。
|             | 77-gen2  | LVM    | Linux 代理程式 | 預設會附加至 EUS 存放庫。
|             | 7.8      | LVM    | Linux 代理程式 | 已附加至一般存放庫（RHEL 7.8 無法使用 EUS）
|             | 78-gen2  | LVM    | Linux 代理程式 | 已附加至一般存放庫（RHEL 7.8 無法使用 EUS）
|             | 8-LVM    | LVM    | Linux 代理程式 | RHEL 8.x 系列映射。 已附加至一般存放庫。
|             | 8-lvm-gen2| LVM    | Linux 代理程式 | Hyper-v 第2代-RHEL 8.x 系列映射。 已附加至一般存放庫。
|             | 8        | LVM    | Linux 代理程式 | RHEL 8.0 映射。
|             | 8-gen2   | LVM    | Linux 代理程式 | Hyper-v 第2代-RHEL 8.0 映射。
|             | 8.1      | LVM    | Linux 代理程式 | RHEL 8.2 映射。 目前已附加至一般存放庫。
|             | 81gen2   | LVM    | Linux 代理程式 | Hyper-v 第2代-RHEL 8.1 映射。 目前已附加至一般存放庫。
|             | 8.1-ci   | LVM    | Linux 代理程式 | 使用雲端 init 作為布建代理程式的 RHEL 8.1 映射。 目前已附加至一般存放庫。
|             | 81-ci-gen2| LVM    | Linux 代理程式 | Hyper-v 第2代-使用雲端 init 作為布建代理程式的 RHEL 8.1 映射。 目前已附加至一般存放庫。
|             | 8.2      | LVM    | Linux 代理程式 | RHEL 8.2 映射。 目前已附加至一般存放庫。
|             | 82gen2   | LVM    | Linux 代理程式 | Hyper-v 第2代-RHEL 8.1 映射。 目前已附加至一般存放庫。
RHEL-SAP      | 7.4      | LVM    | Linux 代理程式 | 適用于 SAP Hana 和商務應用程式的 RHEL 7.4。 附加至 E4S 存放庫，將會收取 SAP 和 RHEL 的 premium，以及基礎計算費用。
|             | 74sap-gen2| LVM    | Linux 代理程式 | 適用于 SAP Hana 和商務應用程式的 RHEL 7.4。 第2代映射。 附加至 E4S 存放庫，將會收取 SAP 和 RHEL 的 premium，以及基礎計算費用。
|             | 7.5       | LVM    | Linux 代理程式 | 適用于 SAP Hana 和商務應用程式的 RHEL 7.5。 附加至 E4S 存放庫，將會收取 SAP 和 RHEL 的 premium，以及基礎計算費用。
|             | 75sap-gen2| LVM    | Linux 代理程式 | 適用于 SAP Hana 和商務應用程式的 RHEL 7.5。 第2代映射。 附加至 E4S 存放庫，將會收取 SAP 和 RHEL 的 premium，以及基礎計算費用。
|             | 7.6       | LVM    | Linux 代理程式 | 適用于 SAP Hana 和商務應用程式的 RHEL 7.6。 附加至 E4S 存放庫，將會收取 SAP 和 RHEL 的 premium，以及基礎計算費用。
|             | 76sap-gen2| LVM    | Linux 代理程式 | 適用于 SAP Hana 和商務應用程式的 RHEL 7.6。 第2代映射。 附加至 E4S 存放庫，將會收取 SAP 和 RHEL 的 premium，以及基礎計算費用。
|             | 7.7       | LVM    | Linux 代理程式 | 適用于 SAP Hana 和商務應用程式的 RHEL 7.7。 附加至 E4S 存放庫，將會收取 SAP 和 RHEL 的 premium，以及基礎計算費用。
RHEL-SAP-HANA | 6.7       | RAW    | Linux 代理程式 | 適用于 SAP Hana 的 RHEL 6.7。 已過期，以支援 RHEL-SAP 映射。
|             | 7.2       | LVM    | Linux 代理程式 | 適用于 SAP Hana 的 RHEL 7.2。 已過期，以支援 RHEL-SAP 映射。
|             | 7.3       | LVM    | Linux 代理程式 | 適用于 SAP Hana 的 RHEL 7.3。 已過期，以支援 RHEL-SAP 映射。
RHEL-SAP-APPS | 6.8       | RAW    | Linux 代理程式 | 適用于 SAP Business Applications 的 RHEL 6.8。 已過期，以支援 RHEL-SAP 映射。
|             | 7.3       | LVM    | Linux 代理程式 | 適用于 SAP Business Applications 的 RHEL 7.3。 已過期，以支援 RHEL-SAP 映射。
RHEL-HA       | 7.4       | LVM    | Linux 代理程式 | RHEL 7.4 搭配 HA 附加元件。 會在基礎計算費用上收取 HA 和 RHEL 的 premium 費用。
|             | 7.5       | LVM    | Linux 代理程式 | RHEL 7.5 搭配 HA 附加元件。 會在基礎計算費用上收取 HA 和 RHEL 的 premium 費用。
|             | 7.6       | LVM    | Linux 代理程式 | RHEL 7.6 搭配 HA 附加元件。 會在基礎計算費用上收取 HA 和 RHEL 的 premium 費用。
RHEL-SAP-HA   | 7.4          | LVM    | Linux 代理程式 | 具有 HA 和更新服務的 RHEL 7.4 for SAP。 已附加至 E4S 存放庫。 會在基礎計算費用之上收取 SAP 和 HA 存放庫以及 RHEL 的 premium。
|             | 74sapha-gen2 | LVM    | Linux 代理程式 | 具有 HA 和更新服務的 RHEL 7.4 for SAP。 第2代映射。 已附加至 E4S 存放庫。 會在基礎計算費用之上收取 SAP 和 HA 存放庫以及 RHEL 的 premium。
|             | 7.5          | LVM    | Linux 代理程式 | 具有 HA 和更新服務的 RHEL 7.5 for SAP。 已附加至 E4S 存放庫。 會在基礎計算費用之上收取 SAP 和 HA 存放庫以及 RHEL 的 premium。
|             | 7.6          | LVM    | Linux 代理程式 | 具有 HA 和更新服務的 RHEL 7.6 for SAP。 已附加至 E4S 存放庫。 會在基礎計算費用之上收取 SAP 和 HA 存放庫以及 RHEL 的 premium。
|             | 76sapha-gen2 | LVM    | Linux 代理程式 | 具有 HA 和更新服務的 RHEL 7.6 for SAP。 第2代映射。 已附加至 E4S 存放庫。 會在基礎計算費用之上收取 SAP 和 HA 存放庫以及 RHEL 的 premium。
|             | 7.7          | LVM    | Linux 代理程式 | 具有 HA 和更新服務的 RHEL 7.7 for SAP。 已附加至 E4S 存放庫。 會在基礎計算費用之上收取 SAP 和 HA 存放庫以及 RHEL 的 premium。
|             | 77sapha-gen2 | LVM    | Linux 代理程式 | 具有 HA 和更新服務的 RHEL 7.7 for SAP。 第2代映射。 已附加至 E4S 存放庫。 會在基礎計算費用之上收取 SAP 和 HA 存放庫以及 RHEL 的 premium。
rhel-byos     |rhel-lvm74| LVM    | Linux 代理程式 | RHEL 7.4 BYOS 映射（不會附加至任何更新來源）不會向 RHEL premium 收費。
|             |rhel-lvm75| LVM    | Linux 代理程式 | RHEL 7.5 BYOS 映射（不會附加至任何更新來源）不會向 RHEL premium 收費。
|             |rhel-lvm76| LVM    | Linux 代理程式 | RHEL 7.6 BYOS 映射（不會附加至任何更新來源）不會向 RHEL premium 收費。
|             |rhel-lvm77| LVM    | Linux 代理程式 | RHEL 7.7 BYOS 映射（不會附加至任何更新來源）不會向 RHEL premium 收費。
|             |rhel-lvm8 | LVM    | Linux 代理程式 | RHEL 8.0 BYOS 映射（不會附加至任何更新來源）不會向 RHEL premium 收費。
|             |rhel-lvm81 | LVM    | Linux 代理程式 | RHEL 8.1 BYOS 映射（不會附加至任何更新來源）不會向 RHEL premium 收費。

> [!NOTE]
> RHEL-SAP-HANA 產品供應專案被 Red Hat 視為生命週期結束。 現有的部署將會繼續正常運作，但 Red Hat 建議客戶從 RHEL-SAP-HANA 映射遷移到 RHEL-SAP-HA 映射，其中包括 SAP Hana 存放庫以及 HA 附加元件。 如需 Red Hat SAP 雲端供應專案的更多詳細資料，請參閱[這裡](https://access.redhat.com/articles/3751271)。

## <a name="next-steps"></a>後續步驟
* 深入瞭解[Azure 中的 Red Hat 映射](./redhat-images.md)。
* 深入瞭解[Red Hat 更新基礎結構](./redhat-rhui.md)。
* 深入瞭解[RHEL BYOS 供應](./byos.md)專案。
* 如需所有 RHEL 版本的 Red Hat 支援原則資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)頁面。
