---
title: Azure 防火牆的基礎結構 FQDN
description: Azure 防火牆包含內建的規則集合，適用於依預設允許的基礎結構 FQDN。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: d4de81bad1ddda2f5b54f47c1be78925ad4cdbf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "74168969"
---
# <a name="infrastructure-fqdns"></a>基礎結構 FQDN

Azure 防火牆包含內建的規則集合，適用於依預設允許的基礎結構 FQDN。 這些 FQDN 是平台特定的，且無法用於其他用途。 

下列服務包含在內建規則集合中：

- 儲存體平台映像存放庫 (PIR) 的計算存取權
- 受控磁碟狀態儲存體存取權
- Azure 診斷和記錄 (MDS)

## <a name="overriding"></a>覆寫 

您可以建立最後處理的「全部拒絕」應用程式規則集合，來覆寫此內建的基礎結構規則集合。 它一律會在基礎結構規則集合之前進行處理。 依預設會拒絕不在基礎結構規則集合中的任何項目。

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)。