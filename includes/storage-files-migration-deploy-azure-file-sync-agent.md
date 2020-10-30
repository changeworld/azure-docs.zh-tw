---
title: 部署 Azure 檔案同步代理程式
description: 部署 Azure 檔案同步代理程式。 共通的文字區塊，會跨遷移檔共用。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: f038392f03b94aa2c2450531c9da4a11d9900295
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043192"
---
在本節中，您會將 Azure 檔案同步代理程式安裝在您的 Windows Server 實例上。

[部署指南](../articles/storage/files/storage-sync-files-deployment-guide.md)說明您需要關閉 **Internet Explorer 增強式安全性** 設定。 此安全性措施不適用於 Azure 檔案同步。將其關閉可讓您向 Azure 進行驗證，而不會有任何問題。

開啟 PowerShell，並使用下列命令安裝必要的 PowerShell 模組。 當系統提示您時，請務必安裝完整的模組和 NuGet 提供者。

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

如果您有任何從伺服器抵達網際網路的問題，現在就是解決這些問題的時候了。 Azure 檔案同步使用任何可用的網際網路網路連線。 也支援需要 proxy 伺服器才能連線到網際網路。 您可以立即設定整個電腦的 proxy，或指定只有 Azure 檔案同步將在代理程式安裝期間使用的 proxy。

如果設定 proxy 表示您需要為此伺服器開啟防火牆，您可能可以接受該方法。 在伺服器安裝結束時，當您完成伺服器註冊之後，網路連線報告將會顯示 Azure 中的確切端點 Url，Azure 檔案同步需要與您所選取的區域進行通訊。 報表也會告訴您需要進行通訊的原因。 您可以使用此報告，將此伺服器周圍的防火牆鎖定到特定 Url。

您也可以遵循更保守的方法，也就是不會開啟防火牆的範圍，而是限制伺服器與較高層級的 DNS 命名空間進行通訊。 如需詳細資訊，請參閱 [Azure 檔案同步 proxy 和防火牆設定](../articles/storage/files/storage-sync-files-firewall-and-proxy.md)。 遵循您自己的網路最佳做法。

在伺服器安裝精靈結束時，將會開啟 [伺服器註冊] wizard。 向先前的儲存體同步服務 Azure 資源註冊伺服器。

這些步驟會在部署指南中更詳細地說明，包括您應該先安裝的 PowerShell 模組： [Azure 檔案同步代理程式安裝](../articles/storage/files/storage-sync-files-deployment-guide.md)。

使用最新的代理程式。 您可以從 Microsoft 下載中心下載： [Azure 檔案同步代理程式](https://aka.ms/AFS/agent "Azure 檔案同步代理程式下載")。

成功安裝和伺服器註冊之後，您可以確認您已成功完成此步驟。 移至 Azure 入口網站中的儲存體同步服務資源。 在左側功能表上，移至 [ **已註冊的伺服器** ]。 您會看到您的伺服器列在那裡。
