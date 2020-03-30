---
title: 部署 Azure 檔同步代理
description: 部署 Azure 檔同步代理。 在遷移文檔之間共用的通用文字區塊。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209411"
---
在本節中，您將在 Windows 伺服器上安裝 Azure 檔同步代理。
[部署指南](../articles/storage/files/storage-sync-files-deployment-guide.md)說明您需要關閉**IE 增強的安全性**。 IE 增強的安全性是一種不適用於 Azure 檔同步的安全措施，關閉它允許您對 Azure 進行身份驗證，而不會出現任何問題。

打開 PowerShell 並安裝具有以下命令所需的 PowerShell 模組。 請務必在提示時安裝完整模組和 NuGet 提供程式：

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

如果您有任何問題，從你的伺服器到達互聯網，現在是時候解決這些問題。 Azure 檔同步使用任何可用的互聯網連接到互聯網。
還支援要求代理伺服器訪問互聯網。 您可以立即配置電腦範圍的代理，也可以指定在代理安裝期間僅使用檔同步的代理。

如果這意味著您需要為此伺服器打開防火牆，那麼這可能是您可以接受的方法。 在伺服器安裝結束時，在完成伺服器註冊後，將有一個網路連接報告，顯示 Azure 中的確切終結點 URL，該檔同步需要與所選區域進行通信。 該報告還告訴您需要溝通的原因。 您可以使用報表將此伺服器周圍的防火牆鎖定為特定的 URL。

您還可以遵循更保守的方法，即不打開防火牆範圍，而是限制伺服器以與更高級別的 DNS 名稱空間進行通信 - [Azure 檔同步代理和防火牆設置](../articles/storage/files/storage-sync-files-firewall-and-proxy.md)文章中提供了更多文檔和詳細資訊。 遵循您自己的網路最佳實踐。

在伺服器*安裝*嚮導的末尾，將彈出一個伺服器*註冊*嚮導。
從較早時間將伺服器註冊到存儲同步服務 Azure 資源。

這些步驟在部署指南中進行了更詳細的介紹，包括應首先安裝的上述 PowerShell 模組：安裝[Azure 檔同步代理](../articles/storage/files/storage-sync-files-deployment-guide.md)。

應使用最新的代理，並可以從 Microsoft 下載中心下載[：Azure 檔同步代理](https://aka.ms/AFS/agent "Azure 檔同步代理下載")。

成功安裝和伺服器註冊後，可以檢查您是否成功完成了此步驟：導航到 Azure 門戶中的存儲同步服務資源，然後按照左側功能表導航到"已註冊伺服器"。 您將看到您的伺服器列出在那裡馬上。
