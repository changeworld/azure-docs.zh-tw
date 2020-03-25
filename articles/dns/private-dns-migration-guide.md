---
title: 將舊版 Azure DNS 私人區域遷移至新的資源模型
titleSuffix: Azure DNS
description: 本指南提供有關如何將舊版 DNS 私人區域移轉至最新的資源模型的逐步指示
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: d29885104d6f39a17b5bdeb786cda8f56f58d987
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76939356"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>將舊版 Azure DNS 私人區域移轉至新的資源模型

在公開預覽期間，私人 DNS 區域會使用 “dnszones” 資源來建立，然後將 “zoneType” 屬性設定為 “Private”。 2019 年 12 月 31 日之後將不支援這類區域，且必須遷移至 GA 資源模型，改為使用 “privateDnsZones” 資源類型，而不是 “dnszones”。 移轉程序很簡單，我們提供了一個 PowerShell 指令碼來自動化此程序。 本指南提供將 Azure DNS 私人區域移轉至新資源模型的逐步指示。

找出需要移轉的 dnszones 資源；在 Azure CLI 中執行下列命令。
```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>Prerequisites

請確定您已安裝最新版本的 Azure PowerShell。 如需 Azure PowerShell (Az) 以及如何安裝它的詳細資訊，請造訪 https://docs.microsoft.com/powershell/azure/new-azureps-module-az

請確定您已安裝 Azure PowerShell 的 Az.PrivateDns 模組。 若要安裝此模組，請開啟提升權限的 PowerShell 視窗 (管理模式)，並輸入下列命令

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>移轉程序完全自動化，且預期不會造成任何停機時間。 但是，如果您在關鍵實際執行環境中使用 Azure DNS 私人區域 (預覽)，則應在規劃的維護時間範圍內執行下列移轉程序。 在執行移轉指令碼時，請確定您不會修改私人 DNS 區域的設定或記錄集。

## <a name="installing-the-script"></a>安裝指令碼

開啟提升權限的 PowerShell 視窗 (系統管理模式)，並執行下列命令

```powershell
install-script PrivateDnsMigrationScript
```

當系統提示安裝指令碼時輸入 "A"

![安裝指令碼](./media/private-dns-migration-guide/install-migration-script.png)

您還可以在 https://www.powershellgallery.com/packages/PrivateDnsMigrationScript 手動取得最新版的 PowerShell 指令碼

>[!IMPORTANT]
>移轉指令碼不得在 Azure Cloud Shell 中執行，必須在連線到網際網路的 VM 或本機電腦上執行。

## <a name="running-the-script"></a>執行指令碼

執行下列命令來執行指令碼

```powershell
PrivateDnsMigrationScript.ps1
```

![執行指令碼](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>輸入訂用帳戶識別碼並登入 Azure

系統會提示您輸入包含您想要移轉之私人 DNS 區域的訂用帳戶識別碼。 系統會要求您登入您的 Azure 帳戶。 完成登入，讓指令碼可以存取訂用帳戶中的私人 DNS 區域資源。

![登入 Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>選取您要移轉的 DNS 區域

該指令碼取得訂用帳戶中所有私人 DNS 區域的清單，並提示您確認要移轉的區域。 請輸入"A" 以移轉所有私人 DNS 區域。 執行此步驟後，指令碼將使用新的資源模型建立新的私人 DNS 區域，並將資料複製到新的 DSN 區域。 無論如何，此步驟不會更改您現有的私人 DNS 區域。

![選取 DNS 區域](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>將 DNS 解析切換至新的 DNS 區域

將區域和記錄複製到新的資源模型後，指令碼會提示您將 DNS 解析切換至新的 DNS 區域。 此步驟會移除舊版私人 DNS 區域與虛擬網路之間的關聯。 當舊版區域與虛擬網路中斷連結時，在上述步驟中建立的新 DNS 區域將自動接管這些虛擬網路的 DNS 解析。

選取 'A' 以切換所有虛擬網路的 DNS 解析。

![切換名稱解析](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>驗證 DNS 解析

在進一步進行之前，請驗證 DNS 區域上的 DNS 解析是否運作正常。 您可以登入您的 azure VM 並針對移轉區域發出nslookup 查詢，以驗證 DNS 解析是否運作正常。

![驗證名稱解析](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

如果您發現 DNS 查詢未解析，請等候幾分鐘的時間，然後重試查詢。 如果 DNS 查詢如預期般運作，請在指令碼提示您從私人 DNS 區域中移除虛擬網路時輸入 'Y'。

![確認名稱解析](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>如果因為任何原因，針對移轉區域的 DNS 解析無法如預期般運作，請在上述步驟中輸入 'N'，指令碼會將 DNS 解析切換回舊版區域。 建立支援票證，我們可協助您移轉 DNS 區域。

## <a name="cleanup"></a>清理

此步驟將刪除舊版 DNS 區域，並且只有在您確認 DNS 解析如預期般運作之後才能執行。 系統會提示您刪除每個私人 DNS 區域。 確認該區域的 DNS 解析正常運作之後，在每個提示字元處輸入“Y”。

![清除](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>更新您的自動化

如果您正在使用自動化，包括範本、PowerShell 指令碼或使用 SDK 開發的自訂程式碼，則必須更新自動化以使用私人 DNS 區域的新資源模型。 以下是新的私人 DNS CLI/PS/SDK 文件的連結。
* [Azure DNS 私人區域 REST API](https://docs.microsoft.com/rest/api/dns/privatedns/privatezones) \(英文\)
* [Azure DNS 私人區域 CLI](https://docs.microsoft.com/cli/azure/ext/privatedns/network/private-dns?view=azure-cli-latest) \(英文\)
* [Azure DNS 私人區域 PowerShell](https://docs.microsoft.com/powershell/module/az.privatedns/?view=azps-2.3.2) \(英文\)
* [Azure DNS 私人區域 SDK](https://docs.microsoft.com/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview) \(英文\)

## <a name="need-further-help"></a>需要進一步協助

如果您需要有關移轉程序的進一步協助，或因為任何原因上述列出的步驟不適用於您，請建立支援票證。 使用支援票證包括 PowerShell 指令碼所產生的文字記錄檔。

## <a name="next-steps"></a>後續步驟

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md)，在 Azure DNS 中建立私人區域。

* 請參閱一些常見[私人區域案例](./private-dns-scenarios.md)，這些案例可在 Azure DNS 中透過私人區域實現。

* 如需 Azure DNS 中私人區域的常見問題和解答，包括特定作業類型預期會有的特定行為，請參閱[私人 DNS 常見問題集](./dns-faq-private.md)。

* 如需了解 DNS 區域和記錄，請瀏覽：[DNS 區域和記錄概觀](dns-zones-records.md)。

* 了解 Azure 的一些其他重要[網路功能](../networking/networking-overview.md)。
