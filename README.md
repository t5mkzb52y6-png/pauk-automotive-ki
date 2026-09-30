# PAUK Automotive KI-Fahrzeugbewertung – Cloudflare Workers

Fertiges GitHub/Cloudflare-Workers-Projekt.

## Cloudflare
1. Projekt in ein GitHub-Repository hochladen.
2. Cloudflare → Workers & Pages → Create → Continue with GitHub.
3. Repository auswählen.
4. Cloudflare erkennt `wrangler.jsonc` und deployt `src/index.js` als Worker.
5. Im Worker unter **Settings → Variables and Secrets** ein Secret anlegen:
   - Name: `OPENAI_API_KEY`
   - Wert: eigener OpenAI API-Key
6. Neu deployen und in der PAUK-App **KI-Verbindung testen**.

Der API-Key gehört niemals in `src/index.js` oder ins GitHub-Repository.
