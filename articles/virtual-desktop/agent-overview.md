---
title: 開始使用 Windows 虛擬桌面代理程式
description: Windows 虛擬桌面代理程式和更新程式的總覽。
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 6450b44e5c7281f0a24fd5000c9feec6e61fa29c
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540556"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>開始使用 Windows 虛擬桌面代理程式

在 Windows 虛擬桌面服務架構中，有三個主要元件：遠端桌面用戶端、服務和虛擬機器。 這些虛擬機器存留在安裝 Windows 虛擬桌面代理程式和代理程式開機載入器的客戶訂用帳戶中。 代理程式可做為服務與虛擬機器之間的中繼 communicator，以啟用連線能力。 因此，如果您在代理程式安裝、更新或設定方面遇到任何問題，您的虛擬機器將無法連接至服務。 代理程式開機載入器是載入代理程式的可執行檔。 

本文將簡短介紹代理程式安裝和更新程式。

>[!NOTE]
>本檔不適用於 FSLogix 代理程式或遠端桌面用戶端代理程式。


## <a name="initial-installation-process"></a>初始安裝程式

Windows 虛擬桌面代理程式一開始是以兩種方式來安裝。 如果您在 Azure 入口網站和 Azure Marketplace 中)  (Vm 布建虛擬機器，系統會自動安裝代理程式和代理程式開機載入器。 如果您使用 PowerShell 布建 Vm，您必須在 [使用 powershell 建立 Windows 虛擬桌面主機集](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)區時，手動下載代理程式和代理程式開機載入 .msi 檔案。 安裝代理程式時，也會同時安裝 Windows 虛擬桌面並存堆疊和 Geneva 監視代理程式。 使用者必須並存堆疊元件，才能安全地建立反向伺服器對用戶端連接。 Geneva 監視代理程式會監視代理程式的健康情況。 這三個元件都是對端對端使用者連線能力正常運作的必要元件。

>[!IMPORTANT]
>若要成功安裝 Windows 虛擬桌面代理程式、並存堆疊和 Geneva 監視代理程式，您必須解除封鎖 [必要 URL 清單](safe-url-list.md#virtual-machines)中列出的所有 url。 需要解除封鎖這些 Url，才能使用 Windows 虛擬桌面服務。

## <a name="agent-update-process"></a>代理程式更新流程

每當有更新可供使用時，Windows 虛擬桌面服務就會自動更新代理程式。 代理程式更新可包含新功能或修正先前的問題。 安裝 Windows 虛擬桌面代理程式的初始版本之後，代理程式會定期查詢 Windows 虛擬桌面服務，以判斷是否有較新版本的代理程式和其元件可用。 如果有新版本，代理程式開機載入器會自動下載最新版本的代理程式、並存堆疊，以及 Geneva 監視代理程式。

>[!NOTE]
>- 當 Geneva 監視代理程式更新為最新版本時，會先尋找並停用舊的 GenevaTask 工作，然後再為新的監視代理程式建立新的工作。 如果監視代理程式的最新版本發生需要還原至舊版以修正的問題，則不會刪除舊版監視代理程式。 如果最新版本發生問題，將會重新啟用舊的監視代理程式，以繼續傳遞監視資料。 所有早于您在更新之前安裝的監視器版本，都將從您的 VM 刪除。
>- 您的 VM 一次保留三個版本的並存堆疊。 這可讓您在更新發生問題時進行快速復原。 每當堆疊更新時，就會從 VM 中移除最早版本的堆疊。

此更新安裝通常會在新的 VM 上持續2-3 分鐘，而不會導致您的 VM 失去連線或關機。 此更新程式適用于 Windows 虛擬桌面 (傳統) 和最新版的 Windows 虛擬桌面（具有 Azure Resource Manager）。

## <a name="next-steps"></a>後續步驟

既然您已經更瞭解 Windows 虛擬桌面代理程式，以下是一些可能有助於您的資源：

- 查看 [ [Windows 虛擬桌面代理程式更新](whats-new.md) ] 區段，以查看新的代理程式更新每個月的相關資訊。
- 如果您遇到代理程式或連線能力相關的問題，請參閱 [Windows 虛擬桌面代理程式問題疑難排解指南](troubleshoot-agent.md)。