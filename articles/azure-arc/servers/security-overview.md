---
title: 安全性概觀
description: Azure Arc 已啟用伺服器的安全性資訊。
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: be79be3030af76425b54fd683784d0e216ac2cf5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329035"
---
# <a name="azure-arc-for-servers-security-overview"></a>適用於伺服器的 Azure Arc 安全性總覽

本文說明在您的企業中部署 Azure Arc 啟用的伺服器之前，應該評估的安全性設定和考慮。

## <a name="identity-and-access-control"></a>身分識別與存取控制

每個啟用 Azure Arc 的伺服器都具有受控識別作為 Azure 訂用帳戶內資源群組的一部分，此身分識別代表執行內部部署或其他雲端環境的伺服器。 此資源的存取權是由標準的 [Azure 角色型存取控制](../../role-based-access-control/overview.md)所控制。 您可以從 Azure 入口網站中的 [ [**存取控制] (IAM) **](../../role-based-access-control/role-assignments-portal.md#access-control-iam) ] 頁面，確認可存取已啟用 Azure Arc 之伺服器的人員。

:::image type="content" source="./media/security-overview/access-control-page.png" alt-text="Azure Arc 啟用的伺服器存取控制" border="false" lightbox="./media/security-overview/access-control-page.png":::

授與 [參與者](../../role-based-access-control/built-in-roles.md#contributor) 或系統管理員角色存取權的使用者和應用程式可以對資源進行變更，包括在電腦上部署或刪除 [擴充](manage-vm-extensions.md) 功能。 延伸模組可以包含在具特殊許可權的內容中執行的任意腳本，因此請考慮 Azure 資源上的任何參與者，以成為伺服器的間接系統管理員。

**Azure Connected Machine**上線角色可供大規模上線，而且只能在 Azure 中讀取或建立新的啟用 Arc 的伺服器。 它無法用來刪除已經註冊或管理延伸模組的伺服器。 建議的最佳作法是只將此角色指派給 Azure Active Directory (Azure AD) 的服務主體，以供大規模登入機器。

以 **Azure Connected Machine 資源管理員** 角色成員身分的使用者可以讀取、修改、重新上架和刪除電腦。 此角色的設計目的是支援對啟用 Arc 的伺服器進行管理，但不支援資源群組或訂用帳戶中的其他資源。

## <a name="agent-security-and-permissions"></a>代理程式安全性和許可權

若要在 Windows 上管理 Azure Connected Machine 代理程式 (azcmagent) 您的使用者帳戶必須是本機系統管理員群組的成員。 在 Linux 上，您必須具有根存取權限。

Azure Connected Machine 代理程式是由三個服務所組成，這些服務會在您的電腦上執行。

* 混合式 Instance Metadata Service (himds) 服務會負責弧線的所有核心功能。這包括將心跳傳送至 Azure、公開其他應用程式的本機實例中繼資料服務，以瞭解機器的 Azure 資源識別碼，以及抓取 Azure AD 權杖來向其他 Azure 服務進行驗證。 此服務會在 Windows 上以無特殊許可權的虛擬服務帳戶執行，並在 Linux 上以 **himds** 使用者的形式執行。

* 來賓設定服務 (GCService) 負責評估電腦上的 Azure 原則。

* 來賓設定延伸模組服務 (ExtensionService) 負責安裝、更新及刪除電腦上 (代理程式、腳本或其他軟體) 的延伸模組。

來賓設定和擴充服務會在 Windows 上以本機系統的形式執行，並在 Linux 上以 root 身份執行。

## <a name="using-a-managed-identity-with-arc-enabled-servers"></a>使用已啟用 Arc 之伺服器的受控識別

根據預設，Arc 使用 Azure Active Directory 系統指派的身分識別，只能用來在 Azure 中更新已啟用 Arc 之伺服器的狀態。 例如， *最後看到* 的信號狀態。 如果伺服器上的應用程式使用系統指派的身分識別來存取其他 Azure 服務，您可以選擇性地將其他角色指派給身分識別。

雖然混合式 Instance Metadata Service 可以由電腦上執行的任何應用程式存取，但是只有經過授權的應用程式可以為系統指派的身分識別要求 Azure AD token。 第一次嘗試存取權杖 URI 時，服務會在檔案系統上的位置（只有受信任的呼叫端可讀取）產生隨機產生的密碼編譯 blob。 接著，呼叫端必須讀取檔案 (證明它具有適當的許可權) 然後以授權標頭中的檔案內容重試要求，以成功取出 Azure AD token。

* 在 Windows 上，呼叫端必須是本機系統 **管理員** 群組的成員，或是 **混合式代理程式擴充功能應用程式** 群組，才能讀取 blob。

* 在 Linux 上，呼叫端必須是 **himds** 群組的成員，才能讀取 blob。

## <a name="using-disk-encryption"></a>使用磁片加密

Azure Connected Machine 代理程式會使用公開金鑰驗證來與 Azure 服務通訊。 當您將伺服器上架到 Azure Arc 之後，會將私密金鑰儲存至磁片，並在代理程式與 Azure 進行通訊時使用。 如果遭竊，您可以在另一部伺服器上使用私密金鑰來與服務通訊，並如同源伺服器一樣運作。 這包括取得系統指派之身分識別的存取權，以及身分識別可存取的任何資源。 私密金鑰檔案受到保護，只允許 **himds** 帳戶存取讀取該檔案。 為了防止離線攻擊，我們強烈建議您在伺服器的作業系統磁片區上使用完整磁片加密 (例如，BitLocker、dm crypt 等 ) 。

## <a name="next-steps"></a>後續步驟

在為多部混合式電腦評估或啟用已啟用 Arc 的伺服器之前，請參閱[連線的電腦代理程式概觀](agent-overview.md)來了解需求、代理程式的相關技術詳細資料，以及部署方法。
