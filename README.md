# afip-cuil-lookup

<p>
  <img alt="Python" src="https://img.shields.io/badge/python-3.14%2B-blue?logo=python&logoColor=white">
  <img alt="License" src="https://img.shields.io/badge/license-MIT-green">
  <img alt="Status" src="https://img.shields.io/badge/status-stable-green">
</p>

> Consulta el CUIL/CUIT y el nombre de una persona a partir de su DNI y sexo, usando la página pública
> "CUIT Digital" de ARCA (ex AFIP). Devuelve JSON, CSV o Excel.

Sirve cuando tenés DNIs pero no CUILs y no hay un web service a mano. Automatiza con un navegador
(Playwright) el mismo formulario que completaría una persona en
`serviciosweb.afip.gob.ar/publico/cuitOnline/InfoPersonal.aspx`.

## Features

- Una consulta suelta por línea de comandos, o muchas desde un CSV o Excel con columnas `dni,sexo`.
- Salida en JSON por pantalla (default), o a un archivo `.json`, `.csv` o `.xlsx`.
- Si ARCA rechaza la consulta (DNI inexistente, sexo que no coincide), guarda su mensaje en `error`
  y sigue con el resto.
- Una consulta por vez con pausa configurable entre consultas, para no saturar el sitio.

## Requirements

- Python 3.14+
- Chromium de Playwright (se instala con un comando, ver abajo).

## Install

```bash
git clone https://github.com/GDelpo/afip-cuil-lookup.git
cd afip-cuil-lookup
python -m venv env
.\env\Scripts\Activate.ps1       # Windows
# source env/bin/activate        # Linux/Mac
pip install -r requirements.txt
playwright install chromium
```

## Usage

Una persona (sexo: `M`, `F` o `X` para no binario):

```bash
python cuil_lookup.py 12345678 M
```

```json
[
  {
    "dni": "12345678",
    "sexo": "M",
    "nombre": "APELLIDO, NOMBRE",
    "cuit": "20123456789",
    "error": null
  }
]
```

Varias personas desde un archivo (ver `personas.example.csv`):

```bash
python cuil_lookup.py --input personas.csv                          # JSON por pantalla
python cuil_lookup.py --input personas.xlsx --output resultados.xlsx
python cuil_lookup.py --input personas.csv --output resultados.csv --delay 2
```

| Opción | Qué hace | Default |
|---|---|---|
| `--input` | CSV o XLSX con columnas `dni` y `sexo` (el DNI puede venir con puntos) | — |
| `--output` | Archivo de salida; el formato sale de la extensión (`.json`, `.csv`, `.xlsx`) | JSON por stdout |
| `--delay` | Segundos de pausa entre consultas | `1` |
| `--headed` | Muestra el navegador mientras consulta | apagado |

El proceso termina con código `1` si alguna consulta dio error, así se puede usar en scripts.

## Notas

- Depende del HTML de la página de ARCA (`#txtDocumento`, `#ddlGenero`, `#btnContinuar`, `#h2Nombre`,
  `#lblCuit`). Si ARCA la cambia, hay que actualizar esos selectores en `cuil_lookup.py`.
- Los archivos de entrada y salida tienen datos personales: el `.gitignore` excluye `*.csv`, `*.xlsx` y
  `*.json` (salvo el ejemplo).
- Es un script plano de una función, sin paquete ni `app/`.

## License

[MIT](LICENSE) © 2026 Guido Delponte
