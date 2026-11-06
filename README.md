# DementiaBot

DementiaBot is a voice agent that answers phone calls for a family member living with dementia. It speaks in a voice the family configures, stays calm when the caller is confused, and emails the family when the conversation shows a real emergency.

It is meant to lighten the day-to-day burden of dementia care. Repeated calls, repeated questions, and the fear of missing a real emergency wear families down. DementiaBot picks up when a person cannot, stays patient through confusion, and only pulls a caregiver in when the call shows that something is actually wrong. The person with dementia gets a steady, reassuring voice. The family gets time back, less constant worry, and a clearer signal when they do need to step in. The aim is a calmer life for both.

The person with dementia is the caller. A family member or caregiver sets up the phone number, the voice, and who should be emailed or called when something is wrong.

**Status:** planning. This repository currently contains the project description and license. The phone agent, voice pipeline, and emergency email are not built yet, so there is nothing to install or run.

## Who it is for

- Families carrying the daily weight of dementia care, who need some of that stress taken off without leaving their relative alone on the phone.
- Family members who want a familiar, patient voice on the phone when they cannot pick up.
- Caregivers who need a prompt when a call suggests a fall, injury, chest pain, an intruder, or another immediate safety problem.
- The person living with dementia, who should hear short sentences, a slow pace, and reassurance instead of corrections.

DementiaBot is a family communication aid. Life-threatening events still belong with local emergency services and a human caregiver.

## What it does

- Answers incoming phone calls on a number the family controls.
- Talks in a configured voice. The first voice to test is a warm, standard therapeutic voice. A cloned family voice is optional and only after that test, because a clone can upset a caller who realizes it is not really their relative.
- Uses validation therapy: it responds to the caller’s feelings, and it does not argue about mixed-up memories or timelines.
- Speaks slowly, in short sentences, with longer pauses between turns.
- During the call, watches for explicit distress: falls, chest pain, intruders, physical injury, and severe confusion about physical safety.
- Sends an email to configured family members when it judges that an actual emergency is happening.
- For severe medical emergencies, tries to transfer the live call to a primary caregiver’s mobile phone. Email alone is not the plan for those cases.
- Can pass the transcript through a second safety check so implicit risk is less likely to be missed.

## What it is built with

| Layer | Planned tools |
| --- | --- |
| Phone calls | Twilio or Vapi, for PSTN numbers and SIP |
| Low-latency voice agent | Vapi, Retell AI, or LiveKit Agents (target around 500 ms) |
| Speech | ElevenLabs or Cartesia, for a configured or cloned voice |
| Conversation | GPT-4o or Claude, with a validation-therapy system prompt |
| Emergency email | SendGrid or Resend, fired from a webhook when the agent calls `trigger_emergency_alert` |
| Extra safety check | A background monitor such as Llama Guard on the transcript |

The agent exposes one tool, `trigger_emergency_alert`, with three fields: `distress_level`, `reason_for_alert`, and `caller_quote`. A small serverless endpoint (or Zapier or Make) receives that webhook and sends the email.

## How a call is supposed to work

1. A call arrives on the project phone number and is routed to the voice agent.
2. The agent listens and answers in the configured voice, using short, slow, non-confrontational turns.
3. If the conversation meets the safety rules, the agent calls `trigger_emergency_alert` while the call is still going.
4. The webhook sends an email that includes the distress level, the reason, and a short quote from the caller.
5. If the situation is severe (chest pain, physical harm, and similar), the agent also attempts a SIP transfer to the caregiver’s phone.

## Design rules

- Reassure the emotion. Do not correct a false memory.
- Keep turns short and leave room for the caller to respond.
- Treat email as the alert for the family. Treat call transfer as the path for severe medical emergencies.
- Start with a therapeutic voice. Add a family-member clone only after listening tests show it helps.
- Run a second pass over the transcript for risks that never became an explicit tool call.
- Do not present the agent as a clinician, a diagnosis tool, or a replacement for emergency services.

## How to run it

Nothing in this repository runs yet. When the first slice exists, setup is expected to look like this:

1. Create a Vapi or Retell account, an ElevenLabs (or Cartesia) API key, a Twilio or Vapi phone number, and a SendGrid or Resend API key.
2. Clone or select a voice from 1–3 minutes of clean audio. Begin with a calm therapeutic voice.
3. Attach the speech API key to the voice agent and point the purchased number at that agent.
4. Load the validation-therapy system prompt, including the safety rules above.
5. Register `trigger_emergency_alert` and point its webhook at the email endpoint.
6. Set the caregiver transfer number for severe emergencies.
7. Place test calls that include both ordinary confusion and scripted emergency phrases, and confirm the email and the transfer behave as intended.

Secrets (API keys, phone numbers, caregiver addresses) stay in environment variables or the host’s secret store. They do not belong in this repository.

## Roadmap

- [ ] Choose the telephony and voice-agent host (Vapi, Retell, or LiveKit plus Twilio) and attach a phone number.
- [ ] Add the first voice profile on ElevenLabs or Cartesia, using a therapeutic voice before any family-member clone.
- [ ] Write the system prompt: validation, slow pacing, and the explicit distress list.
- [ ] Implement `trigger_emergency_alert` and the webhook that emails the family through SendGrid or Resend.
- [ ] Add Twilio SIP transfer to a caregiver’s mobile phone for severe medical emergencies.
- [ ] Add a background transcript check for implicit risk.
- [ ] Run scripted test calls and record latency, missed emergencies, and false alarms.
- [ ] Replace this section’s “planned setup” with exact run commands once a call can be placed end to end.

## License

MIT. See [LICENSE](LICENSE).
