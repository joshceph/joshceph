# AI Office

A local, free, top-view office where AI employees work, talk and hand off jobs. One Python file, no installs.

## Run

1. Install Python 3.8 or newer.
2. Run: `python office.py`
3. Open http://localhost:8765 (it opens by itself).

## What it does

- 12 employees with roles (chief of staff, inbox, admin, marketing, content, product, business development, sales, career, research, website, finance).
- Works in demo mode out of the box. Connect any free OpenAI-compatible endpoint in Settings (FreeLLMAPI, Ollama, Groq) for real output.
- Employees only write drafts. Nothing is sent, posted or published without your approval.
- Optional free connectors: web search, news, maps, weather, calendar (iCal link), Gmail (app password), monday.com, Typefully.
- Listens on 127.0.0.1 only. Settings and work are stored in `~/AI-Office-Data` on your own machine.

## Notes

- Add your own details (name, offers, board IDs, keys) in Settings or in the role prompts inside `office.py`. This copy has the personal details removed.
- Never commit your `AI-Office-Data` folder: it holds your keys.
