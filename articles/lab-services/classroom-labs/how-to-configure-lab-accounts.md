---
title: 在 Azure 實驗室服務中設定 VM 的自動關機
description: 本文說明如何在實驗室帳戶中設定 VM 的自動關機。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: afe74e5c3aec2519ec22eee9573ae7b47c1c73f8
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588202"
---
# <a name="configure-automatic-shutdown-of-vms-on-disconnect-setting-for-a-lab-account"></a>在實驗室帳戶的中斷連線設定上設定 VM 的自動關機
您可以在遠端桌面連線中斷之後，啟用或停用 Windows 實驗室 VM (範本或學生) 的自動關機。 您也可以指定實驗室服務在自動關機之前，應該等候使用者重新連線的時間長度。

## <a name="enable-automatic-shutdown"></a>啟用自動關機

1. 在 [實驗室帳戶] 頁面上，選取左側功能表上的 [實驗室設定]。
2. 選取 [在使用者中斷連線時自動將虛擬機器關機] 選項。
3. 指定實驗室服務在將 VM 自動關機之前，應該等候使用者重新連線的時間長度。

    ![實驗室帳戶的自動關機設定](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

    此設定適用於在實驗室帳戶中建立的所有實驗室。 實驗室建立者 (授課者) 可以在實驗室層級覆寫此設定。 對於實驗室帳戶的此設定所進行的變更，只會影響在進行變更之後所建立的實驗室。

    若要停用此設定，請取消核取此頁面上的 [在使用者中斷連線時自動將虛擬機器關機] 選項核取方塊。 

## <a name="next-steps"></a>後續步驟
若要了解實驗室擁有者該如何在實驗室層級設定或覆寫這項設定，請參閱[這篇文章](how-to-enable-shutdown-disconnect.md)
