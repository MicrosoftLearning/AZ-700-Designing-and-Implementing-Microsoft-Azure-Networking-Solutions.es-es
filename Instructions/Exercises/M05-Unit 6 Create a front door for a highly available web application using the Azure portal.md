---
Exercise:
  title: 'M05: Unidad 6 Creación de un Front Door para una aplicación web global de alta disponibilidad mediante Azure Portal'
  module: Module 05 - Load balancing HTTP(S) traffic in Azure
---



# M05: Unidad 6 Creación de un Front Door para una aplicación web global de alta disponibilidad mediante Azure Portal

## Escenario del ejercicio  

En este ejercicio, configurará una opción de Azure Front Door que agrupa dos instancias de una aplicación web que se ejecuta en diferentes regiones de Azure. Esta configuración dirige el tráfico al sitio más cercano que ejecuta la aplicación. Azure Front Door supervisa continuamente la aplicación web. Demostrará la conmutación automática por error al siguiente sitio disponible si el sitio más cercano no lo está. La configuración de red se muestra en el siguiente diagrama:

![Configuración de red para Azure Front Door.](../media/6-exercise-create-front-door-for-highly-available.png)

En este ejercicio, aprenderá a:

+ Tarea 1: Creación de dos instancias de una aplicación web
+ Tarea 2: Creación de una instancia de Front Door para una aplicación
+ Tarea 3: Visualización de Front Door en acción
+ Tarea 4: Limpieza de recursos

**Nota:** Hay disponible una **[simulación de laboratorio interactiva](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Create%20a%20Front%20Door%20profile%20for%20a%20highly%20available%20web%20application)** que le permite realizar sus propias selecciones a su entera discreción. Es posible que encuentre pequeñas diferencias entre la simulación interactiva y el laboratorio hospedado, pero las ideas y los conceptos básicos que se muestran son los mismos.

### Tiempo estimado: 30 minutos

## Tarea 1: Creación de dos instancias de una aplicación web

Este ejercicio requiere dos instancias de una aplicación web que se ejecuten en regiones diferentes de Azure. Ambas instancias de la aplicación web se ejecutan en modo activo/activo, por lo que cualquiera de ellas puede asumir el tráfico. Esta configuración difiere de una configuración del modo activo/en espera, en la que una realiza la conmutación por error.

1. Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com/).

1. En la página principal de Azure, con la búsqueda global, escribe **WebApp** y selecciona **App Services** en servicios.

1. Selecciona **+ Crear** para crear una aplicación web.

1. En la página Crear aplicación web, en la pestaña **Aspectos básicos**, escriba o seleccione la siguiente información.

   | **Configuración**      | **Valor**                                                    |
   | ---------------- | ------------------------------------------------------------ |
   | Suscripción     | Seleccione su suscripción.                                    |
   | Resource group   | Selecciona el grupo de recursos ContosoResourceGroup               |
   | Nombre             | Escriba un Nombre único para la aplicación web. En este ejemplo se usa WebAppContoso-1. |
   | Publicar          | Seleccione **Código**.                                             |
   | Pila en tiempo de ejecución    | Seleccione **.NET 6 (LTS)**.                                     |
   | Sistema operativo | Seleccione **Windows**.                                          |
   | Region           | Seleccione **Centro de EE. UU.**                                       |
   | Plan de Windows     | Seleccione **Crear nuevo** y escriba myAppServicePlanCentralUS en el cuadro de texto. |
   | Plan de precios    | Seleccione **Estándar S1 100 ACU total, 1,75 GB de memoria**.        |

1. Seleccione **Revisar y crear**, revise el resumen y, después, seleccione **Crear**.
   ‎La implementación puede tardar varios minutos en completarse.

1. Cree una segunda aplicación web. En la página principal de Azure Portal, busca **WebApp**.

1. Selecciona **+ Crear** para crear una aplicación web.

1. En la página Crear aplicación web, en la pestaña **Aspectos básicos**, escriba o seleccione la siguiente información.

   | **Configuración**      | **Valor**                                                    |
   | ---------------- | ------------------------------------------------------------ |
   | Suscripción     | Seleccione su suscripción.                                    |
   | Resource group   | Selecciona el grupo de recursos ContosoResourceGroup               |
   | Nombre             | Escriba un Nombre único para la aplicación web. En este ejemplo se usa WebAppContoso-2. |
   | Publicar          | Seleccione **Código**.                                             |
   | Pila en tiempo de ejecución    | Seleccione **.NET 6 (LTS)**.                                     |
   | Sistema operativo | Seleccione **Windows**.                                          |
   | Region           | Seleccione **Este de EE. UU**.                                          |
   | Plan de Windows     | Seleccione **Crear** y escriba myAppServicePlanEastUS en el cuadro de texto. |
   | Plan de precios     | Seleccione **Estándar S1 100 ACU total, 1,75 GB de memoria**.        |

1. Seleccione **Revisar y crear**, revise el resumen y, después, seleccione **Crear**.
   ‎La implementación puede tardar varios minutos en completarse.

## Tarea 2: Creación de una instancia de Front Door para una aplicación

Configure Azure Front Door para dirigir el tráfico del usuario en función de la latencia más baja entre los dos servidores de las aplicaciones web. Para empezar, agregue un host de front-end para Azure Front Door.

1. En cualquier página de Azure Portal, en **Buscar recursos, servicios y documentos (G+/),**, busca perfiles de Front Door y CDN y luego selecciona **Perfiles de Front Door y CDN**.

1. Selecciona **Perfiles de Front Door y CDN**. En la página Comparación de ofertas selecciona **Creación rápida**. Luego, seleccione **Continue to create a Front Door** (Continuar para crear instancia de Front Door).

1. En la pestaña Aspectos básicos, escriba o seleccione la siguiente información:

   | **Configuración**             | **Valor**                                    |
   | ----------------------- | -------------------------------------------- |
   | Suscripción            | Seleccione su suscripción.                    |
   | Resource group          | Seleccione ContosoResourceGroup.                  |
   | Ubicación del grupo de recursos | Acepta la configuración predeterminada                       |
   | Nombre                    | Escribe un nombre único en esta suscripción, como FrontDoor(yourinitials)   |
   | Nivel                    | Estándar   |
   | Nombre de extremo           | FDendpoint   |
   | Tipo de origen             | App Service|
   | Nombre de host de origen        | Nombre de la aplicación web que has implementado anteriormente |

1. Seleccione **Revisar y Crear** y, luego seleccione **Crear**.

1. Espera a que se implementen los recursos y después selecciona **Ir al recurso**.
1. En el recurso de Front Door de la hoja Información general, busca los **Grupos de origen**, selecciona el grupo de origen creado.
1. Para actualizar el grupo de origen, selecciona el nombre **default-origin-group** de la lista. Selecciona **Agregar un origen** y agrega la segunda aplicación web. Selecciona Agregar y luego Actualizar.

## Tarea 3: Visualización de Front Door en acción

Una vez que cree una instancia de Front Door, la configuración tardará unos minutos en implementarse globalmente. Cuando lo haya hecho, acceda al host de front-end que ha creado.

1. En el recurso de Front Door de la hoja Información general, busca el nombre de host del punto de conexión que se ha creado para el punto de conexión. Debe ser fdendpoint seguido de un guion y una cadena aleatoria. Por ejemplo, **fdendpoint-fxa8c8hddhhgcrb9.z01.azurefd.net**. **Copia** este FQDN.

1. En una nueva pestaña del explorador, ve al FQDN del punto de conexión de Front Door. Se mostrará la página predeterminada de App Service.
   ![Explorador que muestra la página de información de App Service](../media/app-service-info-page.png)

1. Para probar la conmutación por error global instantánea, realice estos pasos:

1. Cambie a Azure Portal, busque y seleccione **App Services**.

1. Seleccione una de las aplicaciones web, seleccione **Detener** y, después, seleccione **Sí** para comprobarlo.

   ![Azure Portal con la aplicación web detenida](../media/stop-web-app.png)

1. Vuelva al explorador y seleccione Actualizar. Debería aparecer la misma página de información.

**Puede haber un retraso mientras se detiene la aplicación web. Si recibe una página de error en el explorador, actualice la página**.

1. Vuelve a Azure Portal, busca la otra aplicación web y detenla.

1. Vuelva al explorador y seleccione Actualizar. Esta vez debería aparecer un mensaje de error.

   ![Explorador que muestra la página de error de App Service](../media/web-apps-both-stopped.png)

   ¡Enhorabuena! Ha configurado y probado una instancia de Azure Front Door.

## Tarea 4: Limpieza de recursos

   >**Nota**: No olvide quitar los recursos de Azure recién creados que ya no use. La eliminación de los recursos sin usar garantiza que no verá cargos inesperados.

1. En Azure Portal, abre la sesión de **PowerShell** en el panel **Cloud Shell**.

1. Ejecute el comando siguiente para eliminar todos los grupos de recursos que ha creado en los laboratorios de este módulo:

   ```powershell

   Remove-AzResourceGroup -Name 'ContosoResourceGroup' -Force -AsJob

   ```

    >**Nota**: El comando se ejecuta de forma asincrónica (según determina el parámetro -AsJob). Aunque podrá ejecutar otro comando de PowerShell inmediatamente después en la misma sesión de PowerShell, los grupos de recursos tardarán unos minutos en eliminarse.
