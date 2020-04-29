---
title: 如何整合 Windows Admin Center 與 Azure 資訊安全中心 |Microsoft Docs
description: 本文說明如何將 Azure 資訊安全中心與 Windows 管理中心整合
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: cbbdf724b9d7fe4948553e7526410b994f491b49
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80435268"
---
# <a name="integrate-azure-security-center-with-windows-admin-center"></a>將 Azure 資訊安全中心與 Windows 系統管理中心整合

Windows 管理中心是適用于 Windows 伺服器的管理工具。 這是系統管理員用來存取大部分最常使用之管理工具的單一位置。 在 Windows 系統管理中心內，您可以將內部內部部署伺服器直接上架到 Azure 資訊安全中心中。 接著，您可以直接在 Windows 管理中心體驗中，查看安全性建議和警示的摘要。

> [!NOTE]
> 您的 Azure 訂用帳戶和相關聯的 Log Analytics 工作區都必須啟用資訊安全中心的標準層，才能啟用 Windows 管理中心整合。
> 如果您先前未在訂用帳戶和工作區上使用標準層，則前30天免費。 如需詳細資訊，請參閱[定價資訊頁面](security-center-pricing.md)。
>

當您成功從 Windows 系統管理中心上架伺服器到 Azure 資訊安全中心時，您可以：

* 在 Windows 系統管理中心的資訊安全中心延伸模組中，查看安全性警示和建議
* 在 Azure 入口網站（或透過 API）中的資訊安全中心，查看安全性狀態，並取出 Windows 管理中心受管理伺服器的其他詳細資訊

藉由結合這兩個工具，資訊安全中心會成為您的單一窗格，以查看所有的安全性資訊，無論資源：保護您的 Windows 管理中心受管理的內部內部部署伺服器、Vm，以及任何其他 PaaS 工作負載。

## <a name="onboarding-windows-admin-center-managed-servers-into-security-center"></a>將 Windows Admin Center 受管理伺服器上線至資訊安全中心

1. 從 Windows 系統管理中心選取其中一部伺服器，然後在 [**工具**] 窗格中，選取 [Azure 資訊安全中心] 延伸模組：

    ![Windows 系統管理中心中的 Azure 資訊安全中心擴充功能](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > 如果伺服器已上架至資訊安全中心，將不會出現 [設定] 視窗。

1. 按一下 [登**入 Azure] 並設定**。
    ![將 Windows 管理中心延伸模組上架至 Azure 資訊安全中心](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. 依照指示將您的伺服器連線到資訊安全中心。 輸入必要的詳細資料並確認之後，資訊安全中心進行必要的設定變更，以確保符合下列所有條件：
    * 已註冊 Azure 閘道。
    * 伺服器具有要向其報告的工作區，以及相關聯的訂用帳戶。
    * 資訊安全中心的標準層 Log Analytics 解決方案已在工作區上啟用。 此解決方案針對向此工作區報告的*所有*伺服器和虛擬機器，提供資訊安全中心的標準層功能。
    * 訂用帳戶上已啟用虛擬機器的資訊安全中心標準層定價。
    * Log Analytics 代理程式會安裝在伺服器上，並設定為向選取的工作區報告。 如果伺服器已經向另一個工作區報告，則也會將它設定為向新選取的工作區報告。

    > [!NOTE]
    > 在上架之後可能需要一些時間才會出現建議。 事實上，視您的伺服器活動而定，您可能不會收到*任何*警示。 若要產生測試警示以測試您的警示是否正常運作，請遵循[警示驗證](security-center-alert-validation.md)程式中的指示。


## <a name="viewing-security-recommendations-and-alerts-in-windows-admin-center"></a>在 Windows 系統管理中心內查看安全性建議和警示

一旦上架之後，您就可以直接在 Windows 系統管理中心的 [Azure 資訊安全中心] 區域中查看警示和建議。 按一下建議或警示，以在 Azure 入口網站中進行查看。 在這裡，您將會取得其他資訊，並瞭解如何修復問題。

[![資訊安全中心 Windows 系統管理中心所見的建議和警示](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="viewing-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>在資訊安全中心中查看 Windows 管理中心受管理伺服器的安全性建議和警示
從 Azure 資訊安全中心：

* 若要查看所有 Windows Admin Center 伺服器的安全性建議，請開啟 [**計算 & 應用程式**]，然後按一下 [ **vm 和電腦**] 索引標籤。依資源 "Server" 篩選清單，如下所示：

    [![查看 Windows 管理中心受管理伺服器的安全性建議](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* 若要查看所有 Windows Admin Center 伺服器的安全性警示，請開啟 [**安全性警示**]。 按一下 [**篩選**]，並確定**只**選取 [非 Azure]：

    ![篩選 Windows 管理中心受管理伺服器的安全性警示](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![查看 Windows 管理中心受管理伺服器的安全性警示](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)