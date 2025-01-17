---
title: Bevezetés az App Service linuxon – Azure |} A Microsoft Docs
description: Az Azure Linux App Service szolgáltatás ismertetése.
keywords: azure app service, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 6bca1b067f5ec667e8b5da92a182a5618582b2f3
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617434"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>A Linuxon futó Azure App Service bemutatása

[Az Azure App Service](../overview.md) egy teljes körűen felügyelt számítógépes platform, amely webhelyek és webalkalmazások üzemeltetéséhez van optimalizálva. A Linuxon futó App Service használatával az ügyfelek natív módon üzemeltethetnek webalkalmazásokat a támogatott alkalmazáscsoportok számára a Linuxon. A [nyelvek](#languages) szakasz sorolja fel a jelenleg támogatott alkalmazáscsoportokat.

## <a name="languages"></a>Languages

Az Linuxon futó App Service számos beépített rendszerképet támogat a fejlesztői termelékenység növelése érdekében. Ha a beépített rendszerképek nem támogatják az alkalmazás számára szükséges futtatókörnyezetet, akkor a [saját Docker rendszerkép felépítésére](tutorial-custom-docker-image.md) vonatkozó utasításokkal üzembe helyezheti azt a Web App for Containers szolgáltatásban.

| Nyelv | Támogatott verziók |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 8.11, 8.12, 9.4, 10.1, 10.10, 10.14 |
| Java* | A tomcat 8.5, 9.0 Java SE, WildFly 14 (összes futó JRE 8) |
| PHP | 5.6, 7.0, 7.2, 7.3 |
| Python | 2.7, 3.6, 3.7 |
| .NET Core | 1.0, 1.1, 2.0, 2.1, 2.2 |
| Ruby | 2.3, 2.4, 2.5, 2.6 |

## <a name="deployments"></a>Központi telepítés

* FTP
* Helyi Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Átmeneti környezetek
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) és DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>Konzol, közzététel és hibakeresés

* Környezetek
* Központi telepítés
* Alapszintű konzol
* SSH

## <a name="scaling"></a>Méretezés

* Az [App Service-csomag](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json) szintjének megváltoztatásával az ügyfelek le- és felskálázhatják webalkalmazásaikat

## <a name="locations"></a>Helyek

Ellenőrizze az [Azure állapot-irányítópultját](https://azure.microsoft.com/status).

## <a name="limitations"></a>Korlátozások

Az Azure Portal megjeleníti a Web App for Containers szolgáltatással jelenleg működő funkciókat. A további funkciók engedélyezésével azok megjelennek a portálon.

A linuxon futó App Service-ben csak támogatott [ingyenes, alapszintű, Standard és prémium szintű](https://azure.microsoft.com/pricing/details/app-service/plans/) app service-csomagok, és nem rendelkezik egy [megosztott](https://azure.microsoft.com/pricing/details/app-service/plans/) szint. Nem hozható létre egy Linuxos webalkalmazásokat az App Service-csomag, amely már üzemeltet nem Linuxos webalkalmazásokat.  

A jelenlegi korlátozás alapján az ugyanabban az erőforráscsoportban nem használhat egyidejűleg ugyanabban a régióban a Windows és Linux-alkalmazások.

## <a name="troubleshooting"></a>Hibaelhárítás

Ellenőrizze a LogFiles könyvtár Docker naplóit, ha az alkalmazás nem indul el, vagy ha meg szeretné tekinteni az alkalmazás naplózását. A könyvtárhoz az SCM-webhelyen vagy FTP-n keresztül férhet hozzá. A napló a `stdout` és `stderr` a tárolóból engedélyeznie kell a **Docker-tároló naplózását** alatt **App Service-naplók**. A beállítás azonnal érvénybe lép. App Service-ben észleli a változást, és automatikusan újraindítja a tárolót.

Az SCM-webhelyet a **Fejlesztési eszközök** menüben található **Haladó eszközök** oldalon érheti el.

![A Kudu használata a Docker naplók megtekintésére][1]

## <a name="next-steps"></a>További lépések

A következő cikkek a Linuxon futó App Service különböző nyelveken írt webalkalmazásokkal való használatának első lépéseit tartalmazzák:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [Többtárolós alkalmazások](quickstart-multi-container.md)

A Linuxon futó App Service további információkért lásd:

* [App Service Linuxhoz, GYIK](app-service-linux-faq.md)
* [SSH-támogatás a Linuxon futó App Service számára](app-service-linux-ssh-support.md)
* [Átmeneti környezetek beállítása az App Service-ben](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker Hub – folyamatos üzembe helyezés](app-service-linux-ci-cd.md)

Kérdéseit és észrevételeit megoszthatja [fórumunkon](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
