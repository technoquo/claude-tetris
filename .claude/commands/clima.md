# Skill: Clima Local

Obtén y muestra la información del clima actual para la ubicación del usuario.

## Pasos

1. Determina la ubicación. Si el usuario no especificó una ciudad en `$ARGUMENTS`, usa **San José, Costa Rica** (escríbela como `San+Jose,Costa+Rica` en la URL).

2. Ejecuta este comando con la ciudad detectada (reemplaza espacios por `+`):

```bash
curl -s "wttr.in/{CIUDAD}?format=v2&lang=es" 2>/dev/null || curl -s "wttr.in/{CIUDAD}?format=j1" 2>/dev/null
```

3. Si `format=v2` responde con texto legible, muéstralo directamente en un bloque de código.

4. Si prefieres un resumen compacto en JSON, usa `?format=j1` y extrae:
   - Temperatura actual (`temp_C`)
   - Sensación térmica (`FeelsLikeC`)
   - Descripción del tiempo (`weatherDesc`)
   - Humedad (`humidity`)
   - Viento (`windspeedKmph`, `winddir16Point`)

5. Presenta el resultado de forma clara e incluye:
   - Ciudad consultada
   - Hora local aproximada
   - Condición actual con emoji apropiado (☀️ 🌤️ ⛅ 🌧️ ❄️ ⛈️)
   - Temperatura y sensación térmica
   - Humedad y viento

## Notas

- `wttr.in` no requiere API key y soporta cientos de ciudades.
- Si el usuario escribe `/clima Madrid` o `/clima New York`, usa esa ciudad en lugar de la predeterminada.
- Si `curl` no está disponible en el entorno, informa al usuario y sugiere instalar curl o usar PowerShell: `Invoke-RestMethod "https://wttr.in/{CIUDAD}?format=j1"`.
