---
title: 設定實驗室以使用 Azure 實驗室服務來教授 MATLAB |Microsoft Docs
description: 瞭解如何設定實驗室，以使用 Azure 實驗室服務來教授 MATLAB。
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 137959f51b08dceee150962f77110ee2ac1dc193
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85444993"
---
# <a name="setup-a-lab-to-teach-matlab"></a>設定實驗室來教授 MATLAB

[MATLAB](https://www.mathworks.com/products/matlab.html)（代表矩陣實驗室）是來自[MathWorks](https://www.mathworks.com/)的程式設計平臺。  它結合了運算能力和視覺效果，讓它在數學、工程、物理和化學等領域中，成為熱門的工具。

如果您使用[全校園的授權](https://www.mathworks.com/academia/tah-support-program/administrators.html)，請參閱[下載 MATLAB 安裝](https://www.mathworks.com/matlabcentral/answers/259632-how-can-i-get-matlab-installation-files-for-use-on-an-offline-machine)檔案中的指示，在範本機器上下載 MATLAB 安裝程式檔案。  

在本文中，我們將示範如何設定使用 MATLAB 用戶端軟體搭配授權伺服器的類別。

## <a name="license-server"></a>授權伺服器

在為您的實驗室修改範本機器之前，您必須先設定伺服器來執行[網路授權管理員](https://www.mathworks.com/help/install/administer-network-licenses.html)軟體。  這些指示僅適用于為 MATLAB 選擇網路授權選項的機構，可讓使用者共用授權金鑰集區。  您也需要儲存授權檔案和檔案安裝金鑰，以供稍後用。  如需有關如何下載授權檔案的詳細指示，請參閱[使用網際網路連線安裝網路授權管理員](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html)一文中的第一個步驟。

如需有關如何安裝授權伺服器的詳細指示，請前往[安裝具有網際網路連線的網路授權管理員](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html)。  若要啟用借用，請參閱[借用授權](https://www.mathworks.com/help/install/license/borrow-licenses.html)一文。

假設授權伺服器位於內部部署網路或 Azure 中的私人網路內，別忘了將[虛擬網路對等](how-to-connect-peer-virtual-network.md)互連至您的[實驗室帳戶](tutorial-setup-lab-account.md)。  您必須先完成網路對等互連，才能建立實驗室，讓實驗室虛擬機器可以存取授權伺服器。

## <a name="lab-configuration"></a>實驗室組態

若要設定此實驗室，您需要 Azure 訂用帳戶才能開始進行。  如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 取得 Azure 訂用帳戶之後，您可以在 Azure 實驗室服務中建立新的實驗室帳戶，或使用現有的帳戶。  若要建立新的實驗室帳戶，請參閱[設定實驗室帳戶教學](tutorial-setup-lab-account.md)課程。

若要建立新的實驗室，請遵循[設定教室實驗室教學課程](tutorial-setup-classroom-lab.md)。  套用下列設定：

| 虛擬機器大小 | Image |
| -------------------- | ----- |
| 中 | Windows 10 |

更多作業系統都支援 MATLAB。  如需詳細資訊，請參閱[MATLAB 系統需求](https://www.mathworks.com/support/requirements/matlab-system-requirements.html)。

> [!WARNING]
> 在建立實驗室之前，請記得將實驗室帳戶的[虛擬網路對等](https://www.mathworks.com/support/requirements/matlab-system-requirements.html)互連至授權伺服器的虛擬網路。

## <a name="template-machine"></a>範本機器

建立範本機器之後，請啟動電腦並聯機到它，以完成下列主要工作。

1. 下載 MATLAB 用戶端軟體的安裝檔案。
2. 使用檔案安裝金鑰來安裝 MATLAB。

安裝 MATLAB 將會是多部分程式。  第一個部分會下載 MATLAB 的檔案，以及您想要安裝的任何其他產品。  使用檔案安裝金鑰需要預先下載產品的所有安裝檔案。  第二個部分將會在範本 VM 上安裝 MATLAB 軟體，並啟用軟體。  如果範本 VM 設定為使用授權伺服器啟用，學生 Vm 將會執行相同的動作。

### <a name="download-installation-files"></a>下載安裝檔案

您必須是授權管理員，才能下載安裝檔案，以及取得授權檔案和檔案安裝金鑰。  下載安裝檔案的步驟如下所示。

1. 登入您的帳戶 [https://www.mathworks.com](https://www.mathworks.com) 。
2. 選擇 [**我的帳戶**]。
3. 在 [帳戶] 頁面的 [**我的軟體**] 區段下，按一下附加至實驗室之 [網路授權管理員] 安裝程式的授權。
4. 在 [授權詳細資料] 頁面上，按一下 [**下載產品**]。
5. 等待安裝程式自行解壓縮。
6. 開始安裝程式。  
7. 在 [登**入您的 MathWorks 帳戶**] 頁面上，輸入您的 MathWorks 帳戶。
8. 在 [ **MathWorks 授權合約**] 頁面上，接受條款，然後按 [**下一步]** 按鈕。
9. 按一下 [ **Advanced Options** ] 下拉式選單，然後選擇 [**我想要下載但不安裝**]。
10. 在 [**選取目的地] 資料夾**中，按 **[下一步]**。
11. 選取 [ **Windows** ] 作為您要安裝 MATLAB 之電腦的平臺。
12. 在 [**選取產品**] 頁面上，確定已選取 [MATLAB] 以及您想要安裝的任何其他 MathWorks 產品。
13. 在 [**確認選取和下載**] 頁面上，按一下 [**開始下載**]。  
14. 等待選取的產品下載。  按一下 [完成] 。

您也可以從 MathWorks 網站下載 ISO 映像。

1. 登入您的帳戶 [https://www.mathworks.com](https://www.mathworks.com) 。
2. 移至 [https://www.mathworks.com/downloads](https://www.mathworks.com/downloads)。
3. 選取您想要安裝的 MATLAB 版本。
4. 按一下相關連結下方的「取得 {版本} .iso 映像」連結，其中 {version} 類似 R2020a。
5. 按一下 [適用于 Windows 的藍色**下載版本**] 連結。

### <a name="run-installer"></a>執行安裝程式

下載檔案之後，第二個步驟是執行安裝程式。 同樣地，您必須是授權管理員才能完成此步驟。  只有授權系統管理員可以使用檔案安裝金鑰來安裝 MATLAB。

1. 檢查下載的授權檔案，並確認伺服器行會正確列出授權伺服器。  如需有關如何格式化授權檔案的詳細資訊，請參閱[更新網路授權](https://www.mathworks.com/help/install/ug/network-license-files.html)、[授權借款](https://www.mathworks.com/help/install/license/borrow-licenses.html)和[尋找主機識別碼](https://www.mathworks.com/matlabcentral/answers/101892-what-is-a-host-id-how-do-i-find-my-host-id-in-order-to-activate-my-license)文章。
2. 啟動 MATLAB 安裝程式。
3. 在 [登**入您的 MathWorks 帳戶**] 頁面上，輸入您的 MathWorks 帳戶。
4. 在 [ **MathWorks 授權合約**] 頁面上，接受條款，然後按 [**下一步]** 按鈕。
5. 按一下 [ **Advanced Options** ] 下拉式選單，然後選擇 [**我有檔案安裝金鑰**]。
6. 在 [**使用檔案安裝金鑰安裝**] 頁面上，輸入授權伺服器的檔案安裝金鑰。   按 [下一步] 。
7. 在 [**選取授權檔案**] 頁面上，流覽至稍早下載安裝檔案時所儲存的授權檔案。
8. 在 [**選取目的地資料夾**] 頁面上，按 **[下一步]**。
9. 在 [**選取產品**] 頁面上，按 **[下一步]**。
10. 在 [**選取選項**] 頁面上，按 **[下一步]**。
11. 在 [**確認選取和安裝**] 頁面上，按一下 [**開始安裝**]。
12. 在 [**安裝完成**] 頁面上，確認已核取 [**啟用 MATLAB** ]。  按一下 [完成] 。

## <a name="cost-estimate"></a>成本預估

讓我們來討論這個類別的可能成本預估。  這項預估不包括執行授權伺服器的成本。  我們將使用25名學生的課程。  已排程的類別時間有20小時。  此外，每個學生在排程的類別時間以外，會取得家庭或指派10小時的配額。  我們選擇的虛擬機器大小為「中」，也就是55實驗室單位。

以下是此類別的可能成本預估範例：

25名學生 \* （20個已排程的時數 + 10 個配額小時） \* 55 個實驗室單位 \* 每小時0.01 美元 = 412.50 美元

>[!IMPORTANT]
> 成本預估僅供範例之用。  如需定價的目前詳細資料，請參閱[Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。  

## <a name="next-steps"></a>後續步驟

下一步是設定任何實驗室的常見步驟。

- [建立、管理及發佈範本](how-to-create-manage-template.md)
- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定排程](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [學生的電子郵件註冊連結](how-to-configure-student-usage.md#send-invitations-to-users)
