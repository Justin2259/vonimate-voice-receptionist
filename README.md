# Vonimate Voice Receptionist

Production AI voice receptionist built on Vapi.ai, n8n, and Cal.com. Answers inbound calls, qualifies callers, books appointments, and escalates to a human when needed. Caller memory persists across calls so returning callers don't repeat themselves.

Live at [vonimate.com](https://vonimate.com).

---

## Architecture

```
Inbound call
    |
    v
Vapi.ai (voice layer)
    handles STT, TTS, interruptions, call state
    |
    | webhooks
    v
n8n (orchestration layer)
    routes events: new call, function calls, end-of-call
    manages caller memory lookup and update
    triggers Cal.com booking flows
    handles escalation logic
    |
    +-- Cal.com API (appointment booking)
    +-- CRM / memory store (caller history)
    +-- Human escalation (transfer or callback queue)
```

---

## Key Behaviors

**Caller memory**: Returning callers are recognized by phone number. The agent greets them by name and skips re-qualification questions they've already answered. Memory is stored in n8n and updated at end-of-call.

**Appointment booking**: The agent checks real-time availability via Cal.com API and books directly during the call. Confirmation sent via SMS/email post-call.

**Escalation**: The agent escalates when: the caller explicitly asks for a human, the issue is outside the agent's scope, or confidence drops below threshold after two failed clarification attempts. Escalation routes to a live transfer or schedules a callback depending on availability.

**After-hours**: When no staff are available, the agent offers a callback slot or voicemail instead of a dead end.

---

## Stack

| Component | Technology |
|-----------|-----------|
| Voice AI | Vapi.ai |
| Orchestration | n8n (self-hosted) |
| Booking | Cal.com API |
| AI model | Claude (via Vapi function calling) |
| Memory | n8n data store / external DB |

---

## Configuration

The receptionist behavior is defined entirely in Vapi.ai's assistant config:
- System prompt: persona, scope, escalation rules
- Functions: `check_availability`, `book_appointment`, `lookup_caller`, `update_caller_memory`, `escalate_to_human`
- Voice: ElevenLabs or Vapi native TTS

n8n workflows handle all function call webhooks and external integrations.

---

## Status

Live in production. n8n workflow definitions and Vapi assistant config in progress for open-source release.
