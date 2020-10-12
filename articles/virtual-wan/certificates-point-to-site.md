---
title: 產生和匯出使用者 VPN 連接的憑證 |Azure 虛擬 WAN
description: 使用 Windows 10 或 Windows Server 2016 上的 PowerShell，建立自我簽署的根憑證、匯出公開金鑰，以及產生使用者 VPN 連線的用戶端憑證。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 2205f170ee846d4db94db7f524a1c424cfbc8f7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328033"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>產生和匯出使用者 VPN 連接的憑證

使用者 VPN (點對站) 連接使用憑證進行驗證。 本文說明如何使用 Windows 10 或 Windows Server 2016 中的 PowerShell 建立自我簽署的根憑證，以及產生用戶端憑證。

您必須在執行 Windows 10 或 Windows Server 2016 的電腦上執行本文中的步驟。 用於產生憑證的 PowerShell Cmdlet 是作業系統的一部分，在其他 Windows 版本上無法運作。 因此，您需要 Windows 10 或 Windows Server 2016 電腦來產生憑證。 產生憑證之後，您即可上傳憑證或將其安裝在任何支援的用戶端作業系統上。

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>後續步驟

繼續進行 [使用者 VPN 連接的虛擬 WAN 步驟](virtual-wan-about.md)
