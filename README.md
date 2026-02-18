# Convex-Hull
El Convex Hull (o envolvente convexa) es el polígono convexo más pequeño capaz de contener completamente un conjunto de puntos en el plano.

En términos más visuales, si imaginamos que cada punto es un clavo sobre una superficie y colocamos una banda elástica alrededor de todos ellos, la forma que adopta esa banda representa el Convex Hull. Es decir, define la frontera exterior que delimita el conjunto de puntos.

Este concepto es fundamental en la geometría computacional, ya que permite identificar los puntos extremos que forman el contorno de una distribución, y tiene aplicaciones en áreas como gráficos por computadora, análisis espacial y procesamiento de datos.

```python
import csv
from typing import List, Tuple
import matplotlib.pyplot as plt
import tkinter as tk
from tkinter import filedialog, messagebox

Point = Tuple[float, float]

def leer_puntos_csv(ruta_csv: str) -> List[Point]:
    """Lee un CSV con encabezados x,y y regresa una lista de tuplas (x,y)."""
    puntos: List[Point] = []
    with open(ruta_csv, "r", encoding="utf-8") as f:
        reader = csv.DictReader(f)
        for row in reader:
            x = float(row["x"])
            y = float(row["y"])
            puntos.append((x, y))
    return puntos

def punto_mas_izquierdo(puntos: List[Point]) -> int:
    idx = 0
    for i in range(1, len(puntos)):
        if puntos[i][0] < puntos[idx][0] or (
            puntos[i][0] == puntos[idx][0] and puntos[i][1] < puntos[idx][1]
        ):
            idx = i
    return idx

def orientacion(a: Point, b: Point, c: Point) -> float:
    return (b[0] - a[0]) * (c[1] - a[1]) - (b[1] - a[1]) * (c[0] - a[0])

def distancia2(a: Point, b: Point) -> float:
    dx = a[0] - b[0]
    dy = a[1] - b[1]
    return dx * dx + dy * dy

def convex_hull(puntos: List[Point]) -> List[Point]:
    if len(puntos) < 3:
        return puntos[:]

    hull: List[Point] = []
    start_idx = punto_mas_izquierdo(puntos)
    p_idx = start_idx

    while True:
        hull.append(puntos[p_idx])
        q_idx = (p_idx + 1) % len(puntos)

        for r_idx in range(len(puntos)):
            if r_idx == p_idx:
                continue

            p = puntos[p_idx]
            q = puntos[q_idx]
            r = puntos[r_idx]

            o = orientacion(p, q, r)

            if o > 0:
                q_idx = r_idx
            elif o == 0:
                if distancia2(p, r) > distancia2(p, q):
                    q_idx = r_idx

        p_idx = q_idx
        if p_idx == start_idx:
            break

    return hull

def dibujar(puntos: List[Point], hull: List[Point], titulo: str = "Convex Hull"):
    xs = [p[0] for p in puntos]
    ys = [p[1] for p in puntos]

    plt.figure()
    plt.scatter(xs, ys)

    if len(hull) >= 2:
        hx = [p[0] for p in hull] + [hull[0][0]]
        hy = [p[1] for p in hull] + [hull[0][1]]
        plt.plot(hx, hy)

    plt.title(titulo)
    plt.xlabel("x")
    plt.ylabel("y")
    plt.tight_layout()
    plt.show()
```

