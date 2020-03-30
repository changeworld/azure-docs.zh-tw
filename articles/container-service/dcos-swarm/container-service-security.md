---
title: (已淘汰) Azure Container Service 中的容器安全性
description: 為部署在 Azure Container Service 和相關 Azure 服務的 Docker 容器提供保護時的注意事項。
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/28/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d90d872175febf775e7d0892e133883f1a8ff8a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552374"
---
# <a name="deprecated-securing-docker-containers-in-azure-container-service"></a>(已淘汰) 保護 Azure Container Service 中的 Docker 容器

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

本文將介紹為部署在 Azure Container Service 中的 Docker 容器提供保護時的注意事項和建議。 其中的許多注意事項通常適用於 Azure 或其他環境中所部署的 Docker 容器。 

## <a name="image-security"></a>映像安全性

容器是透過一或多個存放庫中所儲存的映像來建立的。 這些存放庫可屬於公用或私人的容器登錄。 公用登錄的範例是 [Docker Hub](https://hub.docker.com/)。 私人登錄的範例是 [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/)，它可以安裝在內部部署環境或虛擬私人雲端中。 另外還有以雲端為基礎的私人容器登錄服務，包括 [Azure Container Registry](../../container-registry/container-registry-intro.md)。

### <a name="public-and-private-images"></a>公用和私人映像
一般情況下，可公開取得的容器映像並無法保證安全性，這個情況就和任何已公開發佈的軟體套件一樣。 容器映像包含多個軟體層，每個軟體層可能各有弱點。 它是了解容器映像來源 (包括映像擁有者，以便判斷它是否為可靠來源)、其所含軟體層和軟體版本的關鍵。 

例如，如果您進入 Docker Hub 官方所提供的 [nginx 存放庫](https://hub.docker.com/_/nginx/)，並瀏覽至 [標記]**** 索引標籤，您就會看到每個映像以色彩標示的弱點。 每一種色彩各代表一個映像軟體層弱點。 如需有關 Docker Hub 弱點掃描的詳細資訊，請參閱[了解 Docker Hub 的官方存放庫](https://blog.docker.com/2015/06/understanding-official-repos-docker-hub/)。

![Docker Hub 中的 Nginx 映像](./media/container-service-security/docker-hub-nginx.png)

企業非常注重安全性，而且為了保護企業本身不會遭受安全性攻擊，它們應該在私人登錄中儲存和擷取映像，例如 Azure Container Registry 或 Docker Trusted Registry。 Azure Container Registry 除了提供受控私人登錄外，還可透過適用於基本驗證流程的 Azure Active Directory 來支援[以服務主體為基礎的驗證](../../container-registry/container-registry-authentication.md)，包括以角色為基礎的唯讀、寫入和擁有者存取權限。

### <a name="image-security-scanning"></a>映像安全性掃描

即使使用私人登錄，您最好也要使用映像掃描解決方案來另外進行安全性驗證。 容器映像中的每個軟體層都有可能出現與容器映像中的其他層無關的弱點。 隨著越來越多公司開始根據容器技術部署其生產工作負載，為了確保能夠防止安全性威脅，映像掃描對其組織來說就變得重要。 

安全性監視和掃描解決方案 (例如 [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry) 和 [Aqua Security](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)等) 可用來掃描私人登錄中的容器映像，並找出潛在弱點。 請務必了解不同解決方案所提供的掃描深度。 例如，某些解決方案可能只會就已知弱點對映像層進行交叉驗證。 這些解決方案可能無法驗證透過某些套件管理員軟體所建置的映像層軟體。 其他解決方案則可能已更加深入地整合掃描功能，因此可以找出任何已封裝軟體中的弱點。

### <a name="production-deployment-rules-and-audit"></a>生產部署的規則和稽核
當應用程式部署在生產環境後，請務必要設定一些規則，以確保生產環境所使用的映像安全無虞，而且沒有任何弱點。

* 一般而言，即使映像所含的弱點再輕微，您也不應允許該映像在生產環境中執行。 此外，在生產環境中部署的所有映像，最好是儲存在只有少數幾個選定人員可存取的私人登錄中。 也請務必不要保存太多生產映像，以便您能夠有效管理。

* 由於您很難從可公開取得的容器映像中精確指出軟體的來源，因此您最好從來源建置映像，以確保您知道映像層的來源。 當自行建置的容器映像出現弱點，客戶可以很快地找到解決途徑。 如果是公用映像，客戶就必須找到公用映像的根源才能解決問題，或者也可以從發佈者取得其他安全的映像。

* 在生產環境中部署的映像即使經過徹底掃描，也不保證永遠會是應用程式存留期內的最新映像。 針對先前未知的映像層或在部署生產環境後才導入的映像層，系統可能會報告它們有安全性弱點。 請定期稽核生產環境中所部署的映像，以找出過期或已有一段時間未更新的映像。 您可以使用藍綠部署方法和輪流升級機制來更新容器映像，而不需要停機。 使用上一節所說的工具即可掃描映像。 

* 使用持續整合 (CI) 管線來建置映像和整合式安全性掃描，可協助您以安全的容器映像來維持安全的私人登錄。 CI 解決方案內建的弱點掃描，可確保通過所有測試的映像都會推送至用來部署生產工作負載的私人登錄。 CI 管線失敗則可確保有弱點的映像不會推送至用來部署生產工作負載的私人登錄。 它也會在有大量映像時自動掃描映像是否安全。 否則，手動稽核映像中是否有安全性弱點會是一件痛苦、冗長又容易出錯的工作。

## <a name="host-level-container-isolation"></a>主機層級的容器隔離
當客戶在 Azure 資源上部署容器應用程式時，這些應用程式會部署在資源群組的訂用帳戶層級，而且不會是多租用戶。 這表示客戶如果與其他人共用訂用帳戶，那麼相同訂用帳戶中的兩個部署之間就無法立下界限。 因此，系統無法保證容器層級的安全性。 

也請務必了解，容器會共用主機的核心和資源 (在 Azure Container Service 中，主機便是叢集中的 Azure VM)。 因此，在生產環境中執行的容器必須以不具權限的使用者模式執行。 使用根權限執行容器可能會危害整個環境。 駭客在握有容器的根層級存取權後，就可以存取主機上的完整根權限。 此外，請務必執行具有唯讀檔案系統的容器。 這可防止其他人在可以存取遭入侵的容器後，在檔案系統中寫入惡意指令碼，並存取其他檔案。 同樣地，請務必要限制為容器配置的資源 (例如記憶體、CPU 和網路頻寬)。 這有助於防止駭客佔用資源和進行非法活動，如信用卡欺詐或位硬幣挖掘，這可能會阻止其他容器在主機或群集上運行。

## <a name="orchestrator-considerations"></a>Orchestrator 的注意事項

Azure Container Service 中提供的每個 Orchestrator 都有它自己的安全性注意事項。 例如，您應該對直接以 SSH 存取 Container Service 中 Orchestrator 節點的行為進行限制。 相反地，您應該使用每個 Orchestrator 的 UI 或命令列工具 (例如 Kubernetes 的 `kubectl`) 來管理容器的環境，這樣就不需要存取主機。

如需其他 Orchestrator 特有的安全性資訊，請參閱下列資源︰

* **Kubernetes**：[Kubernetes 部署的安全性最佳做法](https://kubernetes.io/blog/2016/08/security-best-practices-kubernetes-deployment/)

* **DC/OS**：[保護您的叢集](https://docs.mesosphere.com/1.12/administering-clusters/securing-your-cluster)

* **Docker Swarm**：[Docker 安全性](https://www.docker.com/docker-security)

## <a name="next-steps"></a>後續步驟

* 如需 Docker 架構和容器安全性的詳細資訊，請參閱[容器安全性簡介](https://www.docker.com/sites/default/files/WP_IntrotoContainerSecurity_08.19.2016.pdf)。

* 如需 Azure 平台安全性的相關資訊，請參閱 [Azure 資訊安全中心](https://www.microsoft.com/trustcenter/cloudservices/azure)。
