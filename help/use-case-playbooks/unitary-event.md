---
title: Evento unitario
description: Esta es una página de instrucciones para simular '[!UICONTROL Evento unitario]' tipo de validación de Recorrido.
source-git-commit: 0a52611530513fc036ef56999fde36a32ca0f482
workflow-type: tm+mt
source-wordcount: '749'
ht-degree: 0%

---


# Evento unitario

## Pasos a seguir {#steps-to-follow}

>[!CONTEXTUALHELP]
>id="marketerexp_sampledata_unitaryevent"
>title="¿Cómo se usa?"
>abstract="Siga el vínculo para obtener más información"

>[!IMPORTANT]
>
>Estas instrucciones pueden cambiar entre **[!UICONTROL Guía]** por lo tanto, consulte siempre la sección Datos de muestra de sus respectivos **[!UICONTROL Guía]**.

## Requisito previo

* Debe tener instalado el software de Postman
* Utilice el manual para crear los recursos de instancias como **[!UICONTROL Recorrido]**, **[!UICONTROL Esquemas]**, **[!UICONTROL Segmentos]**, **[!UICONTROL Mensajes]** etc.

Los recursos creados se mostrarán el `Bill Of Material` Página

![Página Lista De Materiales](../assets/bom-page.png)

## Preparar Postman con la colección requerida

1. Visita **[!UICONTROL Guía de casos de uso]** aplicación.
1. Haga clic en la **[!UICONTROL Guía]** tarjeta de visita **[!UICONTROL Guía]** página de detalles.
1. Visita **[!UICONTROL Lista De Materiales]** y busque la **[!UICONTROL Datos de muestra]** sección.
1. Descargue la `postman.json` haciendo clic en los botones correspondientes de la interfaz de usuario.
1. Importar `postman.json` en el **[!DNL Postman Software]**.
1. Cree un entorno de Postman dedicado para esta validación (por ejemplo, `Adobe <PLAYBOOK_NAME>`).

## Recuperar token de IMS

>[!NOTE]
>
>Todas las variables de entorno distinguen entre mayúsculas y minúsculas, por lo que siempre utilice el nombre de variable exacto.

1. Siga por favor [Autenticación y acceso a las API de Experience Platform](https://experienceleague.adobe.com/docs/experience-platform/landing/platform-apis/api-authentication.html) para generar el token de acceso.
1. Almacene el valor del token de acceso en las variables de entorno denominadas `ACCESS_TOKEN`.
1. Almacenar otros valores relacionados con la autenticación como `API_KEY`, `IMS_ORG` y `SANDBOX_NAME` en Variables de entorno.

>[!IMPORTANT]
>
>Antes de ejecutar cualquier API desde Postman, asegúrese de que se deben agregar todas las variables de entorno necesarias.

## Publicar el Recorrido creado por el Libro de estrategias

Existen dos formas de publicar el recorrido; puede elegir cualquiera de ellas:

1. **Uso de la IU de AJO** - haga clic en el vínculo de Recorrido en `Bill Of Material Page`; esto lo redireccionará a la página Recorrido en la que puede hacer clic **[!UICONTROL Publish]** botón y Recorrido se publicarían.

   ![Objeto recorrido](../assets/journey-object.png)

1. **Uso de la API de Postman**

   1. Déclencheur **[!DNL Publish Journey]** solicitud de **[!DNL Journey Publish]** > **[!DNL Queue journey publish job]**.
   1. La publicación de recorrido puede tardar algún tiempo, por lo que para comprobar el estado, ejecute la API Comprobar estado de publicación de Recorrido hasta que `response.status` es `SUCCESS`, asegúrese de esperar 10-15 segundos si la publicación en recorrido tarda.

   >[!NOTE]
   >
   >Todas las variables de entorno distinguen entre mayúsculas y minúsculas, por lo que siempre utilice el nombre de variable exacto.

## Ingesta del perfil de cliente

>[!TIP]
>
>Puede reutilizar la misma dirección de correo electrónico añadiendo `+<variable>` en su correo electrónico, p. ej. `usertest@email.com` se puede reanudar como `usertest+v1@email.com` o `usertest+24jul@email.com`. Esto sería útil para tener un perfil nuevo cada vez, pero utilizando el mismo ID de correo electrónico.

1. La primera vez que el usuario debe crear el **[!DNL customer dataset]** y **[!DNL HTTP Streaming Inlet Connection]**.
1. Si ya ha creado el **[!DNL customer dataset]** y **[!DNL HTTP Streaming Inlet Connection]**, vaya al paso `5`.
1. Déclencheur **[!DNL Customer Profile Ingestion]** > **[!DNL Create Customer Profile InletId]** > **[!DNL Create Dataset]** para crear **[!DNL customer dataset]**; esto almacenará un `CustomerProfile_dataset_id` en variables de entorno postman.
1. Crear **[!DNL HTTP Streaming Inlet Connection]**, utilice las API de Postman en **[!DNL Customer Profile Ingestion > Create Customer Profile InletId]**.

   1. `CustomerProfile_dataset_id` debe estar disponible en las variables de entorno de postman; si no es así, consulte el paso `3`.
   1. Déclencheur **[!DNL `CREATE Base Connection`]** hasta [!DNL create base connection].
   1. Déclencheur **[!DNL `CREATE Source Connection`]** hasta [!DNL create source connection].
   1. Déclencheur **[!DNL `CREATE Target Connection`]** hasta [!DNL create target connection].
   1. Déclencheur **[!DNL `CREATE Dataflow`]** hasta [!DNL create dataflow].
   1. Déclencheur **[!DNL `GET Base Connection`]**- esto almacenará automáticamente `CustomerProfile_inlet_id` en las variables de entorno de postman.

1. En este paso debe tener `CustomerProfile_dataset_id` y `CustomerProfile_inlet_id` en variables de entorno de postman; si no es así, consulte el paso `3` o `4` respectivamente.
1. Para ingerir un cliente, el usuario debe almacenar `customer_country_code`, `customer_mobile_no`, `customer_first_name`, `customer_last_name` y `email` en variables de entorno postman.

   1. `customer_country_code` sería el código de país del número de móvil, p. ej. `91` o `1`
   1. `customer_mobile_no` sería un número de móvil, p. ej., `9987654321`
   1. `customer_first_name` sería el nombre del usuario
   1. `customer_last_name` sería el apellido del usuario
   1. `email` sería la dirección de correo electrónico del usuario, esto es crucial para utilizar un id de correo electrónico distinto para que se pueda ingerir un perfil nuevo.

1. Actualizar la solicitud de Postman **[!DNL Customer Ingestion]** > **[!DNL Customer Streaming Ingestion]** para cambiar el canal preferido del cliente; de forma predeterminada [!DNL `email`] está configurado en la solicitud de.

   ```js
   "consents": {
       "marketing": {
           "preferred": "email",
           "email": {
               "val": "y"
           },
           "push": {
               "val": "n"
           },
           "sms": {
               "val": "n"
           }
       }
   }
   ```

1. Cambiar canal preferido por `sms` o `push` y asigne el valor correspondiente al canal a `y` y `n` a otros valores, p. ej.,

   ```js
   "consents": {
       "marketing": {
           "preferred": "sms",
           "email": {
               "val": "n"
           },
           "push": {
               "val": "n"
           },
           "sms": {
               "val": "y"
           }
       }
   }
   ```

1. Finalmente, Déclencheur **[!DNL `Customer Profile Ingestion > Customer Profile Streaming Ingestion`]** para introducir el perfil del cliente.

## Ingesta de evento

1. La primera vez que el usuario debe crear **[!DNL event dataset]** y **[!DNL HTTP Streaming Inlet Connection for events]**
1. Si ya ha creado el **[!DNL event dataset]** y **[!DNL HTTP Streaming Inlet Connection for events]**, vaya al paso `5`.
1. Déclencheur **[!DNL `Schemas Data Ingestion > AEP Demo Schema Ingestion > Create AEP Demo Schema InletId > Create Dataset`]** para crear **[!DNL event dataset]**, se almacenará un `AEPDemoSchema_dataset_id` en variables de entorno postman
1. Crear **[!DNL HTTP Streaming Inlet Connection for events]**, utilice las API de Postman en **[!DNL Schemas Data Ingestion]** > **[!DNL AEP Demo Schema Ingestion]** > **[!DNL Create AEP Demo Schema InletId]**.

   1. `AEPDemoSchema_dataset_id` debe estar disponible en las variables de entorno de postman; si no es así, consulte el paso `3`
   1. Déclencheur **[!DNL `CREATE Base Connection`]** hasta [!DNL create base connection]
   1. Déclencheur **[!DNL `CREATE Source Connection`]** hasta [!DNL create source connection]
   1. Déclencheur **[!DNL `CREATE Target Connection`]** hasta [!DNL create target connection]
   1. Déclencheur **[!DNL `CREATE Dataflow`]** hasta [!DNL create dataflow]
   1. Déclencheur **[!DNL `GET Base Connection`]**- esto almacenará automáticamente `AEPDemoSchema_inlet_id` en las variables de entorno de postman

1. En este paso debe tener `AEPDemoSchema_dataset_id` y `AEPDemoSchema_inlet_id` en las variables de entorno de postman, si no es así, consulte el paso `3` o `4` respectivamente
1. Para ingerir un evento, el usuario debe cambiar la variable de tiempo `timestamp` en el cuerpo de la solicitud de **[!DNL Schemas Data Ingestion]** > **[!DNL AEP Demo Schema Ingestion]** > **[!DNL AEP Demo Schema Streaming Ingestion]** en cartero.

   1. `timestamp` para determinar la hora de ocurrencia del evento, utilice la marca de tiempo actual, p. ej. `2023-07-21T16:37:52+05:30` ajuste la zona horaria según sus necesidades.

1. Déclencheur **[!DNL Schemas Data Ingestion > AEP Demo Schema Ingestion > AEP Demo Schema Streaming Ingestion]** para introducir el evento, de modo que se pueda activar el recorrido

## Validación final

Debe recibir un mensaje sobre el canal preferido seleccionado que se utiliza en **[!DNL Ingest the Customer Profile]** escalón `8`

* `SMS` si el canal preferido es `sms` el `customer_country_code` y `customer_mobile_no`
* `Email` si el canal preferido es `email` el `email`

Como alternativa, puede marcar `Journey Report`, para comprobarlo, haga clic en `Journey Object` el `Bill of Materials page` esto le redirigirá a `Journey Details page`.

Para cualquier Recorrido publicado, el usuario debe obtener un **[!UICONTROL Ver informe]** botón
![Página Informe de recorrido](../assets/journey-report-page.png)


## Limpiar

No tenga las varias instancias de `Journey` si se ejecuta simultáneamente, detenga la Recorrido si solo es para validación una vez completada la validación.
