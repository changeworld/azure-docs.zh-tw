---
title: 產生並匯出點對站的憑證： Linux： CLI
description: 建立自我簽署的根憑證、匯出公開金鑰，以及使用 Linux (strongSwan) CLI 產生用戶端憑證。
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: 89f6014b548bd3dd66622d15149051e6b28e94b0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984594"
---
# <a name="generate-and-export-certificates"></a>產生並匯出憑證

點對站連線使用憑證進行驗證。 本文說明如何建立自我簽署的根憑證，以及使用 Linux CLI 和 strongSwan 產生用戶端憑證。 如果您要尋找不同的憑證指示，請參閱 [Powershell](vpn-gateway-certificates-point-to-site.md) 或 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) 文章。 如需如何使用 GUI (而非使用 CLI) 安裝 strongSwan 的相關資訊，請參閱[用戶端組態](point-to-site-vpn-client-configuration-azure-cert.md#install)一文中的步驟。

## <a name="install-strongswan"></a>安裝 strongSwan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>產生並匯出憑證

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>後續步驟

請繼續進行點對站設定，以[建立和安裝 VPN 用戶端組態檔](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)。
