---
title: 通過瀏覽器連接到虛擬機器 - Azure |微軟文檔
description: 瞭解如何通過瀏覽器連接到虛擬機器。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: 4549ee0f50c06040b70307c96b5ca96cd4162214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974274"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>通過瀏覽器連接到虛擬機器 

DevTest 實驗室與[Azure 堡壘](https://docs.microsoft.com/azure/bastion/)集成 ，使您能夠通過瀏覽器連接到虛擬機器。 有關如何在 DevTest 實驗室中啟用此功能的資訊，請參閱[在實驗室虛擬機器上啟用瀏覽器連接](enable-browser-connection-lab-virtual-machines.md)。

啟用*瀏覽器連接*後，實驗室使用者可以通過瀏覽器訪問虛擬機器。  

## <a name="create-a-lab-virtual-machine"></a>創建實驗室虛擬機器

您首先需要在虛擬網路中創建實驗室虛擬機器，該虛擬網路在其上配置了堡壘。 選擇您創建的第二**個子網**，而不是 AzureBastion 子網。 您可以通過訪問 **"高級設置"** 選項卡在虛擬機器創建期間選擇虛擬網路。

![建立虛擬機器](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>在瀏覽器中啟動虛擬機器

創建虛擬機器後，您可以通過按一下*瀏覽器連接*按鈕並輸入電腦的使用者名和密碼在瀏覽器中啟動它。  

![在瀏覽器中啟動](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>後續步驟

[在 Azure DevTest Labs 中對實驗室新增 VM](devtest-lab-add-vm.md)
