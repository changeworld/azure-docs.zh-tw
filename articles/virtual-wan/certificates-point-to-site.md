---
title: 產生和匯出使用者 VPN 連線的憑證 |Azure 虛擬 WAN
description: 建立自我簽署的根憑證、匯出公開金鑰，以及使用 Windows 10 或 Windows Server 2016 中的 PowerShell 產生用戶端憑證。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: 0303bac88f34c895a4a680cd5bff0e9d1513d2e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80059931"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>產生和匯出使用者 VPN 連線的憑證

使用者 VPN （點對站）連線會使用憑證來進行驗證。 本文說明如何使用 Windows 10 或 Windows Server 2016 中的 PowerShell 建立自我簽署的根憑證，以及產生用戶端憑證。

您必須在執行 Windows 10 或 Windows Server 2016 的電腦上執行本文中的步驟。 用於產生憑證的 PowerShell Cmdlet 是作業系統的一部分，在其他 Windows 版本上無法運作。 因此，您需要 Windows 10 或 Windows Server 2016 電腦來產生憑證。 產生憑證之後，您即可上傳憑證或將其安裝在任何支援的用戶端作業系統上。

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>後續步驟

繼續[進行使用者 VPN 連線的虛擬 WAN 步驟](virtual-wan-about.md)
