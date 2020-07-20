---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 86d03e980a233a60e4f3a6541462264dae0862af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74005568"
---
這篇文章提供使用 Azure 進階儲存體來建置高效能應用程式的指導方針。 您可以使用這份文件所提供的指示，並根據您的應用程式所採用的技術，結合適合的效能最佳作法。 為了說明指導方針，在這整份文件中，我們以進階儲存體上執行的 SQL Server 為範例。

雖然我們在本文中說明儲存體層的效能案例，但您必須將應用程式層最佳化。 例如，如果您在 Azure 進階儲存體上裝載 SharePoint 伺服器陣列，您可以使用本文件的 SQL Server 範例將資料庫伺服器最佳化。 此外，也要將 SharePoint 伺服器陣列的 Web 伺服器和應用程式伺服器最佳化，才能發揮最高效能。

關於在 Azure 進階儲存體上將應用程式效能最佳化方面，本文有助於回答以下常見的問題。

* 如何衡量應用程式效能？  
* 為什麼看不到預期的高效能？  
* 哪些因素會影響進階儲存體上的應用程式效能？  
* 這些因素如何影響進階儲存體上的應用程式效能？  
* 如何最佳化 IOPS、頻寬和延遲？  

我們特別針對進階儲存體提供這些指導方針，因為進階儲存體上執行的工作負載非常重視效能。 我們在適當的地方都提供範例。 針對在具有標準儲存體磁碟的 IaaS VM 上執行的應用程式，您也可以運用這些指導方針。