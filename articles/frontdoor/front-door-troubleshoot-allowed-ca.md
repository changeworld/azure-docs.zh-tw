---
title: 允許的 CA 在 Azure Front Door 上啟用自訂 HTTPS
description: 如果您要使用自己的憑證在 Azure Front Door 自訂網域上啟用 HTTPS，您必須使用允許的憑證授權單位單位 (CA) 建立它。
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 20c5d611272ee2159ce8ddcc2865797a225a7ebb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613674"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>允許在 Azure Front Door 上啟用自訂 HTTPS 的憑證授權單位單位

當您使用自己的憑證為 Azure Front Door 自訂網域 [啟用 HTTPS 功能](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate) 時。 您需要 (CA) 的允許憑證授權單位單位，才能建立您的 TLS/SSL 憑證。 否則，如果您使用非允許的 CA 或自我簽署憑證，系統將會拒絕您的要求。

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
