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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74896621"
---
開始設定之前，請確認您符合下列準則：

* 如果您已經有想要連線的虛擬網路，請確認您內部部署網路的子網不會與其重迭。 您的虛擬網路不需要閘道子網，也不能有任何虛擬網路閘道。 如果您沒有虛擬網路，您可以使用本文中的步驟建立一個。
* 取得中樞區域的 IP 位址範圍。 中樞是虛擬網路，而您為中樞區域指定的位址範圍不能與您連線的現有虛擬網路重迭。 它也不能與您連接至內部部署的位址範圍重迭。 如果您不熟悉位於內部部署網路設定中的 IP 位址範圍，請與可以為您提供這些詳細資料的人員協調。
* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。