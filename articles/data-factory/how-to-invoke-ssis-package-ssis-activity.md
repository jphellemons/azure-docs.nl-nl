---
title: Uitvoeren van SSIS-pakket met behulp van de activiteit van SSIS-pakket niet uitvoeren in Azure Data Factory | Microsoft Docs
description: Dit artikel wordt beschreven hoe u een pakket met SQL Server Integration Services (SSIS) uitvoert van een Azure Data Factory-pijplijn met behulp van de activiteit SSIS-pakket niet uitvoeren.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: douglasl
ms.openlocfilehash: fed4e10fcaaa5282c37b175f355b94522c3b2b46
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700482"
---
# <a name="run-an-ssis-package-using-the-execute-ssis-package-activity-in-azure-data-factory"></a>Voer een SSIS-pakket met de activiteit SSIS-pakket niet uitvoeren in Azure Data Factory
In dit artikel wordt beschreven hoe een SSIS-pakket met een Azure Data Factory-pijplijn uitvoeren door een activiteit SSIS-pakket niet uitvoeren. 

> [!NOTE]
> Dit artikel is van toepassing op versie 2 van Data Factory, dat zich momenteel in de previewfase bevindt. De activiteit uitvoeren van SSIS-pakket is niet beschikbaar in versie 1 van de Data Factory-service (GA) is algemeen beschikbaar is. Zie voor een alternatieve methode voor het uitvoeren van SSIS-pakket met versie 1 van de Data Factory-service, [uitvoeren SSIS-pakketten met behulp van de activiteit opgeslagen procedure in versie 1](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Vereisten

### <a name="azure-sql-database"></a>Azure SQL Database 
De procedures in dit artikel maakt gebruik van een Azure SQL-database die als host fungeert voor de SSIS-catalogus. U kunt ook een Azure SQL beheerd-exemplaar (Preview) gebruiken.

## <a name="create-an-azure-ssis-integration-runtime"></a>Een Azure SSIS Integration Runtime maken
Een Azure-SSIS-integratie runtime maken als u geen volgt u de stapsgewijze instructies in de [zelfstudie: implementeren SSIS-pakketten](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Data Factory-gebruikersinterface (Azure-portal)
In deze sectie kunt u Data Factory-gebruikersinterface gebruiken voor het maken van een Data Factory-pijplijn met een SSIS-pakket niet uitvoeren-activiteit die wordt uitgevoerd een SSIS-pakket.

### <a name="create-a-data-factory"></a>Een gegevensfactory maken
Eerste stap is het maken van een gegevensfactory met behulp van de Azure-portal. 

1. Start de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
2. Navigeer naar [Azure Portal](https://portal.azure.com). 
3. Klik op **Nieuw** in het linkermenu en klik vervolgens op **Gegevens en analyses** en **Data Factory**. 
   
   ![Nieuw -> DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Voer op de pagina **Nieuwe data factory** **ADFTutorialDataFactory** in als **naam**. 
      
     ![De pagina Nieuwe data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   De naam van de Azure-gegevensfactory moet **wereldwijd uniek** zijn. Als het volgende foutbericht wordt weergegeven voor het naamveld, wijzigt u de naam van de data factory (bijvoorbeeld uwnaamADFTutorialDataFactory). Zie het artikel [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
  
     ![Naam niet beschikbaar - fout](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Selecteer het Azure-**abonnement** waarin u de gegevensfactory wilt maken. 
4. Voer een van de volgende stappen uit voor de **Resourcegroep**:
     
      - Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst. 
      - Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in.   
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/resource-group-overview.md) voor meer informatie.  
4. Selecteer **V2 (Preview)** als de **versie**.
5. Selecteer de **locatie** voor de gegevensfactory. In de vervolgkeuzelijst ziet u alleen locaties die worden ondersteund in Data Factory. De gegevensopslagexemplaren (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich op andere locaties bevinden.
6. Selecteer **Vastmaken aan dashboard**.     
7. Klik op **Create**.
8. Op het dashboard ziet u de volgende tegel met de status: **Gegevensfactory implementeren**. 

    ![tegel met de status 'gegevensfactory implementeren'](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Na het aanmaken ziet u de pagina **Data Factory** zoals weergegeven in de afbeelding.
   
    ![Startpagina van de gegevensfactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Klik op de tegel **Maken en controleren** om de gebruikersinterface (UI) van Azure Data Factory te openen op een afzonderlijk tabblad. 

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Een pijplijn maken met een activiteit SSIS-pakket uitvoeren
In deze stap gebruikt u de Data Factory-gebruikersinterface voor het maken van een pijplijn. U voegt een activiteit SSIS-pakket uitvoeren aan de pijplijn en configureren voor het uitvoeren van SSIS-pakket. 

1. Klik in de pagina aan de slag, **maken pijplijn**: 

    ![Pagina Aan de slag](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. In de **activiteiten** werkset Vouw **algemene**, en slepen en neerzetten de **SSIS-pakket uitvoeren** activiteit naar het ontwerpoppervlak pijplijn. 

   ![Sleep de activiteit SSIS naar het ontwerpoppervlak](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Op de **algemene** tabblad met eigenschappen voor de activiteit SSIS-pakket uitvoeren, Geef een naam en beschrijving voor de activiteit. Optionele timeout en probeer de waarden.

    ![Eigenschappen worden ingesteld op het tabblad Algemeen](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Op de **instellingen** tabblad met eigenschappen voor de SSIS-pakket uitvoeren activiteit, selecteer de Azure-SSIS-integratie Runtime die zijn gekoppeld aan de `SSISDB` database waar het pakket wordt geïmplementeerd. Geef het pakketpad in de `SSISDB` database in de notatie `<folder name>/<project name>/<package name>.dtsx`. (Optioneel) Geef 32-bits worden uitgevoerd en het niveau van een vooraf gedefinieerde of aangepaste logboekregistratie en geef een omgeving in de indeling `<folder name>/<environment name>`.

    ![Eigenschappen worden ingesteld op het tabblad instellingen](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

5. Voor het valideren van de configuratie van de pipeline, klikt u op **valideren** op de werkbalk. Sluit het venster **Pipeline Validation Report** door op **>>** te klikken.

6. De pijplijn publiceren naar Data Factory door te klikken op **Alles publiceren** knop. 

### <a name="optionally-parameterize-the-activity"></a>U kunt desgewenst parameter van de activiteit

Desgewenst toewijzen waarden, expressies of functies, die kunnen verwijzen naar de Data Factory-systeemvariabelen in uw project of pakket parameters in JSON-indeling op de **Geavanceerd** tabblad. Bijvoorbeeld, kunt u Data Factory-pijplijn parameters aan uw project SSIS of Pakketparameters zoals weergegeven in de volgende schermafbeelding toewijzen:

![Voeg parameters toe aan de activiteit SSIS-pakket uitvoeren](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters.png)

### <a name="run-and-monitor-the-pipeline"></a>Uitvoeren en controleren van de pijplijn
In deze sectie een pijplijn run activeren en deze vervolgens te controleren. 

1. Als u wilt activeren van een pijplijn die worden uitgevoerd, klikt u op **Trigger** op de werkbalk en klikt u op **nu starten**. 

    ![Nu activeren](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Selecteer in het venster **Pijplijnuitvoering** de optie **Voltooien**. 

3. Ga naar het tabblad **Controleren** aan de linkerkant. U ziet de pijplijn uitvoeren en de status ervan samen met andere informatie (zoals tijd uitvoeren starten). Als u de lijst wilt vernieuwen, klikt u op **Vernieuwen**.

    ![Pijplijnuitvoeringen](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

4. Klik op de koppeling **Uitvoeringen van activiteiten weergeven** in de kolom **Acties**. Er is slechts één activiteit die wordt uitgevoerd als de pijplijn slechts één activiteit (de SSIS-pakket uitvoeren heeft).

    ![Uitvoering van activiteiten](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

5. U kunt de volgende uitvoeren **query** tegen de SSISDB-database in uw Azure SQL-server om te controleren of het pakket uitgevoerd. 

    ```sql
    select * from catalog.executions
    ```

    ![Controleer of de pakket-uitvoeringen](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

6. U kunt ook de SSISDB uitvoerings-ID ophalen uit de uitvoer van de pijplijn activiteit uitvoeren, en de ID gebruiken om uitgebreidere uitvoeringslogboeken en foutberichten in SSMS te controleren.

    ![Ophalen van de id van de uitvoering.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

> [!NOTE]
> U kunt ook een geplande trigger maken voor uw pijplijn, zodat de pijplijn wordt uitgevoerd volgens een schema (elk uur, dagelijks, enz.). Zie voor een voorbeeld [Maak een gegevensfactory - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell
In deze sectie kunt u Azure PowerShell gebruiken voor het maken van een Data Factory-pijplijn met een SSIS-activiteit die wordt uitgevoerd een SSIS-pakket. 

Installeer de nieuwste Azure PowerShell-modules met de instructies in [Azure PowerShell installeren en configureren](/powershell/azure/install-azurerm-ps). 

### <a name="create-a-data-factory"></a>Een gegevensfactory maken
U kunt de dezelfde data factory met de Azure-SSIS-IR gebruiken of maak een afzonderlijke gegevensfactory. De volgende procedure bevat stappen voor het maken van een gegevensfactory. U maakt een pijplijn met een SSIS-activiteit in deze gegevensfactory. De SSIS-activiteit actief SSIS-pakket. 

1. Definieer een variabele voor de naam van de resourcegroep die u later gaat gebruiken in PowerShell-opdrachten. Kopieer de tekst van de volgende opdracht naar PowerShell, geef tussen dubbele aanhalingstekens een naam op voor de [Azure-resourcegroep](../azure-resource-manager/resource-group-overview.md) en voer de opdracht uit. Bijvoorbeeld: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Als de resourcegroep al bestaat, wilt u waarschijnlijk niet dat deze wordt overschreven. Wijs een andere waarde toe aan de `$ResourceGroupName`-variabele en voer de opdracht opnieuw uit.
2. Voer de volgende opdracht uit om de resourcegroep te maken: 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Als de resourcegroep al bestaat, wilt u waarschijnlijk niet dat deze wordt overschreven. Wijs een andere waarde toe aan de `$ResourceGroupName`-variabele en voer de opdracht opnieuw uit. 
3. Definieer een variabele voor de naam van de data factory. 

    > [!IMPORTANT]
    >  Werk de naam van de data factory zodanig bij dat deze uniek is. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Voer de volgende cmdlet **Set-AzureRmDataFactoryV2** uit om de data factory te maken met de eigenschap Location en ResourceGroupName van de variabele $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
                                            -Location $ResGrp.Location `
                                            -Name $dataFactoryName 
    ```

Houd rekening met de volgende punten:

* De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als de volgende fout zich voordoet, wijzigt u de naam en probeert u het opnieuw.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Als u Data Factory-exemplaren wilt maken, moet het gebruikersaccount waarmee u zich bij Azure aanmeldt, lid zijn van de rollen **Inzender** of **Eigenaar**, of moet dit een **beheerder** van het Azure-abonnement zijn.
* Op dit moment kunt Gegevensfactory versie 2 u data Factory alleen maken in de VS-Oost, Oost-US2, West-Europa en Zuidoost-Azië regio's. De gegevensopslagexemplaren (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich in andere regio's bevinden.

### <a name="create-a-pipeline-with-an-ssis-activity"></a>Een pijplijn maken met een SSIS-activiteit 
In deze stap maakt u een pijplijn met een SSIS-activiteit. De activiteit actief SSIS-pakket. 

1. Maak een JSON-bestand met de naam **RunSSISPackagePipeline.json** in de **C:\ADF\RunSSISPackage** map met inhoud die vergelijkbaar is met het volgende voorbeeld:

    > [!IMPORTANT]
    > Vervang objectnamen, beschrijvingen en paden, eigenschap en parameterwaarden, wachtwoorden en andere waarden van variabelen voordat u het bestand opslaat. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [{
                "name": "mySSISActivity",
                "description": "My SSIS package/activity description",
                "type": "ExecuteSSISPackage",
                "typeProperties": {
                    "connectVia": {
                        "referenceName": "myAzureSSISIR",
                        "type": "IntegrationRuntimeReference"
                    },
                    "runtime": "x64",
                    "loggingLevel": "Basic",
                    "packageLocation": {
                        "packagePath": "FolderName/ProjectName/PackageName.dtsx"            
                    },
                    "environmentPath":   "FolderName/EnvironmentName",
                    "projectParameters": {
                        "project_param_1": {
                            "value": "123"
                        }
                    },
                    "packageParameters": {
                        "package_param_1": {
                            "value": "345"
                        }
                    },
                    "projectConnectionManagers": {
                        "MyAdonetCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "abc"
                                }
                            }
                        }
                    },
                    "packageConnectionManagers": {
                        "MyOledbCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "def"
                                }
                            }
                        }
                    },
                    "propertyOverrides": {
                        "\\PackageName.dtsx\\MaxConcurrentExecutables ": {
                            "value": 8,
                            "isSensitive": false
                        }
                    }
                },
                "policy": {
                    "timeout": "0.01:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30
                }
            }]
        }
    }
    ```

2.  In Azure PowerShell overschakelen naar de `C:\ADF\RunSSISPackage` map.

3. Maken van de pijplijn **RunSSISPackagePipeline**, voert de **Set AzureRmDataFactoryV2Pipeline** cmdlet.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                   -ResourceGroupName $ResGrp.ResourceGroupName `
                                                   -Name "RunSSISPackagePipeline"
                                                   -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Hier volgt een voorbeeld van uitvoer:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Een pijplijnuitvoering maken
Gebruik de **Invoke-AzureRmDataFactoryV2Pipeline** cmdlet uitvoeren van de pijplijn. De cmdlet retourneert de id voor de pijplijnuitvoering voor toekomstige controle.

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.

Voer het volgende PowerShell-script uit om continu de status van de pijplijnuitvoering te controleren totdat het kopiëren van de gegevens is voltooid. Kopieer/plak het volgende script in het PowerShell-venster en druk op ENTER. 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

U kunt ook de pijplijn met de Azure-portal bewaken. Zie voor stapsgewijze instructies [bewaken van de pijplijn](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="create-a-trigger"></a>Geen trigger maken
In de vorige stap, moet u de pijplijn op verzoek uitgevoerd. U kunt ook een schema-trigger voor het uitvoeren van de pijplijn op een planning (elk uur, dagelijks, enz.) maken.

1. Maak een JSON-bestand met de naam **MyTrigger.json** in **C:\ADF\RunSSISPackage** map met de volgende inhoud: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. In **Azure PowerShell**, overschakelen naar de **C:\ADF\RunSSISPackage** map.
3. Voer de **Set AzureRmDataFactoryV2Trigger** cmdlet, waarbij de trigger wordt gemaakt. 

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                    -DataFactoryName $DataFactory.DataFactoryName `
                                    -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Standaard wordt de trigger is gestopt. Start de trigger met de **Start AzureRmDataFactoryV2Trigger** cmdlet. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                      -DataFactoryName $DataFactory.DataFactoryName `
                                      -Name "MyTrigger" 
    ```
5. Bevestig dat de trigger is gestart door het uitvoeren van de **Get-AzureRmDataFactoryV2Trigger** cmdlet. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                    -DataFactoryName $DataFactoryName `
                                    -Name "MyTrigger"     
    ```    
6. Voer de volgende opdracht na het volgende uur. Bijvoorbeeld, als de huidige tijd 3:25 uur UTC is, voer de opdracht op 4 uur UTC. 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                       -DataFactoryName $DataFactoryName `
                                       -TriggerName "MyTrigger" `
                                       -TriggerRunStartedAfter "2017-12-06" `
                                       -TriggerRunStartedBefore "2017-12-09"
    ```

    U kunt de volgende query uitvoeren tegen de SSISDB-database in uw Azure SQL-server om te controleren of het pakket uitgevoerd. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Volgende stappen
Zie het volgende blogbericht:
-   [Moderniseren en uw werkstromen ETL/ELT met SSIS-activiteiten in ADF pijplijnen uitbreiden](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
