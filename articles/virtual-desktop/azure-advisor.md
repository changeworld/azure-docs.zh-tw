---
title: 整合 Windows 虛擬桌面與 Azure Advisor-Azure
description: 如何搭配使用 Azure Advisor 與 Windows 虛擬桌面部署。
author: Heidilohr
ms.topic: how-to
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 76b7f97b6020a3a0d5571a3a105d15f7d7893485
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147438"
---
# <a name="use-azure-advisor-with-windows-virtual-desktop"></a>搭配使用 Azure Advisor 與 Windows 虛擬桌面

Azure Advisor 可協助使用者自行解決常見問題，而不需要提出支援案例。 這些建議可減少提交說明要求的需求，以節省您的時間和成本。

本文將告訴您如何在 Windows 虛擬桌面部署中設定 Azure Advisor，以協助您的使用者。

## <a name="what-is-azure-advisor"></a>何謂 Azure 建議程式？

Azure Advisor 會分析您的設定和遙測，以提供個人化建議來解決常見的問題。 有了這些建議，您可以將 Azure 資源優化，以獲得可靠性、安全性、卓越的營運、效能和成本。 若要深入瞭解，請參閱 [Azure Advisor 網站](https://azure.microsoft.com/services/advisor/)。

## <a name="how-to-start-using-azure-advisor"></a>如何開始使用 Azure Advisor

您只需開始使用 Azure 入口網站上的 Azure 帳戶。 首先，開啟 Azure 入口網站 <https://portal.azure.com/#home> ，然後選取 [ **Azure 服務**] 下的 [ **Advisor** ]，如下圖所示。 您也可以在 Azure 入口網站的搜尋列中輸入「Azure Advisor」。

> [!div class="mx-imgBorder"]
> ![Azure 入口網站的螢幕擷取畫面。 使用者將滑鼠游標停留在 Azure Advisor 連結上，導致下拉式功能表出現。](media/azure-advisor.png)

當您開啟 Azure Advisor 時，您會看到五個類別：

- 成本
- 安全性
- 可靠性
- 卓越營運
- 效能

> [!div class="mx-imgBorder"]
> ![Azure Advisor 的螢幕擷取畫面，其中顯示每個類別目錄的五個功能表。 在自己的方塊中顯示的五個專案包括成本、安全性、可靠性、卓越的營運和效能。](media/advisor-categories.png)

當您選取類別時，您將會移至其使用中的 [建議] 頁面。 在此頁面上，您可以查看 Azure Advisor 為您提供的建議，如下圖所示。

> [!div class="mx-imgBorder"]
> ![適用于卓越營運的有效建議清單螢幕擷取畫面。 此清單會顯示具有不同優先權層級的七個建議。](media/active-suggestions.png)

## <a name="additional-tips-for-azure-advisor"></a>Azure Advisor 的其他秘訣

- 請務必經常檢查您的建議，至少一周執行一次以上。 Azure Advisor 每天更新其作用中建議數次。 檢查新的建議可協助您找出並解決較小的問題，以防止較大的問題。

- 請一律嘗試解決 Azure Advisor 中最高優先權層級的問題。 高優先順序的問題會以紅色標示。 將高優先順序的建議保持無法解析可能會導致問題行。

- 如果建議較不重要，您可以將它關閉或延後。 若要關閉或延遲建議，請移至 [ **動作** ] 資料行，並變更專案的狀態。

- 除非您知道其出現原因，並確定不會對您或您的使用者造成負面影響，否則請不要關閉建議。 一律選取 [ **深入瞭解** ] 以查看問題是什麼。 如果您遵循 Azure Advisor 中的指示解決問題，則會自動從清單中消失。 您可以更妥善地解決問題，而不是重複延遲。

- 當您在 Windows 虛擬桌面中遇到問題時，請一律先檢查 Azure Advisor。 Azure Advisor 將提供您如何解決問題的指示，或至少指向可提供協助的資源。

## <a name="next-steps"></a>接下來的步驟

若要瞭解如何解決建議，請參閱 [如何解決 Azure Advisor 建議](azure-advisor-recommendations.md)。

如果您對新的建議有任何建議，請將它張貼在我們的 [Azure Advisor User Voice 論壇](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations)上。
