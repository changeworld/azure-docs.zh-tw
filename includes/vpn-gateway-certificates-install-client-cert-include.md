---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0068bd151c3d7d243b05c326ec73a201f4131296
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67173871"
---
如果您想要從不同於用來產生用戶端憑證的用戶端電腦建立 P2S 連線，您需要安裝用戶端憑證。 安裝用戶端憑證時，您需要匯出用戶端憑證時所建立的密碼。

1. 找出 *.pfx* 檔案並複製到用戶端電腦。 在用戶端電腦上，按兩下 *.pfx* 檔案以安裝。 將 [存放區位置]**** 保留為 [目前使用者]****，然後按 [下一步]****。
2. 在 [要匯入的檔案] **** 頁面上，請勿進行任何變更。 按一下 [下一步]。
3. 在 [私密金鑰保護]**** 頁面上，請輸入憑證的密碼，或確認安全性主體是否正確，然後按 [下一步]****。
4. 在 [憑證存放區]**** 頁面上，保留預設的位置，然後按 [下一步]****。
5. 按一下 [完成] 。 在憑證安裝的 [安全性警告]**** 上，按一下 [是]****。 您可以安心地按一下 [是]，因為您已經產生憑證。 現在已成功匯入憑證。