---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 83f0ce27172879a37de9488499e46de30b8e112c
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98147363"
---
使用自訂 IPsec 原則時，請記住下列需求：

* **Ike** -針對 ike，您可以從 ike 加密選取任何參數，再加上來自 ike 完整性的任何參數，再加上來自 DH 群組的任何參數。
* **Ipsec** -如果是 ipsec，您可以從 ipsec 加密選取任何參數，再加上來自 ipsec 完整性的任何參數，再加上 PFS。 如果 IPsec 加密或 IPsec 完整性的任何參數都是 GCM，則這兩個設定的參數都必須是 GCM。

>[!NOTE]
> 使用自訂 IPsec 原則時，不會有回應者和啟動器 (的概念，因為) 的預設 IPsec 原則不同。 兩端 (內部部署和 Azure VPN 閘道) 將會針對 IKE 階段1和 IKE 階段2使用相同的設定。 支援 IKEv1 和 IKEv2 通訊協定。 不支援 Azure 作為回應者。
>

**可用的設定和參數**

| 設定 | 參數 |
|--- |--- |
| IKE 加密 | GCMAES256、GCMAES128、AES256、AES128 |
| IKE 完整性 | SHA384，SHA256 |
| DH 群組 | ECP384、ECP256、DHGroup24、DHGroup14 |
| IPsec 加密 | GCMAES256、GCMAES128、AES256、AES128、None |
| IPsec 完整性 | GCMAES256、GCMAES128、SHA256 |
| PFS 群組 | ECP384、ECP256、PFS24、PFS14、None |
| SA 存留期 |等效最小值 300/預設值27000秒 |
