# Variables de Pobreza - CASEN 2024

## Pobreza por Ingresos

### pobreza (string)
- **Descripción**: Clasificación de pobreza del hogar según nueva metodología
- **Valores**: "pobre_extremo", "pobre_no_extremo", "no_pobre"
- **Unidad de análisis**: household
- **Requiere ponderación**: regional
- **Variable API**: "pobreza_status"

### lp (numeric)
- **Descripción**: Línea de pobreza (ingreso mínimo no pobre)
- **Unidad**: CLP mensual
- **Unidad de análisis**: household
- **Uso**: Comparación con ymonecorh

### li (numeric)
- **Descripción**: Línea de indigencia (ingreso mínimo no indigente)
- **Unidad**: CLP mensual
- **Unidad de análisis**: household

## Pobreza Multidimensional

### pobrezamulti (boolean)
- **Descripción**: Hogar en situación de pobreza multidimensional
- **Valores**: 0 (no pobre), 1 (pobre)
- **Unidad de análisis**: household
- **Variable API**: "multidim_poverty"
- **Requiere**: Al menos una carencia en dimensiones

### Componentes (carencias)

#### Educación
- **hhdasis**: Inasistencia a educación (0/1)
- **hhdrez**: Rezago escolar (0/1)
- **hhdesc**: Escolaridad insuficiente (0/1)
- **hhdape**: Bajo logro educacional adultos (0/1)

[...]