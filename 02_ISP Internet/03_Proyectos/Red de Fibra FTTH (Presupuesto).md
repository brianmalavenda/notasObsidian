tipo:: #internet/proyectos 
tags::
links:: [[Soberanía de telecomunicaciones]]
links_externos:: [Tecnolibre](https://www.youtube.com/@tecnolibre.net.oficial) - [BWNfiber](https://www.bwnfiber.com/odn/)

La construcción de un ISP es importante en lo que refiere al control de privacidad y anonimidad de uso de internet. A pesar que un ISP local, pyme digamos, es pequeño en relacion a un gran distribuidor que es quien nos proveerá del servicio de internet y una cuota de su ancho de banda, a través de nuestra primer "puerta de enlace" adquiriremos los conocimientos en la practica apuntando al control soberano del internet del país en un gran cortafuegos en los puntos de acceso de internet en las fronteras.

Acá presupuestaremos la infraestructura, mano de obra y gastos legales aproximados para poder armar un proveedor local de internet de fibra óptica. Los costos son estimados en dólares al momento de hacer el presupuesto. Estos precios están revisados en el mercado local y productos extranjeros que se pueden importar en el mes de junio de 2025.

### Diagrama conceptual básico

Unidad de conexión por CUADBoc tiene 40 usuarios, En un N tenemos 2 de esta y en los C tenemos 40 en cada uno y podemos llegar a 3, es decir quedaría en N=80 usuarios y en 3C=120 usuarios > TOTAL 200 usuarios. ^proyecto-inicial

hay que tener en cuenta la [[Tasa de transmisión]] para medir cuanta potencia de fibra llega a cada uno.

![[unidad_minima_conexion_odn.png]]

Esto será en una división en **tres etapas físicas** muy bien definidas, lo que simplifica la gestión y el mantenimiento.

**Capacidad y Escalabilidad:**

- **Por cada línea de la MUFA:** 1 fibra → NAP1 (1:8) = 8 fibras → 6 de ellas a NAP2 (6 x 1:8) = **48 fibras finales (clientes)**.
- **Total con 5 líneas activas en la MUFA:** 5 líneas x 48 clientes = **240 clientes**.
- **Capacidad Máxima con Reservas:** La MUFA tiene 3 puertos de sobra (8 totales). Cada NAP1 tiene 2 puertos de sobra (8 totales). La capacidad máxima absoluta es de 8 (MUFA) x 8 (NAP1) x 8 (NAP2) = **512 clientes**.
    
**Ventajas de este diseño:**
1. **Gestión de Fallos:** Si un cable troncal a un NAP1 se daña, se puede parchar la red activando una de las fibras de reserva de la MUFA y reconectando el NAP1 afectado a ella.
2. **Expansión Sencilla:** Agregar nuevos clientes en una zona ya cubierta por un NAP2 es tan simple conectar una nueva fibra drop a uno de sus puertos libres. Para expandirse a una nueva urbanización, se activa una de las líneas de reserva de la MUFA y se despliega un nuevo par (NAP1 + NAP2s).
3. **Organización:** La red está físicamente segmentada, lo que facilita localizar y aislar fallas usando un OTDR.

![[tabla_presupuesto_odn_1N-3C.png]]

1. La OLT envía la señal a través de un **cable de 64 fibras** hacia la **MUFA**.
2. En la MUFA, una de las fibras se conecta a un **Splitter 1:8** (ej., Puerto #1).
3. La salida de este splitter es un cable de 8 fibras que viaja hasta un **NAP de 1er Nivel** (ej., NAP1-1).
4. Dentro del NAP1-1, **6 de las 8 fibras** se conectan a cables que van hacia **6 NAPs de 2do Nivel**. Las 2 fibras restantes quedan en reserva.
5. Cada **NAP de 2do Nivel** es un punto de empalme pasivo donde cada una de sus **8 fibras** se conecta directamente a un **Cable de Acometida (Drop Cable)**.
6. El Drop Cable llega a la casa del usuario y se termina en una **Roseta Óptica**.
7. Un cordón de patcheo óptico conecta la roseta a la **ONT/ONU**, que convierte la señal óptica en Ethernet/Wi-Fi para el usuario.
### Diagrama de conexión

![[descripcion_grafica_OLT-ODN-ONT.png]]

![[red_ffth_01_diagrama.png]]

**A. Herramientas y Equipos de Medición (Inversión Inicial o Alquiler)**

1.      **Fusionadora de Fibra Óptica (Splicer):**

o    Costo Estimado: $1,500 - $7,000 (Compra). Alquiler puede ser una opción inicial.

§  _Presupuesto (Compra de una básica/media):_ **$2,500**

2.      **OTDR (Optical Time Domain Reflectometer):**

o    Costo Estimado: $1,000 - $8,000 (Compra). Alquiler puede ser una opción.

§  _Presupuesto (Compra de uno básico/medio):_ **$2,000**

3.      **Medidor de Potencia Óptica (OPM) y Fuente de Luz (OLS):**

o    Costo Estimado: $100 - $500 por el kit.

§  _Presupuesto:_ **$300**

4.      **Cortadora de Precisión (Cleaver), Peladoras, Herramientas Menores:**

o    Costo Estimado: $100 - $400.

§  _Presupuesto:_ **$250**

**Subtotal D. Herramientas y Equipos de Medición: $5,050**

---

**B. Instalación y Mano de Obra 
1.      **Despliegue de ODN (Tendido de Fibra Troncal y NAPs):**

o    Descripción: Costo de instalar postes (si es aéreo y no alquilas espacio), tender el cable, instalar NAPs, fusionar fibras.

o    Costo Estimado: MUY variable. Puede ir desde $2,000 hasta $20,000+ dependiendo de la complejidad, si es aéreo/subterráneo, si contratas cuadrillas especializadas.

§  _Presupuesto (Estimación conservadora para aéreo y mano de obra local):_ **$8,000**

2.      **Instalación por Abonado:**

o    Descripción: Tender acometida, instalar roseta óptica, fusionar/conectar, configurar ONT.

o    Costo Estimado: $30 - $100 por cliente (depende del tiempo y materiales).

§  _Presupuesto (200 clientes * $50):_ **$10,000**

**Subtotal E. Instalación y Mano de Obra: $18,000**

---

**C. Software de Gestión Comercial y Facturación (Billing & CRM)**

·         Descripción: Para gestionar clientes, planes, facturación, cobros, soporte.

·         Costo Estimado: Desde $0 (Wispro tiene planes iniciales gratuitos o muy económicos para pocos usuarios, u otras opciones open-source) hasta $500+ mensuales para soluciones más avanzadas.

o    _Presupuesto Inicial (Software o desarrollo básico):_ **$500** (puede ser un pago único por una licencia básica o configuración inicial)

**Subtotal F. Software: $500**

---

**D. Costos Administrativos y Regulatorios**

1.      **Licencia ISP y Registro en ENACOM:**

o    Descripción: Necesitas obtener la "Licencia Única Argentina Digital" y registrarte como proveedor de servicios de internet. Hay aranceles y posiblemente costos de gestoría.

o    Costo Estimado: Variable, podría rondar los $200 - $1,000 entre aranceles y gestoría inicial.

§  _Presupuesto:_ **$500**

2.      **Permisos Municipales (si aplica):**

o    Descripción: Para tendido de cable en vía pública.

o    Costo Estimado: Muy variable.

§  _Presupuesto:_ **$500** (placeholder, investigar localmente)

**Subtotal G. Administrativos y Regulatorios: $1,000**

---

**Presupuesto Total Estimado de Inversión Inicial (CAPEX)**

·         A. Herramientas y Equipos de Medición: $5,050
·         B. Instalación y Mano de Obra: $18,000
·         C. Software: $500
·         D. Administrativos y Regulatorios: $1,000

**TOTAL EQUIPOS: $60,000 USD**
**TOTAL HERRAMIENTAS Y MANO DE OBRA: $30,000 USD**

**TOTAL PROYECTO DE GASTO TOTAL CON TODO INSTALADO PARA 200 USUARIOS = 90,000 USD** a precio de dólar actual son 120,5 millones. Con 200 usuarios sería un costo por usuario de 602.500 pesos. ^ftth-inversion-costo

El precio de internet por cada usuario es de 40 lucas masomenos. Podemos estimarlo a 30.000 es decir con 200 usuarios activos son 6 millones de pesos mensualmente. Es decir en 20 meses se recupera la inversión inicial con 200 activos. Eso es 1 año y medio casi. ^ftth-retornoinversion

Si podemos ampliar esto a la capacidad de 400 porque disponemos de un total de 512 pero dejamos una tasa de recuperacion de un 21% por posibles fallos futuros. Esto duplicaría el tiempo de retorno de la inversión inicial. **CON 400 USUARIOS EN 8 MESES SE RECUPERARÍA LA INVERSION INICIAL** ^ftth-inversionampliada

A una tasa de recuperacion del 10%, la cual seguiría siendo una red de alta resiliencia, sería unos 450 usuarios. Es decir, por mes tendríamos un flujo de capital desde los usuarios de 13,5 millones de dólares. Es decir, el costo por usuario se iría a 270.000 pesos. Es casi un tercio del costo por usuario con 200 usuarios. ^ftth-ganancia


#### Notas

Hay que tener en cuenta mantenimiento, como calcular eso. Debemos ver cada cuanto se revisa el  estado de la conexión, estado de los cables.

**Aclaración Importante sobre la Capacidad GPON:** Un puerto GPON estándar tiene una capacidad máxima de **2.488 Gbps downstream (bajada) y 1.244 Gbps upstream (subida)**. Esta capacidad se comparte entre todos los usuarios conectados a ese puerto (hasta 64). Si ofrecemos planes de 1 Gbps, es crucial entender que no todos los usuarios de un mismo puerto podrán consumir 1 Gbps simultáneamente. Aquí entra en juego el concepto de **sobresuscripción (over-subscription ratio)**, que es normal en los ISP. Estimaremos cuánta capacidad total de Tránsito IP necesitamos basándonos en el uso real promedio y los picos de tus 200 clientes.


![[presupuesto_optico_redes_balanceadas.png]]