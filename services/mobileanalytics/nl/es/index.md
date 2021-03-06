---

copyright:
  years: 2016

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen:.screen}
{:codeblock:.codeblock}

# Iniciación a {{site.data.keyword.mobileanalytics_short}} (Experimental)  

{: #gettingstartedtemplate}
*Última actualización: 1 de agosto de 2016*
{: .last-updated}

Utilice el servicio de {{site.data.keyword.mobileanalytics_full}} para medir el estado, el comportamiento y el contexto de sus apps para móvil, los usuarios móviles y los dispositivos móviles.
{: shortdesc}

Para empezar a utilizar de inmediato el servicio de {{site.data.keyword.mobileanalytics_short}}, siga estos pasos:

1. Después de [crear una instancia](https://console.{DomainName}/docs/services/reqnsi.html#req_instance) del servicio de {{site.data.keyword.mobileanalytics_short}}, puede acceder a la consola de {{site.data.keyword.mobileanalytics_short}} pulsando el mosaico de la sección Servicios del panel de control de {{site.data.keyword.Bluemix}}.

  **Importante:** La primera vez que abre el servicio de Mobile Analytics que acaba de crear, puede aparecer una ventana para confirmar que permite que {{site.data.keyword.Bluemix_notm}} proporcione su información personal necesaria al servicio para que este pueda validar su identidad. Pulse **Confirmar** para continuar en la consola de {{site.data.keyword.mobileanalytics_short}}. Si pulsa Cancelar, la consola de {{site.data.keyword.mobileanalytics_short}} no se abrirá.
2. Instale los [SDK de cliente](install-client-sdk.html) de {{site.data.keyword.mobileanalytics_short}}.
3. Importe los SDK de cliente e inicialícelos con el siguiente fragmento de código para registrar las analíticas de uso.
  #### Android
  {: #android-initialize}
  1. Importe el SDK de cliente:
		
		```
		import com.ibm.mobilefirstplatform.clientsdk.android.core.api.*;
		import com.ibm.mobilefirstplatform.clientsdk.android.analytics.api.*;
		```

  2. Obtenga el valor de la [Clave de acceso](sdk.html#analytics-clientkey).
  3. Inicialice el SDK de cliente dentro del código de la aplicación para registrar las analíticas de uso y las sesiones de la aplicación.
		
		```Java
		try {
		        BMSClient.getInstance().initialize(this.getApplicationContext(), "", "", BMSClient.REGION_US_SOUTH);
			}
			catch (MalformedURLException e) {
		        Log.e("your_app_name","URL should not be malformed:  " + e.getLocalizedMessage());
		    }
		   Analytics.init(getApplication(), "your_app_name", "your_access_key", Analytics.DeviceEvent.LIFECYCLE);
		```
    El parámetro **bluemixRegion** especifica el despliegue de Bluemix utilizado (por ejemplo, `BMSClient.REGION_US_SOUTH`, `BMSClient.REGION_UK` o `BMSClient.REGION_SYDNEY`).

  #### iOS
  {: #ios-initialize}
  1. Importe las infraestructuras `BMSCore` y `BMSAnalytics`:

    ```
    import BMSCore
    import BMSAnalytics
    ```

  2. Obtenga el valor de la [Clave de acceso](sdk.html#analytics-clientkey).

  3. Inicialice el SDK de cliente dentro del código de la aplicación para registrar las analíticas de uso y las sesiones de la aplicación.
	
	```Swift
	BMSClient.sharedInstance.initializeWithBluemixAppRoute("nil",bluemixAppGUID: "nil", bluemixRegion: BMSClient.REGION_US_SOUTH) //You can change the region
	Analytics.initializeWithAppName("your_app_name", accessKey: "your_access_key", deviceEvents: DeviceEvent.LIFECYCLE)
	```

    El parámetro **bluemixRegion** especifica el despliegue de Bluemix utilizado (por ejemplo, `BMSClient.REGION_US_SOUTH`, `BMSClient.REGION_UK` o `BMSClient.REGION_SYDNEY`).

4. Envíe las analíticas de uso registradas al servicio de Mobile Analytics. Una manera fácil de probar las analíticas consiste en ejecutar el siguiente código en el momento de iniciar la aplicación:

	#### Android
	{: #android-send}
	
	Puede añadir el método `Analytics.send()` al método `onCreate` de la actividad principal de la aplicación de Android, o bien en una ubicación que sea más adecuada para su proyecto.
	
	```
	Analytics.send(new ResponseListener() {
	    @Override
	    public void onSuccess(Response response) {
	        Log.d("your_app_name", "Successfully sent analytics: " + response.toString());
	    }
		
	    @Override
	    public void onFailure(Response response, Throwable throwable, JSONObject jsonObject) {
	        Log.e("your_app_name", "Failed to send analytics: ");
	        if (response != null) {
	            Log.e("your_app_name", response.toString());
	        }
	        if (throwable != null) {
	            Log.e("your_app_name","Stack trace: ", throwable);
	        }
	    }
	});
	```
	
	#### iOS
	{: #ios-send}
	
	
	Utilice el método `Analytics.send` para enviar datos analíticos al servidor. Coloque el método `Analytics.send` en el método `application(_:didFinishLaunchingWithOptions:)` de la aplicación delegada o en una ubicación que sea más adecuada para su proyecto. 
		
	```
	Analytics.send { (response: Response?, error: NSError?) in
	  if response?.statusCode == 201 {
	      print("Successfully sent analytics: \(response?.responseText)")
	  }
	  else {
	      print("Failed to send analytics: \(response?.responseText). Error: \(error?.localizedDescription)")
	  }
	}
	```
Consulte el tema [Preparación de la aplicación](sdk.html).
5. Compile y ejecute la aplicación en el emulador o dispositivo.

6. Vaya al **panel de control** de {{site.data.keyword.mobileanalytics_short}} para ver las analíticas de uso (por ejemplo, los dispositivos nuevos y el total de dispositivos que utilizan la aplicación). También puede supervisar la app <!-- [creating custom charts](app-monitoring.html#custom-charts), --> [definiendo alertas](app-monitoring.html#alerts) y [supervisando bloqueos de la app](app-monitoring.html#monitor-app-crash). 


# rellinks

## SDK
* [SDK de Android](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-android-analytics){: new_window}  
* [SDK de iOS](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-analytics){: new_window}
