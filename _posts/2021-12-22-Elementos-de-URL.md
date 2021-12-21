---
title: Elementos de una URL
published: false
---

Una URL (*Uniform Resource Locator*) es una dirección web única. Representa la localización de un recurso específico en Internet. El término URL se refiere a un subconjunto de URIs (*Uniform Resource Indicators*) que, además de identificar el recurso web, también proporciona los medios para localizarlo.

Todas las páginas web son recursos *online* y todas tienen una URL. Pero no todas las URLs apuntan a una página web, sino que existen otros recursos en Internet tales como archivos, imágenes, servicios, aplicaciones webs, etc. 

Cada página web se compone de un número de recursos tales como HTML, hipervínculos a otras páginas, CSS, javascript, imágenes, archivos, etc. Cada uno de ellos tienen su propias URL.

Según la URL, puede contener los siguientes elementos:

![](https://zvelo.com/wp-content/uploads/2018/08/full-path-url-structure.jpg)

## [](#header-2)*Top Level Domain* (TLD)

TLD es uno de los dominios en el nivel más alto de la jerarquía del Sistema de nombres de dominio. Es la última parte etiquetada en un nombre de dominio completo (*fully qualified domain name*). 

La administración y la responsabilidad de los TLD son delegadas a las organizaciones por la Corporación de Internet para la Asignación de Nombres y Números ([ICANN](https://www.icann.org/)) y la Autoridad de Números Asignados de Internet ([IANA](https://www.iana.org)), que mantiene la [zona raíz del DNS](https://www.iana.org/domains/root/files). 

Algunos ejemplos son .com, .net, .org, .edu, .ar, .us y [más](https://en.wikipedia.org/wiki/List_of_Internet_top-level_domains).

El TLD siempre está presente.

## [](#header-2)Nombre de dominio

Un nombre de dominio es la “cadena” de identificación registrada (o palabra / frase) utilizada por el [Sistema de Nombres de Dominio](https://www.verisign.com/en_US/website-presence/online/domain-name-system/index.xhtml) para definir un área específica de control y autonomía (también conocida como la ubicación de un sitio web). Un nombre de dominio es un nivel por debajo del TLD. Un solo TLD puede contener cientos de miles e incluso millones de dominios de "segundo nivel" individuales. El dominio también puede denominarse dominio de segundo nivel. 

Algunos ejemplos son google.com, apple.com, amazon.com.

El nombre de dominio siempre está presente.

## [](#header-2)Subdominio

Los subdominios expresan una dependencia relativa y representan parte de un dominio de nivel superior.

De esta manera, los subdominios se utilizan comúnmente para dividir los dominios en segmentos más pequeños con fines de comunicación, tipo de contenido, internacionalización o por otras razones.

Algunos de los ejemplos más comunes de subdominio (en negrita) son:

- **www**.example.com
- **blog**.example.com
- **support**.example.com
- **mail**.example.com

> NOTA: El TLD, Nombre de Dominio y Subdominio conforman el *hostname*


> NOTA: Una URL puede excluir u omitir el dominio de envío y es posible que el *hostname* no incluya el subdominio. Esto se conoce como un "dominio desnudo" (*naked domain*). Por ejemplo, nuestro sitio web https://google.com no muestra un subdominio.


## [](#header-2)Protocolo

El protocolo en una URL determina cómo se transfieren los datos entre el *host* y un navegador web (o cliente). HTTP y HTTPS son dos de los protocolos más comunes que encontrará en la mayoría de las URL, aunque existen otros protocolos de Internet como FTP, DNS, DHCP, IMAP, SMTP, etc.

El protocolo de comunicación aparece antes de cualquier subdominio en una URL. El protocolo va seguido de dos puntos y dos barras diagonales que lo separan del *hostname*. Por ejemplo: **https**://google.com, donde HTTPS es el protocolo.

El subdominio siempre está presente, aunque no siempre visible.

## [](#header-2)Ruta

La ruta se refiere a la ubicación exacta de una página, *post*, archivo u otro *asset*. A menudo es análogo a la estructura de archivos subyacente del sitio web. La ruta reside después del *hostname* y está separada por "/" (barra diagonal). La ruta también consta de cualquier extensión de archivo, como imágenes (.jpg o .png, etc.), documentos (.pdf o .docx) y más. 

Pero no todas las URL mostrarán una ruta. Por ejemplo, cuando visita la página de inicio de un sitio es posible que no vea una ruta o un nombre de archivo. Esto se debe a que muchos sitios web modernos pueden reescribir las URL (como la página de inicio) por motivos de simplicidad y elegancia, como omitir el típico index.html. 

La ruta siempre está presente, aunque no siempre visible.

## [](#header-2)Anclajes HTML

Los anclajes HTML (o fragmentos) se utilizan en sitios web para implementar "marcadores" y elementos de navegación de páginas internas. Se pueden utilizar para proporcionar enlaces a ubicaciones específicas dentro de una página. Los anclajes se ubicarán inmediatamente después del archivo / ruta de una URL (cuando esté presente).

## [](#header-2)Parámetros

Los parámetros se encuentran al final de la URL o dentro de la ruta, según la implementación. Los parámetros de URL se representan en pares clave / valor, comenzando con un "?" Y separados por un "&". También se pueden establecer dinámicamente en la ruta como valores separados por barras y otros caracteres (según el sistema que se utilice y cómo se implemente). Los parámetros se utilizan comúnmente para seguimiento y análisis, así como para codificar información específica para su uso en sitios web y aplicaciones.

Por ejemplo, una URL con parámetros:

- https://www.example.com/solutions?user=123&color=blue
- https://www.example.com/solutions/user/123/color/blue

# [](#header-1)Fuentes

- [https://zvelo.com/anatomy-of-full-path-url-hostname-protocol-path-more/](https://zvelo.com/anatomy-of-full-path-url-hostname-protocol-path-more/)
- [Base Domain URL vs. Full Path URL. What’s the Difference?](https://zvelo.com/base-domain-url-vs-full-path-url-whats-the-difference/)