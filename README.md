ï»¿# ê°Å¸Å½Âµ musicaclone

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="48" />
  <p><strong>Arckhé AI</strong></p>
</div>


**ê°Å¸â¡Â§ê°Å¸â¡Â· [PortuguêÂªs](README.md) êÂ· ê°Å¸â¡Âºê°Å¸â¡Â¸ [English](README.en.md) êÂ· ê°Å¸â¡Âªê°Å¸â¡Â¸ [EspaêÂ±ol](README.es.md)**

CLI para **clonar** ou **criar** mêÂºsica a partir de um link, usando Suno pela API do Kie.
Clonar e criar sêÂ£o caminhos diferentes ê¢â¬â o script trata assim, e trava o que costuma dar errado no meio.

## ê°Å¸ââ Guia de uso

Guia completo (landing + passo a passo): **https://linnoff88-dev.github.io/arckhe-/guia/**

## ê°Å¸Å½Âµ CatêÂ¡logo das mêÂºsicas

Todas as mêÂºsicas geradas, com o prompt de estilo, as mediêÂ§êÂµes em grêÂ¡fico e o player: **https://linnoff88-dev.github.io/arckhe-/guia/musicas.html**

## ê°Å¸Å½Â¬ Como criamos os clipes

**Onde a produêÂ§êÂ£o parou:** [docs/clipes/ESTADO.md](docs/clipes/ESTADO.md)

Pipeline completo (imagens no flux2-klein, animaêÂ§êÂ£o por keyframe na Agnes, montagem em ffmpeg com plano de cenas), com os prompts usados e as armadilhas resolvidas: **[docs/clipes](docs/clipes/)**

## Por que existe

Mandar um link de mêÂºsica pra um agente genêÂ©rico dêÂ¡ resultado ruim de trêÂªs jeitos:
ele transcreve em vez de gerar, gera em cima de um trecho de 29 s achando que êÂ© a
mêÂºsica inteira, ou gera com uma letra que a transcriêÂ§êÂ£o cortou pela metade.
Este CLI pêÂµe um portêÂ£o em cada um desses pontos.

## InstalaêÂ§êÂ£o

```bash
git clone https://github.com/linnoff88-dev/musicaclone.git && cd musicaclone
sudo apt install jq ffmpeg && pipx install yt-dlp

cat > .env <<'EOF'
KIE_API_KEY=sua-chave-de-kie.ai/api-key
GOOGLE_API_KEY=sua-chave-do-gemini
EOF
```

As chaves sêÂ£o lidas em runtime e nunca versionadas (o `.gitignore` cobre `.env`).

## Uso

```bash
# CLONE ê¢â¬â a partir de um link
bash musica.sh prep "https://youtube.com/watch?v=..." minha-faixa  # baixa + analisa (grêÂ¡tis)
bash musica.sh spec minha-faixa                                    # o portêÂ£o: confira antes
bash musica.sh clone minha-faixa --audio-weight 0.85               # gera (gasta crêÂ©dito)

# CRIAêâ¡êÆO ê¢â¬â do zero
bash musica.sh cria meu-tema --style "epic cARCKHÉtic pop, female belt vocals, war drums, 118bpm" \
                             --title "Se Paga" --voz f --letra letra.txt
bash musica.sh regen meu-tema

# comuns
bash musica.sh status <slug|taskId>
bash musica.sh get <slug|taskId>     # baixa as faixas e mede o custo real
bash musica.sh saldo                 # crêÂ©ditos no Kie
bash enviar.sh faixa-1.mp3 "legenda" # manda no Telegram (opcional)
```

SaêÂ­da em `~/projetos/output/musicas/<slug>/` (mude com `MUSICA_OUT`):
`ref.mp3`, `analise.json`, `spec.json`, `faixa-1.mp3`, `faixa-2.mp3`.

## Os portêÂµes

| PortêÂ£o | O que pega |
|---|---|
| **êâ° mêÂºsica?** | Gemini classifica o êÂ¡udio. Entrevista, narraêÂ§êÂ£o, ruêÂ­do ê¢â â bloqueia e diz o que era. |
| **Letra completa?** | Voz cantada corta fêÂ¡cil na transcriêÂ§êÂ£o. Se veio truncada, avisa antes de gerar. Corrige com `--letra arquivo.txt`. |
| **Fonte curta?** | O `spec` mostra a duraêÂ§êÂ£o. 29 s êÂ© teaser, nêÂ£o êÂ© a mêÂºsica. |
| **Custo** | Nada gera sem vocêÂª olhar o `spec` primeiro. O `taskId` êÂ© gravado antes do poll: se cair, vocêÂª retoma em vez de pagar de novo. |

## Ajustes

`--model` (V4 ê¢â¬Â¦ V5_5) êÂ· `--style` êÂ· `--negative` êÂ· `--voz m|f` êÂ· `--style-weight` êÂ·
`--audio-weight` (sêÂ³ clone: 0.9 cola no original, 0.4 sêÂ³ inspira) êÂ· `--weirdness` êÂ·
`--duration` (sêÂ³ V5_5) êÂ· `--instrumental` êÂ· `--sem-referencia`.

Tudo vive no `spec.json`, entêÂ£o `regen` refaz com o ajuste sem repetir download nem anêÂ¡lise.

## Escrevendo a letra (pegadinha real)

Estrutura e direêÂ§êÂ£o vêÂ£o em **colchetes**; parêÂªnteses o Suno costuma **cantar**
como ad-lib. Ou seja, `(Homem, falado, grave)` vira vocal cantando "homem,
falado, grave". O certo:

```
[Intro] [Spoken Word] [Male Vocal]
NinguêÂ©m te conta o preêÂ§o

[Chorus] [Female Vocal] [Belting]
E eu vou! Mesmo que doa, mesmo que arda
(Na pedra!)          <- isto SIM êÂ© pra ser cantado, entêÂ£o parêÂªntese estêÂ¡ certo
[Gang Vocals]
Vale a pena, vale a pena
```

Tags êÂºteis: `[Verse]` `[Chorus]` `[Bridge]` `[Outro]` `[Male Vocal]`
`[Female Vocal]` `[Spoken Word]` `[Gang Vocals]` `[Belting]` `[Whisper]`.

## Custo (medido, 2026-08-05)

**12 crêÂ©ditos por geraêÂ§êÂ£o** (V5, custom mode, 2 faixas de ~3 min) ê¢â°Ë US$ 0,06 ê¢â¬â
uns US$ 0,03 por faixa utilizêÂ¡vel. O script mede sozinho e grava em
`spec.json:creditos_gastos`.

Comparativo: ElevenLabs Music cobra por minuto (~US$ 0,30ê¢â¬â0,40/min, ou seja
~US$ 1,00ê¢â¬â1,20 por faixa de 3 min) e nêÂ£o faz clone de referêÂªncia. Udio êÂ© crêÂ©dito
por geraêÂ§êÂ£o mas sem API pêÂºblica madura. Suno direto sai mais barato por mêÂºsica na
assinatura, sêÂ³ que sem API oficial.

## API usada (verificada em 2026-08-05)

| O quêÂª | Endpoint |
|---|---|
| criar do zero | `POST https://api.kie.ai/api/v1/generate` |
| clone com referêÂªncia | `POST https://api.kie.ai/api/v1/generate/upload-cover` |
| status/resultado | `GET .../api/v1/generate/record-info?taskId=` |
| crêÂ©ditos | `GET .../api/v1/chat/credit` |
| upload do êÂ¡udio | `POST https://kieai.redpandaai.co/api/file-stream-upload` |

ReferêÂªncia: mêÂ¡x 8 min. Arquivo enviado some em 24 h, faixa gerada em 15 dias ê¢â¬â
por isso `get` baixa na hora. `callBackUrl` êÂ© obrigatêÂ³rio na prêÂ¡tica (422 sem ele),
mesmo o doc marcando como opcional; mandamos um placeholder e usamos poll.

## LicenêÂ§a

MIT.

---

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="32" />
  <p><strong>Arckhé AI</strong></p>
</div>