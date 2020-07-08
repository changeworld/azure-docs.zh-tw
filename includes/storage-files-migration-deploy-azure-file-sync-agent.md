---
title: 部署 Azure 檔案同步代理程式
description: 部署 Azure 檔案同步代理程式。 跨遷移檔共用的通用文字區塊。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 66388f14949b4f398de18c9162ca453e7fb3cbe1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82143570"
---
在本節中，您會在 Windows Server 實例上安裝 Azure 檔案同步代理程式。

《[部署指南》](../articles/storage/files/storage-sync-files-deployment-guide.md)說明您必須關閉**Internet Explorer 增強式安全性**設定。 此安全性措施不適用於 Azure 檔案同步。關閉此功能可讓您向 Azure 進行驗證，而不會發生任何問題。

開啟 PowerShell，並使用下列命令來安裝必要的 PowerShell 模組。 當系統提示您時，請務必安裝完整模組和 NuGet 提供者。

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

如果您有任何從伺服器抵達網際網路的問題，現在就是解決問題的時機。 Azure 檔案同步會使用網際網路的任何可用網路連接。 也支援要求 proxy 伺服器來連線到網際網路。 您可以立即設定整部電腦的 proxy，或指定只在代理程式安裝期間 Azure 檔案同步將使用的 proxy。

如果設定 proxy，表示您需要為這部伺服器開啟防火牆，這可能是可接受的方法。 伺服器安裝結束時，在完成伺服器註冊之後，網路連線報表會顯示 Azure 中 Azure 檔案同步需要與您所選區域通訊的確切端點 Url。 此報表也會告訴您需要進行通訊的原因。 您可以使用此報告，將此伺服器周圍的防火牆鎖定至特定的 Url。

您也可以遵循更保守的方法，不要開啟防火牆寬，而是限制伺服器與更高層級的 DNS 命名空間進行通訊。 如需詳細資訊，請參閱[Azure 檔案同步 proxy 和防火牆設定](../articles/storage/files/storage-sync-files-firewall-and-proxy.md)。 請遵循您自己的網路最佳作法。

伺服器*安裝*嚮導結束時，將會開啟 [伺服器*註冊*]。 從先前的儲存體同步服務的 Azure 資源註冊伺服器。

部署指南中會更詳細地說明這些步驟，包括您應該先安裝的 PowerShell 模組： [Azure 檔案同步代理程式安裝](../articles/storage/files/storage-sync-files-deployment-guide.md)。

使用最新的代理程式。 您可以從 Microsoft 下載中心下載： [Azure 檔案同步代理程式](https://aka.ms/AFS/agent "Azure 檔案同步代理程式下載")。

成功安裝和伺服器註冊之後，您可以檢查您是否已成功完成此步驟。 移至 Azure 入口網站中的儲存體同步服務資源，然後在左側功能表中，按 [**已註冊的伺服器**]。 您會看到您的伺服器列在該處。
