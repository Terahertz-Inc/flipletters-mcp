# Publishing to the official MCP Registry

This folder is the content for the public listing repo
**`Terahertz-Inc/flipletters-mcp`**. The official registry (registry.modelcontextprotocol.io)
reads `server.json`; other directories (Glama, MCP Market, MCP.Directory,
PulseMCP, …) copy from the registry or want a public repo with a README and
LICENSE.

Nothing here has been published. Do these steps on your own machine.

## 0. Before you start

- [ ] Confirm the license in `LICENSE` (MIT placeholder; it only covers the
      files in this listing repo, not the hosted service).
- [ ] Create the public repo `Terahertz-Inc/flipletters-mcp` and copy this folder's
      contents to its root. `server.json` → `repository.url` already points
      there.
- [ ] Install the CLI: `brew install mcp-publisher` (or download
      `mcp-publisher` from https://github.com/modelcontextprotocol/registry/releases).
- [ ] OpenSSL 3 (macOS LibreSSL can't do Ed25519): `brew install openssl@3`,
      then use `$(brew --prefix openssl@3)/bin/openssl` below.

## 1. Generate the domain key (once per domain)

```bash
OPENSSL="$(brew --prefix openssl@3)/bin/openssl"   # or plain `openssl` on Linux
DOMAIN="flipletters.com"
mkdir -p ~/.mcp-registry-keys && cd ~/.mcp-registry-keys
"$OPENSSL" genpkey -algorithm Ed25519 -out "$DOMAIN.pem"
chmod 600 "$DOMAIN.pem"

# Public key (safe to publish) → goes in the hosting env var
"$OPENSSL" pkey -in "$DOMAIN.pem" -pubout -outform DER | tail -c 32 | base64
```

Back up `~/.mcp-registry-keys/$DOMAIN.pem` in 1Password. **Never commit
the .pem or the private hex.**

## 2. Serve the proof file

The registry fetches `https://flipletters.com/.well-known/mcp-registry-auth`
(apex domain, no redirects) and expects one line:

```
v=MCPv1; k=ed25519; p=<base64 public key from step 1>
```

`flipletters.com` is Firebase Hosting; unknown paths go to the `agentSite`
function (`functions/`), which now serves this file from an env var:

1. Put the public key in `functions/.env` (public keys are fine to commit;
   see `functions/.env.example`):
   `MCP_REGISTRY_PUBLIC_KEY=<base64 public key>`
2. Redeploy only the `agent-site` functions codebase:
   `firebase deploy --only functions:agent-site`. Hosting doesn't need a
   rebuild; its `**` rewrite already sends this path to the function.

Check it:

```bash
curl -s -H 'Accept: text/plain' https://flipletters.com/.well-known/mcp-registry-auth
# → v=MCPv1; k=ed25519; p=...
```

(Alternative: DNS proof. Put the same string in an apex TXT record on
`flipletters.com` and use `mcp-publisher login dns` instead of `login http`.)

## 3. Log in and publish

```bash
cd path/to/Terahertz-Inc/flipletters-mcp   # the folder with server.json
PRIVATE_KEY="$("$OPENSSL" pkey -in ~/.mcp-registry-keys/flipletters.com.pem -noout -text | grep -A3 'priv:' | tail -n +2 | tr -d ' :\n')"
mcp-publisher login http --domain flipletters.com --private-key "$PRIVATE_KEY"
unset PRIVATE_KEY
mcp-publisher publish

curl -s "https://registry.modelcontextprotocol.io/v0/servers?search=com.flipletters/mcp" | jq '.servers[].server | {name, version}'
```

The login token is short-lived and saved locally; run `mcp-publisher
logout` when done. The server name (`com.flipletters/mcp`) is permanent. To ship a
change later, bump `version` in `server.json` and publish again.
