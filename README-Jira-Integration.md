# 🎫 Integración Jira desde Cursor - Guía Completa

Sistema automatizado para crear tickets de Jira directamente desde Cursor usando reglas personalizadas.

## 📋 **Descripción General**

Este sistema permite:
- ✅ Generar definiciones de tareas estructuradas (formato 9001)
- ✅ Crear tickets en Jira automáticamente (regla 9002)
- ✅ Validación automática de tokens y configuración
- ✅ Soporte para Historia Padre + Sub-tasks o Stories individuales
- ✅ Cleanup automático de archivos temporales

## 🛠️ **Configuración Inicial**

### **Paso 1: Configurar credenciales de Jira**

1. **Crear API Token en Jira:**
   ```bash
   # Se abrirá automáticamente la página de tokens
   open https://id.atlassian.com/manage-profile/security/api-tokens
   ```

2. **En la página de Atlassian:**
   - Hacer clic en "Create API token"
   - Nombre: "Cursor KYC Enforcement - [FECHA]"
   - Copiar el token generado

3. **Configurar variables de entorno:**
   ```bash
   # Agregar al final de ~/.zshrc
   echo "
   # Jira API Configuration for KYC Enforcement
   export JIRA_BASE_URL=\"https://mercadolibre.atlassian.net\"
   export JIRA_EMAIL=\"YOUR_CORPORATE_EMAIL\"
   export JIRA_API_TOKEN=\"YOUR_GENERATED_TOKEN\"
   # JIRA_PROJECT_KEY se consulta dinámicamente en cada creación" >> ~/.zshrc
   ```

4. **Recargar configuración:**
   ```bash
   source ~/.zshrc
   ```

### **Paso 2: Verificar reglas de Cursor**

Las reglas deberían estar en:
- `.cursor/rules/9001-jira-task-generator.mdc` (v2.2.0)
- `.cursor/rules/9002-jira-api-integration.mdc` (v2.4.0)

## 🚀 **Uso del Sistema**

### **Opción 1: Flujo Integrado (Recomendado)**

Para generar tareas y crear tickets automáticamente:

```
"Generar tarea JIRA para implementar circuit breaker en enforcement usando 9001-jira-task-generator"
```

**El sistema automáticamente:**
1. 📝 Genera archivo `.md` con formato estructurado
2. ❓ Pregunta: "¿Deseas crear estos tickets en Jira ahora?"
3. ✅ Si respondes "Sí" → Ejecuta flujo de creación automáticamente

### **Opción 2: Flujo Manual**

Si ya tienes archivos `.md`:

```
"Crear ticket JIRA desde archivo [nombre].md usando 9002-jira-api-integration"
```

### **Opción 3: Desde Archivos Existentes**

Para procesar múltiples tareas desde un documento:

```
"En base a las tareas definidas en @archivo.md crear las tareas en la plataforma"
```

## 📋 **Formatos de Creación**

### **Historia Padre + Sub-tasks**
- Crea 1 Story principal para contexto
- Crea N Sub-tasks (uno por cada paso técnico)
- Ideal para: Epics grandes, trabajo colaborativo

### **Story Individual**  
- Crea 1 Story completo con todo el contenido
- Autocontenido y directo
- Ideal para: Tareas específicas, trabajo individual

## 🔍 **Estructura de Archivos .md**

Los archivos deben seguir el formato 9001-jira-task-generator:

```markdown
# Ticket JIRA: [Título de la Tarea]

## ¿Qué hay que hacer?
[Descripción clara y específica]

## ¿Cómo se va a hacer?
- [Paso técnico 1]
- [Paso técnico 2]
- [Paso técnico 3]

## ¿Por qué se va a hacer?
[Justificación de negocio y valor]

## Dependencias
- [Dependencia 1]
- [Dependencia 2]

## DoD (Definition of Done)
- [ ] [Criterio específico y medible 1]
- [ ] [Criterio específico y medible 2]
- [ ] [Criterio específico y medible 3]

---
```

## ⚙️ **Flujo Detallado del Sistema**

### **1. Verificación Automática**
```
✅ Variables de entorno encontradas
🔍 Verificando validez del token...
✅ Token válido, continuando...
```

### **2. Selección de Formato**
```
¿Cómo deseas crear la tarea?
- Opción 1: Historia padre + sub-tasks asociadas
- Opción 2: Story individual directa
```

### **3. Selección de Proyecto**
```
¿Para qué proyecto deseas crear el ticket?
- KYCSERVENF (KYC Enforcement)
- PAYMENTS
- USERS  
- SECURITY
- [Otro proyecto]
```

### **4. Creación en Jira**
```
🎫 Historia Padre creada: KYCSERVENF-XXX
🎫 Sub-task 1 creada: KYCSERVENF-XXX
🎫 Sub-task 2 creada: KYCSERVENF-XXX
...
✅ Todos los tickets creados exitosamente!
```

### **5. Cleanup**
```
¿Qué deseas hacer con los archivos .md generados?
- 🗑️ Eliminar archivos .md (ya están en Jira)
- 📁 Mantener archivos .md (para referencia futura)
```

## 🚨 **Troubleshooting**

### **Token Expirado/Inválido**
Si aparece: `❌ Token inválido o expirado (HTTP 401)`

**El sistema automáticamente:**
1. Explica el problema
2. Abre página de tokens: `open https://id.atlassian.com/manage-profile/security/api-tokens`
3. Guía creación de nuevo token
4. Abre `.zshrc` para edición: `open ~/.zshrc`
5. Re-verifica el nuevo token

### **Error de Jerarquía**
```json
{"errors":{"parentId":"La actividad principal no corresponde a la jerarquía adecuada."}}
```
**Solución**: El sistema usa "Sub-task" automáticamente (ya corregido en v2.4.0)

### **Error de Formato de Descripción**
```json
{"errors":{"description":"El valor debe ser un documento de Atlassian"}}
```
**Solución**: El sistema usa formato ADF automáticamente (ya corregido en v2.4.0)

### **Variables de Entorno Faltantes**
```bash
# Verificar configuración
echo "JIRA_BASE_URL: ${JIRA_BASE_URL:-'❌ Not set'}"
echo "JIRA_EMAIL: ${JIRA_EMAIL:-'❌ Not set'}"  
echo "JIRA_API_TOKEN: ${JIRA_API_TOKEN:+✅ Set}"
```

## 📊 **Ejemplos de Uso Real**

### **Ejemplo 1: Tarea Individual**
```
User: "Generar tarea JIRA para agregar validación de email usando 9001"

Result:
1. ✅ Genera archivo jira-task-email-validation.md
2. ❓ "¿Deseas crear estos tickets en Jira ahora?"
3. ✅ Usuario acepta
4. 🔍 Verifica token automáticamente  
5. ❓ "¿Historia padre + sub-tasks o Story individual?"
6. ❓ "¿Para qué proyecto? KYCSERVENF"
7. 🎫 Crea ticket en Jira
8. ❓ "¿Eliminar archivos .md?"
9. ✅ Proceso completado
```

### **Ejemplo 2: Epic con Múltiples Tasks**
```
User: "En base a las tareas definidas en @mi-documento.md crear las tareas en la plataforma"

Result:
1. 📝 Lee y procesa documento con 5 tareas
2. 📄 Genera 5 archivos .md individuales
3. ❓ "¿Deseas crear estos tickets en Jira ahora?"
4. ✅ Usuario acepta → "Historia padre + sub-tasks"  
5. 🎫 Crea 1 Historia Padre + 5 Sub-tasks
6. 🗑️ Elimina archivos .md temporales
7. ✅ 6 tickets creados en total
```

### **Ejemplo 3: Manejo de Token Expirado**
```
User: "Crear ticket JIRA para nueva funcionalidad"

Flow:
1. ✅ Variables encontradas
2. 🔍 Verificando token...
3. ❌ Token inválido (HTTP 401)
4. 🔧 Proceso automático de regeneración:
   - Abre página de tokens
   - Guía creación paso a paso
   - Abre .zshrc para edición
   - Re-verifica nuevo token
5. ✅ Continúa con creación normal
```

## 🏗️ **Arquitectura del Sistema**

### **Reglas de Cursor**
```
9001-jira-task-generator v2.2.0
├── Genera archivos .md estructurados
├── Sin estimaciones de tiempo
├── Formato compatible con ambas opciones
└── Ofrece integración automática con 9002

9002-jira-api-integration v2.4.0  
├── Validación automática de tokens
├── Soporte para Historia + Sub-tasks
├── Formato ADF para descripciones
├── Cleanup post-creación
└── Manejo de errores específicos
```

### **Variables de Entorno**
```bash
JIRA_BASE_URL     # https://mercadolibre.atlassian.net
JIRA_EMAIL        # Tu email corporativo
JIRA_API_TOKEN    # Token generado en Atlassian
# JIRA_PROJECT_KEY se consulta dinámicamente
```

## 🎯 **Características del Sistema**

### ✅ **Automatización Completa**
- Validación previa de credenciales
- Detección de tokens expirados
- Regeneración guiada de tokens
- Creación automática en Jira
- Cleanup de archivos temporales

### ✅ **Flexibilidad**
- Soporte para múltiples proyectos
- Dos formatos de creación
- Manejo de tareas individuales o masivas
- Compatible con diferentes tipos de tickets

### ✅ **Robustez**
- Manejo de errores específicos
- Validación de formato de archivos
- Verificación de jerarquías
- Rollback automático en caso de fallas

### ✅ **Seguridad**
- Tokens en variables de entorno
- Sin credenciales en código
- Validación continua de permisos
- Rotación guiada de tokens

## 📚 **Comandos Útiles**

### **Verificar Configuración**
```bash
# Verificar variables
echo "JIRA_BASE_URL: $JIRA_BASE_URL"
echo "JIRA_EMAIL: $JIRA_EMAIL"
echo "JIRA_API_TOKEN: ${JIRA_API_TOKEN:+✅ Set}"

# Probar conexión
curl -X GET "$JIRA_BASE_URL/rest/api/3/myself" \
  -H "Authorization: Basic $(echo -n "$JIRA_EMAIL:$JIRA_API_TOKEN" | base64)"
```

### **Gestión de Tokens**
```bash
# Abrir gestión de tokens
open https://id.atlassian.com/manage-profile/security/api-tokens

# Editar configuración
open ~/.zshrc

# Recargar configuración
source ~/.zshrc
```

### **Búsqueda de Tickets Creados**
```bash
# Buscar tickets por proyecto
curl -X GET "$JIRA_BASE_URL/rest/api/3/search" \
  -H "Authorization: Basic $(echo -n "$JIRA_EMAIL:$JIRA_API_TOKEN" | base64)" \
  -G -d 'jql=project="KYCSERVENF" ORDER BY created DESC'
```

## 🏆 **Mejores Prácticas**

### **Para Tareas**
- ✅ Usar títulos descriptivos y específicos
- ✅ Incluir contexto técnico del proyecto (Go, hexagonal architecture)
- ✅ Definir DoD clara y medible
- ✅ Especificar dependencias explícitamente
- ❌ No incluir estimaciones de tiempo (se manejan manualmente)

### **Para Proyectos**
- ✅ Usar keys de proyecto correctos (KYCSERVENF, etc.)
- ✅ Mantener consistencia en labels (backend, go, kyc)
- ✅ Agrupar tareas relacionadas con Historia Padre
- ❌ No asignar tickets automáticamente (manejo manual)

### **Para Seguridad**
- ✅ Rotar tokens regularmente (incluir fecha en nombre)
- ✅ Verificar permisos mínimos necesarios
- ✅ No commitear tokens en repositorios
- ✅ Usar nombres descriptivos para tokens

## 🆕 **Actualizaciones Recientes**

### **v2.4.0 (Actual)**
- ✅ Validación automática de tokens
- ✅ Regeneración guiada de tokens expirados
- ✅ Cleanup automático de archivos .md
- ✅ Manejo mejorado de errores específicos

### **v2.3.0**
- ✅ Formato ADF obligatorio para descripciones
- ✅ Sub-tasks correctos para jerarquías
- ✅ Ejemplos actualizados con formato real

### **v2.2.0**
- ✅ Integración automática 9001 → 9002
- ✅ Flujo seamless sin llamadas manuales

## 📞 **Soporte**

Para problemas o mejoras:
1. Verificar que las reglas estén actualizadas (v2.2.0 y v2.4.0)
2. Revisar configuración de variables de entorno
3. Probar validación de token manualmente
4. Consultar sección de Troubleshooting

---

**Sistema desarrollado para optimizar la creación de tickets de Jira desde Cursor en proyectos de KYC Enforcement.**