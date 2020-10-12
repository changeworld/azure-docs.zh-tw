---
title: 解決儲存體防火牆設定
description: 在 Azure HPC Cache 中建立 Azure Blob 儲存體目標時，儲存體帳戶網路防火牆設定可能會造成失敗。 本文提供在軟體修正之前的限制因應措施。
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.author: v-erkel
ms.openlocfilehash: 6916c79e9110a88beff65d487fac72441382c2f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092365"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>解決 Blob 儲存體帳戶防火牆設定問題

儲存體帳戶防火牆中使用的特定設定可能會導致您的 Blob 儲存體目標建立失敗。 Azure HPC Cache 小組正在處理此問題的軟體修正程式，但您可以遵循本文中的指示來解決此問題。

只允許從 [選取的網路] 存取的防火牆設定可以防止快取建立 Blob 儲存體目標。 這項設定位於儲存體帳戶的 [ **防火牆和虛擬網路** 設定] 頁面中。

問題是快取服務會使用與客戶環境不同的隱藏服務虛擬網路。 無法明確授權此網路存取您的儲存體帳戶。

當您建立 Blob 儲存體目標時，快取服務會使用此網路來檢查容器是否為空的。 如果防火牆不允許從隱藏的網路進行存取，則檢查會失敗，而且儲存體目標建立會失敗。

若要解決此問題，請在建立儲存體目標時，暫時變更您的防火牆設定：

1. 移至 [儲存體帳戶 **防火牆和虛擬網路** ] 頁面，並將 [允許存取來源] 設定變更為 [ **所有網路**]。
1. 在您的 Azure HPC Cache 中建立 Blob 儲存體目標。
1. 成功建立儲存體目標之後，請將帳戶的防火牆設定變更回 [選取的 **網路**]。

Azure HPC Cache 不會使用服務虛擬網路來存取已完成的儲存體目標。

如需解決此因應措施的協助，請 [洽詢 Microsoft 服務及支援](hpc-cache-support-ticket.md)。
