---
title: 透過 Azure 原則啟用虛擬機器的 Automanage
description: 瞭解如何透過 Azure 入口網站內建的 Azure 原則來啟用適用于 Vm 的 Azure Automanage。
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 47f4085ff01526853fab29da2c1bc1a3e8998d23
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933957"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>透過 Azure 原則啟用虛擬機器的 Automanage

如果您想要為許多 Vm 啟用 Automanage，您可以使用內建的 [Azure 原則](..\governance\azure-management.md)來進行。 本文將逐步引導您尋找正確的原則，以及如何加以指派，以便在 Azure 入口網站中啟用 Automanage。


## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)再開始。

> [!NOTE]
> 免費試用帳戶無法存取本教學課程中使用的虛擬機器。 請升級為隨用隨付訂用帳戶。

> [!IMPORTANT]
> 若要啟用 Automanage： **擁有** 者角色或 **參與者** 以及 **使用者存取系統管理員** 角色，則需要下列 RBAC 許可權。


## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com/)。


## <a name="locate-and-assign-the-policy"></a>找出並指派原則

1. 流覽至 Azure 入口網站中的**原則**
1. 移至 [ **定義** ] 窗格
1. 按一下 [ **類別** ] 下拉式清單，以查看可用的選項
1. 選取 [ **啟用 Automanage-Azure 虛擬機器最佳做法** ] 選項
1. 現在清單會更新，以顯示名稱開頭為*Enable Automanage*的內建原則 .。。
1. 按一下 [ *啟用 Automanage-Azure 虛擬機器最佳作法* 內建原則名稱]
1. 按一下原則之後，您現在可以看到 [ **定義** ] 索引標籤

    > [!NOTE]
    > Azure 原則定義可用來設定 Automanage 參數，例如設定設定檔或帳戶。 它也會設定篩選準則，以確保原則僅適用于正確的 Vm。

1. 按一下 [ **指派** ] 按鈕以建立指派
1. 在 [**基本**] 索引標籤中，藉由設定*訂*用帳戶和*資源群組*來填滿**範圍**

    > [!NOTE]
    > 範圍可讓您定義要套用此原則的 Vm。 您可以在訂用帳戶層級或資源群組層級設定應用程式。 如果您設定了資源群組，則目前在該資源群組中的所有 Vm 或任何未來新增的 Vm 將會自動啟用 Automanage。 

1. 按一下 [ **參數** ] 索引標籤，並設定 **Automanage 帳戶** 和所需的設定 **設定檔** 
1. 在 [ **審核 + 建立** ] 索引標籤下，檢查設定
1. 按一下 [**建立**] 以套用指派
1. 在**定義**旁邊的 [**指派**] 索引標籤中，查看您的指派

> [!NOTE]
> 該原則需要一些時間才會開始在目前位於資源群組或訂用帳戶中的 Vm 上生效。


## <a name="next-steps"></a>下一步 

瞭解透過 Azure 入口網站為虛擬機器啟用 Azure Automanage 的另一種方式。 

> [!div class="nextstepaction"]
> [針對 Azure 入口網站中的虛擬機器啟用 Automanage](quick-create-virtual-machines-portal.md)