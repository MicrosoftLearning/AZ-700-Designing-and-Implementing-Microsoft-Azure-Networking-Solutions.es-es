---
Exercise:
  title: 'M04: Unidad 4 Creación y configuración de un equilibrador de carga de Azure'
  module: Module 04 - Load balancing non-HTTP(S) traffic in Azure
---


# M04: Unidad 4 Creación y configuración de un equilibrador de carga de Azure.

En este ejercicio, creará un equilibrador de carga interno para la organización ficticia Contoso Ltd.

### Tiempo estimado: 60 minutos (incluye aproximadamente 45 minutos de tiempo de espera de implementación)

Los pasos para crear un equilibrador de carga interno son muy similares a los que ya ha aprendido en este módulo para crear un equilibrador de carga público. La principal diferencia es que con un equilibrador de carga público se accede al front-end a través de una dirección IP pública y se prueba la conectividad desde un host que se encuentra fuera de la red virtual; mientras que, con un equilibrador de carga interno, el front-end es una dirección IP privada dentro de la red virtual y se prueba la conectividad desde un host dentro de la misma red.

![diagrama de equilibrador de carga estándar interno](../media/4-exercise-create-configure-azure-load-balancer.png)

### Aptitudes de trabajo

En este ejercicio, aprenderás a:

+ Tarea 1: Creación de la red virtual
+ Tarea 2: Creación de servidores backend
+ Tarea 3: Creación del equilibrador de carga
+ Tarea 4: Creación de recursos del equilibrador de carga
+ Tarea 5: Prueba del equilibrador de carga

## Tarea 1: Creación de la red virtual

En esta sección, creará una red virtual y una subred.

1. Inicie sesión en el Portal de Azure.

2. En la página principal de Azure Portal, vaya a la barra Búsqueda global, busque **Redes virtuales** y seleccione Redes virtuales en servicios.  ![Resultados de la barra Búsqueda global de la página principal de Azure Portal para red virtual.](../media/global-search-bar.PNG)

3. En la página Redes virtuales, seleccione **Crear**.  ![Asistente para la creación de una red virtual.](../media/create-virtual-network.png)

4. En la pestaña **Datos básicos**, use la información de la tabla siguiente para crear la red virtual.

   | **Configuración**    | **Valor**                                  |
   | -------------- | ------------------------------------------ |
   | Suscripción   | Selecciona la suscripción                   |
   | Resource group | Seleccione **Crear nuevo** Nombre: **IntLB-RG** |
   | Nombre           | **IntLB-VNet**                             |
   | Region         | **(EE. UU.) Este de EE. UU.**                           |

5. Selecciona **Siguiente** (te llevará a la pestaña Seguridad).

6. En **Azure Bastion**, selecciona **Habilitar Azure Bastion** y escribe la información de la tabla siguiente.

    | **Configuración**                   | **Valor**                                                    |
    | ----------------------------- | ------------------------------------------------------------ |
    | Nombre de host                     | **myBastionHost**                                            |
    | Dirección IP pública             | Selecciona **Crear una dirección IP pública** Nombre: **myBastionIP** |

7. Selecciona **Siguiente** (te lleva a la pestaña Direcciones IP).

8. En la pestaña **Direcciones IP**, en el cuadro **Espacio de direcciones IPv4**, reemplace el espacio de direcciones IPv4 rellenado previamente por: **10.1.0.0/16**.

9. En la pestaña **Direcciones IP**, en **Subredes**, elimine la subred **predeterminada**.

10. En la pestaña **Direcciones IP**, selecciona **+ Agregar una subred**.

11. En el panel **Agregar subred**, proporcione un nombre de subred de **myBackendSubnet**y una dirección inicial de: **10.1.0.0/24**. Seleccione **Agregar**.

12. Seleccione **+ Agregar una subred** de nuevo, proporcione un nombre de subred de **myFrontEndSubnet** y una dirección inicial de: **10.1.2.0/24**. Seleccione **Agregar**.

13. Compruebe que **AzureBastionSubnet** existe, agréguelo si fuera necesario.

14. Selecciona **Revisar + crear.**

15. Seleccione **Crear**.

## Tarea 2: Creación de servidores backend

En esta sección, creará tres máquinas virtuales, que estarán en el mismo conjunto de disponibilidad, para el grupo de back-end del equilibrador de carga, las agregará al grupo de back-end y, después, instalará IIS en las tres máquinas virtuales para probar el equilibrador de carga.

1. En Azure Portal, selecciona el icono Cloud Shell (parte superior derecha). Si es necesario, configura el shell.  
    + Selecciona **PowerShell**.
    + Selecciona **No se requiere cuenta de almacenamiento** y tu **Suscripción**, después, selecciona **Aplicar**.
    + Espera a que se cree el terminal y se muestre una solicitud. 

2. En la barra de herramientas del panel de Cloud Shell, selecciona el icono **Cargar/Descargar archivos**, en el menú desplegable, selecciona **Cargar** y carga los siguientes archivos azuredeploy.json, y azuredeploy.parameters.json en el directorio de inicio de Cloud Shell.

    > **Nota:** Si estás trabajando en tu propia suscripción los [archivos de plantilla](https://github.com/MicrosoftLearning/AZ-700-Designing-and-Implementing-Microsoft-Azure-Networking-Solutions/tree/master/Allfiles/Exercises) están disponibles en el repositorio de laboratorio de GitHub.

4. Implementa las plantillas de ARM siguientes a fin de crear las máquinas virtuales necesarias para este ejercicio:

   >**Nota**: Se le pedirá que proporcione una contraseña de administrador.

   ```powershell
   $RGName = "IntLB-RG"
   
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
   ```

Puede tardar entre 5 y 10 minutos en crear estas tres máquinas virtuales. No tienes que esperar hasta que se complete este trabajo, ya puedes continuar con la siguiente tarea.

## Tarea 3: Creación del equilibrador de carga

En esta sección, creará un equilibrador de carga de SKU estándar interno. La razón por la que en este ejercicio se va a crear un equilibrador de carga de SKU Estándar, en lugar de uno de SKU Básica, es que en ejercicios posteriores se necesitará una versión de SKU Estándar del equilibrador de carga.

1. En la página principal de Azure Portal, seleccione **Crear un recurso**.

1. En el cuadro de búsqueda de la parte superior de la página, escribe **Equilibrador de carga** y luego presiona **Entrar** (**Nota:** No selecciones uno de la lista).

1. En la página de resultados, encuentre y seleccione **Load Balancer** (el que muestra "Microsoft" y "Azure Service" debajo del nombre).

1. Seleccione **Crear**.

1. En la pestaña **Datos básicos**, use la información de la tabla siguiente para crear el equilibrador de carga.

   | **Configuración**           | **Valor**                |
   | --------------------- | ------------------------ |
   | Suscripción          | Selecciona la suscripción |
   | Resource group        | **IntLB-RG**             |
   | Nombre                  | **myIntLoadBalancer**    |
   | Region                | **(EE. UU.) Este de EE. UU.**         |
   | SKU                   | **Estándar**             |
   | Tipo                  | **Interno**             |
   | Nivel                  | **Regional**             |

1. Selecciona **Siguiente: Configuraciones de direcciones IP de front-end**.
   
1. Selecciona Agregar una IP de front-end.

1. En el panel **Agregar dirección IP de front-end**, escriba la información de la tabla siguiente y seleccione **Guardar**.

   | **Configuración**     | **Valor**                |
   | --------------- | ------------------------ |
   | Nombre            | **LoadBalancerFrontEnd** |
   | Virtual network | **IntLB-VNet**           |
   | Subnet          | **myFrontEndSubnet**     |
   | Asignación      | **Dinámica**              |

1. Seleccione **Revisar + crear**.

1. Seleccione **Crear**.

## Tarea 4: Creación de recursos del equilibrador de carga

En esta sección, va a configurar las opciones del equilibrador de carga para un grupo de direcciones de back-end. Luego creará un sondeo de estado y una regla de un equilibrador de carga.

### Creación de un grupo de back-end y adición de máquinas virtuales a dicho grupo

Un grupo de direcciones de back-end contiene las direcciones IP de las tarjetas de interfaz de red virtuales conectadas al equilibrador de carga.

1. En la página principal de Azure Portal, selecciona **Todos los recursos** y después selecciona **myIntLoadBalancer** en la lista de recursos.

1. En **Configuración**, seleccione **Grupos de back-end** y después **Agregar**.

1. En la página **Agregar grupo de back-end,** escriba la información de la tabla siguiente.

   | **Configuración**     | **Valor**            |
   | --------------- | -------------------- |
   | Nombre            | **myBackendPool**    |
   | Virtual network | **IntLB-VNet**       |

1. En **Máquinas virtuales**, seleccione **Guardar**.

1. Activa las casillas de las tres máquinas virtuales (**myVM1**, **myVM2** y **myVM3**) y después selecciona **Agregar**.

1. Seleccione **Guardar**.
   ![Imagen 7](../media/add-vms-backendpool.png)

### Creación de un sondeo de estado

El equilibrador de carga supervisa el estado de la aplicación con un sondeo de estado. El sondeo de estado agrega o quita las máquinas virtuales del equilibrador de carga a partir de su respuesta a las comprobaciones de estado. Aquí creará un sondeo de estado para supervisar el estado de las máquinas virtuales.

1. En **Configuración**, seleccione **Sondeos de estado** y, a continuación, seleccione **Agregar**.

1. En la página **Agregar sondeo de estado**, escriba la información de la tabla siguiente.

   | **Configuración**         | **Valor**         |
   | ------------------- | ----------------- |
   | Nombre                | **myHealthProbe** |
   | Protocolo            | **HTTP**          |
   | Port                | **80**            |
   | Ruta de acceso                | **/**             |
   | Intervalo            | **15**            |

1. Seleccione **Agregar**.
   ![Imagen 5](../media/create-healthprobe.png)

### Creación de una regla de equilibrador de carga

Las reglas de equilibrador de carga se utilizan para definir cómo se distribuye el tráfico a las máquinas virtuales. Defina la configuración IP del front-end para el tráfico entrante y el grupo de direcciones IP de back-end para recibir el tráfico. Los puertos de origen y de destino se definen en la regla. Aquí creará una regla de equilibrador de carga.

1. En **Configuración**, seleccione **Reglas de equilibrio de carga** y, a continuación, seleccione **Agregar**.

1. En la página **Agregar regla de equilibrio de carga**, escriba la información de la tabla siguiente.

   | **Configuración**            | **Valor**                |
   | ---------------------- | ------------------------ |
   | Nombre                   | **myHTTPRule**           |
   | Versión de la dirección IP             | **IPv4**                 |
   | Dirección IP del front-end    | **LoadBalancerFrontEnd** |
   | Grupo back-end           | **myBackendPool**        |
   | Protocolo               | **TCP**                  |
   | Port                   | **80**                   |
   | Puerto back-end           | **80**                   |
   | Sondeo de mantenimiento           | **myHealthProbe**        |
   | Persistencia de la sesión    | **None**                 |
   | Tiempo de espera de inactividad (minutos) | **15**                   |
   | Dirección IP flotante            | **Deshabilitado**             |

1. Seleccione **Guardar**.
   ![Imagen 6](../media/create-loadbalancerrule.png)

## Tarea 5: Prueba del equilibrador de carga

En esta sección, creará una máquina virtual de prueba y, después, probará el equilibrador de carga.

### Creación de la máquina virtual de prueba

1. En la página principal de Azure Portal, selecciona **Crear un recurso**, luego **virtual** y después selecciona **Máquina virtual** (si este tipo de recurso no aparece en la página, usa el cuadro de búsqueda de la parte superior de la página para buscarlo y selecciónalo).

1. En la página **Crear una máquina virtual**, en la pestaña **Datos básicos**, use la información de la tabla siguiente para crear la primera máquina virtual.

   | **Configuración**          | **Valor**                                    |
   | -------------------- | -------------------------------------------- |
   | Suscripción         | Selecciona la suscripción                     |
   | Resource group       | **IntLB-RG**                                 |
   | Nombre de la máquina virtual | **myTestVM**                                 |
   | Region               | **(EE. UU.) Este de EE. UU.**                             |
   | Opciones de disponibilidad | **No se requiere redundancia de la infraestructura**    |
   | Imagen                | **Windows Server 2019 Datacenter - Gen 2**   |
   | Size                 | **Standard_DS2_v3: 2 vcpu, 8 GiB de memoria**   |
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
   | Opciones de equilibrio de carga                                       | **Ninguno**                      |

1. Seleccione **Revisar + crear**.

1. Seleccione **Crear**.

1. Espere a que se implemente esta última máquina virtual antes de avanzar con la tarea siguiente.

### Conexión a la máquina virtual de prueba para probar el equilibrador de carga

1. En la página principal de Azure Portal, selecciona **Todos los recursos** y después selecciona **myIntLoadBalancer** en la lista de recursos.

1. En la página **Información general**, anote la **Dirección IP privada** o cópiela en el Portapapeles. Nota: Es posible que tengas que seleccionar **Ver más** para ver el campo **Dirección IP privada**.

1. Selecciona **Inicio** y después en la página principal de Azure Portal, selecciona **Todos los recursos** y luego selecciona en la máquina virtual **myTestVM** que acabas de crear.

1. En la página **Introducción**, seleccione **Conectar** y después **Instancia de Bastion**.

1. Seleccione **Usar Bastion**.

1. En el cuadro **Nombre de usuario**, escribe **TestUser** y en el cuadro **Contraseña**, escribe la contraseña que has creado y luego selecciona **Conectar**. Si el bloqueador de elementos emergentes impide la nueva ventana, permite el bloqueador de elementos emergentes y **conéctate** de nuevo.

1. La ventana **myTestVM** se abrirá en otra pestaña del explorador.

1. Si aparece el panel **Redes**, selecciona **Sí**.

1. Selecciona el icono **Internet Explorer** de la barra de tareas para abrir el explorador web.

1. Selecciona **Aceptar** en el cuadro de diálogo **Configurar Internet Explorer 11**.

1. Escriba (o pegue) la **Dirección IP privada** (por ejemplo, 10.1.0.4) del paso anterior en la barra de direcciones del explorador y presione Entrar.

1. La página principal web predeterminada del servidor web IIS se muestra en la ventana del explorador. Responderá una de las tres máquinas virtuales del grupo de back-end.
    ![Imagen 8](../media/load-balancer-web-test-1.png)

1. Si seleccionas varias veces el botón Actualizar del explorador, verás que la respuesta procede aleatoriamente de las distintas máquinas virtuales del grupo back-end del equilibrador de carga interno.
    ![Imagen 9](../media/load-balancer-web-test-2.png)

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
+ ¿En qué se diferencian los equilibradores de carga públicos y privados de Azure? Proporciona escenarios de ejemplo para cada tipo.
+ Proporciona una tabla que compare las SKU básicas y estándar de Azure Load Balancer.
+ ¿Cómo decide Azure Load Balancer procesar las solicitudes entrantes?


## Más información con el aprendizaje autodirigido
+ [Introducción a Azure Load Balancer](https://learn.microsoft.com/training/modules/intro-to-azure-load-balancer/). En este módulo se explica qué hace Azure Load Balancer, cómo funciona y cuándo debe usarlo como solución para satisfacer las necesidades de su organización.
+ [Solución de problemas de conectividad de red entrante para Azure Load Balancer](https://learn.microsoft.com/en-us/training/modules/troubleshoot-inbound-connectivity-azure-load-balancer/). En este módulo, identificarás y solucionarás problemas comunes de conectividad entrante de Azure Load Balancer.

## Puntos clave

Enhorabuena por completar el laboratorio. Estas son las principales conclusiones del laboratorio. 
+ El equilibrio de carga se refiere a distribuir eficientemente el tráfico de red entrante entre un grupo de servidores o recursos backend.
+ Azure Load Balancer distribuye flujos de entrada del front-end del equilibrador de carga a instancias del grupo de back-end. Estos flujos se distribuyen según las reglas de equilibrio de carga configuradas y los sondeos de estado. Las instancias del grupo de back-end pueden ser máquinas virtuales (VM) de Azure o conjuntos de escalado de máquinas virtuales.
+ Azure ofrece equilibradores de carga públicos y privados. Los equilibradores de carga públicos son ideales para aplicaciones accesibles desde Internet, conexiones salientes y aplicaciones web. Los equilibradores de carga privados son mejores para aplicaciones internas, servicios back-end y escenarios híbridos.


