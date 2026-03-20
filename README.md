# Codigo-Establecimientos
import requests
from bs4 import BeautifulSoup
import pandas as pd

def datos_establecimientos():
    # 1. URL de la página (Lista de países por población)
    url = "https://slepchiloe.gob.cl/establecimientos-educacionales/"

    # 2. Hacer la petición a la web
    headers = {'User-Agent': 'Mozilla/5.0'} # Evita que Wikipedia bloquee el script
    respuesta = requests.get(url, headers=headers)

    if respuesta.status_code != 200:
        print("Error al acceder a la página")
        return

    # 3. Parsear el HTML
    soup = BeautifulSoup(respuesta.text, 'html.parser')

    # 4. Buscar la tabla (Wikipedia suele usar la clase 'wikitable')
    tabla = soup.find('table')

    # 5. Extraer filas
    colegios = []
    for fila in tabla.find_all('tr')[1:]:  # [1:] para saltar el encabezado
        columnas = fila.find_all(['td', 'th'])
        # print(type(columnas[0]))
        if len(columnas) > 1:
            # Limpiamos el texto de saltos de línea y espacios extra
            datos = {
                "Nombre Establecimiento": columnas[0].get_text(strip=True),
                "Comuna": columnas[1].get_text(strip=True),
                "Direcicion": columnas[2].get_text(strip=True), 
                "Telefono": columnas[3].get_text(strip=True),
                'Sitio Web': columnas[4].get_text(strip=True)
            }
            colegios.append(datos)

    # 6. Convertir a DataFrame de Pandas para visualizar mejor
    df = pd.DataFrame(colegios)
    return df

# Ejecutar y mostrar resultados
resultado = datos_establecimientos()
print("### Datos Recolectados ###")
print(resultado)

# Guardar a CSV (se guarda en la carpeta 'Files' de la izquierda en Colab)
resultado.to_csv("datos_poblacion.csv", index=False)
