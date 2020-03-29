---
title: 允許 CA 在 Azure CDN 上啟用自訂 HTTPS
description: 如果您使用自己的憑證來啟用自訂網域上的 HTTPS，就必須使用允許的憑證授權單位 (CA) 來建立它。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 7b71611d43bc2d4de4c3e609462906c44fba0443
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919969"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>可在 Azure CDN 上啟用自訂 HTTPS 的允許憑證授權單位

使用[自己的](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates)證書訪問 Azure 內容傳遞網路 （CDN） 自訂域時，必須滿足特定的證書要求。 Microsoft 設定檔**中的 Azure CDN 標準**要求以下清單中已批准的憑證授權單位 （CA） 之一的證書。 如果來自未經批准的 CA 的證書或使用自簽章憑證，則請求將被拒絕。 **來自 Verizon 的 Azure CDN 標準版**和來自 Verizon 設定檔**的 Azure CDN 高級版**接受來自任何有效 CA 的任何有效證書。

> [!NOTE]
> 使用您自己的證書啟用自訂域 HTTPS 功能的選項*不適用於*Akamai 設定檔中的**Azure CDN 標準**版。 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

