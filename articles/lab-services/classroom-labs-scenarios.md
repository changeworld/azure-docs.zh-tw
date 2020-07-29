---
title: 使用教室實驗室進行訓練 - Azure Lab Services
description: 本文說明如何使用 Azure DevTest Labs 在 Azure 上建立實驗室，以進行訓練案例。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 582508968609ba53de14b95b71ee75b5fec9ba93
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445248"
---
# <a name="use-classroom-labs-for-trainings"></a>使用教室實驗室進行訓練
Azure Lab Services 可讓教師 (老師、教授、講師或助教等) 快速且輕鬆地建立線上實驗室，為受訓者佈建預先設定的學習環境。 每個受訓者都可以使用相同且隔離的環境進行訓練。 您可套用原則，以確保訓練環境只會在受訓者需要時才提供給他們使用，且包含足夠的訓練所需資源，例如虛擬機器。 

![教室實驗室](./media/classroom-labs-scenarios/classroom.png)

教室實驗室符合下列在任何虛擬環境進行訓練所必須具備的需求︰ 

- 受訓者可以快速佈建其訓練環境
- 每台訓練用機器應該相同
- 受訓者無法看到其他受訓者所建立的 VM
- 確保受訓者無法取得超過訓練所需數量的 VM 以控制成本，並在受訓者不使用 VM 時將其關閉
- 輕易地與每位受訓者共用訓練實驗室
- 一再重複使用訓練實驗室

在本文中，您會了解可用來符合先前所述訓練需求的各種 Azure Lab Services 功能，以及可供遵循的訓練實驗室詳細設定步驟。  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>將實驗室帳戶建立為實驗室帳戶管理員
使用 Azure Lab Services 的第一個步驟是在 Azure 入口網站中建立實驗室帳戶。 在實驗室帳戶管理員建立實驗室帳戶之後，管理員會將想要建立實驗室的使用者新增至 [實驗室建立者] 角色。 教師使用虛擬機器建立實驗室，讓學生針對他們教導的課程進行練習。 如需詳細資訊，請參閱[建立和管理實驗室帳戶](how-to-manage-lab-accounts.md)。

## <a name="create-and-manage-classroom-labs"></a>建立和管理教室實驗室
身為實驗室帳戶中 [實驗室建立者] 角色成員的教師，可以在實驗室帳戶中建立一或多個實驗室。 您可以使用所有必要的軟體來建立及設定範本 VM，以便在課程中進行練習。 您會從可用的映像中挑選現成的映像，以便建立教室實驗室，然後藉由安裝實驗室所需的軟體進行自訂。 如需詳細資訊，請參閱[建立和管理教室實驗室](how-to-manage-classroom-labs.md)。

## <a name="configure-usage-settings-and-policies"></a>設定使用方式設定和原則
實驗室建立者可以在實驗室中新增或移除使用者、取得註冊連結以傳送給實驗室使用者、設定原則 (例如設定每位使用者的個別配額)、更新實驗室中可用的 VM 數目等等。 如需詳細資訊，請參閱[設定使用方式設定和原則](how-to-configure-student-usage.md)。

## <a name="create-and-manage-schedules"></a>建立和管理排程
排程可讓您設定教室實驗室，使實驗室中的 VM 可在指定的時間自動啟動和關閉。 您可以定義一次性排程或週期性排程。 如需詳細資訊，請參閱[建立和管理教室實驗室的排程](how-to-create-schedules.md)。

## <a name="set-up-and-publish-a-template-vm"></a>設定和發佈範本 VM
實驗室中的範本是所有使用者的虛擬機器據以建立的基底虛擬機器映像。 設定範本 VM 以便能確實設定您要提供給訓練出席者的 VM。 您可以提供要給實驗室使用者查看的範本名稱與描述。 然後，發佈範本以便讓範本 VM 的執行個體可供實驗室使用者使用。 當您發佈範本時，Azure 實驗室服務會使用範本在實驗室中建立 VM。 在此程序中建立的 VM 數目與實驗室中允許的使用者數量上限相同 (此上限可在實驗室的使用原則中設定)。 所有虛擬機器都有與範本相同的設定。 如需詳細資訊，請參閱[設定和發佈範本虛擬機器](how-to-create-manage-template.md)。 

## <a name="use-vms-in-the-classroom-lab"></a>在教室實驗室中使用 VM
學生或訓練出席者會向實驗室註冊並連線至 VM，以進行課程練習。 如需詳細資訊，請參閱[如何存取教室實驗室](how-to-use-classroom-lab.md)。

## <a name="next-steps"></a>後續步驟
遵循下列文章中的指示，開始在教室實驗室中建立實驗室帳戶：[教學課程：使用 Azure Lab Services 設定實驗室帳戶](tutorial-setup-lab-account.md)。