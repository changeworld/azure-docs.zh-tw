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
ms.openlocfilehash: e6109a87750e588b12bfc9836c5db3db55420ec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77133791"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Azure 中提供的紅帽企業 Linux （RHEL） 映射
Azure 為不同的用例提供各種 RHEL 映射。

> [!NOTE]
> 所有 RHEL 映射在 Azure 公共雲和 Azure 政府雲中都有可用。 它們在 Azure 中國雲中不可用。

## <a name="list-of-rhel-images"></a>RHEL 圖像清單
這是 Azure 中可用的 RHEL 映射的清單。 除非另有說明，否則所有圖像均通過 LVM 分區並附加到常規 RHEL 存儲庫（不是 EUS，而不是 E4S）。 以下圖像目前可供一般使用：

供應項目| SKU | 資料分割 | 佈建 | 注意
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Linux 代理程式 |
|             | 6.8      | RAW    | Linux 代理程式 |
|             | 6.9      | RAW    | Linux 代理程式 |
|             | 6.10     | RAW    | Linux 代理程式 |
|             | 7-RAW    | RAW    | Linux 代理程式 | RHEL 7.x 圖像系列。 <br> 預設情況下附加到常規存儲庫（不是 EUS）。
|             | 7-LVM    | LVM    | Linux 代理程式 | RHEL 7.x 圖像系列。 <br> 預設情況下附加到常規存儲庫（不是 EUS）。 如果要查找要部署的標準 RHEL 映射，請使用這組映射和/或其第 2 代對應映射。
|             | 7lvm-gen2| LVM    | Linux 代理程式 | 第 2 代，RHEL 7.x 系列圖像。 <br> 預設情況下附加到常規存儲庫（不是 EUS）。 如果要查找要部署的標準 RHEL 映射，請使用這組映射和/或其第 1 代對應映射。
|             | 7-RAW-CI | RAW-CI | Cloud-init  | RHEL 7.x 圖像系列。 <br> 預設情況下附加到常規存儲庫（不是 EUS）。
|             | 7.2      | RAW    | Linux 代理程式 |
|             | 7.3      | RAW    | Linux 代理程式 |
|             | 7.4      | RAW    | Linux 代理程式 | 預設自 2019 年 4 月起附加到 EUS 存儲庫。
|             | 74代2  | RAW    | Linux 代理程式 | 預設情況下附加到 EUS 存儲庫。
|             | 7.5      | RAW    | Linux 代理程式 | 預設截至 2019 年 6 月連接到 EUS 存儲庫。
|             | 75 代2  | RAW    | Linux 代理程式 | 預設情況下附加到 EUS 存儲庫。
|             | 7.6      | RAW    | Linux 代理程式 | 預設截至 2019 年 5 月連接到 EUS 存儲庫。
|             | 76 代2  | RAW    | Linux 代理程式 | 預設情況下附加到 EUS 存儲庫。
|             | 7.7      | LVM    | Linux 代理程式 | 預設情況下附加到 EUS 存儲庫。
|             | 8        | LVM    | Linux 代理程式 | RHEL 8.x 圖像系列
|             | 8 代2   | LVM    | Linux 代理程式 | Hyper-V 第 2 代 - RHEL 8.x 圖像系列。
RHEL-SAP      | 7.4      | LVM    | Linux 代理程式 | RHEL 7.4 適用于 SAP HANA 和業務應用。 連接到 E4S 存儲庫後，SAP 和 RHEL 將收取額外費用以及基本計算費用。
|             | 74sap-gen2| LVM    | Linux 代理程式 | RHEL 7.4 適用于 SAP HANA 和業務應用。 第 2 代圖像。 連接到 E4S 存儲庫後，SAP 和 RHEL 將收取額外費用以及基本計算費用。
|             | 7.5       | LVM    | Linux 代理程式 | RHEL 7.5 適用于 SAP HANA 和業務應用。 連接到 E4S 存儲庫後，SAP 和 RHEL 將收取額外費用以及基本計算費用。
|             | 75sap-gen2| LVM    | Linux 代理程式 | RHEL 7.5 適用于 SAP HANA 和業務應用。 第 2 代圖像。 連接到 E4S 存儲庫後，SAP 和 RHEL 將收取額外費用以及基本計算費用。
|             | 7.6       | LVM    | Linux 代理程式 | 適用于 SAP HANA 和商業應用的 RHEL 7.6。 連接到 E4S 存儲庫後，SAP 和 RHEL 將收取額外費用以及基本計算費用。
|             | 76sap-gen2| LVM    | Linux 代理程式 | 適用于 SAP HANA 和商業應用的 RHEL 7.6。 第 2 代圖像。 連接到 E4S 存儲庫後，SAP 和 RHEL 將收取額外費用以及基本計算費用。
|             | 7.7       | LVM    | Linux 代理程式 | RHEL 7.7 適用于 SAP HANA 和業務應用。 連接到 E4S 存儲庫後，SAP 和 RHEL 將收取額外費用以及基本計算費用。
RHEL-SAP-HANA | 6.7       | RAW    | Linux 代理程式 | RHEL 6.7 用於 SAP HANA。 過時的，有利於RHEL-SAP圖像。
|             | 7.2       | LVM    | Linux 代理程式 | RHEL 7.2 用於 SAP HANA。 過時的，有利於RHEL-SAP圖像。
|             | 7.3       | LVM    | Linux 代理程式 | RHEL 7.3 用於 SAP HANA。 過時的，有利於RHEL-SAP圖像。
RHEL-SAP-APPS | 6.8       | RAW    | Linux 代理程式 | 適用于 SAP 商務應用程式的 RHEL 6.8。 過時的，有利於RHEL-SAP圖像。
|             | 7.3       | LVM    | Linux 代理程式 | 適用于 SAP 商務應用程式的 RHEL 7.3。 過時的，有利於RHEL-SAP圖像。
雷爾-哈       | 7.4       | LVM    | Linux 代理程式 | RHEL 7.4 與 HA 附加元件。 將在基本計算費的基礎上收取 HA 和 RHEL 的額外費用。
|             | 7.5       | LVM    | Linux 代理程式 | RHEL 7.5 與 HA 附加元件。 將在基本計算費的基礎上收取 HA 和 RHEL 的額外費用。
|             | 7.6       | LVM    | Linux 代理程式 | RHEL 7.6 與 HA 附加元件。 將在基本計算費的基礎上收取 HA 和 RHEL 的額外費用。
雷爾-薩普哈   | 7.4          | LVM    | Linux 代理程式 | RHEL 7.4 用於 SAP 與 HA 和更新服務。 附加到 E4S 存儲庫。 除了基本計算費用外，還將對 SAP 和 HA 存儲庫以及 RHEL 收取額外費用。
|             | 74薩帕-根2 | LVM    | Linux 代理程式 | RHEL 7.4 用於 SAP 與 HA 和更新服務。 第 2 代圖像。 附加到 E4S 存儲庫。 除了基本計算費用外，還將對 SAP 和 HA 存儲庫以及 RHEL 收取額外費用。
|             | 7.5          | LVM    | Linux 代理程式 | RHEL 7.5 用於 SAP 與 HA 和更新服務。 附加到 E4S 存儲庫。 除了基本計算費用外，還將對 SAP 和 HA 存儲庫以及 RHEL 收取額外費用。
|             | 7.6          | LVM    | Linux 代理程式 | RHEL 7.6 用於 SAP 和更新服務。 附加到 E4S 存儲庫。 除了基本計算費用外，還將對 SAP 和 HA 存儲庫以及 RHEL 收取額外費用。
|             | 76薩帕-根2 | LVM    | Linux 代理程式 | RHEL 7.6 用於 SAP 和更新服務。 第 2 代圖像。 附加到 E4S 存儲庫。 除了基本計算費用外，還將對 SAP 和 HA 存儲庫以及 RHEL 收取額外費用。
|             | 7.7          | LVM    | Linux 代理程式 | RHEL 7.7 用於 SAP 與 HA 和更新服務。 附加到 E4S 存儲庫。 除了基本計算費用外，還將對 SAP 和 HA 存儲庫以及 RHEL 收取額外費用。
|             | 77薩帕-根2 | LVM    | Linux 代理程式 | RHEL 7.7 用於 SAP 與 HA 和更新服務。 第 2 代圖像。 附加到 E4S 存儲庫。 除了基本計算費用外，還將對 SAP 和 HA 存儲庫以及 RHEL 收取額外費用。
雷爾-比奧斯     |rhel-lvm74| LVM    | Linux 代理程式 | RHEL 7.4 BYOS 映射（未附加到任何更新源）不會收取 RHEL 溢價。
|             |rhel-lvm75| LVM    | Linux 代理程式 | RHEL 7.5 BYOS 映射（未附加到任何更新源）不會收取 RHEL 溢價。
|             |rhel-lvm76| LVM    | Linux 代理程式 | RHEL 7.6 BYOS 映射（未附加到任何更新源）不會收取 RHEL 溢價。
|             |rhel-lvm77| LVM    | Linux 代理程式 | RHEL 7.7 BYOS 映射（未附加到任何更新源）不會收取 RHEL 溢價。
|             |rhel-lvm8 | LVM    | Linux 代理程式 | RHEL 8 BYOS 映射（RHEL 次要版本顯示在映射版本值中），不附加到任何更新源，不會收取 RHEL 溢價。

## <a name="next-steps"></a>後續步驟
* 瞭解有關 Azure[中的紅帽映射](./redhat-images.md)的更多詳細資訊。
* 瞭解有關[紅帽更新基礎結構](./redhat-rhui.md)的詳細資訊。
* 瞭解有關[RHEL BYOS 產品/](./byos.md)
* 如需所有 RHEL 版本的 Red Hat 支援原則資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)頁面。
