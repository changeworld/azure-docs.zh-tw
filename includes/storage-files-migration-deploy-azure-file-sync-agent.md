---
title: 部署 Azure 檔案同步代理程式
description: 部署 Azure 檔案同步代理程式。 共同的文字區塊，在遷移檔之間共用。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209411"
---
在本節中，您會在 Windows Server 上安裝 Azure 檔案同步代理程式。
《[部署指南》](../articles/storage/files/storage-sync-files-deployment-guide.md)說明您必須關閉**IE 增強式安全性**。 IE 增強式安全性是一種不適用 Azure 檔案同步的安全性措施，並將其關閉可讓您向 Azure 進行驗證，而不會發生任何問題。

開啟 PowerShell，並使用下列命令安裝必要的 PowerShell 模組。 當出現提示時，請務必安裝完整模組和 NuGet 提供者：

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

如果您有任何從伺服器抵達網際網路的問題，現在就是解決問題的時機。 Azure 檔案同步會使用網際網路的任何可用網路連接。
也支援要求 proxy 伺服器來連線到網際網路。 您可以立即設定整部電腦的 proxy，或指定只在代理程式安裝期間，檔案同步將使用的 proxy。

如果這表示您需要開啟這部伺服器的防火牆，這可能是可接受的方法。 伺服器安裝結束後，在完成伺服器註冊之後，將會有一個網路連線報告顯示 Azure 中的確切端點 Url，而該檔案同步需要與您所選區域的通訊。 此報表也會告訴您需要進行通訊的原因。 您可以使用報表，將此伺服器周圍的防火牆鎖定到特定的 Url。

您也可以遵循更保守的方法，也就是您不會開啟防火牆範圍，而是限制伺服器與更高層級的 DNS 命名空間進行通訊- [Azure 檔案同步 proxy 和防火牆設定](../articles/storage/files/storage-sync-files-firewall-and-proxy.md)一文中有更多的檔和詳細資料可用。 請遵循您自己的網路最佳作法。

伺服器*安裝*嚮導結束時，將會顯示 [伺服器*註冊*]。
向您先前的儲存體同步服務 Azure 資源註冊伺服器。

部署指南中會更詳細地說明這些步驟，包括您應該先安裝的上述 PowerShell 模組： [Azure 檔案同步代理程式安裝](../articles/storage/files/storage-sync-files-deployment-guide.md)。

您應該使用最新的代理程式，並可從 Microsoft 下載中心下載： [Azure 檔案同步代理](https://aka.ms/AFS/agent "Azure 檔案同步代理程式下載")程式。

成功安裝和伺服器註冊之後，您可以檢查您是否已成功完成此步驟：流覽至 Azure 入口網站中的儲存體同步服務資源，然後依照左側功能表 [已註冊的伺服器]。 您會立即看到您的伺服器列在該處。
