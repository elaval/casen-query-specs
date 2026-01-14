# Variables de Ingreso - CASEN 2024

## Ingresos del Hogar

### ymonecorh (ingreso monetario)
- **Descripción**: Ingreso monetario total del hogar (base para cálculo de pobreza)
- **Tipo**: Numérico (CLP mensuales)
- **Unidad de análisis**: household
- **Requiere ponderación**: regional
- **Variable API**: `ingreso_monetario_hogar`
- **Per cápita disponible**: Sí (dividir por numper)

### ytrabajocorh (ingreso laboral)
- **Descripción**: Ingreso del trabajo del hogar (sueldos, honorarios, negocio propio)
- **Tipo**: Numérico (CLP mensuales)
- **Unidad de análisis**: household
- **Requiere ponderación**: regional
- **Variable API**: `ingreso_laboral_hogar`
- **Per cápita disponible**: Sí

### yauth (ingreso autónomo)
- **Descripción**: Ingreso autónomo del hogar (sin transferencias del Estado)
- **Tipo**: Numérico (CLP mensuales)
- **Unidad de análisis**: household
- **Requiere ponderación**: regional
- **Variable API**: `ingreso_autonomo_hogar`
- **Per cápita disponible**: Sí

### ytotcorh (ingreso total)
- **Descripción**: Ingreso total del hogar (incluye todas las fuentes)
- **Tipo**: Numérico (CLP mensuales)
- **Unidad de análisis**: household
- **Requiere ponderación**: regional
- **Variable API**: `ingreso_total_hogar`
- **Per cápita disponible**: Sí

## Ingresos Per Cápita (ya calculados)

### ypchmonecor
- **Descripción**: Ingreso monetario per cápita del hogar
- **Tipo**: Numérico (CLP mensuales por persona)
- **Unidad de análisis**: household
- **Variable API**: Use `ingreso_monetario_hogar` con `per_capita: true`

## Dimensiones Comunes

Todas las métricas de ingreso pueden desagregarse por:
- **area**: Urbano/Rural
- **region**: Regiones 1-16
- **dau**: Decil autónomo nacional (1-10)
- **qaut**: Quintil autónomo nacional (1-5)
- **sexo_jefe**: Sexo del jefe de hogar (requiere filtro pco1a=1)
- **edad_jefe**: Grupos etarios del jefe de hogar

## Ejemplos de Consultas
```json// Ingreso promedio nacional
{
"analysis_unit": "household",
"metrics": [{"name": "ingreso_monetario_hogar", "per_capita": false}],
"weights": "regional"
}// Ingreso per cápita por decil
{
"analysis_unit": "household",
"metrics": [{"name": "ingreso_monetario_hogar", "per_capita": true}],
"dimensions": [{"type": "geographic", "attribute": "dau"}],
"weights": "regional"
}// Ingreso laboral en zona urbana por región
{
"analysis_unit": "household",
"metrics": [{"name": "ingreso_laboral_hogar", "per_capita": false}],
"dimensions": [{"type": "geographic", "attribute": "region"}],
"filters": {"area": "urbano"},
"weights": "regional"
}
```