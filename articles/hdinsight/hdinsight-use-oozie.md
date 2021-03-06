---
title: Hadoop Oozie in HDInsight gebruiken | Microsoft Docs
description: Hadoop Oozie in HDInsight, een big data-service gebruiken. Informatie over het definiëren van een werkstroom Oozie, en het verzenden van een Oozie-taak.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 870098f0-f416-4491-9719-78994bf4a369
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: c0558432c0d74e2c9fcec108182a4dbafa332904
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2018
---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-in-hdinsight"></a>Oozie gebruiken met Hadoop om te definiëren en uitvoeren van een werkstroom in HDInsight
[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Informatie over het gebruik van Apache Oozie voor het definiëren van een werkstroom en de werkstroom uitvoert op HDInsight. Zie voor meer informatie over de Oozie-coördinator, [op basis van tijd Hadoop Oozie-coördinator gebruiken met HDInsight][hdinsight-oozie-coordinator-time]. Zie voor meer informatie over Azure Data Factory, [Use Pig en Hive met Data Factory][azure-data-factory-pig-hive].

Apache Oozie is een werkstroom/coördinatie systeem waarmee Hadoop-taken worden beheerd. Het is geïntegreerd met de Hadoop-stack en Hadoop-taken voor Apache MapReduce, Apache Pig, Apache Hive en Apache Sqoop ondersteunt. Het kan ook worden gebruikt voor het plannen van taken die specifiek voor een systeem, zoals Java-programma's of shell-scripts zijn.

De werkstroom die u implementeert door de instructies in deze zelfstudie bevat twee acties:

![Werkstroomdiagram][img-workflow-diagram]

1. Een Hive-actie wordt een HiveQL-script voor het tellen van elk type logboek-niveau in een bestand log4j uitgevoerd. Elk bestand log4j bestaat uit een line-of velden met een veld [LOGBOEKNIVEAU] waarin het type en de ernst, bijvoorbeeld:
   
        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...
   
    De uitvoer van de Hive-script is vergelijkbaar met:
   
        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4
   
    Zie [Hive gebruiken met HDInsight][hdinsight-use-hive] voor meer informatie over Hive.
2. Een actie Sqoop exporteert u de uitvoer HiveQL naar een tabel in een Azure SQL database. Zie voor meer informatie over Sqoop [Hadoop-Sqoop gebruiken met HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Zie voor ondersteunde versies van de Oozie op HDInsight-clusters, [wat is er nieuw in de Hadoop-clusterversies geleverd door HDInsight?] [hdinsight-versions].
> 
> 

### <a name="prerequisites"></a>Vereisten
Voordat u deze zelfstudie begint, hebt u het volgende item:

* **Een werkstation met Azure PowerShell**. 
  

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
  

## <a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Oozie-werkstroom en het bijbehorende HiveQL-script definiëren
Oozie werkstromen definities worden geschreven in hPDL (een XML-proces Definition Language). De standaardnaam van de werkstroom is *workflow.xml*. Hieronder volgt de werkstroombestand dat u in deze zelfstudie gebruikt.

    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>

        <action name="RunHiveScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.job.queue.name</name>
                        <value>${queueName}</value>
                    </property>
                </configuration>
                <script>${hiveScript}</script>
                <param>hiveTableName=${hiveTableName}</param>
                <param>hiveDataFolder=${hiveDataFolder}</param>
                <param>hiveOutputFolder=${hiveOutputFolder}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>

        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.compress.map.output</name>
                        <value>true</value>
                    </property>
                </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveOutputFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\001"</arg>
            </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>

        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>

        <end name="end"/>
    </workflow-app>

Er zijn twee acties die zijn gedefinieerd in de werkstroom. De actie start voor *RunHiveScript*. Als de actie is uitgevoerd, is de volgende actie *RunSqoopExport*.

De RunHiveScript heeft meerdere variabelen. U kunt de waarden doorgeven bij het verzenden van de taak Oozie vanuit uw werkstation met behulp van Azure PowerShell.

<table border = "1">
<tr><th>Werkstroomvariabelen</th><th>Beschrijving</th></tr>
<tr><td>${jobTracker}</td><td>Hiermee geeft u de URL van de taak-tracering in Hadoop. Gebruik <strong>jobtrackerhost:9010</strong> in HDInsight versie 3.0 en 2.1.</td></tr>
<tr><td>${nameNode}</td><td>Hiermee geeft u de URL van het knooppunt Hadoop-naam. Het bestand system standaardadres gebruiken, bijvoorbeeld <i>wasb: / /&lt;containerName&gt;@&lt;storageAccountName&gt;. blob.core.windows.net</i>.</td></tr>
<tr><td>${Wachtrijnaam}</td><td>Hiermee geeft u de naam van de wachtrij voor de taak wordt verzonden naar. Gebruik de <strong>standaard</strong>.</td></tr>
</table>

<table border = "1">
<tr><th>Hive actie variabele</th><th>Beschrijving</th></tr>
<tr><td>${hiveDataFolder}</td><td>Hiermee geeft u de bronmap voor de opdracht Create Table Hive.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Hiermee geeft u de map voor uitvoer voor de instructie INSERT OVERSCHRIJVEN.</td></tr>
<tr><td>${hiveTableName}</td><td>Hiermee geeft u de naam van de Hive-tabel die verwijst naar de gegevensbestanden log4j.</td></tr>
</table>

<table border = "1">
<tr><th>Sqoop actie variabele</th><th>Beschrijving</th></tr>
<tr><td>${sqlDatabaseConnectionString}</td><td>Hiermee geeft u de verbindingsreeks voor Azure SQL database.</td></tr>
<tr><td>${sqlDatabaseTableName}</td><td>Hiermee geeft u de waar de gegevens worden geëxporteerd naar Azure SQL-databasetabel.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Hiermee geeft u de map voor uitvoer voor de instructie Hive invoegen OVERSCHRIJVEN. Dit is dezelfde map voor het exporteren van Sqoop (export-dir).</td></tr>
</table>

Zie voor meer informatie over Oozie-werkstroom en het gebruik van de werkstroomacties [Apache Oozie 4.0 documentatie] [ apache-oozie-400] (voor HDInsight versie 3.0) of [Apache Oozie 3.3.2 documentatie] [ apache-oozie-332] (voor HDInsight versie 2.1).

De Hive-bewerking in de werkstroom aangeroepen door een scriptbestand HiveQL. Dit scriptbestand bevat drie HiveQL-instructies:

    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

1. **De instructie DROP TABLE** de log4j Hive-tabel wordt verwijderd als deze bestaat.
2. **De instructie CREATE TABLE** maakt een log4j Hive externe tabel die naar de locatie van het logboekbestand log4j verwijst. Het veldscheidingsteken is ",". Het scheidingsteken voor regel is '\n'. Een externe Hive-tabel wordt om te voorkomen dat het gegevensbestand van de oorspronkelijke locatie wordt verwijderd als u wilt uitvoeren van de werkstroom Oozie meerdere keren gebruikt.
3. **De instructie INSERT OVERSCHRIJVEN** telt instanties van elk type logboek-niveau van de log4j Hive-tabel en de uitvoer wordt opgeslagen naar een blob in Azure Storage.

Er zijn drie variabelen die worden gebruikt in het script:

* ${hiveTableName}
* ${hiveDataFolder}
* ${hiveOutputFolder}

De werkstroom-definitiebestand (workflow.xml in deze zelfstudie) geeft deze waarden aan dit HiveQL-script tijdens runtime.

Bestand van de werkstroom en het bestand HiveQL beide worden opgeslagen in een blob-container.  Het PowerShell-script dat u later in deze zelfstudie gebruiken beide bestanden gekopieerd naar het standaardaccount voor opslag. 

## <a name="submit-oozie-jobs-using-powershell"></a>Met behulp van PowerShell Oozie-taken verzenden
Azure PowerShell biedt momenteel niet alle cmdlets voor het definiëren van Oozie-taken. U kunt de **Invoke-RestMethod** cmdlet Oozie-webservices aanroepen. De Oozie-webservices-API is een HTTP REST-API JSON. Zie voor meer informatie over de webservices Oozie API [Apache Oozie 4.0 documentatie] [ apache-oozie-400] (voor HDInsight versie 3.0) of [Apache Oozie 3.3.2 documentatie] [ apache-oozie-332] (voor HDInsight versie 2.1).

Het PowerShell-script in deze sectie voert de volgende stappen uit:

1. Verbinding maken met Azure.
2. Maak een Azure-resourcegroep. Zie voor meer informatie [gebruik Azure PowerShell met Azure Resource Manager](../powershell-azure-resource-manager.md).
3. Een Azure SQL Database-server, een Azure SQL database en twee tabellen maken. Deze worden gebruikt door de actie Sqoop in de werkstroom.
   
    De tabelnaam van de is *log4jLogCount*.
4. Maak een HDInsight-cluster gebruikt voor het uitvoeren van Oozie-taken.
   
    Als u wilt onderzoeken van het cluster, kunt u de Azure portal of Azure PowerShell.
5. Kopieer het bestand van de oozie-werkstroom en het scriptbestand HiveQL naar het standaardbestandssysteem.
   
    Beide bestanden worden opgeslagen in een openbare Blob-container.
   
   * Kopieer het HiveQL-script (useoozie.hql) naar Azure Storage (wasb:///tutorials/useoozie/useoozie.hql).
   * Kopieer workflow.xml naar wasb:///tutorials/useoozie/workflow.xml.
   * Kopieer het bestand (/ example/data/sample.log) naar wasb:///tutorials/useoozie/data/sample.log.
6. Verzenden van een Oozie-taak.
   
    Gebruiken om te controleren van de resultaten van de taak OOzie, Visual Studio of andere hulpprogramma's verbinding maken met de Azure SQL Database.

Hier wordt het script.  U kunt het script uitvoeren van Windows PowerShell ISE. U moet alleen de eerste 7 variabelen configureren.

    #region - provide the following values

    $subscriptionID = "<Enter your Azure subscription ID>"

    # SQL Database server login credentials used for creating and connecting
    $sqlDatabaseLogin = "<Enter SQL Database Login Name>"
    $sqlDatabasePassword = "<Enter SQL Database Login Password>"

    # HDInsight cluster HTTP user credential used for creating and connectin
    $httpUserName = "admin"  # The default name is "admin"
    $httpPassword = "<Enter HDInsight Cluster HTTP User Password>"

    # Used for creating Azure service names
    $nameToken = "<Enter an Alias>"
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion

    #region - variables

    # Resource group variables
    $resourceGroupName = $namePrefix + "rg"
    $location = "East US 2" # used by all Azure services defined in this tutorial

    # SQL database varialbes
    $sqlDatabaseServerName = $namePrefix + "sqldbserver"
    $sqlDatabaseName = $namePrefix + "sqldb"
    $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $sqlDatabaseMaxSizeGB = 10

    # Used for retrieving external IP address and creating firewall rules
    $ipAddressRestService = "http://bot.whatismyipaddress.com"
    $fireWallRuleName = "UseSqoop"

    # HDInsight variables
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionId $subscriptionID
    }
    #endregion

    #region - Create Azure resouce group
    Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
    try{
        Get-AzureRmResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    }
    #endregion

    #region - Create Azure SQL database server
    Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
    try{
        Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $sqlLoginCredentials = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

        $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                    -ResourceGroupName $resourceGroupName `
                                    -ServerName $sqlDatabaseServerName `
                                    -SqlAdministratorCredentials $sqlLoginCredentials `
                                    -Location $location).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan

        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-workstation" `
            -StartIpAddress $workstationIPAddress `
            -EndIpAddress $workstationIPAddress

        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
        #Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-Azureservices" `
            -StartIpAddress "0.0.0.0" `
            -EndIpAddress "0.0.0.0"
    }
    #endregion

    #region - Create and validate Azure SQL database
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green

    try {
        Get-AzureRmSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName
    }
    catch {
        New-AzureRMSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName `
            -Edition "Standard" `
            -RequestedServiceObjectiveName "S1"
    }
    #endregion

    #region - Create SQL database tables
    Write-Host "Creating the log4jlogs table  ..." -ForegroundColor Green

    $sqlDatabaseTableName = "log4jLogsCount"
    $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
            [Level] [nvarchar](10) NOT NULL,
            [Total] float,
        CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
        (
        [Level] ASC
        )
        )"

    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = $sqlDatabaseConnectionString
    $conn.Open()

    # Create the log4jlogs table and index
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.Connection = $conn
    $cmd.CommandText = $cmdCreateLog4jCountTable
    $cmd.ExecuteNonQuery()

    $conn.close()
    #endregion

    #region - Create HDInsight cluster

    Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green

    # Create the default storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS

    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                        -StorageAccountName $defaultStorageAccountName `
                                        -StorageAccountKey $defaultStorageAccountKey 
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext 

    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -Location $location `
        -ClusterType Hadoop `
        -OSType Windows `
        -ClusterSizeInNodes 2 `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultBlobContainerName 

    # Validate the cluster
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
    #endregion

    #region - copy Oozie workflow and HiveQL files

    Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green

    # Both files are stored in a public Blob
    $publicBlobContext = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous

    # WASB folder for storing the Oozie tutorial files.
    $destFolder = "tutorials/useoozie"  # Do NOT use the long path here

    Start-CopyAzureStorageBlob `
        -Context $publicBlobContext `
        -SrcContainer "useoozie" `
        -SrcBlob "useooziewf.hql"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -DestBlob "$destFolder/useooziewf.hql" `
        -Force

    Start-CopyAzureStorageBlob `
        -Context $publicBlobContext `
        -SrcContainer "useoozie" `
        -SrcBlob "workflow.xml"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -DestBlob "$destFolder/workflow.xml" `
        -Force

    #validate the copy
    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/workflow.xml

    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/useooziewf.hql

    #endregion

    #region - copy the sample.log file

    Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green

    Start-CopyAzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -SrcContainer $defaultBlobContainerName `
        -SrcBlob "example/data/sample.log"  `
        -DestContext $defaultStorageAccountContext `
        -DestContainer $defaultBlobContainerName `
        -destBlob "$destFolder/data/sample.log" 

    #validate the copy
    Get-AzureStorageBlob `
        -Context $defaultStorageAccountContext `
        -Container $defaultBlobContainerName `
        -Blob $destFolder/data/sample.log

    #endregion

    #region - submit Oozie job

    $storageUri="wasb://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net"

    $oozieJobName = $namePrefix + "OozieJob"

    #Oozie WF variables
    $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
    $waitTimeBetweenOozieJobStatusCheck=10

    #Hive action variables
    $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
    $hiveTableName = "log4jlogs"
    $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
    $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

    #Sqoop action variables
    $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    $OoziePayload =  @"
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

    <property>
        <name>nameNode</name>
        <value>$storageUrI</value>
    </property>

    <property>
        <name>jobTracker</name>
        <value>jobtrackerhost:9010</value>
    </property>

    <property>
        <name>queueName</name>
        <value>default</value>
    </property>

    <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
    </property>

    <property>
        <name>hiveScript</name>
        <value>$hiveScript</value>
    </property>

    <property>
        <name>hiveTableName</name>
        <value>$hiveTableName</value>
    </property>

    <property>
        <name>hiveDataFolder</name>
        <value>$hiveDataFolder</value>
    </property>

    <property>
        <name>hiveOutputFolder</name>
        <value>$hiveOutputFolder</value>
    </property>

    <property>
        <name>sqlDatabaseConnectionString</name>
        <value>&quot;$sqlDatabaseConnectionString&quot;</value>
    </property>

    <property>
        <name>sqlDatabaseTableName</name>
        <value>$SQLDatabaseTableName</value>
    </property>

    <property>
        <name>user.name</name>
        <value>$httpUserName</value>
    </property>

    <property>
        <name>oozie.wf.application.path</name>
        <value>$oozieWFPath</value>
    </property>

    </configuration>
    "@

    Write-Host "Checking Oozie server status..." -ForegroundColor Green
    $clusterUriStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/admin/status"
    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $httpCredential -OutVariable $OozieServerStatus

    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $oozieServerSatus = $jsonResponse[0].("systemMode")
    Write-Host "Oozie server status is $oozieServerSatus."

    # create Oozie job
    Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
    Write-Host "`n--------`n$OoziePayload`n--------"
    $clusterUriCreateJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/jobs"
    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $httpCredential -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug

    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $oozieJobId = $jsonResponse[0].("id")
    Write-Host "Oozie job id is $oozieJobId..."

    # start Oozie job
    Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
    $clusterUriStartJob = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $httpCredential | Format-Table -HideTableHeaders #-debug

    # get job status
    Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
    Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

    Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
    $clusterUriGetJobStatus = "https://$hdinsightClusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
    $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
    $JobStatus = $jsonResponse[0].("status")

    while($JobStatus -notmatch "SUCCEEDED|KILLED")
    {
        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $httpCredential
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")
        $jobStatus
    }

    Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green

    #endregion


**De zelfstudie opnieuw uitvoeren**

Als u wilt de werkstroom opnieuw uitvoert, moet u de volgende items verwijderen:

* Het uitvoerbestand voor Hive-script
* De gegevens in de tabel log4jLogsCount

Hier volgt een voorbeeld PowerShell-script die u kunt gebruiken:

    $resourceGroupName = "<AzureResourceGroupName>"

    $defaultStorageAccountName = "<AzureStorageAccountName>"
    $defaultBlobContainerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServerName = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabasePassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $defaultBlobContainerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u een werkstroom Oozie definiëren en hoe u een Oozie-taak uitvoert met behulp van PowerShell. Zie voor meer informatie de volgende artikelen:

* [Tijd gebaseerde Oozie-coördinator gebruiken met HDInsight][hdinsight-oozie-coordinator-time]
* [Aan de slag met Hadoop Hive in HDInsight analyseren mobiele telefoon gebruiken][hdinsight-get-started]
* [Azure Blob storage gebruiken met HDInsight][hdinsight-storage]
* [HDInsight met behulp van PowerShell beheren][hdinsight-admin-powershell]
* [Uploaden van gegevens voor Hadoop-taken in HDInsight][hdinsight-upload-data]
* [Sqoop gebruiken met Hadoop in HDInsight][hdinsight-use-sqoop]
* [Hive gebruiken met Hadoop in HDInsight][hdinsight-use-hive]
* [Pig gebruiken met Hadoop in HDInsight][hdinsight-use-pig]
* [Het ontwikkelen van Java-MapReduce-programma's voor HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: ../sql-database-create-configure.md
[sqldatabase-get-started]: ../sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
