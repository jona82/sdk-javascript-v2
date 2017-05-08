<a name="inicio"></a>
Decidir SDK Javascript
===============

Modulo para conexión con gateway de pago DECIDIR2

+ [Introducción](#introduccion)
  + [Alcance](#scope)
  + [Diagrama de secuencia](#secuencia)
+ [Instalación](#instalacion)
  + [Ambientes](#environments)
+ [Uso](#uso)
  + [Inicializar la clase correspondiente al conector](#initconector)
  + [Operatoria del Gateway](#operatoria)
    + [Generaci&oacute;n de Token de Pago](#authenticate)
     +  [Con datos de tarjeta](#datostarjeta)
     +  [Con tarjeta tokenizada](#tokentarjeta)
  + [Integración con Cybersource](#cybersource)

<a name="introduccion"></a>
## Introducción
El flujo de una transacción a través de las **sdks** consta de dos pasos, la **generaci&oacute;n de un token de pago** por parte del cliente y el **procesamiento de pago** por parte del comercio. Existen sdks espec&iacute;ficas para realizar estas funciones en distintos lenguajes que se detallan a continuaci&oacute;n:

+ **Generaci&oacute;n de un token de pago.**  Se utiliza alguna de las siguentes **sdks front-end** :
  + [sdk IOS](https://github.com/decidir/sdk-ios-v2)
  + [sdk Android](https://github.com/decidir/sdk-android-v2)
  + [sdk Javascript](https://github.com/decidir/sdk-javascript-v2)
+ **Procesamiento de pago.**  Se utiliza alguna de las siguentes **sdks back-end** :
  + [sdk Java](https://github.com/decidir/sdk-java-v2)
  + [sdk PHP](https://github.com/decidir/sdk-php-v2)
  + [sdk .Net](https://github.com/decidir/sdk-.net-v2)
  + [sdk Node](https://github.com/decidir/sdk-node-v2)


[<sub>Volver a inicio</sub>](#inicio)

<a name="scope"></a>
## Alcance
La **sdk Javascript** provee soporte para su **aplicaci&oacute;n front-end**, encargandose de la **generaci&oacute;n de un token de pago** por parte de un cliente. Este **token** debe ser enviado al comercio al realizar el pago.
Esta sdk permite la comunicaci&oacute;n del cliente con la **API Decidir** utilizando su **API Key p&uacute;blica**<sup>1</sup>.

Para procesar el pago con **Decidir**, el comercio podr&acute; realizarlo a trav&eacute;s de alguna de las siguentes **sdks front-backend**:
+ [sdk Java](https://github.com/decidir/sdk-java-v2)
+ [sdk PHP](https://github.com/decidir/sdk-php-v2)
+ [sdk .Net](https://github.com/decidir/sdk-.net-v2)
+ [sdk Node](https://github.com/decidir/sdk-node-v2)

![imagen de sdks](./docs/img/DiagramaSDKs.png)

---
<sup>_1 - Las API Keys serán provistas por el equipo de Soporte de DECIDIR (soporte@decidir.com.ar)</sup>

[<sub>Volver a inicio</sub>](#inicio)

<a name="secuencia"></a>

## Diagrama de secuencia
El flujo de una transacción a través de las **sdks** consta de dos pasos, a saber:

1. **sdk front-end:** Se realiza una solicitud de token de pago con la Llave de Acceso pública (public API Key), enviando los datos sensibles de la tarjeta (PAN, mes y año de expiración, código de seguridad, titular, y tipo y número de documento) y obteniéndose como resultado un token que permitirá realizar la transacción posterior.

2. **sdk back-end:** Se ejecuta el pago con la Llave de Acceso privada (private API Key), enviando el token generado en el Paso 1 más el identificador de la transacción a nivel comercio, el monto total, la moneda y la cantidad de cuotas.

A continuación, se presenta un diagrama con el Flujo de un Pago.

![imagen de configuracion](./docs/img/FlujoPago.png)</br>

[<sub>Volver a inicio</sub>](#inicio)

<a name="instalacion"></a>
## Instalación

Se debe agregar en el HTML el siguiente tag.

```html
<script src="https://live.decidir.com/static/v1/decidir.js"></script>
```
[<sub>Volver a inicio</sub>](#inicio)

<a name="test"></a>

## Ambientes

El **sdk Javascript** permite trabajar con los ambientes Sandbox y Producc&oacute;n de Decidir.
El ambiente se debe instanciar indicando su URL.


```javascript
// ...codigo...
const urlSandbox = "https://developers.decidir.com/api/v1";
const urlProduccion = "https://live.decidir.com/api/v1";
//Para el ambiente de desarrollo
const decidirSandbox = new Decidir(urlSandbox);
decidirSandbox.setTimeout(0);//se configura sin timeout
//Para el ambiente de produccion
const decidirProduccion = new Decidir(urlProduccion);
decidirProduccion.setTimeout(3000);//se configura el timeout en milisegundos
// ...codigo...
```
[<sub>Volver a inicio</sub>](#inicio)

<a name="uso"></a>
## Uso

<a name="initconector"></a>
### Inicializar la clase correspondiente al conector.

Instanciación de la clase `Decidir`

La misma recibe como parámetros la public key provista por Decidir para el comercio y el ambiente en que se trabajar&aacute;.

La API Key será provista por el equipo de Soporte de DECIDIR (soporte@decidir.com.ar).

A partir de ahora y por el resto de la documentaci&oacute;n, se ejemplificar&aacute; utilizando una APIKey habilitada para operar en el ambiente Sandbox.


```javascript
const publicApiKey = "e9cdb99fff374b5f91da4480c8dca741";
const urlSandbox = "https://developers.decidir.com/api/v1";
//Para el ambiente de desarrollo
const decidir = new Decidir(urlSandbox);
//Se indica la public API Key
decidir.setPublishableKey(publicApiKey);
decidir.setTimeout(5000);//timeout de 5 segundos
// ...codigo...
```

[<sub>Volver a inicio</sub>](#inicio)

<a name="operatoria"></a>

## Operatoria del Gateway

<a name="authenticate"></a>

### Generaci&oacute;n de Token de Pago

La **sdk javascript** permite generar, desde el navegador, un token de pago con los datos de la tarjeta del cliente. &Eacute;ste se deber&aacute; enviar luego al backend del comercio para realizar la transacci&oacute;n de pago correpondiente.

El token de pago puede ser generado de 2 formas como se muestra a continuaci&oacute;n.

[<sub>Volver a inicio</sub>](#inicio)

<a name="datostarjeta"></a>

#### Con datos de tarjeta

Mediante este recurso, se genera una token de pago a partir de los datos de la tarjeta del cliente.
Debe enviarse un formulario web, con los campos marcados con el atributo `data-decidir` indicando el parametro al que corresponde.
```html
<form action="" method="post" id="formulario" >
  <fieldset>
		<ul>
      <li>
        <label for="card_number">Numero de tarjeta:</label>
        <input type="text" data-decidir="card_number" placeholder="XXXXXXXXXXXXXXXX" value="4507990000004905"/>
      </li>
      <li>
        <label for="security_code">Codigo de seguridad:</label>
      <input type="text"  data-decidir="security_code" placeholder="XXX" value="123" />
      </li>
      <li>
        <label for="card_expiration_month">Mes de vencimiento:</label>
        <input type="text"  data-decidir="card_expiration_month" placeholder="MM" value="12"/>
      </li>
      <li>
        <label for="card_expiration_year">Año de vencimiento:</label>
        <input type="text"  data-decidir="card_expiration_year" placeholder="AA" value="20"/>
      </li>
      <li>
        <label for="card_holder_name">Nombre del titular:</label>
        <input type="text" data-decidir="card_holder_name" placeholder="TITULAR" value="TITULAR"/>
      </li>
      <li>
        <label for="card_holder_doc_type">Tipo de documento:</label>
        <select data-decidir="card_holder_doc_type">
					<option value="dni">DNI</option>
				</select>
      </li>
      <li>
        <label for="card_holder_doc_type">Numero de documento:</label>
        <input type="text"data-decidir="card_holder_doc_number" placeholder="XXXXXXXXXX" value="27859328"/>
      </li>
    </ul>
    <input type="submit" value="Generar Token" />
  </fieldset>
</form>
```
Y la invocici&oacute;n en **Javascript**

```javascript
const publicApiKey = "e9cdb99fff374b5f91da4480c8dca741";
const urlSandbox = "https://developers.decidir.com/api/v1";
//Para el ambiente de desarrollo
const decidir = new Decidir(urlSandbox);
//Se indica la public API Key
decidir.setPublishableKey(publicApiKey);
decidir.setTimeout(5000);//timeout de 5 segundos
//formulario
var form = document.querySelector('#formulario');
//Asigna la funcion de invocacion al evento de submit del formulario
addEvent(form,'submit',sendForm));
//funcion para manejar la respuesta
function sdkResponseHandler(status, response) {
	if (status != 200 && status != 201) {
    //Manejo de error donde response = {error: [{error:"tipo de error", param:"parametro con error"},...]}
    //...codigo...
  } else {
    //Manejo de respuesta donde response = {token: "99ab0740-4ef9-4b38-bdf9-c4c963459b22"}
    //..codigo...
  }
}
//funcion de invocacion con sdk
function sendForm(event) {
  event.preventDefault();
  decidir.createToken(form, sdkResponseHandler);//formulario y callback
  return false;
}
//..codigo...
```

[<sub>Volver a inicio</sub>](#inicio)

<a name="tokentarjeta"></a>

#### Con tarjeta tokenizada

Mediante este recurso, se genera una token de pago a partir de los datos de la tarjeta del cliente.
Debe enviarse un formulario web, con los campos marcados con el atributo `data-decidir` indicando el parametro al que corresponde.

```html
<form action="" method="post" id="formulario" >
	<fieldset>
		<ul>
			<li>
				<label for="token">Tarjeta tokenizada:</label>
				<input type="text"  data-decidir="token" placeholder="xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx" value="70b03ded-9116-45be-91ca-27a6969ad6ac"/>
			</li>
			<li>
				<label for="security_code">Codigo de seguridad:</label>
				<input type="text"  data-decidir="security_code" placeholder="XXX" value="123" />
			</li>
		</ul>
	  <input type="submit" value="Generar Token" />
	</fieldset>
</form>
```
Y la invocici&oacute;n en **Javascript**

```javascript
const publicApiKey = "e9cdb99fff374b5f91da4480c8dca741";
const urlSandbox = "https://developers.decidir.com/api/v1";
//Para el ambiente de desarrollo
const decidir = new Decidir(urlSandbox);
//Se indica la public API Key
decidir.setPublishableKey(publicApiKey);
decidir.setTimeout(5000);//timeout de 5 segundos
//formulario
var form = document.querySelector('#formulario');
//Asigna la funcion de invocacion al evento de submit del formulario
addEvent(form,'submit',sendForm));
//funcion para manejar la respuesta
function sdkResponseHandler(status, response) {
	if (status != 200 && status != 201) {
    //Manejo de error donde response = {error: [{error:"tipo de error", param:"parametro con error"},...]}
    //...codigo...
  } else {
    //Manejo de respuesta donde response = {token: "99ab0740-4ef9-4b38-bdf9-c4c963459b22"}
    //..codigo...
  }
}
//funcion de invocacion con sdk
function sendForm(event){
  event.preventDefault();
  decidir.createToken(form, sdkResponseHandler);//formulario y callback
  return false;
};
//..codigo...
```
[<sub>Volver a inicio</sub>](#inicio)


<a name="cybersource"></a>

## Integración con Cybersource

Por default, la sdk javascript utiliza el Servicio de Control de Fraude Cybersource. Para inhabilitar esta funcionalidad, debe indicarse en un par&aacute;metro al momento de instanciar el objeto `Decidir`. Para realizar esto, debe invocarse al contrucutor con dos argumentos, el primero es la url el cual puede enviarse nulo para tomar la url por defecto, y el segundo que indica si se inhabilita Cybersource

```javascript
const publicApiKey = "e9cdb99fff374b5f91da4480c8dca741";
const urlSandbox = "https://developers.decidir.com/api/v1";
const inhabilitarCS = true;
//Para el ambiente de desarrollo
const decidir = new Decidir(urlSandbox, inhabilitarCS);
//Se indica la public API Key
decidir.setPublishableKey(publicApiKey);
decidir.setTimeout(5000);//timeout de 5 segundos
// ...codigo...
}
```

[<sub>Volver a inicio</sub>](#inicio)
