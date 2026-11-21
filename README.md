# ONagy Subtitle

Purple-studio subtitle editor for short Arabic/English audio and video. Built for free-tier hosting, with honest limits.

## What it does
- Browser-side audio decoding and 16 kHz mono conversion.
- Timed transcription through Cloudflare Workers AI Whisper large-v3-turbo.
- Original and optional Arabic/English translation tracks using Qwen 3.8 27B.
- Editable cue text and timestamps, local media preview, SRT/VTT export.
- Local ONagy project JSON save/import, plus SRT/VTT import.
- Arabic/English interface and reduced-motion support.

## Limits
This is a prototype, not unlimited transcription and not an accuracy guarantee. App inputs are limited to 5 minutes and 20 MB. Browser/codec support varies, especially for video containers. No video burn-in or speaker diarization is included.

Arabic dialects, English accents, noisy recordings and mixed-language speech can be transcribed incorrectly. Auto mode does not guarantee both scripts will be preserved. Review all output. Translation is separate from transcription, retains the original track, and needs its own review.

The current server-side guard allows 600 transcription seconds per day-salted connection identifier and 3600 seconds across the app per UTC day, plus 20,000 text translation characters overall, 8000 per identifier, and a maximum of 30 translation model calls shared daily. There is also a short rate limit. These are app safety caps, not guaranteed available quota. Chunk overlap and retried or cancelled requests count toward processing. The account-wide Workers AI allowance can run out sooner. No paid plan is required or automatically enabled.

## Privacy
Clicking Create sends short audio chunks to Cloudflare. Selecting a file, importing subtitles, editing and exporting happen in the browser. This app does not save uploads or transcripts on a server and does not log content. Its D1 database stores only usage counts and hashes derived from the current UTC date and connection IP, not the raw IP, audio or transcript. Old counters are opportunistically removed. Operational metadata may still be processed by the hosting provider.

Work is held in memory in the open page. Download a project to keep it. The project contains subtitle text and timing, not the source media. The UI language preference is stored locally.

## Development
Requires Node.js. `npm install`, `npm test`, then `npx wrangler dev` for local UI/backend development. AI inference requires an authorized Cloudflare account and remote bindings when testing the actual model. Do not commit credentials or `.dev.vars`.

For deployment, create a dedicated D1 database, apply `schema.sql`, set its database ID in a local configuration, and deploy with the AI and LIMITS bindings. The committed example config must contain a database-ID placeholder, not a working account identifier. Frontend changes on GitHub Pages do not deploy backend changes.

## References
- https://developers.cloudflare.com/workers-ai/models/whisper-large-v3-turbo/
- https://developers.cloudflare.com/workers-ai/models/qwen3.8-27b/
- https://developers.cloudflare.com/workers-ai/platform/pricing/
- https://developers.cloudflare.com/workers-ai/platform/data-usage/
- https://github.com/openai/whisper

Whisper turbo's translation task is not used, because the upstream model documentation says turbo was not trained for translation. A separate Qwen model translates text instead.

## Live setup
Cloudflare app and API: https://onagy-subtitle.s21smk.workers.dev
GitHub Pages serves a second copy of the interface and uses this Cloudflare API. Interface changes in `public/` auto-deploy to Pages using Actions. Backend changes in `worker.js` need a separate Cloudflare deployment. Do not change the Pages source to branch/root.
