---
demo:
  title: Load Balancer
  module: 'Module 04 - Load balancing non-HTTPS traffic '
---
## Configuración de Azure Load Balancer

En esta demostración aprenderá a crear un equilibrador de carga público. 

**Referencia**: [Inicio rápido: Uso de Azure Portal para crear un equilibrador de carga público para equilibrar la carga de máquinas virtuales](https://learn.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal).

**Nota:**  Para esta demostración se necesita una red virtual con al menos una subred. 

**Mostrar la característica “Ayudarme a elegir” del portal**

1. Acceda a Azure Portal.

1. Busque y seleccione **Equilibrio de carga: ayuda para elegir**.

1. Use el asistente para recorrer diferentes escenarios.
   
**Creación de un equilibrador de carga**

1. Continúe en Azure Portal.

1. Busque y seleccione **Equilibrador de carga**. **Cree** un equilibrador de carga. 

1. En la pestaña **Datos básicos**, analice los valores de **SKU**, **Tipo** y **Nivel**.

1. En la pestaña **Configuración de IP de front-end**, analice el uso de una dirección IP pública.

1. En la pestaña **Grupos de back-end**, seleccione la red virtual con un intervalo de direcciones IP.

1. En la pestaña **Reglas de entrada**, cree una regla de equilibrio de carga. Analice parámetros como **Protocolo**, **Puertos**, **Sondeos de estado** y **Persistencia de la sesión**. 


