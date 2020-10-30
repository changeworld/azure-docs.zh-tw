---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 393b29245141b2970e7c1a227d6e8b1b131c445c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061592"
---
如果您想要從不同於用來產生用戶端憑證的用戶端電腦建立 P2S 連線，您需要安裝用戶端憑證。 安裝用戶端憑證時，您需要匯出用戶端憑證時所建立的密碼。

1. 找出 *.pfx* 檔案並複製到用戶端電腦。 在用戶端電腦上，按兩下 *.pfx* 檔案以安裝。 將 [ **存放區位置** ] 保留為 [ **目前使用者** ]，然後選取 **[下一步]** 。
1. 在 [要匯入的檔案]  頁面上，請勿進行任何變更。 選取 [下一步]  。
1. 在 [ **私密金鑰保護** ] 頁面上，輸入憑證的密碼，或確認安全性主體是否正確，然後選取 **[下一步]** 。
1. 在 [ **憑證存放區** ] 頁面上，保留預設位置，然後選取 **[下一步]** 。
1. 選取 [完成]。 在 [憑證安裝的 **安全性警告** ] 上，選取 [ **是]** 。 您可以針對此安全性警告輕鬆地選取 [是]，因為您產生的是憑證。
1. 現在已成功匯入憑證。
