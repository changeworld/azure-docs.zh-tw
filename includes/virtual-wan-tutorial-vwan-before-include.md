---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 945d701a2a7dffc259c601b4dab9fa1333ccc066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74896621"
---
在開始配置之前，請驗證是否滿足以下條件：

* 如果已有要連接到的虛擬網路，請驗證本地網路的任何子網是否與該網路重疊。 虛擬網路不需要閘道子網，並且不能有任何虛擬網路閘道。 如果沒有虛擬網路，則可以使用本文中的步驟創建虛擬網路。
* 取得中樞區域的 IP 位址範圍。 中心是虛擬網路，您為中心區域指定的位址範圍不能與連接到的現有虛擬網路重疊。 它還不能與連接到本地的位址範圍重疊。 如果您不熟悉本地網路設定中的 IP 位址範圍，請與可以為您提供這些詳細資訊的人員進行協調。
* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。