---
title: Azure Advisor Windows 虛擬桌面逐步解說-Azure
description: 如何解決 Windows 虛擬桌面的 Azure Advisor 建議。
author: Heidilohr
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 760e0212d2d863e6b869c23c2e523a0e056a28ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90033025"
---
# <a name="how-to-resolve-azure-advisor-recommendations"></a>如何解決 Azure Advisor 建議

本文說明如何解決在 Windows 虛擬桌面的 Azure Advisor 中出現的建議。

## <a name="no-validation-environment-enabled"></a>「未啟用驗證環境」

>[!div class="mx-imgBorder"]
>![Azure Advisor 營運卓越] 頁面的螢幕擷取畫面。 [未啟用驗證環境] 建議會以紅色反白顯示。](media/no-validation-environment.png)

此建議會出現在卓越的營運中。 建議也應該顯示如下的警告訊息：

「您沒有在此訂用帳戶中啟用驗證環境。 當您建立主機集區時，在 [內容] 索引標籤中為 [驗證環境] 選取 [ **否** ]。若要確保透過 Windows 虛擬桌面服務部署的業務持續性，請確定您至少有一個具有驗證環境的主機集區，可供您測試是否有潛在問題。」

您可以在其中一個主機集區啟用驗證環境，以使此警告訊息消失。

若要啟用驗證環境：

1. 移至您的 Azure 入口網站首頁，然後選取您想要變更的主機集區。

2. 接下來，選取您要從生產環境變更為驗證環境的主機集區。

3. 在您的主機集區中，選取左側資料行上的 [ **屬性** ]。 接下來，向下切入直到您看到「驗證環境」。 選取 **[是]**， **Apply**然後選取 [套用]。

>[!div class="mx-imgBorder"]
>![[屬性] 功能表的螢幕擷取畫面。 [驗證環境] 會以紅色反白顯示，並選取 [是] 反升。](media/validation-yes.png)

這些變更不會立即出現警告，但最後應該會消失。 Azure Advisor 一天更新兩次。 在那之前，您可以手動延期或關閉建議。 建議您讓建議消失。 如此一來，Azure Advisor 可讓您知道在設定變更時是否遇到任何問題。

## <a name="not-enough-production-non-validation-environments-enabled"></a>「沒有足夠的生產 (非驗證) 環境已啟用」

此建議會出現在卓越的營運中。

針對這項建議，會因為下列其中一個原因而出現警告訊息：

- 您的驗證環境中有太多主機集區。
- 您沒有任何生產主機集區。

我們建議使用者在驗證環境中有少於一半的主機集區。

若要解決此警告：

1. 移至您的 Azure 入口網站首頁。

2. 請選取您想要從驗證變更為生產環境的主機集區。

3. 在您的主機集區中，選取畫面右側資料行中的 [ **屬性** ] 索引標籤。 接下來，向下切入直到您看到「驗證環境」。 選取 [**否**]，然後選取 [套用 **]。**

>[!div class="mx-imgBorder"]
>![[屬性] 功能表的螢幕擷取畫面。 [驗證環境] 會以紅色反白顯示，並選取 [否] 反升。](media/validation-no.png)

這些變更不會立即出現警告，但最後應該會消失。 Azure Advisor 一天更新兩次。 在那之前，您可以手動延期或關閉建議。 建議您讓建議消失。 如此一來，Azure Advisor 可讓您知道在設定變更時是否遇到任何問題。

## <a name="not-enough-links-are-unblocked-to-successfully-implement-your-vm"></a>「沒有足夠的連結已解除封鎖，無法成功執行您的 VM」

此建議會出現在卓越的營運中。

您必須解除封鎖特定的 Url，以確保虛擬機器 (VM) 正常運作。 您可以在 [ [安全 URL] 清單](safe-url-list.md)中看到清單。 如果未解除封鎖 Url，則您的 VM 將無法正常運作。

若要解決此建議，請務必解除封鎖 [安全 URL 清單](safe-url-list.md)上的所有 url。 您也可以使用服務標籤或 FQDN 標記來解除封鎖 Url。

## <a name="propose-new-recommendations"></a>建議新的建議

您可以藉由提交建議的建議來協助我們改善 Azure Advisor。 您的建議可協助其他使用者退出困難的地方。 若要提交建議，請前往 [我們的 UserVoice 論壇](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations) ，並填寫提交表單。 當您填寫表單時，請務必盡可能提供詳細的資料。

## <a name="next-steps"></a>後續步驟

如果您想要深入瞭解如何解決常見問題的詳細資訊，請參閱 [Windows 虛擬桌面的疑難排解總覽、意見反應和支援](troubleshoot-set-up-overview.md)。
