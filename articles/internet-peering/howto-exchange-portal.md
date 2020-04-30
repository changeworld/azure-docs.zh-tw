---
title: 使用 Azure 入口網站建立或修改 Exchange 對等互連
titleSuffix: Azure
description: 使用 Azure 入口網站建立或修改 Exchange 對等互連
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e4f2ee72cbe17c094567aab5c7cc4720b02cde68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680915"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>使用 Azure 入口網站建立或修改 Exchange 對等互連

本文說明如何使用 Azure 入口網站建立 Microsoft Exchange 對等互連。 本文也會說明如何檢查資源的狀態、加以更新，或刪除並取消布建。

如果您想要的話，可以使用[PowerShell](howto-exchange-powershell.md)來完成本指南。

## <a name="before-you-begin"></a>開始之前
* 開始設定之前，請先參閱[必要條件](prerequisites.md)和[Exchange 對等的逐步](walkthrough-exchange-all.md)解說。
* 如果您已有 Microsoft 的 Exchange 對等互連未轉換成 Azure 資源，請參閱[使用入口網站將舊版 exchange 對等互連轉換成 azure 資源](howto-legacy-exchange-portal.md)。

## <a name="create-and-provision-an-exchange-peering"></a>建立和布建 Exchange 對等互連

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>登入入口網站並選取您的訂用帳戶
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>建立 Exchange 對等互連

您可以使用對**等互連**資源建立新的對等互連要求。

#### <a name="launch-the-resource-and-configure-basic-settings"></a>啟動資源並設定基本設定
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>設定連接並提交
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>驗證 Exchange 對等互連
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>修改 Exchange 對等互連
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>取消布建 Exchange 對等互連
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>後續步驟

* [使用入口網站建立或修改直接對等互連](howto-direct-portal.md)
* [使用入口網站將舊版直接對等互連轉換成 Azure 資源](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>其他資源

如需詳細資訊，請參閱[網際網路對等常見問題](faqs.md)。
