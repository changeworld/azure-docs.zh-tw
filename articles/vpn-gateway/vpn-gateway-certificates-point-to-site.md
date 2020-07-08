---
title: 產生和匯出 P2S 的憑證： PowerShell
titleSuffix: Azure VPN Gateway
description: 建立自我簽署的根憑證、匯出公開金鑰，以及使用 Windows 10 或 Windows Server 2016 中的 PowerShell 產生用戶端憑證。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cherylmc
ms.openlocfilehash: f9792f52ae60039ff4891ea95688f961e73283af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84983435"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>使用 PowerShell 來產生並匯出點對站的憑證

點對站連線使用憑證進行驗證。 本文說明如何使用 Windows 10 或 Windows Server 2016 中的 PowerShell 建立自我簽署的根憑證，以及產生用戶端憑證。 如果您要尋找不同的憑證指示，請參閱[憑證 - Linux](vpn-gateway-certificates-point-to-site-linux.md) 或[憑證 - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)。

您必須在執行 Windows 10 或 Windows Server 2016 的電腦上執行本文中的步驟。 用於產生憑證的 PowerShell Cmdlet 是作業系統的一部分，在其他 Windows 版本上無法運作。 因此，您需要 Windows 10 或 Windows Server 2016 電腦來產生憑證。 產生憑證之後，您即可上傳憑證或將其安裝在任何支援的用戶端作業系統上。

如果您無法使用 Windows 10 或 Windows Server 2016 電腦，則可以使用 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) 來產生憑證。 使用任一種方法所產生的憑證均可安裝在任何[支援的](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)用戶端作業系統上。

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install-an-exported-client-certificate"></a><a name="install"></a>安裝匯出的用戶端憑證

透過 P2S 連線連接至 VNet 的每個用戶端都需要以本機方式安裝用戶端憑證。

若要安裝用戶端憑證，請參閱[安裝點對站連線的用戶端憑證](point-to-site-how-to-vpn-client-install-azure-cert.md)。

## <a name="next-steps"></a>後續步驟

繼續使用您的點對站設定。

* 如需 **Resource Manager** 部署模型的步驟，請參閱[使用原生 Azure 憑證驗證來設定 P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。
* 如需**傳統**部署模型步驟，請參閱[設定 VNet 的點對站 VPN 連線 (傳統)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)。