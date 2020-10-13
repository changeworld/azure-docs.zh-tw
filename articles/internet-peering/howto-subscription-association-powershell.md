---
title: 使用 PowerShell 將對等 ASN 與 Azure 訂用帳戶產生關聯
titleSuffix: Azure
description: 使用 PowerShell 將對等 ASN 與 Azure 訂用帳戶產生關聯
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aed079c467139ac5819951c5895ba753ee38ae2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89067734"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>使用 PowerShell 將對等 ASN 與 Azure 訂用帳戶產生關聯

提交對等互連要求之前，您應該先使用下列步驟，將您的 ASN 與 Azure 訂用帳戶建立關聯。

如果您想要的話，可以使用 [入口網站](howto-subscription-association-portal.md)完成本指南。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>建立 PeerASN 以將您的 ASN 與 Azure 訂用帳戶產生關聯

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登入您的 Azure 帳戶並且選取您的訂用帳戶
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>註冊對等互連資源提供者
使用下列命令，在您的訂用帳戶中註冊對等互連資源提供者。 如果您未執行此動作，則無法存取設定對等互連所需的 Azure 資源。

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

您可以使用下列命令來檢查註冊狀態：
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> 等候 *>registrationstate* 開啟「已註冊」，再繼續進行。 執行命令時，可能需要5到30分鐘的時間。

### <a name="update-the-peer-information-associated-with-this-subscription"></a>更新與此訂用帳戶相關聯的對等資訊

以下是更新對等資訊的範例。

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -名稱會對應至資源名稱，可以是您選擇的任何名稱。 但是，-peerName 會對應到您公司的名稱，而且必須盡可能靠近您的 PeeringDB 設定檔。 請注意，peerName 的值僅支援字元 a-z、a-z 和空格。

訂用帳戶可以有多個 Asn。 更新每個 ASN 的對等互連資訊。 確定每個 ASN 的「名稱」都是唯一的。

對等應該會在 [PeeringDB](https://www.peeringdb.com)上擁有完整且最新的設定檔。 在註冊期間，我們會使用此資訊來驗證對等的詳細資料，例如 NOC 資訊、技術連絡人資訊，以及它們在對等互連設備上的存在等。

請注意，在上面的輸出中，將會顯示 **實際的訂** 用帳戶識別碼。

## <a name="view-status-of-a-peerasn"></a>查看 PeerASN 的狀態

使用下列命令檢查 ASN 驗證狀態：

```powershell
Get-AzPeerAsn
```

以下是範例回應：
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> 先等待 ValidationState 變成 [已核准]，然後再提交對等互連要求。 此核准最多可能需要 12 小時。

## <a name="modify-peerasn"></a>修改 PeerAsn
您可以隨時修改 NOC 連絡人資訊。

以下是範例：

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>刪除 PeerAsn
目前不支援刪除 PeerASN。 如果您需要刪除 PeerASN，請與 [Microsoft 對等互連](mailto:peering@microsoft.com)。

## <a name="next-steps"></a>後續步驟

* [建立或修改直接對等互連](howto-direct-powershell.md)
* [將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-powershell.md)
* [建立或修改 Exchange 對等互連](howto-exchange-powershell.md)
* [將舊版 Exchange 對等互連轉換為 Azure 資源](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>其他資源

如需詳細資訊，請造訪[網際網路對等互連常見問題集](faqs.md)
