---
title: 如何將 Windows 管理中心與 Azure 安全中心集成 |微軟文檔
description: 本文介紹如何將 Azure 安全中心與 Windows 管理中心集成
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5467794bf246fab4ff7ded9c445dbeee0c4093b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139618"
---
# <a name="integrate-azure-security-center-with-windows-admin-center"></a>將 Azure 安全中心與 Windows 管理中心集成

Windows 管理中心是 Windows 伺服器的管理工具。 它是系統管理員訪問大多數最常用的管理工具的單個位置。 在 Windows 管理中心內，可以直接將機上伺服器登機到 Azure 安全中心。 然後，您可以在 Windows 管理中心體驗中直接查看安全建議和警報的摘要。

> [!NOTE]
> Azure 訂閱和關聯的日誌分析工作區都需要啟用安全中心的標準層才能啟用 Windows 管理中心集成。
> 如果您以前未在訂閱和工作區上使用標準層，則標準層在前 30 天內是免費的。 有關詳細資訊，請參閱[定價資訊頁](security-center-pricing.md)。
>

成功將伺服器從 Windows 管理中心連接到 Azure 安全中心後，可以：

* 在 Windows 管理中心的安全中心擴展室內查看安全警報和建議
* 查看安全狀況，並在 Azure 門戶（或通過 API）的安全中心中檢索 Windows 管理中心託管伺服器的其他詳細資訊

通過組合這兩個工具，安全中心將成為您的單一窗格，以查看所有安全資訊，無論資源是什麼：保護您的 Windows 管理中心管理的前期伺服器、VM 和任何其他 PaaS 工作負載。

## <a name="onboarding-windows-admin-center-managed-servers-into-security-center"></a>將 Windows 管理中心託管伺服器安裝到安全中心

1. 在 Windows 管理中心，選擇其中一台伺服器，並在 **"工具"** 窗格中選擇 Azure 安全中心副檔名：

    ![Windows 管理中心中的 Azure 安全中心擴展](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > 如果伺服器已裝機到安全中心，則不會顯示設定視窗。

1. 按一下"**登錄到 Azure 並設置**"。
    ![將 Windows 管理中心擴展到 Azure 安全中心](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. 按照說明將伺服器連接到安全中心。 輸入必要的詳細資訊並確認後，安全中心會進行必要的配置更改，以確保以下所有內容都為 true：
    * 已註冊 Azure 閘道。
    * 伺服器具有要向其報告的工作區和關聯的訂閱。
    * 安全中心的標準層日誌分析解決方案在工作區上啟用。 此解決方案為報告為此工作區*的所有*伺服器和虛擬機器提供安全中心的標準層功能。
    * 安全中心針對虛擬機器的標準層定價在訂閱上啟用。
    * Microsoft 監視代理 （MMA） 安裝在伺服器上，並配置為向所選工作區報告。 如果伺服器已報告到另一個工作區，則將其配置為也報告給新選擇的工作區。

    > [!NOTE]
    > 在加入建議後可能需要一些時間才能顯示建議。 事實上，根據伺服器活動的不同，您可能不會收到*任何*警報。 要生成測試警報以測試警報是否正常工作，請按照[警報驗證過程中](security-center-alert-validation.md)的說明操作。


## <a name="viewing-security-recommendations-and-alerts-in-windows-admin-center"></a>在 Windows 管理中心查看安全建議和警報

板載後，可以直接在 Windows 管理中心的 Azure 安全中心區域查看警報和建議。 按一下建議或警報以查看它們。 在那裡，您將獲得其他資訊，並瞭解如何修復問題。

[![安全中心建議和警報，如 Windows 管理中心所示](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="viewing-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>查看安全中心中 Windows 管理中心託管伺服器的安全建議和警報
從 Azure 安全中心：

* 要查看所有 Windows 管理中心伺服器的安全建議，請打開 **"計算&應用"，** 然後按一下 **"VM 和電腦**"選項卡。

    [![查看 Windows 管理中心託管伺服器的安全建議](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* 要查看所有 Windows 管理中心伺服器的安全警報，請打開**安全警報**。 按一下 **"篩選器**"並確保**僅**選擇"非 Azure"：

    ![篩選 Windows 管理中心託管伺服器的安全警報](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![查看 Windows 管理中心託管伺服器的安全警報](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)