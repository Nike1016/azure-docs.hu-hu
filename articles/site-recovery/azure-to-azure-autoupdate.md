---
title: A mobilitási szolgáltatás automatikus frissítése az Azure-ban az Azure vész-helyreállítással | Microsoft Docs
description: A mobilitási szolgáltatás automatikus frissítésének áttekintése az Azure-beli virtuális gépek Azure Site Recovery használatával történő replikálása során.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/20/2019
ms.author: rajanaki
ms.openlocfilehash: 92a46f7be116d0664b438c9039e311f802c873e5
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708080"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>A mobilitási szolgáltatás automatikus frissítése az Azure-ról az Azure-ba történő replikációra

Azure Site Recovery a havi kiadási lépésszám segítségével elháríthatja a problémákat, és javíthatja a meglévő szolgáltatásokat, illetve újakat vehet fel. Ha továbbra is meg szeretné őrizni a szolgáltatást, minden hónapban meg kell terveznie a javítások telepítését. Az egyes frissítésekhez kapcsolódó terhelés elkerülése érdekében Ehelyett engedélyezheti Site Recovery számára az összetevők frissítését.

Az [Azure – Azure vész-helyreállítási architektúrában](azure-to-azure-architecture.md)említettek szerint a mobilitási szolgáltatás minden olyan Azure-beli virtuális gépre (VM) telepítve van, amelyen engedélyezve van a replikáció, miközben a virtuális gépeket az egyik Azure-régióból a másikba replikálja. Ha automatikus frissítéseket használ, minden új kiadás frissíti a mobilitási szolgáltatás bővítményét.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Az automatikus frissítések működése

Ha a Site Recovery használatával kezeli a frissítéseket, az az Azure-szolgáltatások által használt globális runbook helyez üzembe egy Automation-fiókon keresztül, amely ugyanabban az előfizetésben jön létre, mint a tároló. Mindegyik tároló egy Automation-fiókot használ. A runbook a tárolóban lévő összes virtuális gépet ellenőrzi az aktív automatikus frissítésekhez, és frissíti a mobilitási szolgáltatás bővítményét, ha újabb verzió érhető el.

Az alapértelmezett runbook a replikált virtuális gép földrajzi helyének időzónájában naponta ismétlődik a 12:00-es időpontban. A runbook-ütemtervet az Automation-fiók használatával is módosíthatja.

> [!NOTE]
> A 35-es kumulatív frissítéstől kezdve kiválaszthatja a frissítésekhez használandó meglévő Automation-fiókot. A frissítés előtt Site Recovery alapértelmezés szerint létrehozta ezt a fiókot. Ez a beállítás akkor érhető el, ha engedélyezi a virtuális gép replikálását. Ha módosítja a beállítást, a rendszer az azonos tárolóban lévő összes Azure-beli virtuális gép esetében alkalmazza.
 
> Az automatikus frissítések bekapcsolása nem igényli az Azure-beli virtuális gépek újraindítását, vagy a folyamatban lévő replikációt is befolyásolja.

> Az Automation-fiókban a feladatok számlázása a hónap során felhasznált feladatok futtatókörnyezeti percének számától függ. Alapértelmezés szerint a 500 perc egy Automation-fiókhoz ingyenes egységként szerepel. A feladatok végrehajtása naponta körülbelül egy percet vesz igénybe, és ingyenes egységként van lefoglalva.

| A csomagban foglalt ingyenes egységek (havonta) | Ár |
|---|---|
| Feladatok futtatókörnyezete 500 perc | ₹ 0.14/perc

## <a name="enable-automatic-updates"></a>Automatikus frissítések engedélyezése

A következő módokon engedélyezheti Site Recovery számára a frissítések kezelését.

### <a name="manage-as-part-of-the-enable-replication-step"></a>Kezelés a replikálás engedélyezése lépés részeként

Ha engedélyezi egy virtuális gép replikálását [a](azure-to-azure-quickstart.md) virtuálisgép-nézetből vagy [a Recovery Services](azure-to-azure-how-to-enable-replication.md)-tárolóból, engedélyezheti site Recovery számára a site Recovery bővítmény frissítéseinek kezelését, vagy manuálisan is kezelheti azt.

![Bővítmény beállításai](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>A bővítmény frissítési beállításainak váltása a tárolón belül

1. A tárolóban lépjen a**site Recovery infrastruktúra** **kezelése** > elemre.
2. Az **Azure Virtual Machines** > **bővítmény frissítési beállításai**területen kapcsolja be a **site Recovery engedélyezése a váltógomb kezelésére lehetőséget** . A manuális kezeléshez kapcsolja ki. 
3. Kattintson a **Mentés** gombra.

![Bővítmény frissítési beállításai](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important]
> Ha a **kezelés engedélyezése site Recovery lehetőséget**választja, a rendszer a megfelelő tárolóban lévő összes virtuális gépre alkalmazza a beállítást.


> [!Note]
> Bármelyik lehetőség értesíti a frissítések kezeléséhez használt Automation-fiókról. Ha első alkalommal használja ezt a szolgáltatást egy tárolóban, a rendszer alapértelmezés szerint új Automation-fiókot hoz létre. Másik lehetőségként testreszabhatja a beállítást, és kiválaszthat egy meglévő Automation-fiókot. Az egyazon tárban lévő összes további replikáció az előzőleg létrehozottt használja. Jelenleg a legördülő lista csak azokat az Automation-fiókokat sorolja fel, amelyek ugyanabban az erőforráscsoporthoz találhatók, mint a tároló.  

Egyéni Automation-fiók esetén használja a következő parancsfájlt:

```azurepowershell
param(
    [Parameter(Mandatory=$true)]
    [String] $VaultResourceId,

    [Parameter(Mandatory=$true)]
    [ValidateSet("Enabled",'Disabled')]
    [Alias("Enabled or Disabled")]
    [String] $AutoUpdateAction,

    [Parameter(Mandatory=$false)]
    [String] $AutomationAccountArmId
)

$SiteRecoveryRunbookName = "Modify-AutoUpdateForVaultForPatner"
$TaskId = [guid]::NewGuid().ToString()
$SubscriptionId = "00000000-0000-0000-0000-000000000000"
$AsrApiVersion = "2018-01-10"
$RunAsConnectionName = "AzureRunAsConnection"
$ArmEndPoint = "https://management.azure.com"
$AadAuthority = "https://login.windows.net/"
$AadAudience = "https://management.core.windows.net/"
$AzureEnvironment = "AzureCloud"
$Timeout = "160"

function Throw-TerminatingErrorMessage
{
    Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
    )

    throw ("Message: {0}, TaskId: {1}.") -f $Message, $TaskId
}

function Write-Tracing
{
    Param
    (
        [Parameter(Mandatory=$true)]      
        [ValidateSet("Informational", "Warning", "ErrorLevel", "Succeeded", IgnoreCase = $true)]
        [String]
        $Level,

        [Parameter(Mandatory=$true)]
        [String]
        $Message,

        [Switch]
        $DisplayMessageToUser
    )

    Write-Output $Message

}

function Write-InformationTracing
{
    Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
    )

    Write-Tracing -Message $Message -Level Informational -DisplayMessageToUser
}

function ValidateInput()
{
    try
    {
        if(!$VaultResourceId.StartsWith("/subscriptions", [System.StringComparison]::OrdinalIgnoreCase))
        {
            $ErrorMessage = "The vault resource id should start with /subscriptions."
            throw $ErrorMessage
        }

        $Tokens = $VaultResourceId.SubString(1).Split("/")
        if(!($Tokens.Count % 2 -eq 0))
        {
            $ErrorMessage = ("Odd Number of tokens: {0}." -f $Tokens.Count)
            throw $ErrorMessage
        }

        if(!($Tokens.Count/2 -eq 4))
        {
            $ErrorMessage = ("Invalid number of resource in vault ARM id expected:4, actual:{0}." -f ($Tokens.Count/2))
            throw $ErrorMessage
        }

        if($AutoUpdateAction -ieq "Enabled" -and [string]::IsNullOrEmpty($AutomationAccountArmId))
        {
            $ErrorMessage = ("The automation account ARM id should not be null or empty when AutoUpdateAction is enabled.")
            throw $ErrorMessage
        }
    }
    catch
    {
        $ErrorMessage = ("ValidateInput failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Initialize-SubscriptionId()
{
    try
    {
        $Tokens = $VaultResourceId.SubString(1).Split("/")

        $Count = 0
        $ArmResources = @{}
        while($Count -lt $Tokens.Count)
        {
            $ArmResources[$Tokens[$Count]] = $Tokens[$Count+1]
            $Count = $Count + 2
        }
        
        return $ArmResources["subscriptions"]
    }
    catch
    {
        Write-Tracing -Level ErrorLevel -Message ("Initialize-SubscriptionId: failed with [Exception: {0}]." -f $_.Exception) -DisplayMessageToUser
        throw
    }
}

function Invoke-InternalRestMethod($Uri, $Headers, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-RestMethod -Uri $Uri -Headers $Headers    
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Invoke-InternalWebRequest($Uri, $Headers, $Method, $Body, $ContentType, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-WebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -UseBasicParsing
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Get-Header([ref]$Header, $AadAudience, $AadAuthority, $RunAsConnectionName){
    try 
    {
        $RunAsConnection = Get-AutomationConnection -Name $RunAsConnectionName
        $TenantId = $RunAsConnection.TenantId
        $ApplicationId = $RunAsConnection.ApplicationId
        $CertificateThumbprint = $RunAsConnection.CertificateThumbprint
        $Path = "cert:\CurrentUser\My\{0}" -f $CertificateThumbprint
        $Secret = Get-ChildItem -Path $Path
        $ClientCredential = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate(
                $ApplicationId,
                $Secret)

        # Trim the forward slash from the AadAuthority if it exist.
        $AadAuthority = $AadAuthority.TrimEnd("/")
        $AuthContext = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(
            "{0}/{1}" -f $AadAuthority, $TenantId )
        $AuthenticationResult = $authContext.AcquireToken($AadAudience, $Clientcredential)
        $Header.Value['Content-Type'] = 'application\json'
        $Header.Value['Authorization'] = $AuthenticationResult.CreateAuthorizationHeader()
        $Header.Value["x-ms-client-request-id"] = $TaskId + "/" + (New-Guid).ToString() + "-" + (Get-Date).ToString("u")
    }
    catch
    {
        $ErrorMessage = ("Get-BearerToken: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Get-ProtectionContainerToBeModified([ref] $ContainerMappingList)
{
    try 
    {
        Write-InformationTracing ("Get protection container mappings : {0}." -f $VaultResourceId)
        $ContainerMappingListUrl = $ArmEndPoint + $VaultResourceId + "/replicationProtectionContainerMappings" + "?api-version=" + $AsrApiVersion
        
        Write-InformationTracing ("Getting the bearer token and the header.")
        Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
        
        $Result = @()
        Invoke-InternalRestMethod -Uri $ContainerMappingListUrl -Headers $header -Result ([ref]$Result)
        $ContainerMappings = $Result[0]

        Write-InformationTracing ("Total retrieved container mappings: {0}." -f $ContainerMappings.Value.Count)
        foreach($Mapping in $ContainerMappings.Value)
        {
            if(($Mapping.properties.providerSpecificDetails -eq $null) -or ($Mapping.properties.providerSpecificDetails.instanceType -ine "A2A"))
            {
                Write-InformationTracing ("Mapping properties: {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the provider does not match." -f ($Mapping.Id))
                continue;
            }

            if($Mapping.Properties.State -ine "Paired")
            {
                Write-InformationTracing ("Ignoring container mapping: {0} as the state is not paired." -f ($Mapping.Id))
                continue;
            }

            Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties.providerSpecificDetails))
            $MappingAutoUpdateStatus = $Mapping.properties.providerSpecificDetails.agentAutoUpdateStatus
            $MappingAutomationAccountArmId = $Mapping.properties.providerSpecificDetails.automationAccountArmId
            $MappingHealthErrorCount = $Mapping.properties.HealthErrorDetails.Count

            if($AutoUpdateAction -ieq "Enabled" -and
                ($MappingAutoUpdateStatus -ieq "Enabled") -and
                ($MappingAutomationAccountArmId -ieq $AutomationAccountArmId) -and
                ($MappingHealthErrorCount -eq 0))
            {
                Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the auto update is already enabled and is healthy." -f ($Mapping.Id))
                continue;
            }

            ($ContainerMappingList.Value).Add($Mapping.id)
        }
    }
    catch
    {
        $ErrorMessage = ("Get-ProtectionContainerToBeModified: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

$OperationStartTime = Get-Date
$ContainerMappingList = New-Object System.Collections.Generic.List[System.String]
$JobsInProgressList = @()
$JobsCompletedSuccessList = @()
$JobsCompletedFailedList = @()
$JobsFailedToStart = 0
$JobsTimedOut = 0
$Header = @{}

$AzureRMProfile = Get-Module -ListAvailable -Name Az.Accounts | Select Name, Version, Path
$AzureRmProfileModulePath = Split-Path -Parent $AzureRMProfile.Path
Add-Type -Path (Join-Path $AzureRmProfileModulePath "Microsoft.IdentityModel.Clients.ActiveDirectory.dll")

$Inputs = ("Tracing inputs VaultResourceId: {0}, Timeout: {1}, AutoUpdateAction: {2}, AutomationAccountArmId: {3}." -f $VaultResourceId, $Timeout, $AutoUpdateAction, $AutomationAccountArmId)
Write-Tracing -Message $Inputs -Level Informational -DisplayMessageToUser
$CloudConfig = ("Tracing cloud configuration ArmEndPoint: {0}, AadAuthority: {1}, AadAudience: {2}." -f $ArmEndPoint, $AadAuthority, $AadAudience)
Write-Tracing -Message $CloudConfig -Level Informational -DisplayMessageToUser
$AutomationConfig = ("Tracing automation configuration RunAsConnectionName: {0}." -f $RunAsConnectionName)
Write-Tracing -Message $AutomationConfig -Level Informational -DisplayMessageToUser

ValidateInput
$SubscriptionId = Initialize-SubscriptionId
Get-ProtectionContainerToBeModified ([ref]$ContainerMappingList)

$Input = @{
  "properties"= @{
    "providerSpecificInput"= @{
        "instanceType" = "A2A"
        "agentAutoUpdateStatus" = $AutoUpdateAction
        "automationAccountArmId" = $AutomationAccountArmId
    }
  }
}
$InputJson = $Input |  ConvertTo-Json

if ($ContainerMappingList.Count -eq 0)
{
    Write-Tracing -Level Succeeded -Message ("Exiting as there are no container mappings to be modified.") -DisplayMessageToUser
    exit
}

Write-InformationTracing ("Container mappings to be updated has been retrieved with count: {0}." -f $ContainerMappingList.Count)

try
{
    Write-InformationTracing ("Start the modify container mapping jobs.")
    ForEach($Mapping in $ContainerMappingList)
    {
    try {
            $UpdateUrl = $ArmEndPoint + $Mapping + "?api-version=" + $AsrApiVersion
            Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
            
            $Result = @()
            Invoke-InternalWebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -Result ([ref]$Result)
            $Result = $Result[0]

            $JobAsyncUrl = $Result.Headers['Azure-AsyncOperation']
            Write-InformationTracing ("The modify container mapping job invoked with async url: {0}." -f $JobAsyncUrl)
            $JobsInProgressList += $JobAsyncUrl;

            # Rate controlling the set calls to maximum 60 calls per minute.
            # ASR throttling for set calls is 200 in 1 minute.
            Start-Sleep -Milliseconds 1000
        }
        catch{
            Write-InformationTracing ("The modify container mappings job creation failed for: {0}." -f $Ru)
            Write-InformationTracing $_
            $JobsFailedToStart++
        }
    }

    Write-InformationTracing ("Total modify container mappings has been initiated: {0}." -f $JobsInProgressList.Count)
}
catch
{
    $ErrorMessage = ("Modify container mapping jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

try
{
    while($JobsInProgressList.Count -ne 0)
    {
        Sleep -Seconds 30
        $JobsInProgressListInternal = @()
        ForEach($JobAsyncUrl in $JobsInProgressList)
        {
            try
            {
                Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
                $Result = Invoke-RestMethod -Uri $JobAsyncUrl -Headers $header
                $JobState = $Result.Status
                if($JobState -ieq "InProgress")
                {
                    $JobsInProgressListInternal += $JobAsyncUrl
                }
                elseif($JobState -ieq "Succeeded" -or `
                    $JobState -ieq "PartiallySucceeded" -or `
                    $JobState -ieq "CompletedWithInformation")
                {
                    Write-InformationTracing ("Jobs succeeded with state: {0}." -f $JobState)
                    $JobsCompletedSuccessList += $JobAsyncUrl
                }
                else
                {
                    Write-InformationTracing ("Jobs failed with state: {0}." -f $JobState)
                    $JobsCompletedFailedList += $JobAsyncUrl
                }
            }
            catch
            {
                Write-InformationTracing ("The get job failed with: {0}. Ignoring the exception and retrying the next job." -f $_.Exception)

                # The job on which the tracking failed, will be considered in progress and tried again later.
                $JobsInProgressListInternal += $JobAsyncUrl
            }

            # Rate controlling the get calls to maximum 120 calls each minute.
            # ASR throttling for get calls is 10000 in 60 minutes.
            Start-Sleep -Milliseconds 500
        }

        Write-InformationTracing ("Jobs remaining {0}." -f $JobsInProgressListInternal.Count)

        $CurrentTime = Get-Date
        if($CurrentTime -gt $OperationStartTime.AddMinutes($Timeout))
        {
            Write-InformationTracing ("Tracing modify cloud pairing jobs has timed out.")
            $JobsTimedOut = $JobsInProgressListInternal.Count
            $JobsInProgressListInternal = @()
        }

        $JobsInProgressList = $JobsInProgressListInternal
    }
}
catch
{
    $ErrorMessage = ("Tracking modify cloud pairing jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage  -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage 
}

Write-InformationTracing ("Tracking modify cloud pairing jobs completed.")
Write-InformationTracing ("Modify cloud pairing jobs success: {0}." -f $JobsCompletedSuccessList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed: {0}." -f $JobsCompletedFailedList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed to start: {0}." -f $JobsFailedToStart)
Write-InformationTracing ("Modify cloud pairing jobs timedout: {0}." -f $JobsTimedOut)

if($JobsTimedOut -gt  0)
{
    $ErrorMessage = "One or more modify cloud pairing jobs has timedout."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)   
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
elseif($JobsCompletedSuccessList.Count -ne $ContainerMappingList.Count)
{
    $ErrorMessage = "One or more modify cloud pairing jobs failed."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

Write-Tracing -Level Succeeded -Message ("Modify cloud pairing completed.") -DisplayMessageToUser
```

### <a name="manage-updates-manually"></a>Frissítések manuális kezelése

1. Ha a virtuális gépekre telepített mobilitási szolgáltatásnak új frissítései vannak, akkor a következő értesítés jelenik meg: "A site Recovery replikációs ügynök új frissítése elérhető. Kattintson ide a telepítéshez: "

     ![Replikált elemek ablak](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
2. Válassza ki az értesítést a virtuális gép kiválasztási oldalának megnyitásához.
3. Válassza ki a frissíteni kívánt virtuális gépeket, majd kattintson **az OK gombra**. Minden kiválasztott virtuális gép esetében elindul a mobilitási szolgáltatás frissítése.

     ![Replikált elemek virtuálisgép-listája](./media/vmware-azure-install-mobility-service/update-okpng.png)


## <a name="common-issues-and-troubleshooting"></a>Gyakori problémák és hibaelhárítás

Ha probléma merül fel az automatikus frissítésekkel kapcsolatban, hibaüzenet jelenik meg a **konfigurációs problémák** alatt a tároló irányítópultján.

Ha nem engedélyezte az automatikus frissítéseket, tekintse meg az alábbi gyakori hibákat és ajánlott műveleteket:

- **Hiba**: Nem jogosult Azure-beli futtató fiók (szolgáltatásnév) létrhozására és a Közreműködő szerepkör szolgáltatásnévhez való hozzárendelésére.

   **Javasolt művelet**: Győződjön meg arról, hogy a bejelentkezett fiók Közreműködőiként van hozzárendelve, és próbálkozzon újra. Tekintse meg a szükséges engedélyek szakaszt a [portál használatával olyan Azure ad-alkalmazás és-szolgáltatásnév létrehozásához, amely hozzáférhet](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) az erőforrásokhoz az engedélyek kiosztásával kapcsolatos további információkért.
 
   Az automatikus frissítések engedélyezése után a legtöbb probléma kijavításához válassza a **javítás**lehetőséget. Ha a javítás gomb nem érhető el, tekintse meg a bővítmény frissítési beállításai ablaktáblájában megjelenő hibaüzenetet.

   ![Site Recovery szolgáltatás javítása gomb a bővítmény frissítési beállításainál](./media/azure-to-azure-autoupdate/repair.png)

- **Hiba**: A futtató fiók nem jogosult a Recovery Services-erőforrás elérésére.

    **Javasolt művelet**: Törölje, majd [hozza létre újra a futtató fiókot](https://docs.microsoft.com/azure/automation/automation-create-runas-account). Vagy győződjön meg arról, hogy az Automation futtató fiók Azure Active Directory alkalmazás rendelkezik hozzáféréssel a Recovery Services-erőforráshoz.

- **Hiba**: A futtató fiók nem található. Ezek egyike törölve lett vagy nem lett létrehozva – Azure Active Directory alkalmazás, szolgáltatásnév, szerepkör, Automation-tanúsítvány eszköze, Automation-kapcsolati eszköz –, vagy az ujjlenyomat nem azonos a tanúsítvány és a kapcsolat között. 

    **Javasolt művelet**: Törölje, majd [hozza létre újra a futtató fiókot](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

-  **Hiba**: Az Automation-fiók által használt Azure-beli futtató tanúsítvány hamarosan lejár. 

    A futtató fiókhoz létrehozott önaláírt tanúsítvány a létrehozás dátumától számítva egy évig lejár. A tanúsítványt bármikor meg lehet újítani a lejárata előtt. Ha regisztrált az e-mailes értesítésekre, akkor is megkapja az e-maileket, amikor egy műveletre van szükség az Ön oldaláról. Ez a hiba a lejárati dátum előtt 2 hónappal jelenik meg, és kritikus hibára vált, ha a tanúsítvány lejárt. Ha a tanúsítvány lejárt, az automatikus frissítés nem lesz működőképes, amíg meg nem újítja a megújítását.

   **Javasolt művelet**: A probléma megoldásához kattintson a javítás gombra, majd a tanúsítvány megújítása elemre.
    
   ![tanúsítvány megújítása](media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG)

> [!NOTE]
> A tanúsítvány megújítása után frissítse a lapot, hogy az aktuális állapot frissítve legyen.
