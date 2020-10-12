---
title: 允許的 CA 在 Azure CDN 上啟用自訂 HTTPS
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84887410"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>可在 Azure CDN 上啟用自訂 HTTPS 的允許憑證授權單位

當您啟用 HTTPS 功能時，您必須符合特定的憑證需求， [方法是使用您自己的憑證](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) 作為 Azure 內容傳遞網路 (CDN) 自訂網域。 Microsoft 設定檔的 **AZURE CDN 標準** 需要下列清單中的其中一個核准的憑證授權單位單位 (CA) 的憑證。 如果憑證來自未核准的 CA 或使用自我簽署憑證，則會拒絕要求。 來自**Verizon 的 AZURE Cdn 標準**和來自 Verizon 設定檔的**azure cdn Premium**接受來自任何有效 CA 的有效憑證。

> [!NOTE]
> 使用您自己的憑證來啟用自訂網域 HTTPS 功能的選項， *不適* 用於來自 Akamai 設定檔的 **Azure CDN 標準** 。 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

