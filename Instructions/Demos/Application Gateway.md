---
demo:
  module: Module 05 - Load balancing HTTPS traffic
  title: Application Gateway (Módulo 05)
---
## Configuración de Azure Application Gateway

En esta demostración aprenderá a crear una instancia de Azure Application Gateway. 

**Simulación:**[Implementación de una instancia de Azure Application Gateway](https://mslabs.cloudguides.com/guides/AZ-700%20Lab%20Simulation%20-%20Deploy%20Azure%20Application%20Gateway)

**Referencia**: [Inicio rápido: Dirección del tráfico web con Azure Application Gateway: Azure Portal](https://learn.microsoft.com/azure/application-gateway/quick-create-portal)

**Referencia**: [Cifrado del tráfico de red de un extremo a otro con Azure Application Gateway](https://github.com/MicrosoftDocs/mslearn-end-to-end-encryption-with-app-gateway)

**Nota:** Para hacerlo más sencillo, cree nuevas redes virtuales y subredes a medida que defina la configuración. 

**Crear la instancia de Azure Application Gateway**

1. Acceda a Azure Portal.

1. Busque y seleccione **Azure Application Gateway**.

1. **Cree** una puerta de enlace.

1. En la pestaña **Datos básicos**, analice los parámetros **Niveles**, **Escalado automático** y **Recuento de instancias**.

1. En la pestaña **Front-end**, analice los tipos de dirección IP.

1. En la pestaña **Back-end**, analice cuándo debe usarse un grupo de back-end vacío.

1. En la pestaña **Configuración**, analice las reglas de enrutamiento. Compárelas con las reglas del equilibrador de carga.

1. Explique que, después de crear la puerta de enlace, agregaría destinos de back-end y haría pruebas. 
