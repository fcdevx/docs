# Manual de Configuración Visual de Sitio Personalizado

Este manual describe todas las configuraciones visuales que se pueden aplicar a un sitio personalizado, incluyendo campos de texto, imágenes y colores.

## 📋 Tabla de Contenidos

1. [Campos de Texto](#campos-de-texto)
2. [Configuración de Imágenes del Sitio](#configuración-de-imágenes-del-sitio)
3. [Personalización de Colores](#personalización-de-colores)
4. [Otros Campos Configurables del Sitio](#otros-campos-configurables-del-sitio)
5. [Configuraciones Visuales desde la Experiencia](#configuraciones-visuales-desde-la-experiencia)
   - [Imágenes Configurables en la Experiencia](#imágenes-configurables-en-la-experiencia)
   - [Campos Visuales Configurables](#campos-visuales-configurables)

---

## 📝 Campos de Texto

### Nombre de Experiencia (`mainExperienceName`)
- **Tipo**: Campo de texto
- **Requerido**: ✅ Sí
- **Caracteres mínimos**: 1
- **Caracteres máximos**: 256
- **Descripción**: Nombre principal que identifica la experiencia en el sitio
- **Ejemplo**: "Concierto de Rock", "Tour por la Ciudad"

### Nombre de Tour (`mainExperienceTour`)
- **Tipo**: Campo de texto
- **Requerido**: ❌ No (Opcional)
- **Caracteres máximos**: 256
- **Descripción**: Nombre secundario o subtítulo del tour/experiencia
- **Ejemplo**: "Tour Nocturno", "Experiencia Premium"

### Descripción de la Experiencia (`mainExperienceDescription`)
- **Tipo**: Campo de texto (textarea)
- **Requerido**: ❌ No (Opcional)
- **Caracteres máximos**: 256
- **Descripción**: Texto descriptivo que proporciona información adicional sobre la experiencia
- **Ejemplo**: "Una experiencia única que combina música en vivo y gastronomía local"

### Dominio del Sitio (`domain`)
- **Tipo**: Campo de texto
- **Requerido**: ✅ Sí
- **Descripción**: Subdominio personalizado para el sitio
- **Formato**: Solo el nombre del subdominio (sin http://, https://, o extensión)
- **Ejemplo**: Si ingresas "mi-sitio", la URL será: `https://mi-sitio.{SITE_DOMAIN}`
- **Nota**: Este campo no se puede modificar después de crear el sitio

---

## 🖼️ Configuración de Imágenes del Sitio

> **⚠️ Importante**: Las imágenes configuradas en el sitio personalizado se utilizan como respaldo. En el **checkout** y en **páginas específicas que usen una experiencia**, se utilizarán las imágenes configuradas en la experiencia individual (banner desktop/mobile). Si una página usa una experiencia específica y el layout requiere imagen, se usarán las imágenes de la experiencia. Caso contrario, se usarán las imágenes del sitio.

### Imagen Desktop (`mainExperienceImg`)

### Imagen Desktop (`mainExperienceImg`)
- **Ubicación**: Imagen por defecto del sitio para dispositivos de escritorio
- **Requerido**: ✅ Sí
- **Formatos aceptados**: PNG, JPG
- **Tamaño máximo**: 2 MB (2,000,000 bytes)
- **Cantidad máxima**: 1 imagen
- **Proporción recomendada**: 2:1 (ancho:alto)
- **Zona segura**: 400 x 200 píxeles en el centro de la imagen
- **Uso**: Se muestra en las páginas de Tickets, Transferencias y Créditos en dispositivos desktop cuando no hay una experiencia específica asociada o cuando el layout no requiere imágenes de experiencia
- **Nota**: Si una página usa una experiencia específica y el layout requiere imagen, se usarán las imágenes de la experiencia (banner desktop/mobile de la experiencia)

#### Ejemplo de dimensiones recomendadas:
- **Mínimo recomendado**: 800 x 400 píxeles
- **Óptimo**: 1200 x 600 píxeles o superior
- **Zona segura**: Mantener contenido importante dentro de un área de 400 x 200 píxeles centrada

### Imagen Mobile (`mobileExperienceImg`)
- **Ubicación**: Imagen por defecto del sitio para dispositivos móviles
- **Requerido**: ✅ Sí
- **Formatos aceptados**: PNG, JPG
- **Tamaño máximo**: 2 MB (2,000,000 bytes)
- **Cantidad máxima**: 1 imagen
- **Proporción recomendada**: 2:1 (ancho:alto)
- **Zona segura**: 350 x 250 píxeles en el centro de la imagen
- **Uso**: Se muestra en las páginas de Tickets, Transferencias y Créditos en dispositivos móviles cuando no hay una experiencia específica asociada o cuando el layout no requiere imágenes de experiencia
- **Nota**: Si una página usa una experiencia específica y el layout requiere imagen, se usarán las imágenes de la experiencia (banner desktop/mobile de la experiencia)

#### Ejemplo de dimensiones recomendadas:
- **Mínimo recomendado**: 700 x 500 píxeles
- **Óptimo**: 1000 x 500 píxeles o superior
- **Zona segura**: Mantener contenido importante dentro de un área de 350 x 250 píxeles centrada

### Recomendaciones Generales para Imágenes:
1. **Calidad**: Usar imágenes de alta resolución para evitar pixelación
2. **Peso**: Optimizar imágenes antes de subirlas para mantener el tamaño bajo 2 MB
3. **Contenido**: Asegurar que el contenido importante esté dentro de la zona segura
4. **Formato**: Preferir PNG para imágenes con transparencia, JPG para fotografías
5. **Aspecto**: Respetar la proporción 2:1 para evitar recortes o distorsiones

---

## 🎨 Personalización de Colores

### Color Principal (`hexaBaseColor`)
- **Tipo**: Selector de color (color picker)
- **Requerido**: ❌ No (Opcional)
- **Valor por defecto**: `#000000` (Negro)
- **Formato**: Hexadecimal (#RRGGBB)
- **Descripción**: Color principal que se utiliza en elementos clave del sitio
- **Uso**: Afecta botones, enlaces, elementos destacados y otros componentes principales de la interfaz
- **Ejemplos**:
  - Azul: `#0066CC`
  - Verde: `#00AA44`
  - Rojo: `#CC0000`
  - Morado: `#6600CC`

### Color de Navbar y Footer (`hexaNavbarColor`)
- **Tipo**: Selector de color (color picker)
- **Requerido**: ❌ No (Opcional)
- **Valor por defecto**: `#0C0113` (Azul oscuro/negro)
- **Formato**: Hexadecimal (#RRGGBB)
- **Descripción**: Color utilizado en la barra de navegación superior y el pie de página
- **Uso**: Define el color de fondo de la barra de navegación y el footer del sitio
- **Recomendación**: Usar colores oscuros para mejorar la legibilidad del texto blanco típicamente usado en estos elementos

### Recomendaciones para Colores:
1. **Contraste**: Asegurar suficiente contraste entre colores de texto y fondo para legibilidad
2. **Accesibilidad**: Seguir las pautas WCAG para contraste mínimo (4.5:1 para texto normal)
3. **Consistencia**: Mantener una paleta de colores coherente en todo el sitio
4. **Pruebas**: Verificar cómo se ven los colores en diferentes dispositivos y condiciones de luz

---

## ⚙️ Otros Campos Configurables del Sitio

### Código de País (`defaultCountryCode`)
- **Tipo**: Selector (dropdown)
- **Requerido**: ✅ Sí
- **Descripción**: Código de país por defecto para el sitio
- **Uso**: Define la configuración regional del sitio (moneda, formato de fecha, etc.)

### Canal de Distribución (`salesGateway`)
- **Tipo**: Selector (dropdown)
- **Requerido**: ✅ Sí
- **Descripción**: Canal de venta asociado al sitio
- **Nota**: No se puede modificar después de crear el sitio

### Tema (`theme`)
- **Tipo**: Selector (dropdown)
- **Requerido**: ✅ Sí
- **Descripción**: Tema visual predefinido del sitio
- **Nota**: Al seleccionar un tema, se pueden aplicar colores por defecto automáticamente
- **Nota**: No se puede modificar después de crear el sitio

### Orientación del Sitio (`layout`)
- **Tipo**: Toggle (interruptor)
- **Requerido**: ✅ Sí
- **Opciones**:
  - **Vertical**: Diseño con scroll vertical
  - **Horizontal**: Diseño con scroll horizontal
- **Valor por defecto**: `HORIZONTAL`

### Estado del Sitio (`isActive`)
- **Tipo**: Checkbox
- **Requerido**: ✅ Sí
- **Valor por defecto**: `true` (Activo)
- **Descripción**: Activa o desactiva la disponibilidad del sitio

### Compra como Invitado (`publicCheckout`)
- **Tipo**: Checkbox
- **Requerido**: ✅ Sí
- **Valor por defecto**: `false` (Desactivado)
- **Descripción**: Permite que los usuarios realicen compras sin necesidad de autenticarse
- **Funcionalidad**: Los usuarios pueden reservar asientos y crear órdenes sin crear una cuenta

### Facebook Pixel ID (`metaPixelId`)
- **Tipo**: Campo numérico
- **Requerido**: ❌ No (Opcional)
- **Descripción**: ID del pixel de Facebook para seguimiento y publicidad
- **Uso**: Permite rastrear conversiones y crear audiencias para campañas publicitarias

### Sala de Espera (`waitingRoomEnable`)
- **Tipo**: Toggle (interruptor)
- **Requerido**: ❌ No (Opcional)
- **Disponible**: Solo para Super Administradores
- **Descripción**: Habilita o deshabilita la sala de espera para el sitio
- **Nota**: Solo puede haber una sala de espera activa a la vez. Si se activa para un sitio, se desactiva automáticamente en otros sitios que la tengan activa.

---

## 🎯 Configuraciones Visuales desde la Experiencia

Además de las configuraciones del sitio personalizado, puedes configurar elementos visuales directamente desde la creación o edición de cada experiencia individual. Estas configuraciones tienen prioridad sobre las del sitio cuando una página específica usa una experiencia.

### 📸 Imágenes Configurables en la Experiencia

Las siguientes imágenes se configuran en cada experiencia y se utilizan en el checkout y en páginas específicas que usen una experiencia.

#### Banner Desktop (`banner`)
- **Ubicación**: Banner principal de la experiencia para dispositivos desktop
- **Requerido**: ❌ No (Opcional)
- **Formatos aceptados**: PNG, JPG
- **Cantidad máxima**: 1 imagen
- **Proporción recomendada**: 2:1 (ancho:alto)
- **Zona segura**: 400 x 200 píxeles en el centro de la imagen
- **Uso**: Se muestra en el checkout y en páginas específicas que usen esta experiencia
- **Cuándo se usa**: Cuando una página usa una experiencia específica y el layout requiere imagen, se prioriza esta imagen sobre la del sitio

#### Banner Mobile (`bannerMobile`)
- **Ubicación**: Banner principal de la experiencia para dispositivos móviles
- **Requerido**: ❌ No (Opcional)
- **Formatos aceptados**: PNG, JPG
- **Cantidad máxima**: 1 imagen
- **Proporción recomendada**: 2:1 (ancho:alto)
- **Zona segura**: 350 x 250 píxeles en el centro de la imagen
- **Uso**: Se muestra en el checkout y en páginas específicas que usen esta experiencia
- **Cuándo se usa**: Cuando una página usa una experiencia específica y el layout requiere imagen, se prioriza esta imagen sobre la del sitio

#### Imagen de Entradas Agotadas (`soldOutImg`)
- **Ubicación**: Imagen que se muestra cuando las entradas están agotadas
- **Requerido**: ❌ No (Opcional)
- **Formatos aceptados**: PNG, JPG
- **Cantidad máxima**: 1 imagen
- **Proporción recomendada**: 25:20 (ancho:alto)
- **Uso**: Se muestra cuando todas las entradas de la experiencia están agotadas
- **Ejemplo de dimensiones recomendadas**: 300 x 240 píxeles o proporciones similares

#### Imagen del Mapa (`mapImg`)
- **Ubicación**: Imagen que representa el mapa o ubicación de la experiencia
- **Requerido**: ❌ No (Opcional)
- **Formatos aceptados**: PNG, JPG
- **Cantidad máxima**: 1 imagen
- **Proporción recomendada**: 25:20 (ancho:alto)
- **Uso**: Se muestra en las páginas donde se requiere mostrar la ubicación de la experiencia
- **Ejemplo de dimensiones recomendadas**: 300 x 240 píxeles o proporciones similares
- **Nota**: Esta imagen se usa cuando está habilitada la opción "Mostrar mapa en checkout"

#### Publicidad (`customAd`)
- **Ubicación**: Imagen publicitaria que aparece en el paso 2 del checkout
- **Requerido**: ❌ No (Opcional)
- **Formatos aceptados**: PNG, JPG
- **Tamaño máximo**: 1 MB (1,000,000 bytes)
- **Cantidad máxima**: 1 imagen
- **Dimensiones recomendadas**: 780 × 130 píxeles
- **URL requerida**: Debe incluir una URL de destino (mínimo 2 caracteres, máximo 100 caracteres)
- **Uso**: Se muestra en el paso 2 del proceso de checkout como banner publicitario
- **Ejemplo de URL**: `https://www.ejemplo.com/promocion`

#### QR Logo (`qrCodeLogo`)
- **Ubicación**: Logo que aparece dentro del código QR de los tickets
- **Requerido**: ❌ No (Opcional)
- **Formatos aceptados**: PNG, JPG
- **Tamaño máximo**: 100 KB (102,400 bytes)
- **Cantidad máxima**: 1 imagen
- **Proporción recomendada**: 1:1 (cuadrado)
- **Zona segura**: 80 x 80 píxeles en el centro de la imagen
- **Uso**: Se muestra como logo superpuesto en el centro del código QR de los tickets impresos
- **Ejemplo de dimensiones recomendadas**: 
  - **Mínimo recomendado**: 160 x 160 píxeles
  - **Óptimo**: 200 x 200 píxeles o superior
  - **Zona segura**: Mantener logo dentro de un área de 80 x 80 píxeles centrada

### 🎨 Campos Visuales Configurables

#### Campaña (`campaignHome`)
- **Tipo**: Editor de texto enriquecido
- **Requerido**: ❌ No (Opcional, pero si se habilita requiere contenido)
- **Caracteres mínimos**: 1 (cuando está habilitado)
- **Descripción**: Contenido de texto que se muestra en el checkout paso 1
- **Uso**: Permite agregar información promocional, términos especiales, o mensajes importantes que aparecen en la primera pantalla del checkout
- **Formato**: Editor de texto que permite formato HTML
- **Ejemplo**: "¡Oferta especial! Compra antes del 31 de diciembre y obtén un 20% de descuento"

#### Campos Personalizados (`record`)
- **Tipo**: Configuración de campos de formulario
- **Requerido**: ❌ No (Opcional)
- **Descripción**: Permite solicitar datos adicionales a los compradores antes del pago
- **Configuración**:
  - **Tipo de campo**: Puede ser por ORDEN (se solicita una vez por orden) o por TICKET (se solicita para cada ticket)
  - **Campos disponibles**: Hasta 3 campos personalizados por experiencia
  - **Propiedades de cada campo**:
    - **Label**: Etiqueta del campo (obligatorio)
    - **Placeholder**: Texto de ayuda que aparece en el campo (opcional)
    - **Required**: Si el campo es obligatorio o no
- **Uso**: Útil para solicitar información como número de identificación, preferencias especiales, o cualquier dato relevante para la experiencia
- **Ejemplo**: Campo "Número de identificación" con placeholder "Ingresa tu DUI con guion" marcado como requerido

#### Cuotas (`installments`)
- **Tipo**: Configuración de opciones de pago
- **Requerido**: ❌ No (Opcional)
- **Disponible**: Solo para Super Administradores y países diferentes a Venezuela
- **Descripción**: Permite habilitar el pago por cuotas para las compras de la experiencia
- **Configuración cuando está habilitado**:
  - **Cuotas disponibles**: Selección múltiple de tipos de cuotas (ej: 3, 6, 12 meses)
  - **Bancos**: Selección múltiple de bancos que ofrecen las cuotas (requerido al menos 1 banco)
- **Requisitos**: 
  - Si está habilitado, requiere al menos 1 tipo de cuota seleccionado
  - Si está habilitado, requiere al menos 1 banco seleccionado
- **Uso**: Permite a los clientes pagar en múltiples cuotas en lugar de un pago único
- **Nota**: Solo disponible para ciertos países y requiere configuración bancaria previa

#### Identificación (`identification`)
- **Tipo**: Configuración de validación de identificación
- **Requerido**: ❌ No (Opcional)
- **Disponible**: Solo para países diferentes a Venezuela
- **Descripción**: Permite configurar un campo de identificación con validación específica
- **Configuración cuando está habilitado**:
  - **Título del campo**: Texto que aparece como etiqueta (requerido, mínimo 1 carácter)
  - **Placeholder del campo**: Texto de ayuda (requerido, mínimo 1 carácter)
  - **Validación**: Tipo de validación según el país (requerido)
    - Ejemplos: DUI, NIT, Pasaporte, etc.
  - **Límite de tickets**: Número máximo de tickets que se pueden comprar con la misma identificación (requerido, puede ser 0 para sin límite)
- **Uso**: Permite validar y limitar compras por identificación, útil para prevenir compras múltiples o controlar acceso
- **Ejemplo**: 
  - Título: "Número de DUI"
  - Placeholder: "Ingresa el DUI con guion"
  - Validación: "DUI"
  - Límite: 4 tickets por identificación

#### Mostrar Mapa en Checkout (`renderMapSite`)
- **Tipo**: Toggle (interruptor)
- **Requerido**: ❌ No (Opcional)
- **Valor por defecto**: `false` (Deshabilitado)
- **Descripción**: Habilita la visualización del mapa en la página de checkout
- **⚠️ Advertencia importante**: 
  - **NO habilitar esta opción para eventos masivos**, ya que puede bloquear el sistema de asientos
  - Esta función **NO estará habilitada** cuando el checkout contenga más de una localidad seleccionada
- **Uso**: Muestra la imagen del mapa (`mapImg`) en el proceso de checkout para ayudar a los usuarios a visualizar la ubicación
- **Recomendación**: Usar solo para eventos pequeños o medianos con una sola localidad

---

## 📊 Resumen de Límites

### Límites del Sitio Personalizado

| Campo | Tipo | Requerido | Mínimo | Máximo |
|-------|------|-----------|--------|--------|
| Nombre de Experiencia | Texto | ✅ | 1 carácter | 256 caracteres |
| Nombre de Tour | Texto | ❌ | - | 256 caracteres |
| Descripción | Textarea | ❌ | - | 256 caracteres |
| Imagen Desktop | Imagen | ✅ | - | 2 MB |
| Imagen Mobile | Imagen | ✅ | - | 2 MB |

### Límites de Imágenes de la Experiencia

| Campo | Tipo | Requerido | Tamaño Máximo | Cantidad |
|-------|------|-----------|---------------|----------|
| Banner Desktop | Imagen | ❌ | - | 1 imagen |
| Banner Mobile | Imagen | ❌ | - | 1 imagen |
| Imagen Entradas Agotadas | Imagen | ❌ | - | 1 imagen |
| Imagen del Mapa | Imagen | ❌ | - | 1 imagen |
| Publicidad | Imagen | ❌ | 1 MB | 1 imagen |
| QR Logo | Imagen | ❌ | 100 KB | 1 imagen |

### Límites de Campos de la Experiencia

| Campo | Tipo | Requerido | Mínimo | Máximo |
|-------|------|-----------|--------|--------|
| Campaña | Texto enriquecido | ❌* | 1 carácter* | - |
| Campos Personalizados | Campos | ❌ | - | 3 campos |
| URL Publicidad | URL | ✅** | 2 caracteres | 100 caracteres |
| Título Identificación | Texto | ✅*** | 1 carácter | - |
| Placeholder Identificación | Texto | ✅*** | 1 carácter | - |
| Límite Tickets por ID | Número | ✅*** | 0 | - |

\* Requerido solo si se habilita la campaña  
\** Requerido solo si se sube una imagen de publicidad  
\*** Requerido solo si se habilita la identificación

---

## 🔍 Validaciones Importantes

### Validaciones del Sitio
1. **Campos requeridos**: Los campos marcados como requeridos deben completarse antes de guardar
2. **Límites de caracteres**: El sistema validará automáticamente que no se excedan los límites
3. **Tamaño de imágenes**: Las imágenes que excedan 2 MB no se podrán subir
4. **Formato de imágenes**: Solo se aceptan archivos PNG y JPG
5. **Dominio único**: Cada dominio debe ser único en el sistema
6. **Campos bloqueados**: Algunos campos (dominio, canal de distribución, tema) no se pueden modificar después de crear el sitio

### Validaciones de la Experiencia
1. **Imágenes**: 
   - Publicidad: máximo 1 MB
   - QR Logo: máximo 100 KB
   - Otras imágenes: sin límite de tamaño específico, pero se recomienda optimizar
2. **Campos personalizados**: Máximo 3 campos por experiencia
3. **Cuotas**: Si está habilitado, requiere al menos 1 tipo de cuota y 1 banco seleccionado
4. **Identificación**: Si está habilitado, requiere título, placeholder, validación y límite
5. **URL de publicidad**: Requerida si se sube una imagen de publicidad, debe tener entre 2 y 100 caracteres
6. **Mostrar mapa**: No funciona con múltiples localidades en el checkout

---

## 💡 Consejos de Diseño

### Para el Sitio Personalizado

1. **Imágenes**:
   - Usa imágenes de alta calidad y optimizadas
   - Mantén el contenido importante dentro de las zonas seguras
   - Prueba cómo se ven las imágenes en diferentes dispositivos
   - Recuerda que estas imágenes son respaldo; las de la experiencia tienen prioridad

2. **Colores**:
   - Elige colores que reflejen la identidad de tu marca
   - Asegúrate de que haya suficiente contraste para la legibilidad
   - Considera cómo se verán los colores en diferentes pantallas

3. **Texto**:
   - Usa nombres descriptivos pero concisos
   - Aprovecha la descripción para proporcionar información clave
   - Mantén la coherencia en el tono y estilo del texto

### Para las Experiencias

1. **Banners Desktop/Mobile**:
   - Configura banners específicos para cada experiencia cuando quieras personalización individual
   - Usa las mismas proporciones y zonas seguras que las imágenes del sitio
   - Estas imágenes tienen prioridad sobre las del sitio cuando hay una experiencia asociada

2. **Imagen de Entradas Agotadas**:
   - Crea una imagen atractiva que comunique claramente que las entradas están agotadas
   - Considera incluir información sobre próximos eventos o lista de espera

3. **Imagen del Mapa**:
   - Usa un mapa claro y legible
   - Asegúrate de que la ubicación sea fácilmente identificable
   - Considera incluir puntos de referencia cercanos

4. **Publicidad**:
   - Optimiza la imagen a 780 × 130 píxeles para mejor rendimiento
   - Mantén el tamaño bajo 1 MB para carga rápida
   - Asegúrate de que el mensaje sea claro y conciso
   - Verifica que la URL de destino funcione correctamente

5. **QR Logo**:
   - Usa un logo simple y reconocible
   - Mantén el tamaño pequeño (máximo 100 KB) para no afectar la legibilidad del QR
   - Asegúrate de que el logo tenga buen contraste con el código QR
   - Prueba que el QR siga siendo escaneable con el logo superpuesto

6. **Campaña**:
   - Usa texto claro y directo
   - Destaca ofertas o información importante
   - Mantén el mensaje conciso para no abrumar al usuario

7. **Campos Personalizados**:
   - Solicita solo información realmente necesaria
   - Usa placeholders descriptivos que guíen al usuario
   - Marca como requeridos solo los campos esenciales

---

## 📞 Soporte

Para más información o asistencia con la configuración de tu sitio personalizado, contacta al equipo de soporte.

---

## 🔄 Prioridad de Imágenes

Para entender cuándo se usan las imágenes del sitio vs las de la experiencia:

1. **En páginas de checkout**: 
   - Siempre se usan las imágenes de la experiencia (banner desktop/mobile) si están configuradas
   - Si no hay imágenes en la experiencia, se usan las del sitio

2. **En páginas específicas que usen una experiencia**:
   - Si la página usa una experiencia específica y el layout requiere imagen → **Se usan las imágenes de la experiencia**
   - Si no hay experiencia asociada o el layout no requiere imagen de experiencia → **Se usan las imágenes del sitio**

3. **Regla general**:
   - Las imágenes de la experiencia tienen **prioridad** sobre las del sitio
   - Las imágenes del sitio funcionan como **respaldo** cuando no hay configuración en la experiencia
   - El banner desktop y mobile de la experiencia se usan en **checkout** y en **páginas específicas que usen una experiencia**

---

**Última actualización**: Basado en la versión actual del código del sistema de administración.

