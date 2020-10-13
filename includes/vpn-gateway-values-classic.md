---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 75aec9f3509881c35de9309fa1532b961fb2bc03
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875530"
---
當您在 Azure 入口網站中建立傳統 VNet 時，您所檢視的名稱不是在 PowerShell 中使用的完整名稱。 例如，在入口網站中名稱顯示為 **TestVNet1** 的 VNet，在網路組態檔中的名稱可能更長。 名稱可能如下︰**Group ClassicRG TestVNet1**。 當您建立連線時，務必使用您在網路組態檔中看到的值。

在下列步驟中，您將會連線到您的 Azure 帳戶，並且下載及檢視網路組態檔，以取得連線的必要值。

1. 下載並安裝最新版的 Azure 服務管理 (SM) PowerShell Cmdlet。 大部分的人在本機安裝了 Resource Manager 的模組，但沒有服務管理模組。 服務管理模組是舊版，必須另外安裝。 如需詳細資訊，請參閱 [安裝服務管理 Cmdlet](/powershell/azure/servicemanagement/install-azure-ps)。

1. 以提高的權限開啟 PowerShell 主控台並連接到您的帳戶。 您可以使用下列範例來協助您連接。 您必須使用 PowerShell 服務管理模組在本機執行這些命令。 連線至您的帳戶。 使用下列範例來協助您連接：

   ```powershell
   Add-AzureAccount
   ```
1. 檢查帳戶的訂用帳戶。

   ```powershell
   Get-AzureSubscription
   ```
1. 如果您有多個訂用帳戶，請選取您要使用的訂用帳戶。

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
1. 匯出並檢視網路組態檔。 在您的電腦上建立目錄，然後將網路組態檔匯出到該目錄。 在此範例中，會將網路組態檔匯出到 **C:\AzureNet**。

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
1. 使用文字編輯器開啟檔案，並且檢視 VNet 和網站的名稱。 這些名稱會是您在建立連線時所使用的名稱。<br>VNet 名稱會列為 **VirtualNetworkSite name =**<br>網站名稱會列為 **LocalNetworkSiteRef name =**