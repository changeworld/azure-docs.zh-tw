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
ms.openlocfilehash: f7167cbb26e69941cade01ab8c0b8d9dc633f0d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72168410"
---
使用自訂 IPsec 策略時，請記住以下要求：

* **IKE** - 對於 IKE，您可以從 IKE 加密中選擇任何參數，以及 IKE 完整性中的任何參數，以及 DH 組中的任何參數。
* **IPsec** - 對於 IPsec，您可以從 IPsec 加密中選擇任何參數，以及 IPsec 完整性以及 PFS 中的任何參數。 如果 IPsec 加密或 IPsec 完整性的任何參數是 GCM，則這兩個設置的參數必須是 GCM。

>[!NOTE]
> 使用自訂 IPsec 策略時，沒有回應者和開始器的概念（與預設 IPsec 策略不同）。 兩側（本地和 Azure VPN 閘道）將為 IKE 階段 1 和 IKE 階段 2 使用相同的設置。 支援 IKEv1 和 IKEv2 協定。 不支援 Azure 僅作為回應程式。
>

**可用設置和參數**

| 設定 | 參數 |
|--- |--- |
| IKE 加密 | AES256， AES192， AES128 |
| IKE 誠信 | SHA384， SHA256， SHA1 |
| DH 群組 | DHGroup24、 ECP384、 ECP256、 DHGroup14、 DHGroup2048、 DHGroup2 |
| IPsec 加密 | GCMAES256， GCMAES192， GCMAES128， AES256， AES192， AES128 |
| IPsec 完整性 | GCMASE256， GCMAES192， GCMAES128， SHA256， SHA1 |
| PFS 群組 | PFS24、ECP384、ECP256、PFS2048、PFS2 |
