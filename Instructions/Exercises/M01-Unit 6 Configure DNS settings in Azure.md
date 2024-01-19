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

**Nota:** Hay disponible una **[simulación de laboratorio interactiva](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Configure%20DNS%20settings%20in%20Azure)** que le permite realizar sus propias selecciones a su entera discreción. Es posible que encuentre pequeñas diferencias entre la simulación interactiva y el laboratorio hospedado, pero las ideas y los conceptos básicos que se muestran son los mismos.

#### Tiempo estimado: 25 minutos

## Tarea 1: Creación de una zona DNS privada

1. Vaya a [Azure Portal](https://portal.azure.com/).

2. En la página principal de Azure, en la barra de búsqueda, escriba dns y, luego, seleccione **Zonas DNS privadas**.  
   ![Página principal de Azure Portal con la búsqueda de dns.](../media/create-private-dns-zone.png)

3. En Zonas DNS privadas, seleccione **+ Crear**.

4. Use la información de la tabla siguiente para crear la zona DNS privada.

| **Tab**         | **Opción**                             | **Valor**            |
| --------------- | -------------------------------------- | -------------------- |
| Aspectos básicos          | Resource group                         | ContosoResourceGroup |
|                 | Nombre                                   | Contoso.com          |
| Etiquetas            | No se necesitan cambios                    |                      |
| Revisar y crear | Revise la configuración y seleccione Crear. |                      |


5. Espere hasta que se complete la implementación y, luego, seleccione **Ir al recurso**.

6. Compruebe que se ha creado la nueva zona.

## Tarea 2: Vinculación de la subred para el registro automático

1. En Contoso.com, en **Configuración**, seleccione **Vínculos de red virtual**.

2. En Contoso.com | Vínculos de red virtual, seleccione **+ Agregar**.

![contoso.com | Vínculos virtuales con + Agregar resaltado.](../media/add-network-link-dns.png)

3. Use la información de la tabla siguiente para agregar el vínculo de la red virtual.

| **Opción**                          | **Valor**                               |
| ----------------------------------- | --------------------------------------- |
| Nombre del vínculo                           | CoreServicesVnetLink                    |
| Suscripción                        | No se necesitan cambios                     |
| Virtual Network                     | CoreServicesVnet (ContosoResourceGroup) |
| Habilitación del registro automático            | Seleccionada                                |
| Revise la configuración y seleccione Aceptar. |                                         |


4. Seleccione **Actualizar**.

5. Compruebe que se ha creado CoreServicesVnetLink y que se ha habilitado el registro automático.

6. Repita los pasos del 2 al 5 con ManufacturingVnet, con la información de la siguiente tabla: 

| **Opción**                          | **Valor**                                |
| ----------------------------------- | ---------------------------------------- |
| Nombre del vínculo                           | ManufacturingVnetLink                    |
| Suscripción                        | No se necesitan cambios                      |
| Virtual Network                     | ManufacturingVnet (ContosoResourceGroup) |
| Habilitación del registro automático            | Seleccionada                                 |
| Revise la configuración y seleccione Aceptar. |                                          |


7. Seleccione **Actualizar**.

8. Compruebe que se ha creado ManufacturingVnetLink y que se ha habilitado el registro automático.

9. Repita los pasos del 2 al 5 con ResearchVnet, con la información de la tabla siguiente: 

| **Opción**                          | **Valor**                           |
| ----------------------------------- | ----------------------------------- |
| Nombre del vínculo                           | ResearchVnetLink                    |
| Suscripción                        | No se necesitan cambios                 |
| Virtual Network                     | ResearchVnet (ContosoResourceGroup) |
| Habilitación del registro automático            | Seleccionada                            |
| Revise la configuración y seleccione Aceptar. |                                     |


10. Seleccione **Actualizar**.

11. Compruebe que se ha creado ResearchVnetLink y que se ha habilitado el registro automático.

 

##  Tarea 3: Creación de máquinas virtuales para probar la configuración

En esta sección, creará dos máquinas virtuales de prueba para probar la configuración de la zona DNS privada.

1. En Azure Portal, abre la sesión de **PowerShell** en el panel **Cloud Shell**.
    
    > **Nota:** si es la primera vez que has abierto Cloud Shell, puede que se te pida crear una cuenta de almacenamiento. Seleccione **Crear almacenamiento**.

2. En la barra de herramientas del panel de Cloud Shell, selecciona el icono **Cargar/Descargar archivos**; en el menú desplegable, selecciona **Cargar** y carga los siguientes archivos **azuredeploy.json** y **azuredeploy.parameters.json**en el directorio principal de Cloud Shell desde la carpeta de origen **F:\Allfiles\Exercises\M01**.

3. Implemente las plantillas de ARM siguientes a fin de crear las máquinas virtuales necesarias para este ejercicio:

    >**Nota**: Se le pedirá que proporcione una contraseña de administrador.

   ```powershell
   $RGName = "ContosoResourceGroup"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
   ```
  
4. Cuando la implementación esté completa, vaya a la página principal de Azure Portal y, luego, seleccione **Máquinas virtuales**.

5. Compruebe que se han creado ambas máquinas virtuales.

 

## Tarea 4: Comprobación de que existen registros en la zona DNS

1. En la página principal de Azure Portal, selecciona **Zonas DNS privadas**.

2. En Zonas DNS privadas, selecciona **contoso.com**.

3. Compruebe que los registros de host (D) aparecen en ambas máquinas virtuales, como se muestra a continuación:

![Zona DNS Contoso.com que muestra los registros D del host registrado automáticamente.](../media/contoso_com-dns-zone.png)

 

4. Anote los nombres y las direcciones IP de las máquinas virtuales.

 

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

1. En TestVM1, abra un símbolo del sistema y escriba el comando ipconfig /all.

1. Compruebe que la dirección IP es la misma que la que anotó en la zona DNS.

1. Escriba el comando ping TestVM2.contoso.com.

1. Compruebe que el FQDN se resuelve en la dirección IP que ha anotado en la zona de DNS privada. El propio ping agotará el tiempo de espera porque el firewall de Windows está habilitado en las máquinas virtuales.

1. Como alternativa, puede escribir el comando nslookup TestVM2.contoso.com y comprobar que recibe un registro de resolución de nombres correcto para VM2
 

Felicidades. Ha creado una zona DNS privada, ha agregado un vínculo de resolución de nombres y registro automático y ha probado la resolución de nombres en la configuración. 
