---
title: 使用 PowerShell 將對等 ASN 與 Azure 訂閱關聯
titleSuffix: Azure
description: 使用 PowerShell 將對等 ASN 與 Azure 訂閱關聯
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 77cc4732e017d95cbae19578cf26b1111b08fdde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75908995"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>使用 PowerShell 將對等 ASN 與 Azure 訂閱關聯

在提交對等請求之前，應首先使用以下步驟將 ASN 與 Azure 訂閱相關聯。

如果您願意，可以使用[門戶](howto-subscription-association-portal.md)完成本指南。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>創建對等 ASN 以將 ASN 與 Azure 訂閱關聯

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登入您的 Azure 帳戶並且選取您的訂用帳戶
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>註冊對等資來源提供者
使用以下命令註冊訂閱中的對等資來源提供者。 如果不執行此操作，則無法訪問設置對等互連所需的 Azure 資源。

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

您可以使用以下命令檢查註冊狀態：
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> 等待*註冊狀態*轉"已註冊"，然後再繼續。 執行命令可能需要 5 到 30 分鐘。

### <a name="update-the-peer-information-associated-with-this-subscription"></a>更新與此訂閱關聯的對等資訊

下面是更新對等資訊的示例。

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -名稱對應于資源名稱，可以是您選擇的任何名稱。 但是，-對等名稱對應于公司的名稱，並且需要盡可能靠近您的 PeeringDB 設定檔。 請注意，對等名稱的值僅支援 a-z、A-Z 和空格的字元。

訂閱可以有多個 ASN。 更新每個 ASN 的對等資訊。 確保每個 ASN 的"名稱"是唯一的。

同行們有望在[對同儕DB](https://www.peeringdb.com)上擁有完整和最新的設定檔。 我們在註冊期間使用這些資訊來驗證同行的詳細資訊，如 NOC 資訊、技術聯繫資訊及其在對等設施的存在等。

請注意，為了代替上述輸出中的 **[訂閱 Id]，** 將顯示實際訂閱 ID。

## <a name="view-status-of-a-peerasn"></a>查看 PeerASN 的狀態

使用以下命令檢查 ASN 驗證狀態：

```powershell
Get-AzPeerAsn
```

下面是一個示例回應：
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
> 等待驗證狀態在提交對等請求之前將"已批准"變為"已批准"。 此批准可能需要長達 12 小時。

## <a name="modify-peerasn"></a>修改對等Asn
您可以隨時修改 NOC 聯繫資訊。

以下是範例：

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>刪除對等 Asn
當前不支援刪除 PeerASN。 如果需要刪除 PeerASN，請與[Microsoft 對等互連](mailto:peering@microsoft.com)聯繫。

## <a name="next-steps"></a>後續步驟

* [建立或修改直接對等互連](howto-direct-powershell.md)
* [將舊版直接對等互連轉換為 Azure 資源](howto-legacy-direct-powershell.md)
* [創建或修改 Exchange 對等互連](howto-exchange-powershell.md)
* [將舊版 Exchange 對等互連轉換為 Azure 資源](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>其他資源

有關詳細資訊，請訪問[互聯網對等常見問題解答](faqs.md)
