🎯 Descripción
Sistema automatizado que:

✅ Recibe emails en Gmail
✅ Analiza contenido con GPT-4
✅ Categoriza y asigna prioridad
✅ Envía confirmación por email
✅ Notifica al equipo por Telegram
✅ Registra ticket en Notion

Estado: Inactivo | Versión: 1.0 | Nodos: 8

🔄 Flujo de Trabajo
Gmail → Extrae Datos → GPT-4 Analiza → Prioridad Alta? → Email + Telegram → Notion
PasoNodoFunción1Gmail TriggerDetecta nuevos emails2Edit FieldsExtrae sender, subject, body, date3Message a ModelAnaliza con GPT-44Edit Fields1Asigna priority, category5If¿Priority == "alta"?6Send MessageEnvía email de confirmación7TelegramNotifica al equipo8NotionCrea ticket en base de datos

📋 Nodos
1️⃣ Gmail Trigger

Tipo: Trigger
Función: Monitorea bandeja de entrada
Filtro: INBOX (leídos y no leídos)
Salida: From, Subject, snippet, internalDate

2️⃣ Edit Fields

Tipo: Set
Función: Extrae y mapea datos
Campos:

sender: $json.From
subject: $json.Subject
body: $json.snippet
date: $json.internalDate



3️⃣ Message a Model (GPT-4)

Tipo: OpenAI / LangChain
Modelo: gpt-4.1-nano
Max Tokens: 800
Prompt: Analiza email y devuelve JSON con categoria, prioridad, respuesta, email
Costo: ~$0.02 por email

4️⃣ Edit Fields1

Tipo: Set
⚠️ PROBLEMA: Usa valores hardcodeados (HIGH, URGENT)
Debe conectarse a: Salida de GPT-4
Solución:

javascript  "priority": "={{ $('Message a model').item.json.prioridad }}",
  "category": "={{ $('Message a model').item.json.categoria }}"
5️⃣ If (Condicional)

Tipo: Condicional
Condición: priority == "alta"
Rama TRUE: Envía email + Telegram
Rama FALSE: Continúa a Notion

6️⃣ Send Message (Email)

Tipo: Gmail
Para: cliente@example.com
Asunto: Usa asunto original
Mensaje: Template de confirmación

7️⃣ Telegram

Tipo: Telegram Bot
Chat ID: 7014633120
Mensaje: "Ticket urgente detectado - [Asunto] - [Remitente]"
Solo ejecuta si: priority == "alta"

8️⃣ Create Notion Page

Tipo: Notion
BD: SOPORTE - TICKETS (ID: 36afdcfc-98f9-8068-a435-ec3f7c0bc50b)
Propiedades:

Title: subject
Customer: sender
Priority: priority
Category: category
Status: "In progress"
