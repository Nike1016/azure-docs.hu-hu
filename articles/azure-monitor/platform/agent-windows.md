---
title: Windows rendszerű számítógépek összekötése Azure Monitorhoz | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan csatlakoztathatók a más felhőkben vagy a helyszínen üzemeltetett Windows-számítógépek a Windows Log Analytics-ügynökével Azure Monitor.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: magoedte
ms.openlocfilehash: 8ca87f18a91af3937f8b4dd1148ecad8507e0dd5
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68849062"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Windows rendszerű számítógépek összekapcsolásának Azure Monitor

A helyi adatközpontban található virtuális gépek vagy fizikai számítógépek figyeléséhez és kezeléséhez, illetve az Azure Monitor-mel rendelkező más felhőalapú környezethez telepítenie kell az Log Analytics ügynököt (más néven a Microsoft monitoring Agent (MMA)), és konfigurálnia kell a következőre: jelentés egy vagy több Log Analytics-munkaterületre. Az ügynök a Azure Automation hibrid Runbook feldolgozói szerepkörét is támogatja.  

A figyelt Windows rendszerű számítógépeken az ügynök a Microsoft monitoring Agent szolgáltatásként jelenik meg. A Microsoft monitoring Agent szolgáltatás eseményeket gyűjt a naplófájlokból és a Windows-eseménynaplóból, a teljesítményadatokből és más telemetria. Még ha az ügynök nem tud kommunikálni Azure Monitor it-jelentésekkel, az ügynök továbbra is futni fog, és a figyelt számítógép lemezén lévő összegyűjtött adatokat várólistára helyezi. Ha a rendszer visszaállítja a csatlakozást, a Microsoft monitoring Agent szolgáltatás adatokat küld a szolgáltatásnak.

Az ügynököt az alábbi módszerek egyikével lehet telepíteni. A legtöbb telepítés ezeket a metódusokat használja a különböző számítógépek telepítésére, ha szükséges.  Az egyes módszerek használatával kapcsolatos részletes információkat a cikk későbbi részében találja.

* Manuális telepítés. A telepítőt manuálisan kell futtatni a számítógépen a telepítővarázsló, a parancssorból, vagy egy meglévő szoftverterjesztési eszköz használatával.
* Azure Automation kívánt állapot-konfiguráció (DSC). A DSC használata Azure Automation a környezetben már üzembe helyezett Windows rendszerű számítógépekhez használható parancsfájl használatával.  
* PowerShell-parancsfájl.
* Resource Manager-sablon a Azure Stack helyszíni Windows rendszert futtató virtuális gépekhez. 

>[!NOTE]
>Azure Security Center (ASC) a Microsoft monitoring Agenttől (más néven Log Analytics Windows-ügynöktől) függ, és telepíti és konfigurálja azt, hogy a telepítés részeként jelentést készítsen egy Log Analytics munkaterületre. Az ASC tartalmaz egy automatikus kiépítési lehetőséget, amely lehetővé teszi a Log Analytics Windows-ügynök automatikus telepítését az előfizetésben lévő összes virtuális gépre, és úgy konfigurálja, hogy egy adott munkaterületnek jelentsen. További információ erről a lehetőségről: [log Analytics-ügynök automatikus üzembe](../../security-center/security-center-enable-data-collection.md#enable-automatic-provisioning-of-the-log-analytics-agent-)helyezésének engedélyezése.
>

Ha úgy kell beállítania az ügynököt, hogy egynél több munkaterületre jelentsen, ez a kezdeti beállítás során nem hajtható végre, csak ezt követően, ha a Vezérlőpult vagy a PowerShell beállításait a [munkaterület hozzáadása vagy eltávolítása](agent-manage.md#adding-or-removing-a-workspace)című témakörben leírtak szerint frissíti.  

A támogatott konfiguráció megismeréséhez tekintse meg a [támogatott Windows operációs rendszereket](log-analytics-agent.md#supported-windows-operating-systems) és a [hálózati tűzfalkonfigurációkat](log-analytics-agent.md#network-firewall-requirements) ismertető részt.

## <a name="obtain-workspace-id-and-key"></a>A munkaterület-azonosító és -kulcs lekérése
A Windows Log Analytics-ügynök telepítése előtt szüksége lesz a munkaterület-AZONOSÍTÓra és a Log Analytics-munkaterülethez tartozó kulcsra.  Ezek az információk az egyes telepítési módszerekről való telepítéskor szükségesek az ügynök megfelelő konfigurálásához, és annak biztosítása érdekében, hogy az Azure Monitor az Azure kereskedelmi és az USA kormányzati felhőben való sikeres kommunikációhoz. 

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
2. Az Log Analytics-munkaterületek listájában válassza ki azt a munkaterületet, amelyet az ügynöknek a jelentésre való bekapcsolásához kíván beállítani.
3. Válassza ki a **Speciális beállítások** elemet.<br><br> ![A Log Analytics speciális beállításai](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Válassza ki a **Csatlakoztatott források**, majd a **Windowsos kiszolgálók** elemet.   
5. Másolja és illessze be kedvenc szerkesztőjét, a **munkaterület azonosítóját** és az **elsődleges kulcsot**.    
   
## <a name="configure-agent-to-use-tls-12"></a>Az ügynök konfigurálása a TLS 1,2 használatára
Ha a [TLS 1,2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) protokoll használatát szeretné konfigurálni a Windows-ügynök és a log Analytics szolgáltatás közötti kommunikációhoz, az alábbi lépéseket követve engedélyezheti az ügynök telepítését a virtuális gépen, vagy később is.   

1. Keresse meg a következő beállításkulcsot: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Hozzon létre egy alkulcsot a **protokollok** alatt a TLS 1,2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1,2**
3. Hozzon létre egy **ügyfél** -alkulcsot a korábban létrehozott TLS 1,2 protokoll verziójának alkulcsában. Például: **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ Client**.
4. Hozza létre a következő DWORD-értékeket a **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ ügyfél**területen:

    * **Engedélyezve** [Érték = 1]
    * **DisabledByDefault** [Érték = 0]  

Konfigurálja a .NET-keretrendszer 4,6-es vagy újabb verzióját a biztonságos titkosítás támogatásához, ahogy az alapértelmezés szerint le van tiltva. Az [erős titkosítás](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) biztonságosabb hálózati protokollokat használ, mint például a TLS 1,2, és blokkolja a nem biztonságos protokollokat. 

1. Keresse meg a következő beállításkulcsot: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\. NETFramework\v4.0.30319**.  
2. Hozza létre az **1**értékkel rendelkező **alatt** DWORD értéket.  
3. Keresse meg a következő beállításkulcsot: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\. NETFramework\v4.0.30319**.  
4. Hozza létre az **1**értékkel rendelkező **alatt** DWORD értéket. 
5. A beállítások érvénybe léptetéséhez indítsa újra a rendszert. 

## <a name="install-the-agent-using-setup-wizard"></a>Az ügynök telepítése a telepítővarázsló használatával
A következő lépésekkel telepítheti és konfigurálhatja az Log Analytics-ügynököt az Azure-ban, és Azure Government a felhőt a számítógépén található ügynök telepítővarázslója segítségével. Ha szeretné megismerni, hogyan konfigurálhatja az ügynököt egy System Center Operations Manager felügyeleti csoportnak való jelentésre is, tekintse meg [a Operations Manager ügynök telepítése az ügynök telepítése varázslóval](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard)című témakört.

1. A Log Analytics munkaterületen, a **Windows-kiszolgálók** lapon, amelyről korábban navigált, válassza ki a Windows- **ügynök** megfelelő verzióját a letöltéshez a Windows operációs rendszer processzor-architektúrája alapján.   
2. Futtassa a telepítőt, és telepítse az ügynököt a számítógépre.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.
3. A **Licencfeltételek** oldalon olvassa el és fogadja el a licencet, majd kattintson az **Elfogadom** gombra.
4. A **Célmappa** lapon fogadja el az alapértelmezett telepítési mappát, vagy adjon meg egy másikat, majd kattintson a **Tovább** gombra.
5. **Az ügynök telepítésének beállításai** lapon csatlakoztassa az ügynököt az Azure Log Analyticshez, majd kattintson a **Tovább** gombra.   
6. Az **Azure Log Analytics** lapon végezze el a következőket:
   1. Illessze be az előzőleg kimásolt **Munkaterület-azonosítót** és **Munkaterületkulcsot (Elsődleges kulcs)** .  Ha a gépnek egy Azure Government-felhőbeli Log Analytics-munkaterületnek kell jelentenie, válassza az **Azure US Government** lehetőséget az **Azure Cloud** legördülő listából.  
   2. Ha a számítógépnek egy proxykiszolgálón keresztül kell kommunikálnia a Log Analytics szolgáltatással, kattintson a **Speciális** gombra, majd adja meg a proxykiszolgáló URL-címét és portszámát.  Ha a proxykiszolgáló hitelesítést igényel, írja be a felhasználónevet és jelszót a proxykiszolgálóval való hitelesítéshez, majd kattintson a **Tovább** gombra.  
7. A szükséges konfigurációs beállítások megadása után kattintson a **Tovább** gombra.<br><br> ![illessze be a Munkaterület-azonosítót és az Elsődleges kulcsot](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. A **Telepítésre kész** oldalon ellenőrizze a beállításokat, majd kattintson a **Telepítés** elemre.
9. **A konfigurálás sikeresen befejeződött** lapon kattintson a **Befejezés** gombra.

Ennek végeztével a **Microsoft Monitoring Agent** megjelenik a **Vezérlőpulton**. Annak megerősítéséhez, hogy a jelentés Log Analytics, tekintse át az [ügynök kapcsolatának ellenőrzése log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Az ügynök telepítése a parancssor használatával
Az ügynök letöltött fájlja egy önálló telepítési csomag.  Az ügynök és a támogató fájlok telepítőprogramja a csomagban található, és a megfelelő telepítéséhez a következő példákban bemutatott parancssor használatával kell kinyerni.    

>[!NOTE]
>Ha frissíteni szeretne egy ügynököt, akkor a Log Analytics Scripting API-t kell használnia. További információkért tekintse meg a [Windows és a Linux log Analytics ügynökének kezelése és karbantartása](agent-manage.md) című témakört.

Az alábbi táblázat az ügynök számára a telepítő által támogatott paramétereket mutatja be, beleértve a Automation DSC használatával történő üzembe helyezést is.

|MMA-specifikus beállítások                   |Megjegyzések         |
|---------------------------------------|--------------|
| NOAPM=1                               | Nem kötelező paraméter. Telepíti az ügynököt a .NET-alkalmazás teljesítményének figyelése nélkül.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = az ügynök konfigurálása munkaterületre való jelentéshez                |
|OPINSIGHTS_WORKSPACE_ID                | Munkaterület-azonosító (GUID) a hozzáadandó munkaterülethez                    |
|OPINSIGHTS_WORKSPACE_KEY               | A munkaterülettel való első hitelesítéshez használt munkaterület-kulcs |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | A munkaterület helyét tartalmazó felhőalapú környezet meghatározása <br> 0 = Azure kereskedelmi felhő (alapértelmezett) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | A használni kívánt proxy URI azonosítója |
|OPINSIGHTS_PROXY_USERNAME               | A hitelesített proxy eléréséhez használt Felhasználónév |
|OPINSIGHTS_PROXY_PASSWORD               | Jelszó a hitelesített proxy eléréséhez |

1. Az ügynök telepítési fájljainak kibontásához emelt szintű parancssorból `MMASetup-<platform>.exe /c` futtassa a parancsot, és kérni fogja a fájlok kinyerésének elérési útját.  Azt is megteheti, hogy az argumentumok `MMASetup-<platform>.exe /c /t:<Full Path>`átadásával megadhatja az elérési utat.  
2. Ha csendesen szeretné telepíteni az ügynököt, és úgy konfigurálja, hogy az Azure kereskedelmi felhőben lévő munkaterületre jelentsen, a telepítési fájlokat a következő típusra kibontott mappából állítsa be: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   Ha az ügynököt úgy szeretné beállítani, hogy az Azure US government Cloud-nak jelentsen, írja be a következőt: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >A *OPINSIGHTS_WORKSPACE_ID* és a *OPINSIGHTS_WORKSPACE_KEY* paraméterekhez tartozó karakterlánc-értékeket dupla idézőjelek közé kell ágyazni, hogy a rendszer a csomaghoz tartozó érvényes beállításoknak megfelelően interprit Windows Installer. 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Telepítse az ügynököt a DSC használatával Azure Automation

A következő parancsfájl-példa használatával telepítheti az ügynököt Azure Automation DSC használatával.   Ha nem rendelkezik Automation-fiókkal, tekintse meg a [Azure Automation](/azure/automation/) használatának első lépéseit ismertető témakört, amely a Automation DSC használata előtt szükséges Automation-fiók létrehozásának követelményeit és lépéseit ismerteti.  Ha nem ismeri a Automation DSCt, tekintse át [a Automation DSC első lépéseivel foglalkozó](../../automation/automation-dsc-getting-started.md)oktatóanyagot.

Az alábbi példa a 64 bites ügynököt telepíti, amelyet az `URI` érték azonosít. Az 32 bites verziót is használhatja az URI értékének lecserélésével. Mindkét verzió URI-je a következő:

- Windows 64 bites ügynök – https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32 bites ügynök – https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Az eljárás és a parancsfájl például nem támogatja a Windows rendszerű számítógépekre már telepített ügynök frissítését.

Az ügynök csomagjához tartozó 32-bites és 64-bites verziók különböző termékkódok és új verziók is egyedi értékkel rendelkeznek.  A termékkód egy olyan GUID, amely egy alkalmazás vagy termék rendszerbiztonsági azonosítóját jelöli, és a Windows Installer **ProductCode** tulajdonság képviseli.  A **MMAgent. ps1** parancsfájlban szereplő értéknekmegkellegyezniea32bitesvagya64bitesügynöktelepítőcsomagkódjával.`ProductId`

Ha közvetlenül az ügynök telepítési csomagjából szeretné lekérni a termékkód beolvasását, akkor az Orca. exe fájlt használhatja a [Windows SDK összetevőiről Windows Installer fejlesztőknek](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) , amelyek a Windows szoftverfejlesztői készlet összetevője vagy a PowerShell-t követően [ Példa](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) egy Microsoft értékes szakember (MVP) által írt parancsfájlra.  Mindkét módszer esetében először ki kell bontania a **MOMagent. msi** fájlt a MMASetup telepítési csomagjából.  Ez a következő szakaszban látható az [ügynök telepítése parancssor használatával](#install-the-agent-using-the-command-line)című szakasz első lépésében.  

1. Importálja a xPSDesiredStateConfiguration DSC- [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) modult Azure Automation-ba.  
2.  Hozzon létre Azure Automation változó eszközöket a *OPSINSIGHTS_WS_ID* és a *OPSINSIGHTS_WS_KEY*számára. Állítsa be a *OPSINSIGHTS_WS_ID* a log Analytics munkaterület-azonosítóra, és állítsa be a *OPSINSIGHTS_WS_KEY* a munkaterület elsődleges kulcsára.
3.  Másolja a szkriptet, és mentse a MMAgent. ps1 néven.

    ```powershell
    Configuration MMAgent
    {
        $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
        $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
        $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

        Import-DscResource -ModuleName xPSDesiredStateConfiguration
        Import-DscResource -ModuleName PSDesiredStateConfiguration

        Node OMSnode {
            Service OIService
            {
                Name = "HealthService"
                State = "Running"
                DependsOn = "[Package]OI"
            }

            xRemoteFile OIPackage {
                Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
                DestinationPath = $OIPackageLocalPath
            }

            Package OI {
                Ensure = "Present"
                Path  = $OIPackageLocalPath
                Name = "Microsoft Monitoring Agent"
                ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
                Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
                DependsOn = "[xRemoteFile]OIPackage"
            }
        }
    }

    ```

4. Frissítse a `ProductId` parancsfájlban szereplő értéket az ügynök telepítési csomagjának legújabb verziójából kinyert kóddal a korábban javasolt módszerek használatával. 
5. [Importálja az MMAgent. ps1 konfigurációs parancsfájlt](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) az Automation-fiókjába. 
5. [Rendeljen Windows-számítógépet vagy](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) -csomópontot a konfigurációhoz. A csomópont 15 percen belül ellenőrzi a konfigurációját, és az ügynököt leküldi a csomópontra.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Ügynök kapcsolatának ellenőrzése Log Analytics

Az ügynök telepítésének befejezését követően ellenőrizze, hogy sikeresen csatlakozott-e, és hogy a jelentéskészítés két módon hajtható végre.  

A számítógép **Vezérlőpultjában** keresse meg a **Microsoft Monitoring Agent** elemet.  Jelölje ki, ami után az **Azure Log Analytics** lapon az ügynök a következő üzenetet jeleníti meg: **A Microsoft monitoring Agent sikeresen csatlakozott a Microsoft Operations Management Suite szolgáltatáshoz.**<br><br> ![MMA kapcsolati állapota a Log Analytics felé](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Egy egyszerű napló-lekérdezést is végrehajthat a Azure Portal.  

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájában írja be a következőt: **Azure monitor**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Azure monitor**lehetőséget.  
2. A menüben válassza a **naplók** lehetőséget. 
2. A naplók ablaktábla lekérdezés mezőjébe írja be a következőt:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

Az eredményül kapott keresési eredmények között meg kell jelennie a szívverési rekordoknak, amely jelzi, hogy a számítógép csatlakoztatva van, és jelentést küld a szolgáltatásnak.   

## <a name="next-steps"></a>További lépések

- Tekintse át [a Windows és Linux rendszerhez készült log Analytics ügynök](agent-manage.md) felügyeletét és karbantartását, és Ismerje meg, hogyan kezelheti az ügynököt a gépek üzembe helyezési életciklusa során.  

- Ha problémába ütközik az ügynök telepítésekor vagy felügyeletekor, tekintse át [a Windows-ügynök hibaelhárítását](agent-windows-troubleshoot.md) ismertető témakört.
