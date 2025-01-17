---
title: Folyamatos üzembe helyezés – Azure App Service | Microsoft Docs
description: A cikkből többet tudhat meg arról, hogyan engedélyezheti az Azure App Service szolgáltatásba való folyamatos üzembe helyezést.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 3c4811d990cfe107bc3bc4e6d359659b1935c6a4
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297199"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Folyamatos üzembe helyezés Azure App Service

[Azure app Service](overview.md) lehetővé teszi a GitHub, a BitBucket és az [Azure Repos](https://azure.microsoft.com/services/devops/repos/) -Tárházak folyamatos üzembe helyezését a legújabb frissítések behúzásával. Ez a cikk bemutatja, hogyan használható a Azure Portal az alkalmazás folyamatos üzembe helyezéséhez a kudu Build Service vagy az [Azure-folyamatok](https://azure.microsoft.com/services/devops/pipelines/)használatával. 

A forrás-ellenőrzési szolgáltatásokkal kapcsolatos további információkért lásd: adattár [Adattár létrehozása (GitHub)], tárház [Adattár létrehozása (BitBucket)], vagy [Új git-Tárház létrehozása (Azure Repos)]repók).

Ha manuálisan szeretné konfigurálni a folyamatos üzembe helyezést egy olyan felhőalapú adattárból, amelyet a portál nem támogat közvetlenül, például a [GitLab](https://gitlab.com/), olvassa el a [folyamatos üzembe helyezés beállítása manuális lépések használatával](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)című témakört.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Engedélyezés Azure App Service 

Az Azure Repos használatához ellenőrizze, hogy az Azure DevOps-szervezet kapcsolódik-e az Azure-előfizetéséhez. További információkért lásd: [Azure DevOps Services-fiók beállítása](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App), hogy az üzembe helyezhető egy webalkalmazásban.

A bitbucket vagy a GitHub esetében engedélyezze Azure App Service az adattárhoz való kapcsolódást. Csak egyszer kell engedélyeznie a verziókövetés szolgáltatást. 

1. Válassza a **app Services** lehetőséget a [Azure Portal](https://portal.azure.com) bal oldali navigációs menüben, majd válassza ki a telepíteni kívánt webalkalmazást. 
   
1. Az alkalmazás lapon a bal oldali menüben válassza a **központi telepítési központ** elemet.
   
1. A **központi telepítési központ** lapon válassza a **GitHub** vagy a **bitbucket**lehetőséget, majd válassza az **Engedélyezés**lehetőséget. 
   
   ![Válassza a verziókövetés szolgáltatás lehetőséget, majd válassza az Engedélyezés lehetőséget.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Ha szükséges, jelentkezzen be a szolgáltatásba, és kövesse az engedélyezési utasításokat. 

## <a name="enable-continuous-deployment"></a>Folyamatos üzembe helyezés engedélyezése 

Miután engedélyezte a verziókövetés szolgáltatást, konfigurálja az alkalmazást folyamatos üzembe helyezéshez a beépített [Kudu app Service a Build-kiszolgálót](#option-1-use-the-app-service-build-service)vagy az [Azure](#option-2-use-azure-pipelines)-folyamatokat. 

### <a name="option-1-use-the-app-service-build-service"></a>1\. lehetőség: A App Service Build szolgáltatás használata

A beépített kudu App Service felépíteni a kiszolgálót a GitHub, a bitbucket vagy az Azure Repos szolgáltatásból való folyamatos üzembe helyezéshez. 

1. Válassza a **app Services** lehetőséget a [Azure Portal](https://portal.azure.com) bal oldali navigációs menüben, majd válassza ki a telepíteni kívánt webalkalmazást. 
   
1. Az alkalmazás lapon a bal oldali menüben válassza a **központi telepítési központ** elemet.
   
1. A **központi telepítési központ** lapon válassza ki az Ön által jóváhagyott forrásoldali vezérlő szolgáltatót, majd válassza a **Folytatás**lehetőséget. A GitHub vagy a bitbucket esetében a **fiók módosítása** lehetőségre kattintva módosíthatja a hitelesítő fiókot. 
   
   > [!NOTE]
   > Az Azure Repos használatához ellenőrizze, hogy az Azure DevOps Services-szervezet kapcsolódik-e az Azure-előfizetéséhez. További információkért lásd: [Azure DevOps Services-fiók beállítása](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App), hogy az üzembe helyezhető egy webalkalmazásban.
   
1. A GitHub vagy az Azure Repos esetében a **szolgáltató létrehozása** lapon válassza a **app Service Build szolgáltatás**lehetőséget, majd kattintson a **Folytatás**gombra. A bitbucket mindig a App Service Build szolgáltatást használja.
   
   ![Válassza ki App Service Build szolgáltatást, majd válassza a Folytatás lehetőséget.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. A **configure (Konfigurálás** ) lapon:
   
   - A GitHubnál válassza a legördülő listát , és válassza ki azt a szervezetet, **tárat**és **ágat** , amelyet folyamatosan szeretne üzembe helyezni.
     
     > [!NOTE]
     > Ha nem lát adattárakat, lehetséges, hogy engedélyeznie kell Azure App Service a GitHubon. Keresse meg a GitHub-tárházat, és lépjen a **Beállítások** > **alkalmazások** > által**jóváhagyott OAuth**-alkalmazásokhoz. Válassza a **Azure app Service**lehetőséget, majd válassza a **támogatás**lehetőséget.
     
   - A bitbucket területen válassza ki azt a bitbucket- **csoportot**, **tárat**és **ágat** , amelyet folyamatosan telepíteni szeretne.
     
   - Az Azure Repos esetében válassza ki azt az **Azure DevOps**-szervezetet, **projektet**, **tárházat**és **ágat** , amelyet folyamatosan telepíteni szeretne.
     
     > [!NOTE]
     > Ha az Azure DevOps-szervezet nem szerepel a listáján, győződjön meg arról, hogy az Azure-előfizetéshez van csatolva. További információkért lásd: [Azure DevOps Services-fiók beállítása](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App), hogy üzembe helyezhető egy webalkalmazásban.
     
1. Válassza a **Folytatás** elemet.
   
   ![Töltse ki a tárház adatait, majd válassza a Folytatás lehetőséget.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. A Build szolgáltató konfigurálása után tekintse át a beállításokat az **Összefoglalás** lapon, majd válassza a **Befejezés**lehetőséget.
   
   Új véglegesíti a kiválasztott tárházban és ág-ban, most már folyamatosan üzembe helyezi a App Service alkalmazást. A **központi telepítési központ** lapon nyomon követheti a véglegesítő és központi telepítéseket.
   
   ![Véglegesítő és központi telepítések nyomon követése a központi telepítési központban](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>2\. lehetőség: Használja az Azure Pipelinest 

Ha a fiókja rendelkezik a szükséges engedélyekkel, beállíthatja az Azure-folyamatokat a GitHubról vagy az Azure Repos-adattárakból történő folyamatos üzembe helyezéshez. További információ az Azure-folyamatok üzembe helyezéséről: webalkalmazás [üzembe helyezése az Azure](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)-ban app Services.

Az Azure DevOps-szervezetben folyamatos kézbesítésű Azure-folyamatok létrehozásához Azure App Service: 

- Az Azure-fióknak engedéllyel kell rendelkeznie a Azure Active Directory írásához és a szolgáltatás létrehozásához. 
  
- Az Azure-fióknak **tulajdonosi** szerepkörrel kell rendelkeznie az Azure-előfizetésében.

- A használni kívánt Azure DevOps-projekt rendszergazdájának kell lennie.

Az Azure-folyamatok konfigurálása (előzetes verzió):

1. Válassza a **app Services** lehetőséget a [Azure Portal](https://portal.azure.com) bal oldali navigációs menüben, majd válassza ki a telepíteni kívánt webalkalmazást. 
   
1. Az alkalmazás lapon a bal oldali menüben válassza a **központi telepítési központ** elemet.
   
1. A **szolgáltató létrehozása** lapon válassza az **Azure-folyamatok (előzetes verzió)** lehetőséget, majd kattintson a **Folytatás**gombra. 
   
1. A **configure (Konfigurálás** ) lap **kód** szakaszában:
   
   - A GitHubnál válassza a legördülő listát , és válassza ki azt a szervezetet, **tárat**és **ágat** , amelyet folyamatosan szeretne üzembe helyezni.
     
     > [!NOTE]
     > Ha nem lát adattárakat, lehetséges, hogy engedélyeznie kell Azure App Service a GitHubon. Keresse meg a GitHub-tárházat, és lépjen a **Beállítások** > **alkalmazások** > által**jóváhagyott OAuth**-alkalmazásokhoz. Válassza a **Azure app Service**lehetőséget, majd válassza a **támogatás**lehetőséget.
     
   - Az Azure Repos esetében válassza ki a folyamatosan telepíteni kívánt **Azure DevOps**-szervezetet, **projektet**, **tárházat**és **ágat** , vagy állítson be egy új Azure DevOps-szervezetet.
     
     > [!NOTE]
     > Ha a meglévő Azure DevOps-szervezet nem szerepel a listáján, előfordulhat, hogy az Azure-előfizetéshez kell csatolnia. További információ: [a CD-kiadási folyamat meghatározása](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
     
1. Válassza a **Folytatás** elemet.
   
1. Az Azure Repos esetében az **összeállítás** szakaszban adja meg azt a nyelvi keretrendszert, amelyet az Azure-folyamatoknak a Build feladatok futtatásához használniuk kell, majd válassza a **Folytatás**lehetőséget.
   
1. A **teszt** lapon válassza ki, hogy engedélyezi-e a terhelési teszteket, majd kattintson a **Folytatás**gombra.
   
1. A App Service-csomag díjszabási [szintjétől](https://azure.microsoft.com/pricing/details/app-service/plans/)függően előfordulhat, hogy az **üzembe helyezés az átmeneti** oldalon lehetőség látható. Válassza ki, hogy engedélyezi-e az [üzembe helyezési](deploy-staging-slots.md)pontokat, majd kattintson a **Folytatás**gombra.
   
   > [!NOTE]
   > Az Azure-folyamatok nem teszik lehetővé a folyamatos kézbesítést az üzemi tárolóhelyre. Ez a korlátozás megakadályozza a véletlen üzembe helyezést az éles környezetben. Állítsa be a folyamatos kézbesítést egy átmeneti tárolóhelyre, ellenőrizze a módosításokat, majd ha elkészült, cserélje ki a tárolóhelyeket.
   
1. A Build szolgáltató konfigurálása után tekintse át a beállításokat az **Összefoglalás** lapon, majd válassza a **Befejezés**lehetőséget.
   
   Új véglegesíti a kiválasztott tárházban és ág-ban, most már folyamatosan üzembe helyezi a App Service alkalmazást. A **központi telepítési központ** lapon nyomon követheti a véglegesítő és központi telepítéseket.
   
   ![Véglegesítő és központi telepítések nyomon követése a központi telepítési központban](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>Folyamatos üzembe helyezés letiltása

A folyamatos üzembe helyezés letiltásához válassza a **Leválasztás** lehetőséget az alkalmazás **központi telepítési központ** lapjának tetején.

![Folyamatos üzembe helyezés letiltása](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>További források

* [A folyamatos üzembe helyezéssel kapcsolatos gyakori problémák vizsgálata](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell használata](/powershell/azureps-cmdlets-docs)
* [A Git dokumentációja](https://git-scm.com/documentation)
* [A Kudu projekt](https://github.com/projectkudu/kudu/wiki)

[Adattár létrehozása (GitHub)]: https://help.github.com/articles/create-a-repo
[Adattár létrehozása (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Új git-Tárház létrehozása (Azure Repos)]: /azure/devops/repos/git/creatingrepo
