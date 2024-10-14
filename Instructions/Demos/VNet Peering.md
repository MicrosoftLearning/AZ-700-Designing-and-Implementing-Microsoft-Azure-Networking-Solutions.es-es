---
demo:
  title: 'Emparejamiento de VNET            '
  module: Module 01 - Introduction to Azure Virtual Networks
---
## Configuración del emparejamiento de red virtual

**Nota:**  Para esta demostración necesitará dos redes virtuales.

**Referencia**: [Tutorial sobre conexión de redes virtuales con emparejamiento de VNet](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal)

**Configuración del emparejamiento de VNet en la primera red virtual**

1. En  **Azure Portal**, seleccione la primera red virtual. Revise el valor del emparejamiento. 

1. En  **Configuración**, seleccione  **Emparejamientos** y **+ Agregar** un nuevo emparejamiento.

1. Configure el emparejamiento de la segunda red virtual. Use los iconos de información para revisar la configuración diferente. 

1. Una vez completado el emparejamiento, revise el **Estado de emparejamiento**. 

**Confirmación del emparejamiento de VNet en la segunda red virtual**

1. En  **Azure Portal**, seleccione la segunda red virtual.

1. En  **Configuración**, seleccione  **Emparejamientos**.

1. Observe que se ha creado un emparejamiento de forma automática. Observe que el  **Estado de emparejamiento** es  **Conectado**.

1. En el laboratorio, los alumnos crearán el emparejamiento y probarán la conexión entre las máquinas virtuales. 
