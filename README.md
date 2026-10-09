# Prueba técnica Fullstack Junior — Pymedesk

Aplicación pequeña de gestión de pedidos con Flask, SQLite y JavaScript nativo. Tiempo estimado: **30–45 minutos**. Puedes usar asistentes de IA.

## Resolución

### Problemas encontrados

1. **El servidor permitía enviar pedidos cancelados.** El endpoint `POST /api/orders/<id>/ship` solo validaba el estado `enviado`, pero no `cancelado`. Un pedido cancelado pasaba a enviado sin restricción.
2. **Errores silenciosos en la UI.** Cuando una actualización fallaba, el `catch` solo re-habilitaba el botón sin mostrar ningún mensaje al usuario.

### Diagnóstico y solución

- **Backend (`app.py`):** se agregó una validación que rechaza con `409` los pedidos con estado `cancelado`, devolviendo un mensaje claro en JSON.
- **Frontend (`index.html`):** se creó una función `showToast()` que muestra un mensaje de error visible (toast rojo animado) con el texto que devuelve el servidor. Se actualizó `ship()` para leer la respuesta JSON y mostrar el error en lugar de fallar en silencio.
- **CSS (`style.css`):** se añadieron estilos para el componente toast.

### Cómo probarlo

1. Levantar el servidor (`python app.py`).
2. **Prueba por UI:** abrir `http://127.0.0.1:5000`, hacer clic en "Marcar como enviado" en un pedido **pendiente** → debe cambiar a enviado. Los botones de pedidos cancelados y enviados están deshabilitados.
3. **Prueba por curl** (valida el backend directamente):
   ```bash
   # Pedido pendiente → debe funcionar (200)
   curl -X POST http://localhost:5000/api/orders/1/ship

   # Pedido cancelado → debe fallar (409)
   curl -X POST http://localhost:5000/api/orders/3/ship

   # Pedido ya enviado → debe fallar (409)
   curl -X POST http://localhost:5000/api/orders/2/ship
   ```
4. Para reiniciar los datos: `python seed.py`.

> **Nota sobre el proceso:** durante el desarrollo se habilitó temporalmente el botón para pedidos cancelados con el fin de verificar visualmente el toast de error desde la UI. Tras confirmar el funcionamiento, se restauró la validación en el frontend (defensa en profundidad).

**Uso de IA:** ~70% trabajo humano (diagnóstico, decisiones de diseño, pruebas manuales) y ~30% asistencia de IA (sugerencias de implementación y revisión de código).


## Preparación

Requiere Python 3.11 o superior. En la raíz del repositorio:

```bash
python -m venv .venv
```

Activa el entorno virtual con `.venv\Scripts\activate` en Windows o `source .venv/bin/activate` en macOS/Linux. Después:

```bash
pip install -r requirements.txt
python seed.py
python app.py
```

Abre <http://127.0.0.1:5000>. El seed se puede ejecutar más de una vez sin duplicar pedidos. La base local se crea en `data/orders.sqlite3` y no se incluye en Git.

## Tu tarea

1. Un pedido cancelado no debe poder pasar a enviado: el servidor debe rechazar la operación y conservar su estado cancelado. Un pedido pendiente sí debe poder pasar a enviado.
2. Cuando falle una actualización, muestra en la interfaz un mensaje visible y comprensible. Actualmente el fallo no se comunica claramente.

Si encuentras algún otro error imprevisto en la aplicación, puedes corregirlo sin necesidad de preguntar. Si no estás seguro de cómo proceder, documenta el problema y tu propuesta de solución en el repositorio.

Mantén la solución pequeña. No se requiere despliegue, documentación extensa ni pruebas automatizadas adicionales.

## API inicial

- `GET /api/orders`: devuelve todos los pedidos.
- `POST /api/orders/<id>/ship`: intenta marcar un pedido como enviado.

## Entrega

Comparte el enlace a tu repositorio con los cambios y un video de **2–4 minutos** donde muestres la aplicación, expliques el problema encontrado, tu solución y cómo comprobaste el resultado. Puedes usar asistentes de IA durante todo el ejercicio.
