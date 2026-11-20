# ðŸŽµ musicaclone

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="48" />
  <p><strong>Arckhé AI</strong></p>
</div>


**ðŸ‡§ðŸ‡· [PortuguÃªs](README.md) Â· ðŸ‡ºðŸ‡¸ [English](README.en.md) Â· ðŸ‡ªðŸ‡¸ [EspaÃ±ol](README.es.md)**

CLI para **clonar** ou **criar** mÃºsica a partir de um link, usando Suno pela API do Kie.
Clonar e criar sÃ£o caminhos diferentes â€” o script trata assim, e trava o que costuma dar errado no meio.

## ðŸ“– Guia de uso

Guia completo (landing + passo a passo): **https://inematds.github.io/musicaclone/guia/**

## ðŸŽµ CatÃ¡logo das mÃºsicas

Todas as mÃºsicas geradas, com o prompt de estilo, as mediÃ§Ãµes em grÃ¡fico e o player: **https://inematds.github.io/musicaclone/guia/musicas.html**

## ðŸŽ¬ Como criamos os clipes

**Onde a produÃ§Ã£o parou:** [docs/clipes/ESTADO.md](docs/clipes/ESTADO.md)

Pipeline completo (imagens no flux2-klein, animaÃ§Ã£o por keyframe na Agnes, montagem em ffmpeg com plano de cenas), com os prompts usados e as armadilhas resolvidas: **[docs/clipes](docs/clipes/)**

## Por que existe

Mandar um link de mÃºsica pra um agente genÃ©rico dÃ¡ resultado ruim de trÃªs jeitos:
ele transcreve em vez de gerar, gera em cima de um trecho de 29 s achando que Ã© a
mÃºsica inteira, ou gera com uma letra que a transcriÃ§Ã£o cortou pela metade.
Este CLI pÃµe um portÃ£o em cada um desses pontos.

## InstalaÃ§Ã£o

```bash
git clone https://github.com/inematds/musicaclone.git && cd musicaclone
sudo apt install jq ffmpeg && pipx install yt-dlp

cat > .env <<'EOF'
KIE_API_KEY=sua-chave-de-kie.ai/api-key
GOOGLE_API_KEY=sua-chave-do-gemini
EOF
```

As chaves sÃ£o lidas em runtime e nunca versionadas (o `.gitignore` cobre `.env`).

## Uso

```bash
# CLONE â€” a partir de um link
bash musica.sh prep "https://youtube.com/watch?v=..." minha-faixa  # baixa + analisa (grÃ¡tis)
bash musica.sh spec minha-faixa                                    # o portÃ£o: confira antes
bash musica.sh clone minha-faixa --audio-weight 0.85               # gera (gasta crÃ©dito)

# CRIAÃ‡ÃƒO â€” do zero
bash musica.sh cria meu-tema --style "epic cinematic pop, female belt vocals, war drums, 118bpm" \
                             --title "Se Paga" --voz f --letra letra.txt
bash musica.sh regen meu-tema

# comuns
bash musica.sh status <slug|taskId>
bash musica.sh get <slug|taskId>     # baixa as faixas e mede o custo real
bash musica.sh saldo                 # crÃ©ditos no Kie
bash enviar.sh faixa-1.mp3 "legenda" # manda no Telegram (opcional)
```

SaÃ­da em `~/projetos/output/musicas/<slug>/` (mude com `MUSICA_OUT`):
`ref.mp3`, `analise.json`, `spec.json`, `faixa-1.mp3`, `faixa-2.mp3`.

## Os portÃµes

| PortÃ£o | O que pega |
|---|---|
| **Ã‰ mÃºsica?** | Gemini classifica o Ã¡udio. Entrevista, narraÃ§Ã£o, ruÃ­do â†’ bloqueia e diz o que era. |
| **Letra completa?** | Voz cantada corta fÃ¡cil na transcriÃ§Ã£o. Se veio truncada, avisa antes de gerar. Corrige com `--letra arquivo.txt`. |
| **Fonte curta?** | O `spec` mostra a duraÃ§Ã£o. 29 s Ã© teaser, nÃ£o Ã© a mÃºsica. |
| **Custo** | Nada gera sem vocÃª olhar o `spec` primeiro. O `taskId` Ã© gravado antes do poll: se cair, vocÃª retoma em vez de pagar de novo. |

## Ajustes

`--model` (V4 â€¦ V5_5) Â· `--style` Â· `--negative` Â· `--voz m|f` Â· `--style-weight` Â·
`--audio-weight` (sÃ³ clone: 0.9 cola no original, 0.4 sÃ³ inspira) Â· `--weirdness` Â·
`--duration` (sÃ³ V5_5) Â· `--instrumental` Â· `--sem-referencia`.

Tudo vive no `spec.json`, entÃ£o `regen` refaz com o ajuste sem repetir download nem anÃ¡lise.

## Escrevendo a letra (pegadinha real)

Estrutura e direÃ§Ã£o vÃ£o em **colchetes**; parÃªnteses o Suno costuma **cantar**
como ad-lib. Ou seja, `(Homem, falado, grave)` vira vocal cantando "homem,
falado, grave". O certo:

```
[Intro] [Spoken Word] [Male Vocal]
NinguÃ©m te conta o preÃ§o

[Chorus] [Female Vocal] [Belting]
E eu vou! Mesmo que doa, mesmo que arda
(Na pedra!)          <- isto SIM Ã© pra ser cantado, entÃ£o parÃªntese estÃ¡ certo
[Gang Vocals]
Vale a pena, vale a pena
```

Tags Ãºteis: `[Verse]` `[Chorus]` `[Bridge]` `[Outro]` `[Male Vocal]`
`[Female Vocal]` `[Spoken Word]` `[Gang Vocals]` `[Belting]` `[Whisper]`.

## Custo (medido, 2026-08-05)

**12 crÃ©ditos por geraÃ§Ã£o** (V5, custom mode, 2 faixas de ~3 min) â‰ˆ US$ 0,06 â€”
uns US$ 0,03 por faixa utilizÃ¡vel. O script mede sozinho e grava em
`spec.json:creditos_gastos`.

Comparativo: ElevenLabs Music cobra por minuto (~US$ 0,30â€“0,40/min, ou seja
~US$ 1,00â€“1,20 por faixa de 3 min) e nÃ£o faz clone de referÃªncia. Udio Ã© crÃ©dito
por geraÃ§Ã£o mas sem API pÃºblica madura. Suno direto sai mais barato por mÃºsica na
assinatura, sÃ³ que sem API oficial.

## API usada (verificada em 2026-08-05)

| O quÃª | Endpoint |
|---|---|
| criar do zero | `POST https://api.kie.ai/api/v1/generate` |
| clone com referÃªncia | `POST https://api.kie.ai/api/v1/generate/upload-cover` |
| status/resultado | `GET .../api/v1/generate/record-info?taskId=` |
| crÃ©ditos | `GET .../api/v1/chat/credit` |
| upload do Ã¡udio | `POST https://kieai.redpandaai.co/api/file-stream-upload` |

ReferÃªncia: mÃ¡x 8 min. Arquivo enviado some em 24 h, faixa gerada em 15 dias â€”
por isso `get` baixa na hora. `callBackUrl` Ã© obrigatÃ³rio na prÃ¡tica (422 sem ele),
mesmo o doc marcando como opcional; mandamos um placeholder e usamos poll.

## LicenÃ§a

MIT.

---

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="32" />
  <p><strong>Arckhé AI</strong></p>
</div>