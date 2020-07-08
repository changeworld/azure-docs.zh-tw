---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ebe112103bc3eb30239e80095db9bb91a33bebf3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67174174"
---
## <a name="troubleshooting-update-failures"></a>更新失敗的疑難排解
**如果您看到升級前檢查失敗的通知，該怎麼辦？**

如果前置檢查失敗，請確定您已經查看頁面底部的詳細通知列。 這會提供有關失敗的前置檢查的指引。 例如，您收到通知表示控制器健康情況檢查和硬體元件健康情況檢查失敗。 移至 [監視] > [硬體健康狀態]****。 您必須確定兩個控制器是狀況良好且在線上。 您也必須確定在 StorSimple 裝置中的所有硬體元件在此刀鋒視窗中都會顯示為狀況良好。 接著，您可以嘗試安裝更新。 如果您無法修正硬體元件問題，您將需要連絡 Microsoft 支援以了解後續步驟。

**如果您收到「無法安裝更新」錯誤訊息，且建議是參考更新疑難排解指南，以判斷失敗的原因，該怎麼辦？**

其中一個可能的原因是您沒有連線到 Microsoft Update 伺服器。 這是需要執行的手動檢查。 如果您失去更新伺服器的連線，更新作業將會失敗。 您可以從 StorSimple 裝置的 Windows PowerShell 介面執行下列 Cmdlet 以檢查連線：

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

在這兩個控制器上執行 Cmdlet。

如果您已經確認連線存在，而且您持續看到這個問題，請連絡 Microsoft 支援以了解後續步驟。

**如果您在將裝置升級至 Update 4 且兩個控制器都是執行 Update 4 時看到更新失敗？**

從 Update 4 開始，如果兩個控制器執行相同的軟體版本，並且有更新錯誤，則控制器不會進入復原模式。 如果裝置軟體有 Hotfix (第一次順序更新) 成功套用至兩個控制器，但是其他 Hotfix (第二次順序和第三次順序) 尚未套用，則會發生這種情況。 從 Update 4 開始，只有在兩個控制器執行不同軟體版本時，控制器才會進入復原模式。 

如果使用者在兩個控制器正在執行 Update 4 時看到更新失敗，我們建議您稍候數分鐘，然後再重試更新。 如果重試未成功，則他們應連絡 Microsoft 支援服務。
