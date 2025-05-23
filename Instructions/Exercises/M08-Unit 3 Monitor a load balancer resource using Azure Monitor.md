---
Exercise:
  title: 'M08: Unidad 3 Supervisión de un recurso de equilibrador de carga mediante Azure Monitor'
  module: Module 08 - Design and implement network monitoring
---

# M08: Unidad 3 Supervisión de un recurso de equilibrador de carga mediante Azure Monitor

## Escenario del ejercicio

En este ejercicio, creará un equilibrador de carga interno para la organización ficticia Contoso Ltd. Después, creará un área de trabajo de Log Analytics y usará conclusiones de Azure Monitor para ver información sobre el equilibrador de carga interno. Verá la vista de dependencia funcional, verá métricas detalladas para el recurso del equilibrador de carga y verá la información de estado de los recursos para el equilibrador de carga. Por último, establecerá la configuración de diagnóstico del equilibrador de carga para enviar métricas al área de trabajo de Log Analytics que ha creado.

En el diagrama siguiente se muestra el entorno que se va a implementar en este ejercicio.

![Diagrama en el que se ilustra la arquitectura del equilibrador de carga que se creará en el ejercicio: incluye el equilibrador de carga, la red virtual, subred, la subred de Bastion y las máquinas virtuales](../media/3-exercise-monitor-load-balancer-resource-using-azure-monitor.png)

 En este ejercicio, aprenderá a:

+ Tarea 1: Creación de la red virtual
+ Tarea 2: Creación del equilibrador de carga
+ Tarea 3: Creación de un grupo de back-end
+ Tarea 4: Creación de un sondeo de estado
+ Tarea 5: Creación de una regla de equilibrador de carga
+ Tarea 6: Creación de servidores de back-end
+ Tarea 7: Incorporación de máquinas virtuales al grupo de back-end
+ Tarea 8: Prueba del equilibrador de carga
+ Tarea 9: Creación de un área de trabajo de Log Analytics
+ Tarea 110: Uso de la vista de dependencias funcionales
+ Tarea 11: Visualización de métricas detalladas
+ Tarea 12: Visualización del estado de los recursos
+ Tarea 13: Configuración de las opciones de diagnóstico


   >**Nota:** hay disponible una **[simulación de laboratorio interactiva](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Monitor%20a%20load%20balancer%20resource%20using%20Azure%20Monitor)** que te permite realizar tus propias selecciones a tu entera discreción. Es posible que encuentre pequeñas diferencias entre la simulación interactiva y el laboratorio hospedado, pero las ideas y los conceptos básicos que se muestran son los mismos.

> [!Note]  
> Puedes encontrar pequeñas diferencias entre las instrucciones y la interfaz de Azure Portal, pero el concepto principal es el mismo.

### Tiempo estimado: 55 minutos

## Tarea 1: Creación de la red virtual

En esta sección, creará una red virtual y una subred.

1. Inicie sesión en el Portal de Azure.

1. En la página principal de Azure Portal, ve a la barra Búsqueda global, busca **Redes virtuales** y selecciona Redes virtuales en servicios.

1. Seleccione **+ Create** (+ Crear).

   ![Creación de una red virtual](../media/create-virtual-network.png)

1. En la pestaña **Datos básicos**, use la información de la tabla siguiente para crear la red virtual.

   | **Configuración**    | **Valor**                                           |
   | -------------- | --------------------------------------------------- |
   | Suscripción   | Selecciona la suscripción                            |
   | Resource group | Seleccione **Crear nuevo**.<br /><br />Nombre: **IntLB-RG** |
   | NOMBRE           | **IntLB-VNet**                                      |
   | Region         | **(EE. UU.) Oeste de EE. UU.**                                    |

1. Seleccione **Siguiente: Direcciones IP**.

1. En la pestaña **Direcciones IP**, en el cuadro **Espacio de direcciones IPv4**, escribe **10.1.0.0/16**.

1. Encima de **Nombre de subred**, selecciona **Agregar subred**.

1. En el panel **Añadir subred**, proporciona el nombre de subred **myBackendSubnet** y el intervalo de direcciones de subred **10.1.0.0/24**.

1. Seleccione **Agregar**.

1. Seleccione **Siguiente: Seguridad**.

1. En **BastionHost**, seleccione **Habilitar** y escriba la información de la tabla siguiente.

    | **Configuración**                       | **Valor**                                              |
    | --------------------------------- | ------------------------------------------------------ |
    | Nombre del bastión                      | **myBastionHost**                                      |
    | Espacio de direcciones de AzureBastionSubnet  | **10.1.1.0/24**                                        |
    | Dirección IP pública                 | Seleccione **Crear nuevo**.<br /><br />Nombre: **myBastionIP** |

1. Seleccione **Revisar + crear**.

1. Seleccione **Crear**.

## Tarea 2: Creación del equilibrador de carga

En esta sección, creará un equilibrador de carga de SKU estándar interno. La razón por la que en este ejercicio se va a crear un equilibrador de carga de SKU Estándar, en lugar de uno de SKU Básica, es que en ejercicios posteriores se necesitará una versión de SKU Estándar del equilibrador de carga.

1. En la página principal de Azure, en el cuadro de búsqueda, escriba **Load Balancer**.

1. Seleccione **Crear equilibrador de carga**.

1. En la pestaña **Datos básicos**, use la información de la tabla siguiente para crear el equilibrador de carga.

   | **Configuración**           | **Valor**                |
   | --------------------- | ------------------------ |
   | Pestaña Datos básicos            |                          |
   | Suscripción          | Selecciona la suscripción |
   | Resource group        | **IntLB-RG**             |
   | Nombre                  | **myIntLoadBalancer**    |
   | Region                | **(EE. UU.) Oeste de EE. UU.**         |
   | SKU                   | **Estándar**             |
   | Tipo                  | **Interno**             |
   | Pestaña Configuración de IP de front-end | + Agregar una configuración de IP de front-end |
   | Nombre                  | **LoadBalancerFrontEnd** |
   | Virtual network       | **IntLB-VNet**           |
   | Subnet                | **myBackendSubnet**      |
   | Asignación de dirección IP | **Dinámica**              |

1. Seleccione **Revisar + crear**.

1. Seleccione **Crear**.

## Tarea 3: Creación de un grupo de back-end

Un grupo de direcciones de back-end contiene las direcciones IP de las tarjetas de interfaz de red virtuales conectadas al equilibrador de carga.

1. En la página principal de Azure Portal, selecciona **Todos los recursos** y después selecciona **myIntLoadBalancer** en la lista de recursos.

1. En **Configuración**, seleccione **Grupos de back-end** y después **Agregar**.

1. En la página **Agregar grupo de back-end,** escriba la información de la tabla siguiente.

   | **Configuración**     | **Valor**            |
   | --------------- | -------------------- |
   | Nombre            | **myBackendPool**    |
   | Virtual network | **IntLB-VNet**       |
   | Configuración del grupo de back-end   | **NIC** |

1. Seleccione **Agregar**.

   ![Grupo de back-end creado en el equilibrador de carga](../media/create-backendpool.png)

## Tarea 4: Creación de un sondeo de estado

El equilibrador de carga supervisa el estado de la aplicación con un sondeo de estado. El sondeo de estado agrega o quita las máquinas virtuales del equilibrador de carga a partir de su respuesta a las comprobaciones de estado. Aquí creará un sondeo de estado para supervisar el estado de las máquinas virtuales.

1. En la página **Grupos back-end** del equilibrador de carga, en **Configuración**, selecciona **Sondeos de estado** y después selecciona **Agregar**.

1. En la página **Agregar sondeo de estado**, escriba la información de la tabla siguiente.

   | **Configuración**         | **Valor**         |
   | ------------------- | ----------------- |
   | Nombre                | **myHealthProbe** |
   | Protocolo            | **HTTP**          |
   | Port                | **80**            |
   | Ruta de acceso                | **/**             |
   | Intervalo            | **15**            |

1. Seleccione **Agregar**.

   ![Sondeo de estado creado en el equilibrador de carga](../media/create-healthprobe.png)

## Tarea 5: Creación de una regla de equilibrador de carga

Las reglas de equilibrador de carga se utilizan para definir cómo se distribuye el tráfico a las máquinas virtuales. Defina la configuración IP del front-end para el tráfico entrante y el grupo de direcciones IP de back-end para recibir el tráfico. Los puertos de origen y de destino se definen en la regla. Aquí creará una regla de equilibrador de carga.

1. En la página **Grupos back-end** del equilibrador de carga, en **Configuración**, selecciona **Reglas de equilibrio de carga** y después selecciona **Agregar**.

1. En la página **Agregar regla de equilibrio de carga**, escriba la información de la tabla siguiente.

   | **Configuración**            | **Valor**                |
   | ---------------------- | ------------------------ |
   | Nombre                   | **myHTTPRule**           |
   | Versión de la dirección IP             | **IPv4**                 |
   | Dirección IP del front-end    | **LoadBalancerFrontEnd** |
   | Protocolo               | **TCP**                  |
   | Port                   | **80**                   |
   | Puerto back-end           | **80**                   |
   | Grupo back-end           | **myBackendPool**        |
   | Sondeo de mantenimiento           | **myHealthProbe**        |
   | Persistencia de la sesión    | **None**                 |
   | Tiempo de espera de inactividad (minutos) | **15**                   |
   | Dirección IP flotante            | **Deshabilitado**             |

1. Seleccione **Agregar**.

   ![Regla de equilibrio de carga creada en el equilibrador de carga](../media/create-loadbalancerrule.png)

## Tarea 6: Creación de servidores de back-end

En esta sección, creará tres máquinas virtuales para el grupo de back-end del equilibrador de carga, las agregará al grupo de back-end y, después, instalará IIS en las tres máquinas virtuales para probar el equilibrador de carga.

1. En Azure Portal, selecciona el icono Cloud Shell (parte superior derecha). Si es necesario, configura el shell.  
    + Selecciona **PowerShell**.
    + Selecciona **No se requiere cuenta de almacenamiento** y tu **Suscripción**, después, selecciona **Aplicar**.
    + Espera a que se cree el terminal y se muestre una solicitud. 

1. En la barra de herramientas del panel de Cloud Shell, selecciona el icono **Administrar archivos**, en el menú desplegable, selecciona **Cargar** y carga los siguientes archivos **azuredeploy.json** y **azuredeploy.parameters.json** en el directorio particular de Cloud Shell.

    > **Nota:** Si estás trabajando en tu propia suscripción los [archivos de plantilla](https://github.com/MicrosoftLearning/AZ-700-Designing-and-Implementing-Microsoft-Azure-Networking-Solutions/tree/master/Allfiles/Exercises) están disponibles en el repositorio de laboratorio de GitHub.

1. Implementa las siguientes plantillas de ARM para crear la red virtual, las subredes y las máquinas virtuales necesarias para este ejercicio:

   >**Nota**: Se le pedirá que proporcione una contraseña de administrador.

   ```powershell
   $RGName = "IntLB-RG"

   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
   ```
  
    > **Nota:** Esto tardará varios minutos en implementarse.

## Tarea 7: Adición de máquinas virtuales al grupo de back-end

1. En la página principal de Azure Portal, selecciona **Todos los recursos** y después selecciona **myIntLoadBalancer** en la lista de recursos.

1. En **Configuración**, seleccione **Grupos de back-end** y después **myBackendPool**.

1. En el cuadro **Asociado a**, selecciona **Máquinas virtuales**.

1. En **Máquinas virtuales**, selecciona **Agregar**.

1. Activa las casillas de las tres máquinas virtuales (**myVM1**, **myVM2** y **myVM3**) y después selecciona **Agregar**.

1. En la página ** myBackendPool** selecciona **Guardar**.

   ![Máquinas virtuales agregadas al grupo de back-end en el equilibrador de carga](../media/add-vms-backendpool.png)

## Tarea 8: Prueba del equilibrador de carga

En esta sección, creará una máquina virtual de prueba y, después, probará el equilibrador de carga.

### Creación de la máquina virtual de prueba

   >**Nota**: puedes encontrar pequeñas diferencias entre las instrucciones y la interfaz de Azure Portal, pero el concepto principal es el mismo.

1. En la página principal de Azure, en la búsqueda global, escribe **Máquinas virtuales** y selecciona máquinas virtuales en servicios.

1. Selecciona **Crear una máquina virtual**, en la pestaña **Datos básicos**, usa la información de la tabla siguiente para crear la primera máquina virtual.

   | **Configuración**          | **Valor**                                    |
   | -------------------- | -------------------------------------------- |
   | Suscripción         | Selecciona la suscripción                     |
   | Resource group       | **IntLB-RG**                                 |
   | Nombre de la máquina virtual | **myTestVM**                                 |
   | Region               | **(EE. UU.) Oeste de EE. UU.**                             |
   | Opciones de disponibilidad | **No se requiere redundancia de la infraestructura**    |
   | Tipo de seguridad        | **Estándar**                                 |
   | Imagen                | **Ver todas las imágenes** --> **Datacenter para Windows Server 2019**  |
   | Tamaño                 | **Standard_DS2_v3: 2 vcpu, 8 GiB de memoria** |
   | Nombre de usuario             | **TestUser**                                 |
   | Contraseña             | **Proporcione una contraseña segura**                |
   | Confirmar contraseña     | **Proporcione una contraseña segura**                |

1. Selecciona **Siguiente: Discos** y después **Siguiente: Redes**.

1. En la pestaña **Redes**, use la información de la tabla siguiente para configurar las opciones de red.

   | **Configuración**                                                  | **Valor**                     |
   | ------------------------------------------------------------ | ----------------------------- |
   | Virtual network                                              | **IntLB-VNet**                |
   | Subnet                                                       | **myBackendSubnet**           |
   | Dirección IP pública                                                    | Cambiar a **Ninguno**            |
   | Grupo de seguridad de red de NIC                                   | **Avanzado**                  |
   | Configuración del grupo de seguridad de red                             | Seleccione el grupo **myNSG** existente. |
   | Equilibrio de carga                                               | **Ninguno** (o desactivado)       |

1. Seleccione **Revisar + crear**.

1. Seleccione **Crear**.

1. Espere a que se implemente esta última máquina virtual antes de avanzar con la tarea siguiente.

### Conexión a la máquina virtual de prueba para probar el equilibrador de carga

1. En la página principal de Azure Portal, selecciona **Todos los recursos** y después selecciona **myIntLoadBalancer** en la lista de recursos.

1. En la página **Información general**, anote la **Dirección IP privada** o cópiela en el Portapapeles. Nota: Es posible que tengas que seleccionar **Ver más** para ver la **Dirección IP privada**.

1. Selecciona **Inicio** y después en la página principal de Azure Portal, selecciona **Todos los recursos** y luego selecciona en la máquina virtual **myTestVM** que acabas de crear.

1. En la página **Introducción**, seleccione **Conectar** y después **Instancia de Bastion**.

1. Seleccione **Usar Bastion**.

1. En el cuadro **Nombre de usuario**, escribe **TestUser** y en el cuadro **Contraseña**, escribe la contraseña que has proporcionado durante la implementación y luego selecciona **Conectar**.

1. La ventana **myTestVM** se abrirá en otra pestaña del explorador.

1. Si aparece el panel **Redes**, selecciona **Sí**.

1. Selecciona el icono **Internet Explorer** de la barra de tareas para abrir el explorador web.

1. Selecciona **Aceptar** en el cuadro de diálogo **Configurar Internet Explorer 11**.

1. Escriba (o pegue) la **Dirección IP privada** (por ejemplo, 10.1.0.4) del paso anterior en la barra de direcciones del explorador y presione Entrar.

1. La página principal web predeterminada del servidor web IIS se muestra en la ventana del explorador. Responderá una de las tres máquinas virtuales del grupo de back-end.
    ![Ventana del explorador en la que se muestra la respuesta "Hola mundo" de VM1](../media/load-balancer-web-test-1.png)

1. Si seleccionas varias veces el botón Actualizar del explorador, verás que la respuesta procede aleatoriamente de las distintas máquinas virtuales del grupo back-end del equilibrador de carga interno.

    ![Ventana del explorador en la que se muestra la respuesta "Hola mundo" de VM3](../media/load-balancer-web-test-2.png)

## Tarea 9: Creación de un área de trabajo de Log Analytics

1. En la página principal de Azure Portal principal, selecciona **Todos los servicios** y luego en el cuadro de búsqueda de la parte superior de la página, escribe **Log Analytics** y selecciona **Áreas de trabajo de Log Analytics** en la lista filtrada.

   ![Acceso a Áreas de trabajo de Log Analytics desde la página principal de Azure Portal](../media/log-analytics-workspace-1.png)

1. Seleccione **Crear**.

1. En la página **Crear área de trabajo de Log Analytics**, en la pestaña **Datos básicos**, use la información de la tabla siguiente para crear el área de trabajo.

   | **Configuración**    | **Valor**                |
   | -------------- | ------------------------ |
   | Suscripción   | Selecciona la suscripción |
   | Resource group | **IntLB-RG**             |
   | NOMBRE           | **myLAworkspace**        |
   | Region         | **Oeste de EE. UU.**              |

1. Seleccione **Revisar y crear** y, a continuación, seleccione **Crear**.

   ![Lista de áreas de trabajo de Log Analytics](../media/log-analytics-workspace-2.png)

## Tarea 110: Uso de la vista de dependencias funcionales

1. En la página principal de Azure Portal, selecciona **Todos los recursos** y luego selecciona **myIntLoadBalancer** en la lista de recursos.

   ![Lista Todos los recursos de Azure Portal](../media/network-insights-functional-dependency-view-1.png)

1. En **Supervisión**, seleccione **Conclusiones**.

1. En la esquina superior derecha de la página, selecciona la **X** para cerrar el panel **Métricas** por ahora. Lo volverá a abrir en breve.

1. Esta vista de página se conoce como Vista de dependencia funcional y ofrece un diagrama interactivo útil, en el que se muestra la topología del recurso de red seleccionado, en este caso un equilibrador de carga. En el caso de Standard Load Balancer, los recursos del grupo de back-end están codificados por colores con el estado de sondeo de estado que indica la disponibilidad actual del grupo de back-end para atender el tráfico.

1. Use los botones **Acercar (+)** y **Alejar (-)** de la esquina inferior derecha de la página para acercar y alejar el diagrama de topología (también puede usar la rueda del mouse si tiene una). También puede arrastrar el diagrama de topología por la página para moverlo.

1. Mantenga el puntero sobre el componente **LoadBalancerFrontEnd** en el diagrama y después sobre el componente **myBackendPool**.

1. Observe que puede usar los vínculos de estas ventanas emergentes para ver información sobre estos componentes del equilibrador de carga y abrir sus respectivos paneles de Azure Portal.

1. Para descargar una copia del archivo .SVG del diagrama de topología, selecciona **Descargar topología** y guarda el archivo en la carpeta **Descargas**.

1. En la esquina superior derecha, selecciona **Ver métricas** para volver a abrir el panel de métricas en el lado derecho de la pantalla.
    ![Vista de dependencia funcional de las conclusiones de red de Azure Monitor: botón Ver métricas resaltado](../media/network-insights-functional-dependency-view-3.png)

1. En el panel Métricas se proporciona una vista rápida de algunas métricas clave para este recurso de equilibrador de carga, en forma de gráficos de barras y líneas.

    ![Conclusiones de red de Azure Monitor: vista de métricas básicas](../media/network-insights-basicmetrics-view.png)

## Tarea 11: Visualización de métricas detalladas

1. Para ver métricas más completas sobre este recurso de red, selecciona **Ver métricas detalladas**.
   ![Conclusiones de red de Azure Monitor: botón Ver métricas detalladas resaltado](../media/network-insights-detailedmetrics-1.png)

1. Se abrirá una página **Métricas** completa de gran tamaño en la plataforma de conclusiones de red de Azure. La primera pestaña es **Información general**, en la que se muestra el estado de disponibilidad del equilibrador de carga y el rendimiento general de los datos y la disponibilidad de front-end y back-end para cada una de las direcciones IP de front-end asociadas al equilibrador de carga. Estas métricas indican si la dirección IP de front-end tiene capacidad de respuesta y las instancias de proceso del grupo de back-end están respondiendo individualmente a las conexiones entrantes.
   ![Conclusiones de red de Azure Monitor - Vista Métricas detalladas: pestaña Información general](../media/network-insights-detailedmetrics-2.png)

1. Selecciona la pestaña **Disponibilidad de front-end&amp; y back-end** y desplázate hacia abajo por la página para ver los gráficos de estado del sondeo de estado. Si ve **valores inferiores a 100** para estos elementos, significa que hay una interrupción de algún tipo en esos recursos.
   ![Conclusiones de red de Azure Monitor: vista de métricas detalladas: gráficos de estado de sondeo de estado resaltados](../media/network-insights-detailedmetrics-5.png)

1. Selecciona la pestaña **Rendimiento de datos** y desplázate hacia abajo en la página para ver los otros gráficos de rendimiento de datos.

1. Mantenga el puntero sobre algunos de los puntos de datos de los gráficos y verá que los valores cambian para mostrar el valor exacto en ese momento dado.
   ![Conclusiones de red de Azure Monitor: vista Métricas detalladas: pestaña de rendimiento de datos](../media/network-insights-detailedmetrics-3.png)

1. Selecciona la pestaña **Distribución del flujo** y desplázate hacia abajo en la página para ver los gráficos de la sección **Creación de flujos y tráfico de red de máquina virtual**.

   ![Conclusiones de red de Azure Monitor: vista de métricas detalladas: gráficos de creación de máquinas virtuales y tráfico de red](../media/network-insights-detailedmetrics-4.png)

## Tarea 12: Visualización del estado de los recursos

1. Para ver el estado de los recursos de equilibrador de carga, en la página principal de Azure Portal, selecciona **Todos los servicios** y después selecciona **Monitor**.

1. En la página **Monitor&gt;Información general**, en el menú de la izquierda, selecciona **Estado del servicio**.

1. En la página **Estado del servicio&gt;Problemas de servicio**, en el menú de la izquierda selecciona **Estado de los recursos**.

1. En la página **Service Health&gt;Estado de los recursos**, desplácese hacia abajo en la lista desplegable **Tipo de recurso** y seleccione **Equilibrador de carga**.

   ![Acceso a Service Health>Estado de los recursos para el recurso de equilibrador de carga](../media/resource-health-1.png)

1. Después, seleccione el nombre del equilibrador de carga en la lista.

1. En la página **Estado de los recursos** se identificarán los principales problemas de disponibilidad con el recurso de equilibrador de carga. Si hay algún evento en la sección **Historial de estado**, puede expandirlo para ver más detalles. Incluso puede guardar los detalles sobre el evento como un archivo PDF para su posterior revisión y para crear informes.

   ![Service Health>vista Estado de los recursos](../media/resource-health-2.png)

## Tarea 13: Configuración de las opciones de diagnóstico

1. En la página principal de Azure Portal, selecciona **Grupos de recursos** y después selecciona el grupo de recursos **IntLB-RG** en la lista.

1. En la página **IntLB-RG**, selecciona en el nombre del recurso de equilibrador de carga **myIntLoadBalancer** en la lista de recursos.

1. En **Supervisión**, selecciona **Configuración de diagnóstico** y luego selecciona **Agregar configuración de diagnóstico**:

   ![Configuración de diagnóstico>botón Agregar configuración de diagnóstico resaltado](../media/diagnostic-settings-1.png)

1. En la página **Configuración de diagnóstico**, en el cuadro de nombre, escribe **myLBDiagnostics**.

1. Active la casilla **AllMetrics** y después la casilla **Send to Log Analytics workspace** (Enviar al área de trabajo de Log Analytics).

1. Seleccione la suscripción en la lista y después **myLAworkspace (westus)** en la lista desplegable de áreas de trabajo.

1. Seleccione **Guardar**.

   ![Página de configuración de diagnóstico para el equilibrador de carga](../media/diagnostic-settings-2.png)

## Limpieza de recursos

>**Nota**: No olvide quitar los recursos de Azure recién creados que ya no use. La eliminación de los recursos sin usar garantiza que no verás cargos inesperados.

1. En Azure Portal, abre la sesión de **PowerShell** en el panel **Cloud Shell**.

1. Ejecute el comando siguiente para eliminar todos los grupos de recursos que ha creado en los laboratorios de este módulo:

   ```powershell
   Remove-AzResourceGroup -Name 'IntLB-RG' -Force -AsJob
   ```

>**Nota**: el comando se ejecuta de forma asincrónica (según determina el parámetro -AsJob). Aunque podrás ejecutar otro comando de PowerShell inmediatamente después en la misma sesión de PowerShell, los grupos de recursos tardarán unos minutos en eliminarse.
    
## Ampliar el aprendizaje con Copilot

Copilot puede ayudarte a aprender a usar las herramientas de scripting de Azure. Copilot también puede ayudar en áreas no cubiertas en el laboratorio o donde necesitas más información. Abre un explorador Edge y elige Copilot (superior derecha) o ve a *copilot.microsoft.com*. Dedica unos minutos a probar estas indicaciones.
+ Resume las herramientas de Azure que están disponibles para la supervisión de redes virtuales.
+ ¿Qué herramientas de supervisión de Azure Network Watcher están disponibles?

## Obtén más información con el aprendizaje autodirigido

+ [Introducción a Azure Monitor](https://learn.microsoft.com/training/modules/intro-to-azure-monitor/). En este módulos, aprenderás a usar Azure Monitor para proporcionar información sobre el rendimiento y las operaciones de los recursos de Azure.
+ [Supervisión y solución de problemas de la infraestructura de red de Azure de un extremo a otro mediante herramientas de supervisión de red](https://learn.microsoft.com/training/modules/troubleshoot-azure-network-infrastructure/). En este módulo, aprederás a utilizar herramientas, diagnósticos y registros de Azure Network Watcher para encontrar y corregir incidencias de red en la infraestructura de Azure.

## Puntos clave

Enhorabuena por completar el laboratorio. Estas son las principales conclusiones del laboratorio. 

+ Azure Monitor proporciona características y herramientas para recopilar, administrar y analizar datos de TI de todos los recursos de Azure, otras nubes y locales.
+ Las métricas son medidas cuantitativas que muestran instantáneas del rendimiento de las aplicaciones o recursos. Las métricas suelen ser valores numéricos que se pueden medir con el tiempo.
+ Los registros son registros textuales de eventos, acciones y mensajes que se producen en un recurso o aplicación. 
+ La información, las visualizaciones y los paneles de Azure Monitor pueden consumir y transmitir información de supervisión sobre tus aplicaciones.
+ Las alertas le avisan de condiciones críticas y pueden tomar medidas correctivas. Las reglas de alerta se pueden basar en datos de métricas o de registro.+ 
    
