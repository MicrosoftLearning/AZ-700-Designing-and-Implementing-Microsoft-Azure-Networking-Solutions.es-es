---
demo:
  module: Module 01 - Introduction to Azure Virtual Networks
  title: Resolución de nombres DNS (Módulo 01)
---
## Configuración de Azure DNS

En esta demostración, se explorará Azure DNS.

**Referencia**: [Tutorial: Hospedaje del dominio y los subdominios en Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)

**Creación de una zona DNS**

1. Acceda a Azure Portal.

1. Busque el servicio  **Zonas DNS**.

1. Cree una **Zona DNS** y explique el propósito de la zona. Para el nombre, puede usar contoso.internal.com.

1.  Espere a que se cree la zona DNS. Es posible que tenga que  **Actualizar** la página.

**Adición de un conjunto de registros de DNS**

**Referencia**: [Tutorial: Creación de un registro de alias para hacer referencia a un registro de recursos de zona](https://learn.microsoft.com/azure/dns/tutorial-alias-rr)

1. Una vez creada la zona, seleccione  **+Conjunto de registros**.

1. Use la lista desplegable  **Tipo** para ver los distintos tipos de registros. Revise cómo se usan los distintos tipos de registros. Observe cómo cambia la información del registro a medida que selecciona los diferentes tipos de registros.

1. Cree un registro **A** como ejemplo. 

