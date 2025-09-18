# 📘 Guía de Tipos de Datos en MySQL  

## 1. Tipos Numéricos  

### 🔹 Enteros
| Tipo       | Tamaño (bytes) | Rango (con signo) | Rango (sin signo) | Uso recomendado |
|------------|----------------|--------------------|-------------------|-----------------|
| `TINYINT`  | 1              | -128 a 127         | 0 a 255           | Estados lógicos, flags, edades pequeñas, categorías. |
| `SMALLINT` | 2              | -32,768 a 32,767   | 0 a 65,535        | Contadores medianos (ej. inventarios). |
| `MEDIUMINT`| 3              | -8M a 8M           | 0 a 16M           | IDs de tablas medianas, más eficiente que `INT`. |
| `INT`      | 4              | -2B a 2B           | 0 a 4B            | Valores numéricos estándar, claves primarias. |
| `BIGINT`   | 8              | -9 quintillones    | 0 a 18 quintillones| Grandes volúmenes (transacciones, identificadores globales). |

**Recomendación**  
- Usar el más pequeño posible según el rango necesario → mejor rendimiento y menor almacenamiento.  
- Evitar `BIGINT` salvo necesidad real (bases de datos masivas).  

---

### 🔹 Decimales y punto flotante
| Tipo       | Tamaño | Precisión | Uso recomendado |
|------------|--------|-----------|-----------------|
| `DECIMAL(M,D)` | Variable, depende de M | Preciso, exacto | Datos financieros, precios, porcentajes exactos. |
| `FLOAT`    | 4      | 7 dígitos aprox. | Valores científicos, coordenadas, donde un error pequeño es tolerable. |
| `DOUBLE`   | 8      | 15-16 dígitos aprox. | Cálculos más grandes que `FLOAT`. |

**Recomendación**  
- Usar `DECIMAL` para dinero (exactitud).  
- Usar `FLOAT/DOUBLE` para cálculos científicos (velocidad sobre exactitud).  

---

## 2. Tipos de Cadenas  

### 🔹 Fijos
- **`CHAR(n)`**: siempre ocupa `n` bytes (rellena con espacios).  
  - Rápido en búsqueda y comparación.  
  - Ideal para códigos fijos (`ISO`, `DNI`, `Placas de autos`).  

### 🔹 Variables
- **`VARCHAR(n)`**: ocupa longitud real + 1 byte (longitud hasta 255) o +2 bytes (longitud mayor).  
  - Ahorra espacio.  
  - Mejor cuando las longitudes son muy variables (ej. nombres, correos).  

### 🔹 Textos largos
| Tipo       | Tamaño máximo | Uso |
|------------|---------------|-----|
| `TINYTEXT` | 255 chars     | Comentarios cortos. |
| `TEXT`     | 65K chars     | Descripciones medianas. |
| `MEDIUMTEXT` | 16M chars   | Documentos largos. |
| `LONGTEXT` | 4GB chars     | Archivos extensos, logs grandes. |

**Nota rendimiento**  
- `TEXT` y superiores se guardan fuera de la fila, con punteros → más lentos que `VARCHAR`.  
- Evitar `TEXT` salvo que sea estrictamente necesario.  

---

## 3. Tipos de Enumeración  

### 🔹 `ENUM`
- Lista de **valores exclusivos** (uno solo por fila).  
- Internamente guarda un **número entero** (1 byte hasta 255 opciones, 2 bytes si hay más).  
- Ejemplo:  

```sql
CREATE TABLE usuario (
  rol ENUM('admin','user','guest')
);
```

- `admin` → internamente 1  
- `user` → internamente 2  
- `guest` → internamente 3  

✅ Ventajas:  
- Muy compacto (ocupa lo mismo que un `TINYINT` o `SMALLINT`).  
- Legible, ya que ves el valor como texto.  

⚠️ Desventaja:  
- Si necesitas agregar nuevos valores, hay que modificar la tabla.  

---

### 🔹 `SET`
- Permite **múltiples valores a la vez** dentro de una columna.  
- Se almacena como **bits en un mapa de bits (bitmap)**.  
- Ejemplo:  

```sql
CREATE TABLE usuario (
  permisos SET('leer','escribir','borrar')
);
```

- `leer,escribir` → internamente `011`  
- `leer,borrar` → internamente `101`  

✅ Ventajas:  
- Muy eficiente en espacio y búsquedas (`FIND_IN_SET`).  

⚠️ Desventaja:  
- Menos normalizado → en diseño estricto se usaría tabla intermedia `usuario_permisos`.  

---

## 4. Tipo Boolean  

- **`BOOLEAN`** es un alias de `TINYINT(1)` en MySQL.  
- Se almacena como `0` (false) o `1` (true).  
- Recomendado para flags lógicos (activo/inactivo).  

---

## 5. Tipos de Fecha y Hora  

| Tipo       | Tamaño | Rango | Uso recomendado |
|------------|--------|-------|-----------------|
| `DATE`     | 3 bytes | 1000-01-01 a 9999-12-31 | Fechas sin hora (cumpleaños, registro). |
| `DATETIME` | 8 bytes | 1000-01-01 00:00:00 a 9999-12-31 23:59:59 | Tiempos absolutos (logs, transacciones). |
| `TIMESTAMP`| 4 bytes | 1970 a 2038 | Fechas relativas a zona horaria UTC. Ideal para auditoría. |
| `TIME`     | 3 bytes | -838:59:59 a 838:59:59 | Duraciones, horas del día. |
| `YEAR`     | 1 byte  | 1901 a 2155 | Solo años, ocupa poco espacio. |

**Nota rendimiento**  
- `TIMESTAMP` es más compacto y automático (`CURRENT_TIMESTAMP`), pero limitado al año 2038.  
- `DATETIME` es más flexible para fechas futuras.  

---

## 6. Otros Tipos  

| Tipo   | Tamaño | Uso recomendado |
|--------|--------|-----------------|
| `JSON` | Variable (depende del contenido) | Datos semiestructurados, atributos dinámicos. Más lento que tablas normalizadas. |
| `TINYBLOB` | 255 bytes | Archivos binarios muy pequeños (iconos, miniaturas). |
| `BLOB` | 65 KB | Binarios pequeños (imágenes, PDFs). |
| `MEDIUMBLOB` | 16 MB | Archivos binarios medianos (audios, documentos grandes). |
| `LONGBLOB` | 4 GB | Archivos binarios enormes (videos, backups). |

---

## 📊 Recomendaciones generales

1. **Usa el tipo más pequeño posible** para mejorar rendimiento.  
2. **No abuses de `TEXT` o `BLOB`**: son costosos en consultas.  
3. **Para dinero → `DECIMAL`**, nunca `FLOAT`.  
4. **Para valores fijos cortos → `CHAR`**, si son variables → `VARCHAR`.  
5. **Usa `ENUM` para listas cerradas**, pero si pueden crecer, crea una tabla relacionada.  
6. **Fechas**: `TIMESTAMP` para auditoría y `DATETIME` para registros históricos.  

---

## 🌎 ¿Esto aplica a todas las bases de datos?

- **Sí**: los conceptos de almacenamiento, rendimiento y buenas prácticas son **universales**.  
- **Aplicacion segun gestor de BD**:  
  - MySQL/MariaDB → soportan todos estos tipos (`ENUM`, `SET`, `TEXT`, `BLOB`, etc.).  
  - PostgreSQL → no tiene `ENUM`/`SET` igual (usa tipos enumerados propios o arrays).  
  - SQL Server → no tiene `ENUM` ni `SET`, pero sí equivalentes (`CHECK`, `BIT`, `NVARCHAR`).  
  - Oracle → maneja cadenas y números similar, pero usa `CLOB`/`BLOB` en vez de `TEXT`.  

👉 En general:  
- **Enteros, decimales, flotantes, cadenas, fechas, booleanos** → son comunes en todas las BD.  
- **ENUM, SET, TEXT, BLOB** → son más específicos de MySQL/MariaDB.  
