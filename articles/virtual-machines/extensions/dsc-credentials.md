---
title: Referenties doorgeven naar Azure met Desired State Configuration
description: Informatie over het veilig referenties worden doorgegeven aan virtuele machines in Azure met behulp van PowerShell Desired State Configuration (DSC).
services: virtual-machines-windows
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: dacoulte
ms.openlocfilehash: 666253d16ac51dcc21174211f71794f8b0ede07d
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/10/2018
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Referenties doorgeven aan de handler Azure DSCExtension

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

In dit artikel bevat informatie over de uitbreiding Desired State Configuration (DSC) voor Azure. Zie voor een overzicht van de DSC-uitbreiding handler [Inleiding tot de extensie Azure Desired State Configuration handler](dsc-overview.md).

## <a name="pass-in-credentials"></a>Referenties doorgeven

Als onderdeel van het configuratieproces u mogelijk nodig hebt voor het instellen van gebruikersaccounts, toegang tot services of een programma installeert in een gebruikerscontext. Als u wilt doen, moet u referenties opgeven.

DSC kunt u configuraties met parameters instellen. Referenties zijn doorgegeven aan de configuratie en veilig opgeslagen in het MOF-bestanden in een configuratie met parameters. De extensie Azure-handler vereenvoudigt Referentiebeheer dankzij automatisch beheer van certificaten.

De volgende DSC-configuratiescript wordt een lokale gebruikersaccount gemaakt met het opgegeven wachtwoord:

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

Het is belangrijk om op te nemen **knooppunt localhost** als onderdeel van de configuratie. De extensie-handler specifiek zoekt de **knooppunt localhost** instructie. Als deze instructie ontbreekt, werken de volgende stappen niet. Het is ook belangrijk om op te nemen de typecast **[PsCredential]**. Dit specifieke type activeert de uitbreiding voor het versleutelen van de referentie.

Dit script publiceren naar Azure Blob-opslag:

`Publish-AzureRmVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

De extensie Azure DSC instellen en geef de referenties:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzureRmVM -Name 'example-1'

$vm = Set-AzureRmVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzureRmVM
```

## <a name="how-a-credential-is-secured"></a>Hoe een referentie op die wordt beveiligd

Vraagt u deze code uitvoert om een referentie. Nadat de referentie is opgegeven, wordt deze kort opgeslagen in het geheugen. Wanneer de referentie is gepubliceerd met behulp van de **Set AzureRmVMDscExtension** de referentie-cmdlet wordt verzonden via HTTPS naar de virtuele machine. In de virtuele machine opgeslagen Azure de referentie op schijf wordt versleuteld met het lokale certificaatarchief van de virtuele machine. De referentie kort in het geheugen wordt ontsleuteld en vervolgens opnieuw versleuteld deze doorgeven aan DSC.

Dit proces is anders dan [met behulp van veilige configuraties zonder de extensie-handler](/powershell/dsc/securemof). De Azure-omgeving biedt een manier voor het verzenden van configuratiegegevens veilig via certificaten. Wanneer u de handler van DSC-uitbreiding gebruikt, u hoeft niet te bieden **$CertificatePath** of een **$CertificateID**/ **$Thumbprint** vermelding in **ConfigurationData**.

## <a name="next-steps"></a>Volgende stappen

- Ophalen van een [Inleiding tot Azure DSC-uitbreiding handler](dsc-overview.md).
- Bekijk de [Azure Resource Manager-sjabloon voor de uitbreiding van DSC](dsc-template.md).
- Voor meer informatie over PowerShell DSC, gaat u naar de [PowerShell-documentatiecentrum](/powershell/dsc/overview).
- Ga voor meer functionaliteit die u beheren kunt met behulp van PowerShell DSC, en voor meer DSC-resources, het [PowerShell gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).