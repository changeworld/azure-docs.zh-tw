---
title: 使用 Azure 實驗室服務設定 SOLIDWORKS 實驗室以進行工程設計 |Microsoft Docs
description: 瞭解如何使用 SOLIDWORKS 來設定工程課程的實驗室。
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 5511ad5a517bbd320ce3d66de90a8aec084c7e15
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290729"
---
# <a name="set-up-a-lab-for-engineering-classes-using-solidworks"></a>使用 SOLIDWORKS 設定工程類別的實驗室

[SOLIDWORKS](https://www.solidworks.com/)提供3d 電腦輔助設計（CAD）環境來模型化實體物件，並用於不同種類的工程欄位。  透過 SOLIDWORKS，工程師可以輕鬆地建立、視覺化、模擬和記錄其設計。

大學常使用的授權選項是 SOLIDWORKS 的網路授權。   使用此選項時，使用者會共用授權伺服器所管理的授權集區。  這種類型的授權有時稱為「浮動」授權，因為您只需要有足夠的並行使用者數目授權。  當使用者使用 SOLIDWORKS 完成時，他們的授權會回到集中管理的授權集區，讓另一位使用者可以重複使用它。

在本文中，我們將示範如何設定使用 SOLIDWORKS 2019 和網路授權的類別。

## <a name="license-server"></a>授權伺服器

SOLIDWORKS 網路授權要求您必須在授權伺服器上安裝並啟用 SolidNetWork 授權管理員。  此授權伺服器通常位於您的內部部署網路或 Azure 中的私人網路。  如需有關如何在您的伺服器上設定 SolidNetWork 授權管理員的詳細資訊，請參閱 SOLIDWORKS 安裝指南中的[安裝和啟用授權管理員](https://help.solidworks.com/2019/English/Installation/install_guide/t_installing_snl_lic_mgr.htm)。  設定此設定時，請記住所使用的**埠號碼**和[**序號**](https://help.solidworks.com/2019/english/installation/install_guide/r_hid_state_serial_number.htm)，因為在後續步驟中將會用到它們。

設定授權伺服器之後，您必須將[虛擬網路（VNet）](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)對等互連至您的[實驗室帳戶](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account)。  您必須先完成網路對等互連，才能建立實驗室，讓實驗室虛擬機器可以存取授權伺服器，並以另一種方式進行。

> [!NOTE]
> 您應該確認防火牆上已開啟適當的埠，以允許實驗室虛擬機器與授權伺服器之間的通訊。  例如，請參閱[針對 Windows 防火牆修改授權管理員電腦埠](http://help.solidworks.com/2019/english/installation/install_guide/t_mod_ports_on_lic_mgr_for_firewall.htm)的指示，以瞭解如何將輸入和輸出規則新增至授權伺服器的防火牆。  您可能也需要開啟實驗室虛擬機器的埠。  請遵循適用于[實驗室的防火牆設定](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-firewall-settings)一文中的步驟，以取得有關此項的詳細資訊，包括如何取得實驗室的公用 IP 位址。

## <a name="lab-configuration"></a>實驗室組態

若要設定此實驗室，您需要 Azure 訂用帳戶和實驗室帳戶，才能開始使用。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 取得 Azure 訂用帳戶之後，您可以在 Azure 實驗室服務中建立新的實驗室帳戶。 如需建立新實驗室帳戶的詳細資訊，請參閱[如何設定實驗室帳戶](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account)的教學課程。 您也可以使用現有的實驗室帳戶。

### <a name="lab-account-settings"></a>實驗室帳戶設定

針對實驗室帳戶啟用下表所述的設定。 如需如何啟用 marketplace 映射的詳細資訊，請參閱[如何指定可供實驗室建立者使用的 marketplace 映射](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)一文。

| 實驗室帳戶設定 | Instructions |
| ------------------- | ------------ |
|Marketplace 映像| 啟用 Windows 10 Pro 映射以在實驗室帳戶中使用。|

> [!NOTE]
> 除了 Windows 10 以外，SOLIDWORKS 還支援其他版本的 Windows。  如需詳細資訊，請參閱[SOLIDWORKS 系統需求](https://www.solidworks.com/sw/support/SystemRequirements.html)。

### <a name="lab-settings"></a>實驗室設定

設定教室實驗室時，請使用下表中的設定。 如需有關如何建立教室實驗室的詳細資訊，請參閱設定教室實驗室教學課程。

| 實驗室設定 | 值/指示 |
| ------------ | ------------------ |
|虛擬機器大小| **小型 GPU （視覺效果）**。  此 VM 最適用于使用 OpenGL 和 DirectX 這類架構的遠端視覺效果、串流、遊戲、編碼。|  
|虛擬機器映像| Windows 10 Pro|

> [!NOTE]
> **小型 GPU （視覺效果）** 虛擬機器大小設定為啟用高效能的圖形體驗。  如需有關此虛擬機器大小的詳細資訊，請參閱有關[如何使用 gpu 設定實驗室](./how-to-setup-lab-gpu.md)的文章。

> [!WARNING]
> 在建立實驗室**之前**，請記得將實驗室帳戶的[虛擬網路對等](https://www.mathworks.com/support/requirements/matlab-system-requirements.html)互連至授權伺服器的虛擬網路。

## <a name="template-virtual-machine-configuration"></a>範本虛擬機器設定

本節中的步驟示範如何藉由下載 SOLIDWORKS 安裝檔案並安裝用戶端軟體，來設定您的範本虛擬機器：

1. 啟動範本虛擬機器，並使用 RDP 連接到電腦。

1. 下載 SOLIDWORKS 用戶端軟體的安裝檔案。 您有兩個選項可供下載：
   - 從[SOLIDWORKS 客戶入口網站](https://login.solidworks.com/nidp/idff/sso?id=cpenglish&sid=1&option=credential&sid=1&target=https%3A%2F%2Fcustomerportal.solidworks.com%2F)下載。
   - 從伺服器上的目錄下載。  如果您使用此選項，您必須確定可以從範本虛擬機器存取伺服器。  例如，此伺服器可能位於與您的實驗室帳戶對等互連的相同虛擬網路中。
  
    如需詳細資訊，請參閱 SOLIDWORKS 安裝指南[中 SOLIDWORKS 的個別電腦上的安裝](http://help.solidworks.com/2019/english/Installation/install_guide/c_installing_on_individual_computers.htm?id=fc149e8a968a422a89e2a943265758d3#Pg0)。

1. 下載安裝檔案之後，請使用 SOLIDWORKS 安裝管理員來安裝用戶端軟體。 請參閱 SOLIDWORKS 安裝指南中有關[安裝授權用戶端](http://help.solidworks.com/2019/english/installation/install_guide/t_installing_snl_license_client.htm)的詳細資料。

    > [!NOTE]
    > 在 [**新增伺服器**] 對話方塊中，系統會提示您輸入授權伺服器所使用的**埠號碼**，以及授權伺服器的名稱或 IP 位址。

## <a name="cost"></a>成本

讓我們來討論這個類別的可能成本預估。 這項預估不包括執行授權伺服器的成本。 我們將使用25名學生的課程。 已排程的類別時間有20小時。 此外，每個學生在排程的類別時間以外，會取得家庭或指派10小時的配額。 我們選擇的虛擬機器大小為**小型 GPU （視覺效果）**，也就是160實驗室單位。

25名學生 \* （20個排程的時數 + 10 個配額小時） \* 160 個實驗室單位 * 每小時0.01 美元 = 1200.00 美元

>[!IMPORTANT]
> 成本預估僅供範例之用。  如需定價的目前詳細資料，請參閱[Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。  

## <a name="next-steps"></a>後續步驟

下一步是設定任何實驗室的常見步驟。

- [建立和管理範本](how-to-create-manage-template.md)
- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定排程](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [學生的電子郵件註冊連結](how-to-configure-student-usage.md#send-invitations-to-users)
