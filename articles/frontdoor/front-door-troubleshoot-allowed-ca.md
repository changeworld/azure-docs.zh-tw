---
title: 允許的 CA 在 Azure Front Door 上啟用自訂 HTTPS
description: 如果您要使用自己的憑證在 Azure Front Door 0custom 網域上啟用 HTTPS，您必須使用允許的憑證授權單位單位 (CA) 建立它。
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: duau
ms.openlocfilehash: 973df2505eefc2a46aa105b874f32b61fe6e8b36
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91269791"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>允許在 Azure Front Door 上啟用自訂 HTTPS 的憑證授權單位單位

針對 Azure Front Door 的自訂網域，當您 [使用自己的憑證啟用 HTTPS 功能](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)時，必須使用允許的憑證授權單位單位 (CA) 建立您的 TLS/SSL 憑證。 否則，如果您使用非允許的 CA 或自我簽署憑證，系統將會拒絕您的要求。

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
