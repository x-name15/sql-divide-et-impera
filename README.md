# SQL Divide et Impera 🏛️

**Divide et Impera** (Divide y Vencerás) es una herramienta ligera y ultra eficiente escrita en Python diseñada para fragmentar archivos SQL Dump masivos (MySQL) en dos componentes esenciales: 
- **Esquema (estructura)** 
- **Datos (registros)**

## 📦 Requisitos
- Python 3.8 o superior
- **y ya no necesitas nada más, solo tu archivo SQL Dump gigante**

## 🛠️ Configuración
El script utiliza un archivo `config.json` para gestionar las rutas de forma sencilla:
```json
{
    "input_path": "fuente/tu_dump.sql",
    "output_schema": "salida/esquema.sql",
    "output_data": "salida/datos.sql",
    "output_folder_tables": "salida/tablas_individuales/"
}
```
## 🕹️ Modos de Operación
Al ejecutar el script, podrás elegir entre:
1. **Modo Clásico:** Separa todo el dump en `esquema.sql` y `datos.sql`.
2. **Modo Quirúrgico:** Crea una carpeta con archivos individuales por cada tabla (ej. `users.sql`, `orders.sql`), ideal para restauraciones parciales.

## 🫠 Y la forma de uso:
1. Coloca tu archivo SQL Dump gigante en la ruta especificada en `input_path`.
2. Escribe en `config.json` como se llamaran tus archivos `output_schema` y `output_data`, junto con su ruta final
3. Ejecuta el script:
```bash
   python divide_et_impera.py
```
- Al iniciar, verás uno de mis sellos y el progreso en tiempo real de las tablas que están siendo analizadas
- Al finalizar, obtendrás un resumen como este:
```bash
=============================================
      RESUMEN DE OPERACIÓN: DIVIDE ET IMPERA
=============================================
  Tablas identificadas:     22
  Registros procesados:     1,084,201
  Estado de archivos:       Completado ✓
  Errores encontrados:      0
---------------------------------------------
```
4. Listo, a ser feliz

### 🤔 Porque usarlo?
Este script nació solamente porque necesitaba dividir y vencer. Para que te hagas una idea de su potencia: logró el procesamiento de 1,000,000+ de registros en ~2 segundos (o un segundito más, no tomé nota exacta la verdad xD)