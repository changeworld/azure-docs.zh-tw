---
title: 從全區域失敗復原
description: 瞭解 Azure App Service 如何協助您維護商務持續性和嚴重損壞修復 (BCDR) 的功能。 從 Azure 中整個區域的失敗復原您的應用程式。
ms.topic: how-to
ms.date: 06/09/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 1388dc11254324f74efcbaa55c97cac2ccd0c026
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073735"
---
# <a name="move-an-app-service-app-to-another-region"></a>將 App Service 應用程式移至另一個區域

本文說明如何在影響整個 Azure 區域的嚴重損壞期間，讓不同 Azure 區域中的 App Service 資源恢復上線。 當嚴重損壞將整個 Azure 區域離線時，該區域中裝載的所有 App Service 應用程式都會進入嚴重損壞修復模式。 有功能可協助您將應用程式還原至不同的區域，或從受影響的應用程式復原檔案。

App Service 資源是區域特定的，且無法跨區域移動。 您必須將應用程式還原至不同區域中的新應用程式，然後為新的應用程式建立鏡像設定或資源。

## <a name="prerequisites"></a>Prerequisites

- 無。 [從快照集還原](app-service-web-restore-snapshots.md) 通常 **需要進** 階層，但在嚴重損壞修復模式中，它會自動啟用受影響的應用程式，不論受影響的應用程式在哪一層。

## <a name="prepare"></a>準備

找出受影響應用程式目前使用的所有 App Service 資源。 例如：

- App Service 應用程式
- [App Service 方案](overview-hosting-plans.md)
- [部署位置](deploy-staging-slots.md)
- [在 Azure 中購買的自訂網域](manage-custom-dns-buy-domain.md)
- [SSL 憑證](configure-ssl-certificate.md)
- [Azure 虛擬網路整合](web-sites-integrate-with-vnet.md)
- [混合式連接](app-service-hybrid-connections.md)。
- [受控識別](overview-managed-identity.md)
- [備份設定](manage-backup.md)

某些資源，例如匯入的憑證或混合式連線，包含與其他 Azure 服務的整合。 如需有關如何跨區域移動這些資源的詳細資訊，請參閱個別服務的檔。

## <a name="restore-app-to-a-different-region"></a>將應用程式還原至不同的區域

1. 在 *不同* 的 Azure 區域中建立新的 App Service 應用程式，而不是受影響的應用程式。 這是嚴重損壞修復案例中的目標應用程式。

1. 在 [Azure 入口網站](https://portal.azure.com)中，流覽至受影響應用程式的管理頁面。 在失敗的 Azure 區域中，受影響的應用程式會顯示警告文字。 按一下警告文字。

    ![受影響應用程式頁面的螢幕擷取畫面。 會顯示警告通知，說明這種情況，並提供用來還原應用程式的連結。](media/manage-disaster-recovery/restore-start.png)

1. 在 [ **還原備份** ] 頁面中，根據下表設定還原作業。 完成後，請按一下 **[確定]** 。

   | 設定 | 值 | 說明 |
   |-|-|-|
   | **快照 (預覽) ** | 選取快照集。 | 有兩個最新的快照集可以使用。 |
   | **還原目的地** | **現有的應用程式** | 按一下下方的附注，指出 **按一下這裡可變更還原目的地應用程式** ，並選取目標應用程式。 在嚴重損壞的情況下，您只能將快照集還原至不同 Azure 區域中的應用程式。 |
   | **還原網站設定** | **是** | |

    ![[還原備份] 頁面的螢幕擷取畫面。 特定快照集、上表所列的選項以及 [確定] 按鈕會反白顯示。](media/manage-disaster-recovery/restore-configure.png)

3. 設定目標應用程式中的 [其他專案](#prepare) ，以反映受影響的應用程式，並驗證您的設定。

4. 當您準備好要讓自訂網域指向目標應用程式時，請重新對應 [功能變數名稱](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)。

## <a name="recover-app-content-only"></a>僅復原應用程式內容

如果您只想要從受影響的應用程式復原檔案，但不加以還原，請使用下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中，流覽至受影響應用程式的管理頁面，然後按一下 [ **取得發行設定檔**]。

    ![受影響應用程式頁面的螢幕擷取畫面。 警告通知是可見的，但不會反白顯示。 [取得發行設定檔] 專案會改為反白顯示。](media/manage-disaster-recovery/get-publish-profile.png)

1. 開啟下載的檔案，並尋找其名稱中包含的發行設定檔 `ReadOnly - FTP` 。 這是嚴重損壞修復設定檔。 例如：

    ```xml
    <publishProfile profileName="%app-name% - ReadOnly - FTP" publishMethod="FTP" publishUrl="ftp://%ftp-site%/site/wwwroot" ftpPassiveMode="True" userName="%app-name%\$%app-name%" userPWD="" destinationAppUrl="http://%app-name%.azurewebsites.net" SQLServerDBConnectionString="" mySQLDBConnectionString="" hostingProviderForumLink="" controlPanelLink="http://windows.azure.com" webSystem="WebSites">
        <databases />
    </publishProfile>
    ```
    
    複製三個屬性值： 
        
    - `publishUrl`： FTP 主機名稱
    - `userName` 和 `userPWD` ： FTP 認證

1. 使用您選擇的 FTP 用戶端，使用主機名稱和認證連接到受影響的應用程式 FTP 主機。

1. 連線後，請下載整個 */site/wwwroot* 資料夾。 下列螢幕擷取畫面顯示您在 [FileZilla](https://filezilla-project.org/)中的下載方式。

    ![FileZilla 檔案階層的螢幕擷取畫面。 [Wwwroot] 資料夾會反白顯示，並顯示其快捷方式功能表。 在該功能表中，會反白顯示 [下載]。](media/manage-disaster-recovery/download-content.png)

## <a name="next-steps"></a>接下來的步驟
[在 Azure 中透過快照集還原應用程式](app-service-web-restore-snapshots.md)
