---
title: 解決儲存體防火牆設定
description: 在 Azure HPC 快取中建立 Azure Blob 儲存體目標時，儲存體帳戶網路防火牆設定可能會造成失敗。 本文提供解決方法，直到軟體修正就緒為止。
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.author: rohogue
ms.openlocfilehash: 11e57e5d101e35c55dbaa39615dfe48511a8b87c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85513829"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>解決 Blob 儲存體帳戶防火牆設定問題

儲存體帳戶防火牆中使用的特定設定可能會導致建立 Blob 儲存體目標失敗。 Azure HPC 快取小組正致力於解決此問題的軟體，但您可以遵循本文中的指示來解決此問題。

僅允許從「選取的網路」存取的防火牆設定，可能會導致快取無法建立 Blob 儲存體目標。 此設定位於儲存體帳戶的 [**防火牆和虛擬網路**] 設定頁面中。

問題是快取服務會使用不同于客戶環境的隱藏服務虛擬網路。 您無法明確授權此網路存取您的儲存體帳戶。

當您建立 Blob 儲存體目標時，快取服務會使用此網路來檢查容器是否為空的。 如果防火牆不允許從隱藏的網路進行存取，則檢查會失敗，且儲存體目標建立會失敗。

若要解決此問題，請在建立儲存體目標時暫時變更防火牆設定：

1. 移至 [儲存體帳戶] [**防火牆和虛擬網路**] 頁面，並將 [允許存取來源] 設定變更為 [**所有網路**]。
1. 在您的 Azure HPC 快取中建立 Blob 儲存體目標。
1. 成功建立儲存體目標之後，請將帳戶的防火牆設定變更回 [選取的**網路**]。

Azure HPC Cache 不會使用服務虛擬網路來存取完成的儲存體目標。

如需此因應措施的說明，請[聯絡 Microsoft 服務及支援](hpc-cache-support-ticket.md)。
