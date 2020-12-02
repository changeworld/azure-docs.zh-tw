---
title: 如何使用 Azure 資訊安全中心保護 Windows Admin Center 伺服器
description: 本文說明如何整合 Azure 資訊安全中心與 Windows Admin Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: memildin
ms.openlocfilehash: ca3dd410474af0a42a3cd5b4217c3bba90b8ff1a
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511979"
---
# <a name="protect-windows-admin-center-resources-with-security-center"></a>使用安全中心保護 Windows Admin Center 資源

Windows Admin Center 是適用于 Windows 伺服器的管理工具。 它是系統管理員用來存取大部分最常使用之管理工具的單一位置。 在 Windows Admin Center 中，您可以直接將內部部署伺服器上架到 Azure 資訊安全中心。 然後，您可以直接在 Windows Admin Center 體驗中查看安全性建議和警示的摘要。

> [!NOTE]
> 您的 Azure 訂用帳戶和相關聯的 Log Analytics 工作區都必須啟用 Azure Defender，才能啟用 Windows Admin Center 整合。
> 如果您先前未在訂用帳戶和工作區上使用 Azure Defender，則前30天免費。 如需詳細資訊，請參閱 [定價資訊頁面](security-center-pricing.md)。
>

當您成功將伺服器從 Windows Admin Center 上線到 Azure 資訊安全中心時，您可以：

* 在 Windows Admin Center 中的安全性中心延伸模組內查看安全性警示和建議
* 查看安全性狀態，並在 Azure 入口網站 (或透過 API 取得資訊安全中心內 Windows Admin Center 受控伺服器的其他詳細資訊) 

藉由結合這兩個工具，資訊安全中心會變成您的單一窗格，以查看您的所有安全性資訊，無論資源：保護您 Windows Admin Center 管理的內部部署伺服器、Vm，以及任何額外的 PaaS 工作負載。

## <a name="onboard-windows-admin-center-managed-servers-into-security-center"></a>將 Windows Admin Center 受控伺服器上架到安全性中心

1. 從 Windows Admin Center 選取其中一個伺服器，然後在 [ **工具** ] 窗格中選取 Azure 資訊安全中心擴充功能：

    ![Windows Admin Center 中的 Azure 資訊安全中心擴充功能](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > 如果伺服器已上線至「安全性中心」，則不會顯示 [設定] 視窗。

1. 按一下 [登 **入 Azure 並設定**]。
    ![將 Windows Admin Center 擴充功能上架到 Azure 資訊安全中心](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. 請依照指示將您的伺服器連線到「安全中心」。 當您輸入必要的詳細資料並確認之後，資訊安全中心會進行必要的設定變更，以確保下列各項都成立：
    * Azure 閘道已註冊。
    * 伺服器具有要報告的工作區，以及相關聯的訂閱。
    * 在工作區上啟用資訊安全中心的 Log Analytics 解決方案。 此解決方案會針對向此工作區報告的 *所有* 伺服器和虛擬機器，提供 Azure Defender 的功能。
    * 訂用帳戶上已啟用適用于伺服器的 Azure Defender。
    * Log Analytics 代理程式會安裝在伺服器上，並設定為向選取的工作區報告。 如果伺服器已經向另一個工作區報告，則也會將它設定為向新選取的工作區報告。

    > [!NOTE]
    > 上架之後可能需要一些時間才會顯示建議。 事實上，根據您的伺服器活動，您可能不會收到 *任何* 警示。 若要產生測試警示以測試您的警示是否正常運作，請依照 [警示驗證](security-center-alert-validation.md)程式中的指示進行。


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>在 Windows Admin Center 中查看安全性建議和警示

上線之後，您就可以直接在 Windows Admin Center 的 Azure 資訊安全中心區域中查看警示和建議。 按一下建議或警示，以在 Azure 入口網站中加以查看。 在那裡，您將會取得其他資訊，並瞭解如何修復問題。

[![Windows Admin Center 中所見的安全性中心建議和警示](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>查看安全中心內 Windows Admin Center 受管理伺服器的安全性建議和警示
從 Azure 資訊安全中心：

* 若要查看所有 Windows Admin Center 伺服器的安全性建議，請開啟 [ [資產清查](asset-inventory.md) ]，然後篩選至您要調查的電腦類型。 選取 [ **vm 和電腦** ] 索引標籤。

* 若要查看所有 Windows Admin Center 伺服器的安全性警示，請開啟 [ **安全性警示**]。 按一下 [ **篩選** ]，並確定 **只** 選取 [非 Azure]：

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="篩選 Windows Admin Center 受管理伺服器的安全性警示" lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::