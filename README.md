# JIBA — Consultoría Jurídica en el Área Penal

> Landing page profesional con sistema inteligente de gestión de consultas legales, clasificación automática por IA y respuesta multicanal.

---

## ¿Qué hace esta app?

Cuando un visitante envía una consulta a través del formulario de contacto, se activa un sistema automatizado que:

1. **Recibe la consulta** vía webhook (formulario web) o email (Gmail Trigger)
2. **Normaliza los datos** — nombre, email, mensaje, canal y timestamp
3. **Clasifica el ticket con IA** (GPT-4o-mini) determinando prioridad, categoría, complejidad, sentimiento y si es una pregunta frecuente
4. **Bifurca el flujo** según el tipo de consulta:
   - **Si es FAQ** → consulta la base de conocimiento en Google Sheets y genera una respuesta personalizada con 3 niveles de inteligencia
   - **Si no es FAQ** → asigna un agente especializado y notifica al equipo
5. **Responde al cliente** por email con la respuesta generada o con aviso de que un abogado le contactará
6. **Notifica al equipo** vía Slack con todos los detalles del ticket
7. **Escala por Telegram** si la prioridad es CRÍTICA o ALTA
8. **Registra todo** en Google Sheets (hoja Tickets) para seguimiento

---

## Los 3 niveles de respuesta inteligente

| Situación | Respuesta del sistema |
|---|---|
| Pregunta cubierta por la base FAQ | Responde usando la FAQ como guía, con lenguaje empático y profesional |
| Pregunta legal no cubierta por FAQ | Da orientación jurídica general sin entrar en el caso concreto |
| Caso muy específico o complejo | Informa que un abogado contactará en 24 horas hábiles |

---

## Stack técnico

| Capa | Tecnología |
|---|---|
| Frontend | HTML5 + CSS3 + JavaScript vanilla |
| Deploy | GitHub Pages / cualquier hosting estático |
| Automatización | n8n (hosted) |
| IA | GPT-4o-mini via OpenAI API |
| Base de conocimiento | Google Sheets (hoja FAQ) |
| Registro de tickets | Google Sheets (hoja Tickets) |
| Email entrante | Gmail Trigger (polling cada minuto) |
| Email saliente | Gmail API |
| Notificaciones equipo | Slack |
| Escalado urgente | Telegram Bot |

---

## Estructura del proyecto

```
jiba-legal/
├── index.html     # Landing page completa + formulario + chat FAQ
└── README.md
```

---

## Flujo de automatización n8n

```
Webhook POST /soporte-tickets
Gmail Trigger (polling)
        ↓
Normalizar Entrada
        ↓
LLM: Clasificar Ticket (GPT-4o-mini)
        ↓
Parsear Clasificación
        ├── Respuesta Webhook OK (200 inmediato)
        └── ¿Es FAQ?
              ├── SÍ → Buscar en Base FAQ (Google Sheets)
              │         → LLM: Generar Respuesta (3 niveles)
              │         → Enviar email al cliente
              │         → Registrar en Google Sheets
              │
              └── NO → Asignar Agente Inteligente
                        → Notificar Slack
                        → Escalar y Registrar
                              ├── ¿Requiere Telegram?
                              │     └── SÍ → Notificar Telegram (urgente)
                              └── Registrar Ticket en Google Sheets
```

---

## Clasificación automática de tickets

El sistema clasifica cada consulta en:

**Prioridad:** `CRÍTICA | ALTA | MEDIA | BAJA`

**Categoría:** `facturación | soporte_técnico | consulta_general | queja | cancelación | otro`

**Complejidad:** `simple | media | compleja`

**Sentimiento:** `positivo | neutral | negativo | urgente`

Criterios de escalado automático: prioridad CRÍTICA o ALTA, amenaza legal, rescisión de contrato, cliente muy enojado.

---

## Base de conocimiento FAQ

La hoja **FAQ** del Google Sheet contiene columnas:

| pregunta | respuesta |
|---|---|
| ¿Qué plazo tengo para denunciar? | Si es una falta... |
| ¿Qué es una orden de alejamiento? | ... |

Puedes añadir o editar preguntas directamente en Google Sheets sin tocar el workflow.

---

## Áreas de especialización cubiertas

- ⚖️ Defensa Penal
- 🔨 Acusación Particular y Privada
- 💼 Derecho Penal Económico
- 🛡️ Compliance Penal
- 🏛️ Derecho Penitenciario
- 🏠 Violencia de Género y Doméstica

---

## Chat FAQ integrado

La página incluye un chatbot local (sin IA, sin API) basado en una base de conocimiento en JavaScript que responde las preguntas más frecuentes directamente en el navegador, sin llamadas externas.

---

## Credenciales necesarias en n8n

| Servicio | Tipo |
|---|---|
| OpenAI | API Key |
| Google Sheets | OAuth2 |
| Gmail (trigger + envío) | OAuth2 |
| Slack | OAuth2 |
| Telegram | Bot Token |

Todas las credenciales están almacenadas en n8n — ninguna aparece en el código fuente.

---

## Seguridad

- Las credenciales de todos los servicios están encriptadas en n8n
- El código HTML no contiene tokens, API keys ni contraseñas
- Solo la URL del webhook es visible en el frontend — sin credenciales asociadas

---

## Roadmap

- [ ] Migrar a Next.js para proteger la URL del webhook vía API Route
- [ ] Panel de gestión de tickets en tiempo real
- [ ] Respuesta automática de seguimiento a los 48 horas
- [ ] Integración con calendario para agendar primera consulta

---

## Autor

**Jesús Ignacio Briceño Alarcón**  
Consultor en Derechos Humanos & Cooperación Internacional  
Director & Co-fundador — Humanita's Foundation e.V. (Alemania)  
Basado en los Países Bajos · Disponible internacionalmente

---

*Construido con HTML vanilla + n8n + GPT-4o-mini*
