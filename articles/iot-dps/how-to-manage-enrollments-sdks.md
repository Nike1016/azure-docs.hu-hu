---
title: Eszközök regisztrálásának kezelése az Azure Device kiépítési szolgáltatás SDK-k használatával | Microsoft Docs
description: Az eszközök regisztrálásának kezelése a IoT Hub Device Provisioning Service a szolgáltatás SDK-k használatával
author: robinsh
ms.author: robinsh
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 438cb579180458fcdeb75516a7c98b3ab2886366
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883377"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Az eszközök regisztrálásának kezelése az Azure Device kiépítési szolgáltatás SDK-k használatával
Az *eszközök* regisztrálása egyetlen eszköz vagy eszközök egy csoportját hozza létre, amelyek egy bizonyos ponton regisztrálhatnak az eszköz kiépítési szolgáltatásával. A beléptetési rekord tartalmazza az eszköz (ek) kezdeti kívánt konfigurációját a regisztráció részeként, beleértve a kívánt IoT hubot is. Ez a cikk bemutatja, hogyan kezelheti az eszközök regisztrációját a kiépítési szolgáltatás számára az Azure IoT-létesítési szolgáltatás SDK-k használatával programozott módon.  Az SDK-k a GitHubon érhetők el ugyanazon a tárházon, mint az Azure IoT SDK-k.

## <a name="prerequisites"></a>Előfeltételek
* Szerezze be a kapcsolatok karakterláncát az eszköz kiépítési szolgáltatásának példányáról.
* Szerezze be az eszköz biztonsági összetevőit a [](concepts-security.md#attestation-mechanism) használt igazolási mechanizmushoz:
    * [**Platformmegbízhatósági modul (TPM)** ](/azure/iot-dps/concepts-security#trusted-platform-module):
        * Egyéni regisztráció: Regisztrációs azonosító és TPM-kiterjesztési kulcs egy fizikai eszközről vagy a TPM-szimulátorból.
        * A regisztrációs csoport nem vonatkozik a TPM-igazolásra.
    * [**X.509**](/azure/iot-dps/concepts-security):
        * Egyéni regisztráció: A fizikai eszközről vagy az SDK [Dice](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) emulátorból származó [levél-tanúsítvány](/azure/iot-dps/concepts-security) .
        * Beléptetési Csoport: A [hitelesítésszolgáltató/főtanúsítvány](/azure/iot-dps/concepts-security#root-certificate) vagy a [köztes tanúsítvány](/azure/iot-dps/concepts-security#intermediate-certificate), amely az eszköz tanúsítványának fizikai eszközön történő előállítására szolgál.  Az SDK-DICE emulátorból is létrehozható.
* A pontos API-hívások a nyelvi különbségek miatt eltérőek lehetnek. A részletekért tekintse át a GitHubon megadott mintákat:
   * [Java-kiépítési szolgáltatás ügyféloldali mintái](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Node. js kiépítési szolgáltatás ügyféloldali mintái](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [.NET-kiépítési szolgáltatás ügyféloldali mintái](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Eszközök regisztrálásának létrehozása
Az eszközöket kétféleképpen lehet regisztrálni a kiépítési szolgáltatással:

* A **beléptetési csoport** olyan eszközök csoportja, amelyek osztoznak az X. 509 tanúsítványok közös igazolási mechanizmusán, amelyet a főtanúsítvány [](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) vagy a [közbenső tanúsítvány](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)írt alá. Azt javasoljuk, hogy nagy számú, a kívánt kezdeti konfigurációt megosztó eszközhöz használjon beléptetési csoportot, vagy ha az összes eszköz ugyanahhoz a bérlőhöz fog csatlakozni. Vegye figyelembe, hogy csak az X. 509 igazolási mechanizmust használó eszközök regisztrálásabeléptetési csoportként. 

    A munkafolyamatot követő SDK-k segítségével létrehozhat egy regisztrációs csoportot:

    1. Beléptetési csoport esetén az igazolási mechanizmus X. 509 főtanúsítványt használ.  Hívja meg a Service ```X509Attestation.createFromRootCertificate``` SDK API-t a főtanúsítvánnyal a regisztrációhoz szükséges igazolás létrehozásához.  Az X. 509 főtanúsítvány egy PEM-fájlban vagy karakterláncként van megadva.
    1. Hozzon létre ```EnrollmentGroup``` egy új változót a ```attestation``` létrehozott és ```enrollmentGroupId```egy egyedi használatával.  Opcionálisan a következő paramétereket ```Device ID``` ```IoTHubHostName``` ```ProvisioningStatus```is megadhatja:,.
    2. Hívja a Service SDK ```createOrUpdateEnrollmentGroup``` API-t a háttérbeli ```EnrollmentGroup``` alkalmazásban egy regisztrációs csoport létrehozásához.

* Az **Egyéni regisztráció** egy olyan bejegyzés, amely regisztrálható egyetlen eszközön. Az egyéni regisztrációk X. 509 tanúsítványokat vagy SAS-jogkivonatokat (fizikai vagy virtuális TPM-ből) is használhatnak igazolási mechanizmusként. Azt javasoljuk, hogy egyéni regisztrációkat használjon olyan eszközökhöz, amelyek egyedi kezdeti konfigurációt igényelnek, vagy olyan eszközök esetében, amelyek csak a TPM vagy a virtuális TPM modulon keresztül használhatják az igazolási mechanizmusként szolgáló SAS-jogkivonatokat. Előfordulhat, hogy az egyéni regisztrációkhoz meg van határozva a kívánt IoT Hub-eszközazonosító.

    Létrehozhat egy egyéni regisztrációt a munkafolyamatot követő SDK-k használatával:
    
    1. Válassza ki ```attestation``` a mechanizmust, amely TPM vagy X. 509 lehet.
        1. **TPM**: Ha egy fizikai eszközről vagy a TPM-szimulátorból bemenetként kívánja használni a jóváhagyó kulcsot, a Service ```TpmAttestation``` SDK API-val meghívhatja a regisztrálási igazolást. 
        2. **X. 509**: Ha az ügyféltanúsítványt bemenetként használja, hívja meg a Service SDK API ```X509Attestation.createFromClientCertificate``` -t, és hozzon létre igazolást a regisztráláshoz.
    2. Hozzon létre ```IndividualEnrollment``` egy új változót ```attestation``` a létrehozott és egy ```registrationId``` egyedi bemenettel, amely az eszközön található, vagy a TPM-szimulátorból lett létrehozva.  Opcionálisan a következő paramétereket ```Device ID``` ```IoTHubHostName``` ```ProvisioningStatus```is megadhatja:,.
    3. Az egyéni regisztráció létrehozásához hívja a Service SDK ```IndividualEnrollment``` API ```createOrUpdateIndividualEnrollment``` -t a háttérbeli alkalmazásban.

A regisztráció sikeres létrehozása után az eszköz kiépítési szolgáltatása beléptetési eredményt ad vissza. Ezt a munkafolyamatot a [korábban említett](#prerequisites)minták mutatják be.

## <a name="update-an-enrollment-entry"></a>Beléptetési bejegyzés frissítése

A regisztrációs bejegyzés létrehozása után érdemes lehet frissíteni a beléptetést.  Lehetséges forgatókönyvek például a kívánt tulajdonság frissítése, az igazolási módszer frissítése vagy az eszközök hozzáférésének visszavonása.  Különböző API-k vannak az egyéni regisztrációhoz és a csoportos regisztrációhoz, de az igazolási mechanizmus nem tesz különbséget.

A következő munkafolyamat után frissítheti a beléptetési bejegyzést:
* **Egyéni regisztráció**:
    1. Először a Service SDK API ```getIndividualEnrollment```-val szerezze be a kiépítési szolgáltatás legújabb regisztrációját.
    2. Szükség szerint módosítsa a legújabb regisztráció paraméterét. 
    3. A legfrissebb regisztrációval hívja meg a Service SDK ```createOrUpdateIndividualEnrollment``` API-t a beléptetési bejegyzés frissítéséhez.
* **Csoportos regisztráció**:
    1. Először a Service SDK API ```getEnrollmentGroup```-val szerezze be a kiépítési szolgáltatás legújabb regisztrációját.
    2. Szükség szerint módosítsa a legújabb regisztráció paraméterét.
    3. A legfrissebb regisztrációval hívja meg a Service SDK ```createOrUpdateEnrollmentGroup``` API-t a beléptetési bejegyzés frissítéséhez.

Ezt a munkafolyamatot a [korábban említett](#prerequisites)minták mutatják be.

## <a name="remove-an-enrollment-entry"></a>Beléptetési bejegyzés eltávolítása

* Az **Egyéni regisztráció** törléséhez hívja a Service SDK API ```deleteIndividualEnrollment``` - ```registrationId```t a használatával.
* A **csoportos regisztráció** törléséhez hívja a Service SDK API ```deleteEnrollmentGroup``` - ```enrollmentGroupId```t a használatával.

Ezt a munkafolyamatot a [korábban említett](#prerequisites)minták mutatják be.

## <a name="bulk-operation-on-individual-enrollments"></a>Tömeges művelet az egyes regisztrációk esetében

A munkafolyamatot követő több egyéni regisztráció létrehozásához, frissítéséhez vagy eltávolításához tömeges műveletet is végrehajthat:

1. Hozzon létre egy változót ```IndividualEnrollment```, amely többt tartalmaz.  A változó megvalósítása minden nyelv esetében eltér.  A részletekért tekintse át a GitHubon a tömeges művelet mintáját.
2. Hívja meg a Service ```runBulkOperation``` SDK API ```BulkOperationMode``` -t a kívánt művelethez és a változóhoz az egyes regisztrációk esetében. Négy mód támogatott: létrehozás, frissítés, updateIfMatchEtag és törlés.

A művelet sikeres elvégzése után az eszköz kiépítési szolgáltatása tömeges művelet eredményét fogja visszaadni.

Ezt a munkafolyamatot a [korábban említett](#prerequisites)minták mutatják be.
