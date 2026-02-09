import re, os, json
from datetime import datetime
from pathlib import Path
from contextlib import ExitStack

def load_config():
    config_path = Path('config.json')
    if not config_path.exists():
        raise FileNotFoundError("config.json no encontrado")
    with open(config_path, 'r', encoding='utf-8') as f:
        return json.load(f)

def process_logic(config, mode):
    input_file = Path(config['input_path'])
    results = {'tables_found': 0, 'data_rows': 0, 'error': 0}

    if not input_file.exists():
        print(f"\n  [!] ERROR: No existe {input_file.absolute()}")
        return
    start_keywords = ["INSERT INTO", "LOCK TABLES", "DISABLE KEYS"]
    end_keywords = ["UNLOCK TABLES", "ENABLE KEYS"]

    try:
        with ExitStack() as stack:
            f_in = stack.enter_context(open(input_file, 'r', encoding='utf-8', errors='replace'))
            if mode == "1":
                schema_out = Path(config['output_schema'])
                data_out = Path(config['output_data'])
                schema_out.parent.mkdir(parents=True, exist_ok=True)
                data_out.parent.mkdir(parents=True, exist_ok=True)

                f_schema = stack.enter_context(open(schema_out, 'w', encoding='utf-8'))
                f_data = stack.enter_context(open(data_out, 'w', encoding='utf-8'))

                is_data_block = False
                print(f"  [SISTEMA] Modo Clásico activo. Procesando...\n")

                for line in f_in:
                    upper_line = line.upper()
                    stripped = line.strip()
                    if not stripped: continue

                    if "CREATE TABLE" in upper_line:
                        match = re.search(r'CREATE TABLE\s+`?(\w+)`?', line, re.IGNORECASE)
                        table_name = match.group(1) if match else "Desconocida"
                        print(f"  [ESTRUCTURA] -> {table_name}")
                        results['tables_found'] += 1

                    if any(k in upper_line for k in
                           start_keywords) or "-- DUMPING DATA" in upper_line or stripped.startswith('('):
                        is_data_block = True

                    if is_data_block:
                        f_data.write(line)
                        if '(' in stripped and ')' in stripped:
                            results['data_rows'] += stripped.count('),(') + 1
                    else:
                        f_schema.write(line)

                    if is_data_block and stripped.endswith(';'):
                        if any(k in upper_line for k in
                               end_keywords) or "INSERT INTO" in upper_line or not "LOCK TABLES" in upper_line:
                            is_data_block = False
            elif mode == "2":
                output_dir = Path(config.get('output_folder_tables', 'salida/tablas_individuales/'))
                output_dir.mkdir(parents=True, exist_ok=True)
                current_file = None
                print(f"  [SISTEMA] Modo Quirúrgico activo. Destino: {output_dir}\n")

                for line in f_in:
                    upper_line = line.upper()
                    if "CREATE TABLE" in upper_line:
                        if current_file: current_file.close()
                        match = re.search(r'CREATE TABLE\s+`?(\w+)`?', line, re.IGNORECASE)
                        t_name = match.group(1) if match else f"tabla_{results['tables_found']}"
                        print(f"  [FRAGMENTO] -> Creando {t_name}.sql")
                        current_file = open(output_dir / f"{t_name}.sql", 'w', encoding='utf-8')
                        results['tables_found'] += 1

                    if current_file:
                        current_file.write(line)
                        if 'INSERT INTO' in upper_line and '(' in line:
                            results['data_rows'] += line.count('),(') + 1

                if current_file: current_file.close()

    except Exception as e:
        print(f"\n  [ERROR DURANTE PROCESO] {e}")
        results['error'] += 1

    print("\n" + "=" * 45)
    print("      RESUMEN DE OPERACIÓN: DIVIDE ET IMPERA")
    print("=" * 45)
    print(f"  Tablas identificadas:     {results['tables_found']}")
    print(f"  Registros procesados:     {results['data_rows']:,}")
    print(f"  Estado de archivos:       Completado ✓")
    print(f"  Errores encontrados:      {results['error']}")
    print("-" * 45)
    print(f"  Fin: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}")
    print("=" * 45 + "\n")


def main():
    art = [
        "⠀⠀⠀⠀⠀⠀⠀⣿⠀⠀⡄⠀⠀⣤⠇⠀⠀⠀⠀⠀",
        "⠀⠀⠀⢱⠀⠀⡜⣹⢠⠀⣇⡠⠚⠟⢀⣀⢤⠄⠀⠀",
        "⠀⠀⠀⢸⢀⡼⠀⠐⠁⠀⠉⠀⠚⠊⠉⣁⠊⠀⣀⡀",
        "⠒⢦⠤⠼⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣠⣴⣊⣀",
        "⠀⠀⢳⣤⣴⣶⣿⣿⣿⣿⣿⣿⣿⣶⣶⣤⣤⣿⡏⠁",
        "⠀⠀⠈⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⡋⠁⠀",
        "⠀⠀⠀⣿⡿⠿⠿⠛⠛⠛⠋⠉⠉⠙⠛⢫⡍⡹⠀⠀",
        "⠀⢠⡎⠀⠀⠀⠠⢒⠖⠀⠀⠀⠀⣀⣤⣶⣿⠂⠀⠀",
        "⠀⠀⠸⣷⣶⣶⣶⣶⣶⣶⣾⣿⣿⠿⠿⠟⠛⠀⠀⠀",
        "⠀⠀⠀⠘⣿⣿⣿⠛⠛⠉⠉⠀⠀⠀⠀⠀⠀⠀⠀⠀"
    ]
    header_text = [
        "=" * 38,
        "DIVIDE ET IMPERA: SQL SPLITTER",
        f"Inicio: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}",
        "=" * 38
    ]

    print("\n")
    max_lines = max(len(art), len(header_text))
    for i in range(max_lines):
        left_side = art[i] if i < len(art) else " " * 30
        header_index = i - 3
        right_side = header_text[header_index] if 0 <= header_index < len(header_text) else ""
        print(f"  {left_side}   {right_side}")

    try:
        config = load_config()
        print("\n  SELECCIONE MODO DE OPERACIÓN:")
        print("  1. MODO CLÁSICO (1 archivo Esquema + 1 archivo Datos)")
        print("  2. MODO QUIRÚRGICO (1 archivo .sql por cada tabla)")
        mode = input("\n  Selección (1/2): ")

        if mode in ["1", "2"]:
            process_logic(config, mode)
        else:
            print("  [!] Selección no válida. Abortando.")

    except FileNotFoundError:
        print("\n  [!] Error: No se encontró el archivo 'config.json'.")
    except Exception as e:
        print(f"\n  [!] Error inesperado: {e}")


if __name__ == "__main__":
    main()
