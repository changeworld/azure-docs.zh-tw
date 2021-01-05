---
title: Azure VMware Solution by CloudSimple-配置公用 IP 位址
description: 說明如何配置私人雲端環境中虛擬機器的公用 IP 位址
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bb46ad726cd3b99324e9bb96b998ed1b4da885de
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899179"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>配置私用雲端環境的公用 IP 位址

開啟 [網路] 頁面上的 [公用 ip] 索引標籤，以配置私人雲端環境中虛擬機器的公用 IP 位址。

1. [存取 CloudSimple 入口網站](access-cloudsimple-portal.md) ，然後選取側邊功能表上的 [ **網路** ]。
2. 選取 [ **公用 ip**]。
3. 按一下 [ **新增公用 IP**]。

    ![公用 Ip 頁面](media/public-ips-page.png)

4. 輸入名稱以識別 IP 位址專案。
5. 保留預設位置。
6. 如有需要，請使用滑杆來變更閒置的超時時間。
7. 輸入您想要為其指派公用 IP 位址的本機 IP 位址。
8. 輸入相關聯的 DNS 名稱。
9. 按一下 [提交]  。

![配置公用 Ip](media/network-public-ip-allocate.png)

開始配置公用 IP 位址的工作。 您可以在 [ **活動 >** 工作] 頁面上檢查工作的狀態。 配置完成時，新的專案會顯示在 [公用 Ip] 頁面上。
