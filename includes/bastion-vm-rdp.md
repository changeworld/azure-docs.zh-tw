---
title: Include 檔案
description: Include 檔案
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/21/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 083ab61d5a20bfb8e38747ae0694b1176c0a0fd1
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521523"
---
1. 開啟 [Azure 入口網站](https://portal.azure.com)。 瀏覽至您想要連線的虛擬機器，然後選取 [連線]。 從下拉式清單中選取 [Bastion]。

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="選取 Bastion":::

1. 從下拉式清單中選取 Bastion 之後，會出現具有三個索引標籤的提要欄位：RDP、SSH 和 Bastion。 由於已針對虛擬網路佈建 Bastion，因此 Bastion 索引標籤預設為作用中。 選取 [使用 Bastion]。

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="選取 Bastion":::

1. 在 [使用 Azure Bastion 進行連線] 頁面上，輸入您虛擬機器的使用者名稱和密碼，然後選取 [連線]。

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="選取 Bastion":::

1. 透過 Bastion 的這個虛擬機器 RDP 連線，會使用連接埠 443 和 Bastion 服務直接在 Azure 入口網站中開啟 (透過 HTML5)。

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="選取 Bastion":::
