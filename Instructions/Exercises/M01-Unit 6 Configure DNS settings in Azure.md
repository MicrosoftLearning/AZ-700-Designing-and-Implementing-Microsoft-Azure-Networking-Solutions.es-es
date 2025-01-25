---
Exercise:
  title: 'CM01: Unidad 6 Configuración de las opciones de DNS en Azure'
  module: Module 01 - Introduction to Azure Virtual Networks
---

# CM01: Unidad 6 Configuración de las opciones de DNS en Azure

## Escenario del ejercicio

En esta unidad, configurará la resolución de nombres DNS para Contoso Ltd. Creará una zona DNS privada llamada contoso.com, vinculará las redes virtuales para el registro y la resolución y, luego, creará dos máquinas virtuales y probará la configuración.

![Diagrama de arquitectura de ISS.](../media/6-exercise-configure-domain-name-servers-configuration-azure.png)

En este ejercicio, aprenderá a:

+ Tarea 1: Creación de una zona DNS privada
+ Tarea 2: Vinculación de la subred para el registro automático
+ Tarea 3: Creación de máquinas virtuales para probar la configuración
+ Tarea 4: Comprobación de que existen registros en la zona DNS

   >**Nota:** Hay disponible una **[simulación de laboratorio interactiva](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Configure%20DNS%20settings%20in%20Azure)** que le permite realizar sus propias selecciones a su entera discreción. Es posible que encuentre pequeñas diferencias entre la simulación interactiva y el laboratorio hospedado, pero las ideas y los conceptos básicos que se muestran son los mismos.

### Tiempo estimado: 25 minutos

## Tarea 1: Creación de una zona DNS privada

1. Vaya a [Azure Portal](https://portal.azure.com/).

1. En la página principal de Azure, en la barra de búsqueda, escriba dns y, luego, seleccione **Zonas DNS privadas**.  
   ![Página principal de Azure Portal con búsqueda de DNS.](../media/create-private-dns-zone.png)

1. En Zonas DNS privadas, seleccione **+ Crear**.

1. Use la información de la tabla siguiente para crear la zona DNS privada.

    | **Tab**         | **Opción**                             | **Valor**            |
    | --------------- | -------------------------------------- | -------------------- |
    | Aspectos básicos          | Resource group                         | ContosoResourceGroup |
    |                 | Nombre                                   | Contoso.com          |
    | Etiquetas            | No se necesitan cambios                    |                      |
    | Revisar y crear | Revise la configuración y seleccione Crear. |                      |

1. Espere hasta que se complete la implementación y, luego, seleccione **Ir al recurso**.

1. Compruebe que se ha creado la nueva zona.

## Tarea 2: Vinculación de la subred para el registro automático

1. En Contoso.com, en **Administración de DNS**, selecciona **Vínculos de red virtual**.

1. En Contoso.com \| Vínculos de red virtual, seleccione **+ Agregar**.

    ![contoso.com \| Vínculos virtuales con + Agregar resaltado.](../media/add-network-link-dns.png)

1. Use la información de la tabla siguiente para agregar el vínculo de la red virtual.

    | **Opción**                          | **Valor**                               |
    | ----------------------------------- | --------------------------------------- |
    | Nombre del vínculo                           | CoreServicesVnetLink                    |
    | Suscripción                        | No se necesitan cambios                     |
    | Virtual Network                     | CoreServicesVnet (ContosoResourceGroup) |
    | Habilitación del registro automático            | Seleccionada                                |
    | Revise la configuración y seleccione Aceptar. |                                         |

1. Seleccione **Actualizar**.

1. Compruebe que se ha creado CoreServicesVnetLink y que se ha habilitado el registro automático.

1. Repita los pasos del 2 al 5 con ManufacturingVnet, con la información de la siguiente tabla:

    | **Opción**                          | **Valor**                                |
    | ----------------------------------- | ---------------------------------------- |
    | Nombre del vínculo                           | ManufacturingVnetLink                    |
    | Suscripción                        | No se necesitan cambios                      |
    | Virtual Network                     | ManufacturingVnet (ContosoResourceGroup) |
    | Habilitación del registro automático            | Seleccionada                                 |
    | Revise la configuración y seleccione Aceptar. |                                          |

1. Seleccione **Actualizar**.

1. Compruebe que se ha creado ManufacturingVnetLink y que se ha habilitado el registro automático.

1. Repita los pasos del 2 al 5 con ResearchVnet, con la información de la tabla siguiente:

    | **Opción**                          | **Valor**                           |
    | ----------------------------------- | ----------------------------------- |
    | Nombre del vínculo                           | ResearchVnetLink                    |
    | Suscripción                        | No se necesitan cambios                 |
    | Virtual Network                     | ResearchVnet (ContosoResourceGroup) |
    | Habilitación del registro automático            | Seleccionada                            |
    | Revise la configuración y seleccione Aceptar. |                                     |

1. Seleccione **Actualizar**.

1. Compruebe que se ha creado ResearchVnetLink y que se ha habilitado el registro automático.

## Tarea 3: Creación de máquinas virtuales para probar la configuración

En esta sección, creará dos máquinas virtuales de prueba para probar la configuración de la zona DNS privada.

1. En Azure Portal, selecciona el icono Cloud Shell (parte superior derecha). Si es necesario, configura el shell.  
    + Selecciona **PowerShell**.
    + Selecciona **No se requiere cuenta de almacenamiento** y tu **Suscripción**, después, selecciona **Aplicar**.
    + Espera a que se cree el terminal y se muestre una solicitud. 

1. En la barra de herramientas del panel de Cloud Shell, selecciona el icono **Administrar archivos**, en el menú desplegable, selecciona **Cargar** y carga los siguientes archivos **azuredeploy.json** y **azuredeploy.parameters.json** en el directorio principal de Cloud Shell uno a uno desde la carpeta de origen **F:\Allfiles\Exercises\M01**.

1. Implementa las plantillas de ARM siguientes a fin de crear las máquinas virtuales necesarias para este ejercicio:

   >**Nota**: se te pedirá que proporciones una contraseña de administrador.

   ```powershell
   $RGName = "ContosoResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
   ```
  
1. Cuando la implementación esté completa, vaya a la página principal de Azure Portal y, luego, seleccione **Máquinas virtuales**.

1. Compruebe que se han creado ambas máquinas virtuales.

## Tarea 4: Comprobación de que existen registros en la zona DNS

1. En la página principal de Azure Portal, selecciona **Zonas DNS privadas**.

1. En Zonas DNS privadas, selecciona **contoso.com**.

1. Compruebe que los registros de host (D) aparecen en ambas máquinas virtuales, como se muestra a continuación:

    ![Zona DNS Contoso.com que muestra los registros D del host registrado automáticamente.](../media/contoso_com-dns-zone.png)

1. Anote los nombres y las direcciones IP de las máquinas virtuales.

### Conexión a las máquinas virtuales de prueba con RDP

1. En la página principal de Azure Portal, selecciona **Máquinas virtuales**.

1. Seleccione **TestVM1**.

1. En TestVM, selecciona **Conectar&gt; RDP** y descarga el archivo RDP.

    ![TestVM1 con Conectar y RDP resaltados.](../media/connect-to-am.png)

1. Guarde el archivo RDP en el escritorio.

1. Sigue los mismos pasos para **TestVM2.**

1. Conéctate a TestVM1 mediante el archivo RDP y el nombre de usuario **TestUser** y la contraseña que proporcionaste durante la implementación.

1. Conéctate a TestVM2 mediante el archivo RDP y el nombre de usuario **TestUser** y la contraseña que proporcionaste durante la implementación.

1. En las dos máquinas virtuales, en **Elegir la configuración de privacidad para el dispositivo**, seleccione **Aceptar**.

1. En las dos máquinas virtuales, en **Redes**, selecciona **Sí**.

1. En TestVM1, abre un símbolo del sistema y escribe el comando `ipconfig /all`.

1. Compruebe que la dirección IP es la misma que la que anotó en la zona DNS.

1. Escriba el comando ping TestVM2.contoso.com.

1. Compruebe que el FQDN se resuelve en la dirección IP que ha anotado en la zona de DNS privada. El propio ping agotará el tiempo de espera porque el firewall de Windows está habilitado en las máquinas virtuales.

1. Como alternativa, puede escribir el comando nslookup TestVM2.contoso.com y comprobar que recibe un registro de resolución de nombres correcto para VM2

## Ampliar el aprendizaje con Copilot

Copilot puede ayudarle a aprender a usar las herramientas de scripting de Azure. Copilot también puede ayudar en áreas no cubiertas en el laboratorio o donde necesita más información. Abra un explorador Edge y elija Copilot (superior derecha) o vaya a *copilot.microsoft.com*. Dedique unos minutos a probar estas indicaciones.
+ ¿Cuál es la diferencia entre Azure DNS y DNS privado de Azure? Proporciona ejemplos de cuándo usar DNS privado de Azure.
+ ¿Cuál es el propósito del registro automático al crear una zona de Azure DNS?

## Más información con el aprendizaje autodirigido

+ [Introducción a Azure DNS](https://learn.microsoft.com/training/modules/intro-to-azure-dns/). En este módulo se explica Azure DNS, cómo funciona y cuándo debes elegir usar Azure DNS como solución para satisfacer las necesidades de tu organización.
+ [Hospeda el dominio en Azure DNS](https://learn.microsoft.com/training/modules/host-domain-azure-dns/). En este módulo, crearás una zona DNS y registros DNS para asignar el dominio a una dirección IP. Además, comprueba para tener la certeza de que el nombre de dominio se resuelve en tu servidor web.

## Puntos clave

Enhorabuena por completar el laboratorio. Estas son las principales conclusiones del laboratorio. 

+ Azure DNS es un servicio en la nube que permite hospedar y administrar dominios de sistema de nombres de dominio (DNS), también conocidos como zonas DNS. 
+ Los datos de las zonas de nombres de dominio de host de zona pública de Azure DNS para los registros que pretende que resuelva cualquier host de Internet.
+ Las zonas de DNS privado de Azure te permiten configurar un espacio de nombres de zona DNS privada para recursos privados de Azure.
+ Una zona DNS es una colección de registros DNS. Los registros DNS proporcionan información sobre el dominio.
