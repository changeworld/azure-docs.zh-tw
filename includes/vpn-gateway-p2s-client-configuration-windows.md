---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: f37876a0b6b7e8b520b5b6428381ebaf7b5b5c24
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042599"
---
您可以在每個 Windows 用戶端電腦上使用相同的 VPN 用戶端組態套件，但前提是版本必須符合用戶端的架構。 如需用戶端支援的作業系統清單，請參閱 [VPN 閘道常見問題集](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S)的＜點對站＞一節。

>[!NOTE]
>您必須擁有欲連線之 Windows 用戶端電腦上的系統管理員權限。
>

請使用下列步驟來設定原生 Windows VPN 用戶端的憑證驗證：

1. 選取 Windows 電腦架構所對應的 VPN 用戶端組態檔。 若是 64 位元的處理器架構，請選擇 'VpnClientSetupAmd64' 安裝程式套件。 若是 32 位元的處理器架構，請選擇 'VpnClientSetupX86' 安裝程式套件。 
1. 對套件按兩下來加以安裝。 如果您看到 SmartScreen 快顯視窗，請按一下 [ **更多資訊** ]，然後 **繼續執行** 。
1. 在用戶端電腦上，流覽至 [ **網路設定** ]，然後按一下 [ **VPN** ]。 VPN 連線會顯示其連線的虛擬網路名稱。
1. 嘗試連線之前，請確認您已在用戶端電腦上安裝用戶端憑證。 使用原生 Azure 憑證驗證類型時，必須提供用戶端憑證才能通過驗證。