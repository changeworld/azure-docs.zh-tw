---
title: 使用 Azure 入口網站將舊版直接對等互連轉換成 Azure 資源
titleSuffix: Azure
description: 使用 Azure 入口網站將舊版直接對等互連轉換成 Azure 資源
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e49e4d5debe63b99039bbafbc14f7788367314f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678835"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>使用 Azure 入口網站將舊版直接對等互連轉換成 Azure 資源

本文說明如何使用 Azure 入口網站，將現有的舊版直接對等互連轉換成 Azure 資源。

如果您想要的話，可以使用[PowerShell](howto-legacy-direct-powershell.md)來完成本指南。

## <a name="before-you-begin"></a>開始之前
* 開始設定之前，請先參閱[必要條件](prerequisites.md)和[直接對等的逐步](walkthrough-direct-all.md)解說。


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>將舊版直接對等互連轉換成 Azure 資源

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>登入入口網站並選取您的訂用帳戶
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>轉換舊版直接對等互連

您可以使用對**等互連**資源來轉換舊版對等互連連線。

#### <a name="launch-the-resource-and-configure-basic-settings"></a>啟動資源並設定基本設定
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>設定連接並提交
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>驗證直接對等
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>其他資源

如需詳細資訊，請參閱[網際網路對等常見問題](faqs.md)。

## <a name="next-steps"></a>後續步驟

* [使用入口網站建立或修改直接對等互連](howto-direct-portal.md)
