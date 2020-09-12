---
title: Azure VMware Solution by CloudSimple 2020 年9月更新
description: 在本文中，您將瞭解這項維護作業期間的預期內容，以及對私用雲端的變更。
author: dikamath
ms.author: dikamath
ms.date: 09/3/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
manager: dikamath
ms.openlocfilehash: ae9c1ba5259e95ed030d7099e5dafe2d4f7935b4
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89447852"
---
# <a name="azure-vmware-solution-by-cloudsimple-september-2020-update"></a>Azure VMware Solution by CloudSimple 2020 年9月更新

Azure VMware 解決方案服務的重要更新將于九月執行。 在維護過程中傳送的電子郵件通知將包含維護的時間軸。 在本文中，您將瞭解這項維護作業期間的預期事項和私人雲端的變更。

> [!NOTE]
> 這是非干擾性升級。 在升級期間，您可能會看到其中一個多餘的元件停止運作。

## <a name="vmware-infrastructure-upgrade"></a>VMware 基礎結構升級

您私用雲端的 VMware 基礎結構將會更新至較新的版本。 這包括 vCenter、ESXi、NSX 和混合式雲端延伸 (HCX) 元件的更新 (如果您的私人雲端已部署) 。

在升級期間，會先將新的節點新增至您的私人雲端，然後才會將節點置於「維護模式」以進行升級操作。 這可確保您私用雲端的容量和私用雲端的可用性，在升級過程中維持不變。 在 VMware 元件升級期間，您可能會看到顯示在 vCenter web UI 上的警示。 警示是服務營運團隊所執行之維護作業的一部分。

**元件版本**

- ESXi 6.7 U3
- vCenter 6.7 U3
- vSAN 6。7
- NSX Data Center 2.5。1
- HCX 3.5。2

## <a name="datacenter-updates"></a>資料中心更新

此更新包含資料中心基礎結構的更新。 在維護期間，將會執行非干擾性更新。 您會注意到更新程式期間的多餘複本。 您的私人雲端 VMware 基礎結構、ExpressRoute 線路、GlobalReach 連線，以及與其中一個連結可用性相關的任何站對站 VPN 裝置，可能會產生警示。 這在更新期間是正常的，因為元件會在更新過程中重新開機。

-   如果站對站 VPN 部署為 (非 HA) 的單一實例，您可能必須重新建立 VPN 連線。

-   如果您使用點對站 VPN 連線，就必須重新建立 VPN 連線。

## <a name="post-update"></a>更新後

更新完成後，您應該會看到較新版本的 VMware 元件。 如果您發現任何問題或有任何問題，請開啟 [支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)以聯繫我們的支援小組。
