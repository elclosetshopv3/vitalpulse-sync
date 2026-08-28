# VitalPulse Sync — estado del proyecto

_Actualizado: 28 de agosto de 2026_

## Qué es este archivo

`VitalPulse-Sync.html` es el prototipo funcional actual de la app — un solo archivo que corre directo en el navegador (HTML + CSS + JavaScript, sin instalación). Es la versión más reciente de todo lo que hemos revisado y ajustado juntos: pantallas de Inicio, Registrar, Historia, Medicamentos, Reportes y Perfil, con la lógica clínica de alertas (PA, FC, Glucosa en ayunas/posprandial, medicamentos), el diseño condicional según diagnóstico (HTA/Diabetes), las gráficas con ejes y bandas de referencia, y el envío de reportes por WhatsApp/Email.

También debería poder verse y probarse en vivo aquí (se actualiza cada vez que hacemos un cambio):
**https://claude.ai/code/artifact/1b6e48fd-c635-4521-ba75-c047f11cb803**

⚠️ **Ese enlace quedó desactualizado el 27 de agosto.** El entorno de esa sesión de trabajo tenía bloqueado el acceso de red al servicio que actualiza ese enlace, así que no pudimos republicar ahí — el archivo `VitalPulse-Sync.html` en esta carpeta SÍ tiene todo lo último, el enlace en vivo no. Si en la próxima sesión ese acceso de red ya funciona, hay que republicar ahí; si no, seguimos usando el archivo de esta carpeta (o el que Claude te comparta directo en el chat) para revisar los cambios.

Cada vez que ajustamos algo en una sesión de trabajo, se guarda la versión más reciente de `VitalPulse-Sync.html` directamente aquí en esta carpeta, para que no se pierda aunque termine la sesión de Claude.

## Qué tan "real" es esto

Este archivo es un **prototipo de diseño y flujo clínico**, no la app final:

- Corre solo en el navegador. No hay servidor, base de datos ni cuentas de usuario reales.
- Guarda tus datos en la memoria del navegador (localStorage) para que no se pierdan al refrescar la página — pero eso es *local a ese navegador/computadora*, no se sincroniza entre dispositivos ni se respalda en la nube.
- Sirve exactamente para lo que hemos estado haciendo: validar contigo, como médico, que la lógica clínica, las alertas y los flujos de pantalla tengan sentido antes de invertir en construir la app real.

## Qué se hizo en la sesión del 27 de agosto

- **Glucosa en ayunas vs. posprandial**, implementada de punta a punta: se captura el contexto real en Registrar (ya no es una etiqueta fija), y se refleja en el medidor, en Dashboard, en Historia (con selector de contexto y promedio separado), y en Reportes (dos promedios independientes, tabla detallada con dos columnas), aunque las lecturas no sean consecutivas. Cada contexto tiene su propia meta editable en Perfil (ayunas y posprandial por separado).
- **Hora de la medición en Registrar**: se puede escribir directamente con teclado (antes solo se podía con los botones −/+), y se corrigió el bug donde el botón PM no cambiaba la hora mostrada.
- **Auditoría clínica completa** de toda la lógica (clasificación de PA/glucosa/FC, el medidor, las gráficas, los reportes, la persistencia): se encontraron y corrigieron 4 problemas — el más importante era que si la meta personal de glucosa se ponía por encima del umbral clínico fijo de "muy alta", una lectura dentro de la meta se clasificaba (al revés) como la categoría más grave. También se corrigió que el campo de hora perdía el foco al escribir, que la vista de familiar mostraba "0/0 — Normal" en el modo demo sin datos, y se limpian claves viejas del almacenamiento local.
- **Modo claro por defecto**: antes, si el celular/computadora tenía el modo oscuro del sistema activado, la app heredaba modo oscuro automáticamente sin que nadie lo hubiera elegido. Ahora siempre abre en modo claro/día; el botón 🌙 sigue ahí para cambiar a oscuro cuando se quiera.
- Antes de eso (sesión del 26 de agosto): se arregló que la pantalla brincara al tope al usar los +/- o al elegir WhatsApp/Email en Reportes, y se agregó que los datos sobrevivan a un refresh de la página (antes se perdían todos).

## Qué se hizo en la sesión del 28 de agosto

- **Arreglado: la etiqueta "Meta X" de la gráfica de glucosa en Historia ya no queda tapada.** Antes se dibujaba antes que la línea/punto de la lectura y ambas quedaban en el mismo borde derecho, así que la línea la tapaba al pintarse encima (reportado el 27 de agosto con captura de pantalla, "Meta 7.2" tapado). Ahora la etiqueta se dibuja después de la línea de datos, así que siempre queda visible encima.
- **Agregado: estimado de HbA1c**, calculado a partir del promedio de tus glucosas en ayunas y posprandial (fórmula estándar ADAG: HbA1c = (promedio_mg/dL + 46.7) / 28.7 — la misma que usan apps como mySugr para su "A1C estimada"). Se muestra en **Reportes** (semanal, mensual y semestral), junto a los promedios de glucosa en ayunas y posprandial, con una nota aclaratoria de que es un estimado y no reemplaza el examen de laboratorio. Decidí ponerlo ahí (en vez de en Perfil) porque Reportes ya es la pantalla donde ves tendencias y promedios período por período — HbA1c es justo ese tipo de dato, un resumen de varias semanas, no algo que se actualiza lectura por lectura como en Registrar/Dashboard.
- **Texto de ayuda en Dashboard más visible**: "Toca una tarjeta para actualizar" ahora se ve más oscuro y en negrita (antes era gris muy claro y pasaba desapercibido).
- **"Última toma" en Dashboard**: arriba de las tarjetas de Presión/Glucosa ahora dice "Última toma: hoy HH:MM" y debajo "Toca una tarjeta para actualizar" — como Presión, Glucosa y FC se registran juntas en un solo formulario (misma hora para las tres), la hora se muestra una sola vez arriba en vez de repetirla en cada tarjeta. Se quitó la línea duplicada "Última lectura hoy a las..." que estaba al final de la pantalla.
- Confirmado con Humberto que el enlace en vivo (`claude.ai/code/artifact/...`) sigue mostrando una versión vieja (de antes del 27 de agosto, sin ayunas/posprandial siquiera) — se le explicó que ese link no se puede actualizar por ahora y que use el archivo `VitalPulse-Sync.html` de esta carpeta.
- **Auditoría de Avisos (Familiar/Cuidador) y Reportes**, a pedido de Humberto. Se encontraron y corrigieron 4 problemas:
  1. El aviso de Glucosa en la pantalla de Familiar siempre se mostraba en amarillo (advertencia), sin importar qué tan grave fuera el valor — ahora usa el mismo criterio de severidad que Presión: rojo si es Hipoglicemia/Muy alta/CRISIS, amarillo si solo está "Alta", verde/normal si está en meta.
  2. Al cambiar de período en Reportes (Semanal → Mensual → 6 meses), el canal de WhatsApp/Email que se había elegido antes se quedaba "pegado" — si generabas el resumen de 6 meses después de haber elegido WhatsApp en el semanal, se abría WhatsApp de más. Ahora se limpia esa selección al cambiar de período, y además "Generar y Enviar a familiares" (6 meses) nunca abre WhatsApp/Email.
  3. En la pantalla de Familiar, dos avisos de ejemplo ("No registró Losartán AM" y "PA 118/76 — Normal") aparecían siempre, incluso en modo "Sin datos" donde no debería haber ningún aviso todavía. Ahora toda la lista de avisos respeta ese modo, con un mensaje de "Aún no hay avisos" cuando corresponde.
  4. La adherencia a medicamentos que mostraba cualquier reporte guardado (de hace 2 semanas, del mes pasado, de 6 meses) reflejaba las casillas de HOY en vez de la adherencia real de ese período — así que si desmarcabas una dosis hoy, hasta los reportes viejos "cambiaban". Ahora cada período tiene su propia cifra de adherencia fija (92% semanal, 88% mensual, 83% seis meses), que no cambia según lo que marques hoy.

## Búsqueda de financiamiento y trabajo — movida a su propia carpeta

El 28 de agosto exploramos cómo financiar VitalPulse Sync (SBIR, aceleradoras, recursos gratuitos en Miami) y cómo generar ingresos con la experiencia clínica de Humberto (redes de expertos, paneles médicos, asesorías) — a propósito de que es médico retirado, exiliado cubano, residente permanente, viviendo en Miami y necesitando ingresos.

**Para no mezclar esto con la app**, todo ese material (los resúmenes de una página en inglés/español, el perfil de experto, y el detalle completo de la investigación) se movió a su propia subcarpeta: **`Busqueda de Financiamiento y Trabajo/`** (dentro de esta misma carpeta VitalPulse Sync, con su propio README). Revisar ese README para el contexto completo y los próximos pasos de esa parte.

## Pendiente para la próxima sesión

- Intentar republicar el enlace en vivo (`claude.ai/code/artifact/...`) — puede que el acceso de red ya funcione en un entorno nuevo. Sigue bloqueado (probado de nuevo el 28 de agosto).
- Las 4 sugerencias descartadas de la comparación con otras apps (exportar PDF real, correlación síntomas↔lecturas, recordatorio de recarga de medicamentos, integración con glucómetros/tensiómetros Bluetooth) siguen fuera del alcance por ahora — Humberto prefirió mantener la app enfocada. Si más adelante cambia de opinión, la comparación completa está en el historial de la conversación del 27 de agosto.
- No quedó ningún bug ni corrección pendiente de las auditorías anteriores — todo lo reportado hasta el 28 de agosto (incluida la auditoría de Avisos y Reportes) está resuelto.
- Limitación conocida, no corregida (es de diseño, no un bug): las fechas de los reportes guardados en "Respaldo en la app" son fijas ("18–24 Ago 2026", etc.) y no se actualizan solas con el calendario real — es esperable en un prototipo sin backend, pero cuando se construya la app real esas fechas deben generarse dinámicamente.

## Sobre la carpeta `vitalpulse-sync/` que ya estaba aquí

Dentro de esta carpeta vas a encontrar otra subcarpeta (`vitalpulse-sync/vitalpulse/`) con un esqueleto de proyecto en React Native/Expo (pantallas .tsx, Supabase, etc.). Esa es una exploración técnica de una sesión anterior, **desactualizada** — no incluye nada de la lógica clínica, alertas ni pantallas que hemos construido y ajustado desde entonces en el prototipo HTML. Por ahora no la toques ni la uses como referencia; cuando lleguemos a la etapa de construir la app real, lo mejor es empezar ese código de nuevo usando el prototipo HTML actual como especificación, no partir de ese esqueleto viejo.

## El camino hacia Play Store / App Store

Para que quede claro qué falta entre "este prototipo" y "una app publicada":

1. **Una base de código de producción real.** Este HTML es ideal para diseñar y validar, pero no es lo que se sube a las tiendas. El camino más directo suele ser reconstruirla con un framework como **React Native (Expo)** o **Flutter**, que compila a apps nativas de iOS y Android desde un solo código fuente — usando este prototipo como el mapa exacto de cómo debe verse y comportarse.
2. **Un backend real.** Base de datos, autenticación de usuarios, y sincronización entre dispositivos (hoy los datos solo viven en un navegador). Para una app de salud, esto normalmente implica cumplir regulaciones de protección de datos de salud del país donde se publique (el equivalente a HIPAA en EE.UU., o la ley de protección de datos que aplique donde vivas).
3. **Cuentas de desarrollador.** Apple Developer Program (~US$99/año) y Google Play Console (~US$25 pago único), más pasar el proceso de revisión de cada tienda.
4. **Consideraciones regulatorias.** Mientras la app solo registre y muestre datos (como ahora), normalmente entra en la categoría de "bienestar/seguimiento personal". Si en el futuro empieza a dar recomendaciones diagnósticas o de tratamiento, hay que revisar si califica como software médico (SaMD) y qué regulación aplica — vale la pena consultarlo con un abogado especializado antes de esa etapa.
5. **Testing con usuarios reales** antes de publicar, idealmente con pacientes reales o al menos otros médicos, no solo contigo revisando el diseño.

**Recomendación:** sigamos usando este prototipo HTML para terminar de validar toda la lógica clínica y los flujos contigo — es rápido de ajustar y no cuesta nada construirlo así. Cuando sientas que el diseño y el comportamiento ya están donde los quieres, ese es el momento de buscar (o definir conmigo) el plan para la reconstrucción en una base de código de producción real, con alguien (o algún proceso) que se encargue de la parte de backend, seguridad y cumplimiento normativo.

### Plan concreto: profesional pero económico

Cuando llegue ese momento, esta es la ruta que planeamos seguir (discutida y acordada el 28 de agosto), pensada específicamente para mantenerlo profesional sin gastar de más:

1. **Una sola base de código: React Native con Expo**, no dos carpetas ni dos construcciones separadas para Android/iPhone. El servicio de compilación en la nube de Expo (EAS Build) permite generar la versión de iPhone sin necesitar comprar una Mac — un ahorro real de entrada. Además, como usa JavaScript, buena parte de la lógica clínica que ya está en este prototipo HTML (las funciones que clasifican presión, glucosa, frecuencia cardíaca, el cálculo de HbA1c, las metas por contexto) se puede reaprovechar casi tal cual, sin reinventarla.
2. **Backend en su nivel gratuito para empezar**: Supabase o Firebase, ambos con un nivel gratuito generoso, cobrando solo cuando el uso crece de verdad. Importante: ese nivel gratuito normalmente NO cumple por sí solo con regulaciones de datos de salud — si la app llega a manejar datos reales de pacientes (no solo de prueba), hay que subir a un nivel de pago con ese cumplimiento, y presupuestarlo.
3. **Cuentas de desarrollador — el único costo fijo obligatorio**: Apple Developer Program (aprox. US$99/año) y Google Play Console (aprox. US$25 pago único) — confirmar cifras exactas al momento de registrarse, porque pueden cambiar.
4. **Quién lo construye — la palanca de ahorro más grande**: como el prototipo ya está validado (pantallas, lógica clínica, flujos, diseño), contratar a alguien para "traducirlo" a React Native con una cotización cerrada basada en este prototipo como especificación exacta es mucho más barato que contratar un diseño desde cero. También podemos seguir avanzando juntos con este mismo flujo de trabajo para ir armando buena parte del código base, y reservar a un desarrollador humano solo para la revisión final de seguridad y la publicación.
5. **Probar antes de publicar, sin costo extra**: TestFlight (Apple) y las pruebas cerradas/internas de Google Play son gratuitas y están hechas justo para esto — probar con un grupo pequeño (tú, algún colega médico) antes de que esté disponible al público.
6. **Un solo gasto legal, puntual**: una consulta única (no una asesoría permanente) con un abogado especializado en protección de datos de salud del país donde se publique, antes de manejar datos reales de pacientes — para confirmar si aplica alguna regulación tipo HIPAA y si la app se mantiene como "seguimiento/bienestar" o cruza a "software médico" regulado.

Sobre publicar y luego corregir: una vez publicada, la app se puede seguir actualizando sin límite — Google Play revisa actualizaciones en horas o un día, Apple normalmente en 1 a 3 días (con revisión acelerada disponible para bugs urgentes). Publicar no es un punto sin retorno; lo único que amerita más cuidado con usuarios reales es probar bien cada actualización antes de subirla, no que sea imposible corregir después.
