# zero7-live

Página de redirecionamento em **zero7.com.br/live**: dispara os pixels da Zero7 com as UTMs
e manda o visitante para `https://www.youtube.com/@zero7tesouraria/live/` (com a query string original).

## Como funciona

- Tela de carregamento (logo + barra), um único `index.html` com CSS/JS inline.
- **GTM `GTM-KCJQPMC`** (o mesmo do site principal) carrega imediatamente: GA4 `G-K81WL9X541`,
  Google Ads `AW-11118566280`, Hotjar, RD Station e as tags do Meta.
- **Meta Pixel** `553722946901869` e `757410515694979`: iniciado direto na página (pelo GTM ele
  chegaria tarde demais). Dispara `PageView` (com `eventID`, igual ao site) e `AcessoLive` com as UTMs.
- **CAPI**: `PageView` com o mesmo `eventID` para `backend-api-zero7.vercel.app` (mesmo padrão do site).
- **GA4**: evento `live_redirect` com `utm_source`, `utm_medium`, `utm_campaign`, `utm_content` e `utm_term`.
- **dataLayer**: `{ event: 'live_redirect', utm_* }` para o gestor usar no GTM, se quiser.
- Redireciona com `location.replace` assim que os pixels saem, **no máximo em 2s**, mesmo com adblock
  ou erro. Link manual aparece após 3s. Sem JS: `<noscript>` com meta refresh.

## Testar

```bash
npx serve -l 5055 .
# abra http://localhost:5055/?utm_source=teste&utm_campaign=teste
```

Em `localhost` o GTM não dispara as tags (o container filtra por domínio), só o Meta. O teste
completo é em produção: abra `https://zero7.com.br/live?utm_source=teste&utm_campaign=teste`
com o DevTools > Network com "Preserve log" ligado e procure `facebook.com/tr` (PageView/AcessoLive)
e `g/collect` (`en=live_redirect`). No GA4, confira em Relatórios > Tempo real.

## Deploy (TurboCloud / cPanel)

cPanel > Git Version Control > Create > clonar este repo em `public_html/live`
(a pasta precisa estar vazia ou não existir). Para atualizar: Manage > Pull or Deploy > Update from Remote.
O `.htaccess` bloqueia `.git`/arquivos ocultos, cuida do cache e do `/live` → `/live/` (mantendo as UTMs).
