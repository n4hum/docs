---



copyright:

  years: 2015, 2016



---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}

# CLI de admin de {{site.data.keyword.Bluemix_notm}}
{: #bluemixadmincli}

Última actualización: 1 de septiembre de 2016
{: .last-updated}


Puede gestionar usuarios del entorno Local o Dedicado de
{{site.data.keyword.Bluemix_notm}} o {{site.data.keyword.Bluemix_notm}} mediante
la interfaz de línea de mandatos de Cloud Foundry con el plug-in CLI de administración de
{{site.data.keyword.Bluemix_notm}}. Por ejemplo, puede añadir usuarios de un registro
de LDAP. Si busca información sobre la gestión de su cuenta {{site.data.keyword.Bluemix_notm}} pública, consulte
[Administración](../../../admin/adminpublic.html#administer).

Antes de empezar, instale la interfaz de línea de mandatos cf. El plug-in CLI de administración de {{site.data.keyword.Bluemix_notm}} necesita
cf versión 6.11.2 o posterior. [Descargue la interfaz de línea de mandatos
de Cloud Foundry](https://github.com/cloudfoundry/cli/releases){: new_window}

**Restricción:** Cygwin no admite la interfaz de línea de mandatos de Cloud Foundry. Utilice esta interfaz en una ventana de línea de mandatos que no sea la ventana de Cygwin.

## Adición del plug-in CLI de administración de {{site.data.keyword.Bluemix_notm}}

Una vez instalada la interfaz de línea de mandatos cf, puede añadir el plug-in CLI de administración de {{site.data.keyword.Bluemix_notm}}.

**Nota**: si ha instalado previamente el plugin Admin de {{site.data.keyword.Bluemix_notm}}, es posible que
tenga que desinstalarlo, suprimir el repositorio y luego volver a instalarlo para obtener las actualizaciones más recientes.

Siga estos pasos para añadir el repositorio e instalar el plug-in:

<ol>
<li>Para añadir el repositorio de plugin de administración de {{site.data.keyword.Bluemix_notm}}, ejecute el mandato siguiente:<br/><br/> <code>
cf add-plugin-repo BluemixAdmin https://console.&lt;subdominio&gt;.bluemix.net/cli
</code><br/><br/>
<dl class="parml">
<dt class="pt dlterm">&lt;subdominio&gt;</dt>
<dd class="pd">Subdominio del URL correspondiente a la instancia de {{site.data.keyword.Bluemix_notm}}. Por ejemplo, <code>https://console.mycompany.bluemix.net/cli</code></dd>
</dl>
</li>
<li>Para instalar el plug-in CLI de administración de {{site.data.keyword.Bluemix_notm}}, ejecute el siguiente mandato:<br/><br/> <code>
cf install-plugin BluemixAdminCLI -r BluemixAdmin
</code>
</li>
</ol>

Si necesita desinstalar el plug-in, puede utilizar los mandatos siguientes y, a continuación, puede añadir el repositorio actualizado e instalar el plug-in más reciente:

* Desinstale el plug-in: `cf uninstall-plugin-repo BluemixAdminCLI`
* Elimine el repositorio de plug-ins: `cf remove-plugin-repo BluemixAdmin`


## Utilización del plug-in CLI de administración de {{site.data.keyword.Bluemix_notm}}

Puede utilizar el plug-in CLI de administración de {{site.data.keyword.Bluemix_notm}} para añadir o eliminar usuarios, asignar o desasignar usuarios de organizaciones y para realizar otras tareas de gestión. Los caracteres especiales, como espacios, signos de más (+) y ampersands (&) no se admiten al crear nombres de organización, nombres de espacio y grupos de seguridad de aplicación. Utilice mayúsculas y minúsculas combinadas o caracteres de subrayado para crear nombres exclusivos.

Para ver una lista de mandatos, ejecute el mandato siguiente:

```
cf plugins
```
{: codeblock}

Para obtener más ayuda sobre un mandato, utilice la opción `-help`.

### Conexión e inicio de sesión en {{site.data.keyword.Bluemix_notm}}

Para poder utilizar el plug-in CLI de administración para gestionar usuarios, debe conectar e iniciar una sesión si aún no lo ha hecho.

<ol>
<li>Para conectarse al punto final de API de {{site.data.keyword.Bluemix_notm}}, ejecute el siguiente mandato: <br/><br/>
<code>
cf ba api https://console.&lt;subdomain&gt;.bluemix.net
</code>
<dl class="parml">
<dt class="pt dlterm">&lt;subdominio&gt;</dt>
<dd class="pd">Subdominio del URL correspondiente a la instancia de {{site.data.keyword.Bluemix_notm}}.<br />
</dd>
</dl>
<p>Puede consultar la página Recursos e información de la Consola de administración para ver el URL correcto. El URL se muestra en la sección de información de la API, en el campo **URL de API**.</p>
</li>
<li>Inicie sesión en {{site.data.keyword.Bluemix_notm}} con el siguiente mandato:<br/><br/>
<code>
cf login
</code>
</li>
</ol>

### Adición de un usuario

Puede añadir un usuario al entorno de
{{site.data.keyword.Bluemix_notm}} desde el registro de usuarios para su entorno. Escriba este mandato:

```
cf ba add-user <nombre_usuario> <organización>
```
{: codeblock}

**Nota**: para añadir un usuario a una organización específica, debe ser el director de la organización o tener permisos de
**Administrador** (una alternativa disponible es **Superusuario**) o **Usuario** con acceso de
**Escritura**.

<dl class="parml">
<dt class="pt dlterm">&lt;nombre_usuario&gt;</dt>
<dd class="pd">El nombre del usuario en el registro de LDAP.</dd>
<dt class="pt dlterm">&lt;organización&gt;</dt>
<dd class="pd">El nombre o GUID de la organización {{site.data.keyword.Bluemix_notm}} a la que se va a añadir el usuario.</dd>
</dl>

**Sugerencia:** También puede usar **ba au** como alias para un nombre de mandato
de **ba add-user** más largo.

<!-- staging-only commands start. Live for interconnect -->

### Buscar un usuario

Puede buscar un usuario. Indique el siguiente mandato junto con los parámetros de filtro de búsqueda opcionales según convenga (nombre, permiso, organización y rol):

```
cf ba search-users -name=<user_name_value> -permission=<permission_value> -organization=<organization_value> -role=<role_value>
```
{: codeblock}

<dl class="parml">

<dt class="pt dlterm">&lt;user_name_value&gt;</dt>
<dd class="pd">El nombre del usuario en {{site.data.keyword.Bluemix_notm}}. </dd>
<dt class="pt dlterm">&lt;permission_value&gt;</dt>
<dd class="pd">El permiso asignado al usuario. Por ejemplo, superusuario, básico, catálogo, usuario e informes. Para obtener más información sobre los permisos de usuario asignados, consulte [Permisos](../../../admin/index.html#permissions). No se puede utilizar este parámetro con el parámetro de organización en la misma consulta.</dd>
<dt class="pt dlterm">&lt;organization_value&gt;</dt>
<dd class="pd">El nombre de la organización a la que pertenece el usuario. No se puede utilizar este parámetro con el parámetro de organización en la misma consulta.</dd>
<dt class="pt dlterm">&lt;role_value&gt;</dt>
<dd class="pd">El rol de organización asignado al usuario. Por ejemplo, gestor, gestor de facturación o auditor de la organización.Especifique la organización con este parámetro. Para obtener más información acerca de los roles, consulte [Roles de usuario](../../../admin/users_roles.html#userrolesinfo).</dd>

</dl>

**Sugerencia:** También puede usar **ba su** como alias para un nombre de mandato
de **ba search-users** más largo.

### Establecer permisos para un usuario

Puede establecer permisos para un usuario especificado. Escriba este mandato:

```
cf ba set-permissions <nombre_usuario> <permisos> <acceso>
```
{: codeblock}

**Nota**: puede establecer los permisos de uno en uno.

<dl class="parml">
<dt class="pt dlterm">&lt;nombre_usuario&gt;</dt>
<dd class="pd">El nombre del usuario en {{site.data.keyword.Bluemix_notm}}.</dd>
<dt class="pt dlterm">&lt;permiso&gt;</dt>
<dd class="pd">Establecer los permisos para el usuario: Admin (una alternativa válida es Superusuario), Iniciar sesión (una alternativa válida es Básico), Catálogo (acceso de lectura o escritura),
Informes (acceso de lectura o escritura) o Usuarios (acceso de lectura o escritura).</dd>
<dt class="pt dlterm">&lt;acceso&gt;</dt>
<dd class="pd">Para permisos del Catálogo, Informes o Usuarios, también debe tener el nivel de acceso <code>read</code> o <code>write</code> (lectura o escritura).</dd>
</dl>

**Sugerencia:** También puede usar **ba sp** como alias para el nombre de mandato
**ba set-permissions** más largo.

<!-- staging-only commands end -->

### Eliminación de un usuario

Puede eliminar un usuario del entorno de
{{site.data.keyword.Bluemix_notm}} mediante
el siguiente mandato:

```
cf ba remove-user <nombre_usuario>
```
{: codeblock}

<dl class="parml">

<dt class="pt dlterm">&lt;nombre_usuario&gt;</dt>
<dd class="pd">El nombre del usuario en {{site.data.keyword.Bluemix_notm}}.</dd>

</dl>

**Sugerencia:** También puede usar **ba ru** como alias para un nombre de mandato
de **ba remove-user** más largo.

### Adición y supresión de una organización

Puede añadir y suprimir una organización.

* Para añadir una organización, ejecute el siguiente mandato:

```
cf ba create-organization <organización> <gestor>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;organización&gt;</dt>
<dd class="pd">El nombre o GUID de la organización de {{site.data.keyword.Bluemix_notm}} que va a añadir.</dd>
<dt class="pt dlterm">&lt;gestor&gt;</dt>
<dd class="pd">El nombre de usuario del gestor de la organización.</dd>
</dl>

**Sugerencia:** También puede usar **ba co** como alias para el nombre de mandato
**ba create-organization** más largo.

* Para suprimir una organización, ejecute el siguiente mandato:

```
cf ba delete-organization <organización>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;organización&gt;</dt>
<dd class="pd">El nombre o GUID de la organización de {{site.data.keyword.Bluemix_notm}} que va a suprimir.</dd>
</dl>

**Sugerencia:** También puede usar **ba do** como alias para un nombre de mandato
de **ba delete-organization** más largo.

### Asignación de un usuario a una organización

Puede asignar un usuario del entorno
{{site.data.keyword.Bluemix_notm}} a una
determinada organización. Escriba este mandato:

```
cf ba set-org <nombre_usuario> <organización> [<rol>]
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;nombre_usuario&gt;</dt>
<dd class="pd">El nombre del usuario en {{site.data.keyword.Bluemix_notm}}.</dd>
<dt class="pt dlterm">&lt;organización&gt;</dt>
<dd class="pd">El nombre o GUID de la organización {{site.data.keyword.Bluemix_notm}} a la que se va a asignar el usuario.</dd>
<dt class="pt dlterm">&lt;rol&gt;</dt>
<dd class="pd">Consulte [Roles](../../../admin/users_roles.html) para ver los roles de usuario de {{site.data.keyword.Bluemix_notm}} y sus descripciones.</dd>
</dl>

**Sugerencia:** También puede usar **ba so** como alias para el nombre de mandato
**ba set-org** más largo.

### Eliminación de la asignación de un usuario de una organización

Puede eliminar la asignación de un usuario del entorno
{{site.data.keyword.Bluemix_notm}} de una
determinada organización. Escriba este mandato:

```
cf ba unset-org <nombre_usuario> <organización> [<rol>]
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;nombre_usuario&gt;</dt>
<dd class="pd">El nombre del usuario en {{site.data.keyword.Bluemix_notm}}.</dd>
<dt class="pt dlterm">&lt;organización&gt;</dt>
<dd class="pd">El nombre o GUID de la organización {{site.data.keyword.Bluemix_notm}} a la que se va a asignar el usuario.</dd>
<dt class="pt dlterm">&lt;rol&gt;</dt>
<dd class="pd">Consulte [Roles](../../../admin/users_roles.html) para ver los roles de usuario de {{site.data.keyword.Bluemix_notm}} y sus descripciones.</dd>
</dl>

**Sugerencia:** También puede usar **ba uo** como alias para un nombre de mandato
de **ba unset-org** más largo.

### Roles

<dl class="parml">
<dt class="pt dlterm">OrgManager</dt>
<dd class="pd">Gestor de la organización. Un gestor de la organización tiene autorización para realizar las siguientes acciones:
<ul>
<li>Crea o suprime espacios en la organización.</li>
<li>Invita a usuarios a la organización y gestiona usuarios.</li>
<li>Gestiona dominios de la organización.</li>
</ul>
</dd>
<dt class="pt dlterm">BillingManager</dt>
<dd class="pd">Gestor de facturación. Un gestor de facturación puede ver información de uso de tiempo de ejecución y servicio para la organización.</dd>
<dt class="pt dlterm">OrgAuditor</dt>
<dd class="pd">Auditor de la organización. Un auditor de la organización puede ver el contenido de la app y el servicio en el espacio.</dd>
</dl>

### Configuración de una cuota para una organización

Puede establecer la cuota de uso de una determinada organización.

```
cf ba set-quota <organización> <plan>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;organización&gt;</dt>
<dd class="pd">El nombre o GUID de la organización de {{site.data.keyword.Bluemix_notm}} para la que va a definir la cuota.</dd>
<dt class="pt dlterm">&lt;plan&gt;</dt>
<dd class="pd">El plan de cuotas de una organización.</dd>
</dl>

**Sugerencia:** También puede usar **ba sq** como alias para el nombre de mandato
**ba set-quota** más largo.

### Adición, supresión y recuperación de informes

Puede añadir, suprimir y recuperar informes de seguridad.
* Para añadir un informe, ejecute el siguiente mandato:

```
cf ba add-report <categoría> <fecha> <PDF|TXT|LOG> <RTF>
```
{: codeblock}

**Nota**: Si tiene acceso de escritura para el permiso de informes, puede crear una nueva categoría y añadir un informe en cualquiera de los formatos aceptados para sus usuarios. Especifique el nombre de la nueva categoría para el parámetro `category` o añada su informe en una categoría existente.

<dl class="parml">
<dt class="pt dlterm">&lt;categoría&gt;</dt>
<dd class="pd">La categoría del informe. Utilice las comillas si hay un espacio en el nombre.</dd>
<dt class="pt dlterm">&lt;fecha&gt;</dt>
<dd class="pd">La fecha del informe con el formato <samp class="ph codeph">AAAAMMDD</samp>.</dd>
<dt class="pt dlterm">&lt;PDF|TXT|LOG&gt;</dt>
<dd class="pd">La vía de acceso del PDF del informe, del archivo de texto o de registro que va a cargar.</dd>
<dt class="pt dlterm">&lt;RTF&gt;</dt>
<dd class="pd">Opción para incluir una versión de formato de texto enriquecido (RTF) del PDF. Esta opción solo se aplica si ha incluido una vía de acceso al PDF del informe. La versión RTF se utiliza para indexar y buscar.</dd>
</dl>

**Sugerencia:** También puede usar **ba ar** como alias para un nombre de mandato
de **ba add-report** más largo.

* Para suprimir un informe, ejecute el siguiente mandato:

```
cf ba delete-report <categoría> <fecha> <nombre>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;categoría&gt;</dt>
<dd class="pd">La categoría del informe. Utilice las comillas si hay un espacio en el nombre.</dd>
<dt class="pt dlterm">&lt;fecha&gt;</dt>
<dd class="pd">La fecha del informe con el formato <samp class="ph codeph">AAAAMMDD</samp>.</dd>
<dt class="pt dlterm">&lt;nombre&gt;</dt>
<dd class="pd">El nombre del informe.</dd>
</dl>

**Sugerencia:** También puede usar **ba dr** como alias para el nombre de mandato
**ba delete-report** más largo.

* Para recuperar un informe, ejecute el siguiente mandato:

```
cf ba retrieve-report <categoría> <fecha> <nombre>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;categoría&gt;</dt>
<dd class="pd">La categoría del informe. Utilice las comillas si hay un espacio en el nombre.</dd>
<dt class="pt dlterm">&lt;fecha&gt;</dt>
<dd class="pd">La fecha del informe con el formato <samp class="ph codeph">AAAAMMDD</samp>.</dd>
<dt class="pt dlterm">&lt;nombre&gt;</dt>
<dd class="pd">El nombre del informe.</dd>
</dl>

**Sugerencia:** También puede usar **ba rr** como alias para un nombre de mandato
de **ba retrieve-report** más largo.

### Habilitación e inhabilitación de servicios para todas las organizaciones

Puede habilitar o inhabilitar un servicio para que no se visualice en el Catálogo de {{site.data.keyword.Bluemix_notm}} para todas las organizaciones.

* Para permitir que un servicio sea visible en el Catálogo de {{site.data.keyword.Bluemix_notm}} para todas las organizaciones,
ejecute el siguiente mandato:

```
cf ba enable-service-plan <identificador_plan>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;identificador_plan&gt;</dt>
<dd class="pd">Nombre o GUID del plan de servicio que desea habilitar. Si especifica un nombre de plan de servicio no exclusivo, por ejemplo "Estándar" o "Básico", se le ofrecerán planes de servicio entre los que elegir. Para identificar el nombre de un plan de servicio, seleccione la categoría de servicio en la página de inicio y, a continuación, seleccione **Añadir** para ver los servicios para dicha categoría. Pulse el nombre de servicio para abrir la vista de detalles y luego podrá ver los nombres de los planes de servicio disponibles para dicho servicio. </dd>
</dl>

**Sugerencia:** También puede usar **ba esp** como alias para el nombre de mandato
**ba enable-service-plan** más largo.

* Para no permitir que un servicio sea visible en el Catálogo de {{site.data.keyword.Bluemix_notm}} para todas las organizaciones,
ejecute el siguiente mandato:

```
cf ba disable-service-plan <identificador_plan>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;identificador_plan&gt;</dt>
<dd class="pd">Nombre o GUID del plan de servicio que desea habilitar. Si especifica un nombre de plan de servicio no exclusivo, por ejemplo "Estándar" o "Básico", se le ofrecerán planes de servicio entre los que elegir. Para identificar el nombre de un plan de servicio, seleccione la categoría de servicio en la página de inicio y, a continuación, seleccione **Añadir** para ver los servicios para dicha categoría. Pulse el nombre de servicio para abrir la vista de detalles y luego podrá ver los nombres de los planes de servicio disponibles para dicho servicio.</dd>
</dl>

**Sugerencia:** También puede usar **ba dsp** como alias para un nombre de mandato
de **ba disable-service-plan** más largo.

### Adición, eliminación y edición de la visibilidad de los servicios de las organizaciones

Puede añadir o eliminar una organización de la lista de organizaciones que pueden ver un servicio específico en el Catálogo de {{site.data.keyword.Bluemix_notm}}. También puede editar y sustituir la lista de servicios que pueden ver determinadas organizaciones en el Catálogo de {{site.data.keyword.Bluemix_notm}}.

* Para permitir que una organización pueda ver un servicio específico en el Catálogo de {{site.data.keyword.Bluemix_notm}}, especifique el siguiente mandato:

```
cf ba add-service-plan-visibility <identificador_plan> <organización>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;identificador_plan&gt;</dt>
<dd class="pd">Nombre o GUID del plan de servicio que desea habilitar. Si especifica un nombre de plan de servicio no exclusivo, por ejemplo "Estándar" o "Básico", se le ofrecerán planes de servicio entre los que elegir. Para identificar el nombre de un plan de servicio, seleccione la categoría de servicio en la página de inicio y, a continuación, seleccione **Añadir** para ver los servicios para dicha categoría. Pulse el nombre de servicio para abrir la vista de detalles y luego podrá ver los nombres de los planes de servicio disponibles para dicho servicio.</dd>
<dt class="pt dlterm">&lt;organización&gt;</dt>
<dd class="pd">El nombre o GUID de la organización de {{site.data.keyword.Bluemix_notm}} que va a agregar a la lista de visibilidad del servicio.</dd>
</dl>

**Sugerencia:** También puede usar **ba aspv** como alias para el nombre de mandato
**ba add-service-plan-visibility** más largo.

* Para eliminar la visibilidad de un servicio en el Catálogo de {{site.data.keyword.Bluemix_notm}} para una organización, especifique el siguiente mandato:

```
cf ba remove-service-plan-visibility <identificador_plan> <organización>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;identificador_plan&gt;</dt>
<dd class="pd">Nombre o GUID del plan de servicio que desea habilitar. Si especifica un nombre de plan de servicio no exclusivo, por ejemplo "Estándar" o "Básico", se le ofrecerán planes de servicio entre los que elegir. Para identificar el nombre de un plan de servicio, seleccione la categoría de servicio en la página de inicio y, a continuación, seleccione **Añadir** para ver los servicios para dicha categoría. Pulse el nombre de servicio para abrir la vista de detalles y luego podrá ver los nombres de los planes de servicio disponibles para dicho servicio.</dd>
<dt class="pt dlterm">&lt;organización&gt;</dt>
<dd class="pd">El nombre o GUID de la organización de {{site.data.keyword.Bluemix_notm}} que va a eliminar de la lista de visibilidad del servicio.</dd>
</dl>

**Sugerencia:** También puede usar **ba rspv** como alias para un nombre de mandato
de **ba remove-service-plan-visibility** más largo.

* Para sustituir todos los servicios visibles existentes de una o varias organizaciones, utilice el mandato siguiente:

```
cf ba edit-service-plan-visibilities <identificador_plan> <organización_1> <organización_2_opcional>
```
{: codeblock}

**Nota:** Este mandato sustituye los servicios visibles existentes de las organizaciones especificadas por el servicio que especifique en el mandato.

<dl class="parml">
<dt class="pt dlterm">&lt;identificador_plan&gt;</dt>
<dd class="pd">Nombre o GUID del plan de servicio que desea habilitar. Si especifica un nombre de plan de servicio no exclusivo, por ejemplo "Estándar" o "Básico", se le ofrecerán planes de servicio entre los que elegir. Para identificar el nombre de un plan de servicio, seleccione la categoría de servicio en la página de inicio y, a continuación, seleccione **Añadir** para ver los servicios para dicha categoría. Pulse el nombre de servicio para abrir la vista de detalles y luego podrá ver los nombres de los planes de servicio disponibles para dicho servicio.</dd>
<dt class="pt dlterm">&lt;organización&gt;</dt>
<dd class="pd">El nombre o GUID de la organización de {{site.data.keyword.Bluemix_notm}} a la que va a agregar visibilidad. Puede habilitar la visibilidad del servicio a más de una organización especificando nombres de organización o GUID adicionales en el mandato.</dd>
</dl>

**Sugerencia:** También puede usar **ba espv** como alias para el nombre de mandato
**ba edit-service-plan-visibility** más largo.

### Trabajar con intermediarios de servicio

Utilice los mandatos siguientes para mostrar una lista de todos los intermediarios de servicio, añadir o suprimir
intermediarios de servicio, o para actualizar un intermediario de servicio.

* Puede mostrar una lista de los intermediarios de servicio especificando el mandato siguiente:

```
cf ba service-brokers <nombre_intermediario>
```
{: codeblock}

**Nota**: Para mostrar una lista de todos los intermediarios de servicio, especifique el mandato sin el parámetro
`nombre_intermediario`.

<dl class="parml">
<dt class="pt dlterm">&lt;nombre_intermediario&gt;</dt>
<dd class="pd">Opcional: El nombre del intermediario de servicio personalizado. Utilice este parámetro, si quiere obtener información
para un intermediario de servicio específico.</dd>
</dl>

**Sugerencia:** También puede usar **ba sb** como alias para un nombre de mandato
de **ba service-brokers** más largo.

* Puede añadir un intermediario de servicio, con lo que puede añadir un servicio personalizado a su catálogo de
{{site.data.keyword.Bluemix_notm}} especificando el mandato siguiente:

```
cf ba add-service-broker <nombre_intermediario> <nombre_usuario> <contraseña> <url_intermediario>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;nombre_intermediario&gt;</dt>
<dd class="pd">El nombre del intermediario de servicio personalizado.</dd>
<dt class="pt dlterm">&lt;nombre_usuario&gt;</dt>
<dd class="pd">El nombre de usuario de la cuenta que tiene acceso al intermediario de servicios.</dd>
<dt class="pt dlterm">&lt;contraseña&gt;</dt>
<dd class="pd">La contraseña de la cuenta que tiene acceso al intermediario de servicios.</dd>
<dt class="pt dlterm">&lt;url_intermediario&gt;</dt>
<dd class="pd">El URL del intermediario de servicio.</dd>
</dl>

**Sugerencia:** También puede usar **ba asb** como alias para el nombre de mandato
**ba add-service-broker** más largo.

* Puede suprimir un intermediario de servicio; para suprimir el servicio personalizado a su catálogo de
{{site.data.keyword.Bluemix_notm}} especificando el mandato siguiente:

```
cf ba delete-service-broker <intermediario_servicio>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;intermediario_servicio&gt;</dt>
<dd class="pd">El nombre o GUID del intermediario de servicio personalizado.</dd>
</dl>

**Sugerencia:** También puede usar **ba dsb** como alias para un nombre de mandato
de **ba delete-service-broker** más largo.

* Puede actualizar un intermediario de servicio especificando el mandato siguiente:

`cf ba update-service-broker <nombre_intermediario> <nombre_usuario> <contraseña> <url_intermediario>`
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;nombre_intermediario&gt;</dt>
<dd class="pd">El nombre del intermediario de servicio personalizado.</dd>
<dt class="pt dlterm">&lt;nombre_usuario&gt;</dt>
<dd class="pd">El nombre de usuario de la cuenta que tiene acceso al intermediario de servicios.</dd>
<dt class="pt dlterm">&lt;contraseña&gt;</dt>
<dd class="pd">La contraseña de la cuenta que tiene acceso al intermediario de servicios.</dd>
<dt class="pt dlterm">&lt;url_intermediario&gt;</dt>
<dd class="pd">El URL del intermediario de servicio.</dd>
</dl>

**Sugerencia:** También puede usar **ba usb** como alias para el nombre de mandato
**ba update-service-broker** más largo.


### Trabajar con grupos de seguridad de aplicaciones

Para trabajar con grupos de seguridad de aplicaciones (ASG), debe ser un administrador con acceso completo al entorno local o dedicado. Todos los usuarios del entorno pueden mostrar una lista de los ASG disponibles para la organización que el mandato tiene como objetivo. No obstante, para crear, actualizar o enlazar ASG, debe ser administrador del entorno de {{site.data.keyword.Bluemix_notm}}.

Los ASG funcionan como cortafuegos virtuales que controlan el tráfico de salida de las aplicaciones del entorno de
{{site.data.keyword.Bluemix_notm}}. Cada ASG está compuesto por una lista de reglas que permiten comunicaciones y tráfico específicos hacia y desde el exterior de la red. Puede enlazar uno o más ASG con un conjunto de grupos de seguridad específico, por ejemplo, un conjunto de grupos que se utiliza para aplicar el acceso global, o puede enlazar con espacios dentro de la organización en el entorno de
{{site.data.keyword.Bluemix_notm}}.

{{site.data.keyword.Bluemix_notm}} se configura inicialmente con todo el acceso a la red externa restringido. Dos grupos de seguridad creados por IBM, `public_networks` y `dns`, permiten el acceso global a la red externa al enlazar dichos grupos con los conjuntos de grupos de seguridad de Cloud Foundry predeterminados. Los dos conjuntos de grupos de seguridad de Cloud Foundry que se utilizan para aplicar el acceso global son los conjuntos de grupos **Default Staging** y **Default Running**. Estos conjuntos de grupos aplican las reglas para permitir tráfico hacia todas las apps en ejecución o todas las apps en transferencia. Si no desea enlazar estos dos conjuntos de grupos de seguridad, puede desenlazarlos de los conjuntos de grupos de Cloud Foundry y, a continuación, enlazar el grupo de seguridad con un espacio específico. Para obtener más información, consulte [Enlace de grupos de seguridad de aplicaciones](https://docs.cloudfoundry.org/adminguide/app-sec-groups.html#binding-groups){: new_window}.

**Nota**: los mandatos siguientes que le permiten trabajar con grupos de seguridad se basan en la versión 1.6 de Cloud Foundry.

#### Listado, creación, actualización y supresión de grupos de seguridad

Para obtener más información sobre la creación de grupos de seguridad y las reglas que definen el tráfico de salida, consulte
[Creación de grupos de seguridad de aplicaciones](https://docs.cloudfoundry.org/adminguide/app-sec-groups.html#creating-groups){: new_window}.

* Puede obtener una lista de todos los grupos de seguridad especificando el mandato siguiente:

```
cf ba security-groups
```
{: codeblock}

**Sugerencia:** también puede usar **ba sgs** como alias para el nombre de mandato
**ba security-groups** más largo.

* Puede mostrar los detalles de un grupo de seguridad concreto especificando el mandato siguiente:

```
cf ba security-groups <grupo-seguridad>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;grupo-seguridad&gt;</dt>
<dd class="pd">Nombre del grupo de seguridad</dd>
</dl>

**Sugerencia:** también puede usar **ba sg** como alias para el nombre de mandato
**ba security-groups** más largo con el parámetro `security-group`.


* Puede crear un grupo de seguridad especificando el mandato siguiente. Cada grupo de seguridad que cree tiene el prefijo
`adminconsole_` añadido al nombre para distinguirlo de los grupos de seguridad creados por IBM.

```
cf ba create-security-group <grupo-seguridad> <vía-acceso-archivo-reglas>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;grupo-seguridad&gt;</dt>
<dd class="pd">Nombre del grupo de seguridad</dd>
<dt class="pt dlterm">&lt;vía-acceso-archivo-reglas&gt;</dt>
<dd class="pd">Vía de acceso absoluta o relativa al archivo de reglas</dd>
</dl>

**Sugerencia:** También puede usar **ba csg** como alias para un nombre de mandato
de **ba create-security-group** más largo.

* Puede actualizar un grupo de seguridad especificando el mandato siguiente:

```
cf ba update-security-group <grupo-seguridad> <vía-acceso-archivo-reglas>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;grupo-seguridad&gt;</dt>
<dd class="pd">Nombre del grupo de seguridad</dd>
<dt class="pt dlterm">&lt;vía-acceso-archivo-reglas&gt;</dt>
<dd class="pd">Vía de acceso absoluta o relativa al archivo de reglas</dd>
</dl>

**Sugerencia:** también puede usar **ba usg** como alias para el nombre de mandato
**ba update-security-group** más largo.

* Puede suprimir un grupo de seguridad especificando el mandato siguiente:

```
cf ba delete-security-group <grupo-seguridad>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;grupo-seguridad&gt;</dt>
<dd class="pd">Nombre del grupo de seguridad</dd>
</dl>

**Sugerencia:** También puede usar **ba dsg** como alias para un nombre de mandato
de **ba delete-security-group** más largo.


#### Enlace, anulación del enlace y listado de grupos de seguridad enlazados

Para obtener más información sobre el enlace y la anulación del enlace de grupos de seguridad, consulte
[Enlace de grupos de seguridad de aplicaciones](https://docs.cloudfoundry.org/adminguide/app-sec-groups.html#binding-groups){: new_window} y
[Anulación del enlace de grupos de seguridad de aplicaciones](https://docs.cloudfoundry.org/adminguide/app-sec-groups.html#unbinding-groups){: new_window}.

* Puede enlazar con el conjunto de grupos de seguridad Default Staging especificando el mandato siguiente:

```
cf ba bind-staging-security-group <grupo-seguridad>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;grupo-seguridad&gt;</dt>
<dd class="pd">Nombre del grupo de seguridad</dd>
</dl>

**Sugerencia:** también puede usar **ba bssg** como alias para el nombre de mandato
**ba bind-staging-security-group** más largo.

* Puede enlazar con el conjunto de grupos de seguridad Default Running especificando el mandato siguiente:

```
cf ba bind-running-security-group <grupo-seguridad>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;grupo-seguridad&gt;</dt>
<dd class="pd">Nombre del grupo de seguridad</dd>
</dl>

**Sugerencia:** también puede usar **ba brsg** como alias para un nombre de mandato
de **ba bind-running-security-group** más largo.

* Puede desenlazar un conjunto de grupos de seguridad Default Staging especificando el mandato siguiente:

```
cf ba cf ba unbind-staging-security-group <grupo-seguridad>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;grupo-seguridad&gt;</dt>
<dd class="pd">Nombre del grupo de seguridad</dd>
</dl>

**Sugerencia:** también puede usar **ba ussg** como alias para el nombre de mandato
**ba unbind-staging-security-group** más largo.

* Puede desenlazar un conjunto de grupos de seguridad Default Running especificando el mandato siguiente:

```
cf ba unbind-running-security-group <grupo-seguridad>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;grupo-seguridad&gt;</dt>
<dd class="pd">Nombre del grupo de seguridad</dd>
</dl>

**Sugerencia:** también puede usar **ba brsg** como alias para un nombre de mandato
de **ba bind-running-security-group** más largo.

* Puede enlazar un grupo de seguridad con un espacio especificando el mandato siguiente:

```
cf ba bind-security-group <grupo-seguridad> <org> <espacio>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;grupo-seguridad&gt;</dt>
<dd class="pd">Nombre del grupo de seguridad</dd>
<dt class="pt dlterm">&lt;org&gt;</dt>
<dd class="pd">Nombre de la organización con la que enlazar el grupo de seguridad</dd>
<dt class="pt dlterm">&lt;espacio&gt;</dt>
<dd class="pd">Nombre del espacio dentro de la organización con el que enlazar el grupo de seguridad</dd>
</dl>

**Sugerencia:** también puede usar **ba bsg** como alias para un nombre de mandato
de **ba bind-security-group** más largo.

* Puede desenlazar un grupo de seguridad de un espacio especificando el mandato siguiente:

```
cf ba unbind-security-group <grupo-seguridad> <org> <espacio>
```
{: codeblock}

<dl class="parml">
<dt class="pt dlterm">&lt;grupo-seguridad&gt;</dt>
<dd class="pd">Nombre del grupo de seguridad</dd>
<dt class="pt dlterm">&lt;org&gt;</dt>
<dd class="pd">Nombre de la organización con la que enlazar el grupo de seguridad</dd>
<dt class="pt dlterm">&lt;espacio&gt;</dt>
<dd class="pd">Nombre del espacio dentro de la organización con el que enlazar el grupo de seguridad</dd>
</dl>

**Sugerencia:** también puede usar **ba usg** como alias para el nombre de mandato
**ba unbind-staging-security-group** más largo.

