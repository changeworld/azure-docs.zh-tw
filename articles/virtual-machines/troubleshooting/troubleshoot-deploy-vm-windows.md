---
title: 針對 Azure 中的 Windows 虛擬機器部署問題進行疑難排解 | Microsoft Docs
description: 針對 Azure Resource Manager 部署模型中的 Windows 虛擬機器部署問題進行疑難排解。
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 03804229221c2b1deb94f6c32a5be9defd304ff6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82628277"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>針對 Azure 中的 Windows 虛擬機器部署問題進行疑難排解

若要針對 Azure 中的虛擬機器 (VM) 部署問題進行疑難排解，請檢閱[常見問題](#top-issues)以了解常見的失敗和解決方式。

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。

## <a name="top-issues"></a>常見問題
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>叢集無法支援要求的 VM 大小
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- 以較小的 VM 大小重試要求。
- 如果無法變更要求的 VM 的大小︰
    - 停止可用性設定組中的所有 VM。 按一下 [資源群組]**** > 您的資源群組 > [資源]**** > 您的可用性設定組 > [虛擬機器]**** > 您的虛擬機器 > [停止]****。
    - 在所有 VM 都停止後，建立所需大小的 VM。
    - 先啟動新的 VM，然後選取每個已停止的 VM 並按一下 [啟動]。


## <a name="the-cluster-does-not-have-free-resources"></a>叢集沒有可用的資源
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- 稍後再重試要求。
- 如果新的 VM 可以屬於不同的可用性設定組
    - 在不同的可用性設定組 (位於相同區域) 中建立 VM。
    - 將新的 VM 加入相同的虛擬網路。

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>我要如何使用 Windows 用戶端映像並將其部署到 Azure 中？

如果您有適當的 Visual Studio (先前稱為 MSDN) 訂用帳戶，您可以在 Azure 中使用 Windows 7、Windows 8 或 Windows 10 來進行開發/測試案例。 這篇[文章](../windows/client-images.md)概述在 Azure 中執行 Windows 用戶端和使用「Azure 資源庫」映像的資格需求。

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>我要如何使用 Hybrid Use Benefit (HUB) 來部署虛擬機器？

有幾種不同方式可以使用 Azure Hybrid Use Benefit 來部署 Windows 虛擬機器。

針對 Enterprise 合約訂用帳戶：

•   從已使用 Azure Hybrid Use Benefit 來預先設定的特定 Marketplace 映象部署 VM。

針對 Enterprise 合約：

•   上傳自訂的 VM 並使用 Resource Manager 範本或 Azure PowerShell 來進行部署。

如需詳細資訊，請參閱下列資源：

 - [Azure 混合式使用權益總覽](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [可下載的常見問題集](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf) \(英文\)

 - [適用于 Windows Server 和 Windows 用戶端的 Azure 混合式使用權益](../windows/hybrid-use-benefit-licensing.md)。

 - [我要如何在 Azure 中使用 Hybrid Use Benefit in Azure](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>我要如何啟用我的 Visual Studio Enterprise (BizSpark) 每月點數

若要啟用您的每月點數，請參閱這篇[文章](https://azure.microsoft.com/offers/ms-azr-0064p/)。

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>如何將 Enterprise 開發/測試新增到我的 Enterprise 合約 (EA) 以取得 Window 用戶端映像的存取權？

能夠根據 Enterprise 開發/測試供應項目來建立訂用帳戶的人員，僅限於已獲 Enterprise 系統管理員授與此動作所需權限的「帳戶擁有者」。 「帳戶擁有者」需透過「Azure 帳戶入口網站」建立訂用帳戶，然後應將使用中的 Visual Studio 訂閱者新增為共同系統管理員。 如此一來，他們才能管理及使用開發和測試所需的資源。 如需詳細資訊，請參閱 [Enterprise 開發/測試](https://azure.microsoft.com/offers/ms-azr-0148p/)。

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>我的 Windows N 系列 VM 遺失驅動程式

安裝適用于 Windows Vm 之驅動程式的指示位於[這裡](../sizes-gpu.md#supported-operating-systems-and-drivers)。

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>在我的 N 系列 VM 內找不到 GPU 執行個體

若要利用 Azure N 系列 Vm 的 GPU 功能，您必須在部署之後于每個 VM 上安裝圖形驅動程式。 您可以[在這裡](../sizes-gpu.md#supported-operating-systems-and-drivers)取得驅動程式設定資訊。

## <a name="are-n-series-vms-available-in-my-region"></a>我的區域中是否有 N 系列 Vm 可用？

您可以從[依區域提供的產品表](https://azure.microsoft.com/regions/services)查看是否有提供該產品，以及從[這裡](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)查看定價。

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>我可以在 Azure 中使用及部署哪些用戶端映像，以及如何取得這些映像？

假設您有適當的 Visual Studio (先前稱為 MSDN) 訂用帳戶，您可以在 Azure 中使用 Windows 7、Windows 8 或 Windows 10 進行開發/測試案例。 

- 「Azure 資源庫」的[符合資格者的開發/測試供應項目](../windows/client-images.md#eligible-offers)內有提供 Windows 10 映像。 
- 屬於任一供應項目類型的 Visual Studio 訂閱者也可以[適當地準備和建立](../windows/prepare-for-upload-vhd-image.md) 64 位元的 Windows 7、Windows 8 或 Windows 10 映像，然後[上傳至 Azure](../windows/upload-generalized-managed.md)。 僅限有效的 Visual Studio 訂閱者用於開發/測試用途。

這篇[文章](../windows/client-images.md)概述在 Azure 中執行 Windows 用戶端和使用「Azure 資源庫」映像的資格需求。

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>我在調整 VM 大小時，看不到所要的 VM 大小系列。

執行 VM 時，會將 VM 部署到實體伺服器。 Azure 區域中的實體伺服器會群組成共同的實體硬體叢集。 調整 VM 大小時如果需要將 VM 移到不同的硬體叢集，將會依部署 VM 時所用的部署模型而有不同的做法。

- 如果 VM 是以「傳統」部署模型部署的，就必須移除雲端服務部署後再重新部署，才能將 VM 變更成另一個大小系列中的大小。

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

- 如果 VM 是以 Resource Manager 部署模型部署的，您就必須先停止可用性設定組中的所有 VM，才能變更該可用性設定組中任何 VM 的大小。

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>部署在可用性設定組中時，不支援所列的 VM 大小。

請選擇可用性設定組叢集上支援的大小。 建議您在建立可用性設定組時，選擇您認為所需的最大 VM 大小，並且以它作為您的第一個可用性設定組部署項目。

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>我是否可以將現有的傳統 VM 新增到可用性設定組？

是。 您可以將現有的傳統 VM 新增到新的或現有的「可用性設定組」。 如需詳細資訊，請參閱[將現有的虛擬機器新增至可用性設定組](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine)。


## <a name="next-steps"></a>後續步驟
如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。

或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
