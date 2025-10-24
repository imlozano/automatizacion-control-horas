# Sistema de Control de Horas con Telegram y Make

Sistema automatizado para el registro, gestión y reporte de horas de trabajo mediante Telegram Bot y Google Sheets, implementado con Make (Integromat).

## 📋 Descripción General

Este sistema consta de **3 escenarios de Make** que trabajan en conjunto para:
- Solicitar el registro diario de horas mediante Telegram
- Procesar y validar la información ingresada
- Generar reportes ejecutivos en PDF y enviarlos por correo

---

## 🔧 Arquitectura del Sistema

### Componentes Principales
- **Telegram Bot**: Interfaz de usuario para el registro
- **Google Sheets**: Base de datos para almacenar registros
- **Google Docs/PDF**: Generación de reportes
- **Gmail**: Envío de reportes por correo
- **Make Data Store**: Almacenamiento de estado del último prompt

---

## 📱 Escenario 1: Integration Tools, Telegram Bot

### Propósito
Enviar un mensaje automatizado a través de Telegram solicitando el registro diario de horas.

### Flujo de Trabajo

```
┌─────────────────┐
│  Set Variable   │ → Establece fecha actual
└────────┬────────┘
         │
┌────────▼────────┐
│  Send Message   │ → Envía mensaje al usuario via Telegram Bot
└────────┬────────┘
         │
┌────────▼────────┐
│ Update Record   │ → Guarda ID del mensaje en el Data Store
└─────────────────┘
```

---

## 🔄 Escenario 2: Integration Webhooks

### Propósito
Procesamiento completo de la información ingresada por el usuario, validación de datos y registro en Google Sheets.

### Flujo de Trabajo

```
┌──────────────┐
│   Webhook    │ → Recibe mensaje de Telegram
└──────┬───────┘
       │
┌──────▼───────┐
│ Get Record   │ → Obtiene último prompt guardado
└──────┬───────┘
       │
┌──────▼───────────────────────────────────────┐
│            Router Principal                   │
├───────────────┬──────────────┬───────────────┤
│               │              │               │
▼               ▼              ▼               ▼
Respuesta    Formato      Respuesta a      Otras
Válida       Inválido     Prompt Antiguo   Opciones
│               │              │
▼               ▼              ▼
Procesamiento  Error Msg    Error Msg
```
---

## 📊 Escenario 3: Enviar Reporte Ejecutivo de Horas

### Propósito
Generar y enviar automáticamente un reporte ejecutivo en formato PDF con el resumen de horas del período.

### Flujo de Trabajo

```
┌──────────────────┐
│  Get Sheet Data  │ → Lee datos de horas (Rango de hoja)
└────────┬─────────┘
         │
┌────────▼─────────┐
│   Get Record     │ → Obtiene ID último prompt
└────────┬─────────┘
         │
┌────────▼──────────────────────────────────┐
│              Router                        │
├────────────────────┬──────────────────────┤
│                    │                      │
▼                    ▼                      
Hay Datos         No Hay Datos
│                    │
▼                    ▼
Generar PDF       Mensaje Error
```
---

## 🔐 Configuración Requerida

### Credenciales y Conexiones

1. **Telegram Bot**
   - Bot Token
   - Chat ID del usuario

2. **Google Services**
   - Google Sheets
   - Google Docs
   - Google Translate API
   - Gmail

3. **Make Data Store**
   - Nombre: `tg_last_prompt`
   - Estructura:
     ```json
     {
       "id": "last_prompt",
       "message_id": number,
       "created_at": date
     }
     ```
---

## 📅 Programación Recomendada

### Escenario 1: Integration Tools
- **Frecuencia:** Diaria
- **Hora:** 08:00 AM

### Escenario 2: Integration Webhooks
- **Tipo:** Webhook (siempre activo)
- **Trigger:** Mensaje recibido en Telegram

### Escenario 3: Enviar Reporte
- **Frecuencia:** Semanal
- **Día:** Domingo
- **Hora:** 8:00 AM

---

## 🚀 Uso del Sistema

### Registro Diario

1. El bot envía mensaje a las 08:00 AM
2. Usuario responde con formato:
   ```
   8:30, 9:15, Reunión con cliente
   ```
3. Sistema valida y procesa:
   - ✅ Horas programadas: 8:30
   - ✅ Horas realizadas: 9:15
   - ✅ Horas extras: 0.75 (calculado)
   - ✅ Observación: Reunión con cliente

4. Confirmación inmediata por Telegram

### Generación de Reportes

1. Ejecución automática cada Domingo
2. Sistema recopila datos de la semana
3. Genera documento PDF profesional
4. Envía por correo con resumen

---
