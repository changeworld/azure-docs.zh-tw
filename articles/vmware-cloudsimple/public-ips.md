---
title: Azure VMware 解決方案（按雲簡單 ） - 分配公共 IP 位址
description: 描述如何在私有雲環境中為虛擬機器分配公共 IP 位址
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 565c9ad0fbd37b026a1ba555d83b9032d2efcba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024291"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>為私有雲環境分配公共 IP 位址

打開"網路"頁上的公共 IP 選項卡，為私有雲環境中的虛擬機器分配公共 IP 位址。

1. [訪問雲簡單門戶](access-cloudsimple-portal.md)，並在側功能表上選擇 **"網路**"。
2. 選擇**公共 IP**。
3. 按一下 **"新的公共 IP**"。

    ![公共 IP 頁面](media/public-ips-page.png)

4. 輸入名稱以標識 IP 位址條目。
5. 保留預設位置。
6. 如果需要，使用滑塊更改空閒超時。
7. 輸入要為其分配公共 IP 位址的本地 IP 位址。
8. 輸入關聯的 DNS 名稱。
9. 按一下 [提交]****。

![分配公共 IP](media/network-public-ip-allocate.png)

分配公共 IP 位址的任務開始。 您可以在"**活動>任務**"頁上檢查任務的狀態。 分配完成後，新條目將顯示在公共 IP 頁上。
