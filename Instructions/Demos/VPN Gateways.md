---
demo:
  title: Puertas de enlace de VPN (Módulo 02)
  module: Module 02 - Design and implement hybrid networking
---

En esta demostración, se explorarán las puertas de enlace de red virtual.

**Nota:** Esta demostración funciona mejor con dos redes virtuales con subredes.

## Exploración del panel Subred de puerta de enlace
1. Para una de las redes virtuales, selecciona la hoja  **Subredes** .
1. Selecciona la subred  **+ Puerta de enlace**. Observe que no se puede cambiar el nombre de la subred. Observa el  **intervalo de direcciones** de la subred de puerta de enlace. La dirección se debe incluir en el espacio de direcciones de la red virtual.
1. Recuerde que cada red virtual necesita una subred de puerta de enlace.
1. Cierre la página Agregar subred de puerta de enlace. No es necesario guardar los cambios.

## Exploración del panel Dispositivos conectados
1. Para la red virtual, selecciona la hoja  **Dispositivos conectados** .
1. Una vez que se implemente una subred de puerta de enlace, aparecerá en la lista de dispositivos conectados.

## Exploración de la adición de una puerta de enlace de red virtual
1. Busca  **Puertas de enlace de red virtual**.
1. Haz clic en  **+ Agregar**.
1. Revise cada valor de la puerta de enlace de red virtual.
1. Use los iconos de información para obtener más información sobre los valores.
1. Observa el  **Tipo de puerta de enlace**,  **Tipo de VPN** y  **SKU**.
1. Verás que se necesita una  **dirección IP pública**.
1. Recuerde que cada red virtual necesitará una puerta de enlace de red virtual.
1. Cierre Agregar puerta de enlace de red virtual. No es necesario guardar los cambios.
   
## Exploración de la adición de una conexión entre las redes virtuales
1. Busca  **Conexiones**.
1. Haz clic en  **+ Agregar**.
1. Observa que el  **Tipo de conexión** puede ser de red virtual a red virtual, de sitio a sitio (IPsec) o ExpressRoute.
1. Proporciona información suficiente para que puedas hacer clic en el botón  **Aceptar** .
1. En la página  **Configuración** , observa que tendrás que seleccionar las dos redes virtuales diferentes.
1. Lee la información de Ayuda en la casilla  **Establecer conectividad bidireccional** .
1. Observa la información de  **Clave compartida (PSK)** .
1. Cierre la página Agregar conexión. No es necesario guardar los cambios.
