---
title: 允許在 Azure CDN 上啟用自訂 HTTPS 的 CA
description: 如果您使用自己的憑證來啟用自訂網域上的 HTTPS，就必須使用允許的憑證授權單位 (CA) 來建立它。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/18/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 29b6cb25e021e86ce6663b4db5c89217aaf70a37
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84887410"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>可在 Azure CDN 上啟用自訂 HTTPS 的允許憑證授權單位

當您使用自己的 Azure 內容傳遞網路（CDN）自訂網域[憑證啟用 HTTPS 功能](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates)時，必須符合特定的憑證需求。 **來自 Microsoft 的 AZURE CDN 標準**設定檔需要下列清單中其中一個已核准的憑證授權單位單位（CA）的憑證。 如果未核准的 CA 的憑證或使用自我簽署憑證，則會拒絕要求。 來自**Verizon 的 AZURE Cdn 標準**和**來自 VERIZON 的 azure cdn Premium**設定檔會接受任何有效 CA 的有效憑證。

> [!NOTE]
> 使用您自己的憑證來啟用自訂網域 HTTPS 功能的選項，*不適*用於**來自 AKAMAI 的 Azure CDN 標準**設定檔。 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

