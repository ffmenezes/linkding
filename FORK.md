# Linkding Fork — trincheira.dev

Fork customizado do [Linkding](https://github.com/sissbruecker/linkding) para uso em `links.trincheira.dev`.

---

## Estrutura de deploy

```
GitHub (ffmenezes/linkding)
  ↓ push to master
GitHub Actions (build.yaml)
  ↓ build Docker image
GHCR (ghcr.io/ffmenezes/linkding:latest)
  ↓ pull image
VPS — Docker Swarm + Traefik + Portainer
  ↓ links.trincheira.dev
Postgres (dados persistentes, externo à imagem)
```

## Como fazer alterações

### 1. Editar código

```bash
cd C:\Users\filip\Projects\trincheira\linkding

# criar branch para a feature (opcional, mas recomendado)
git checkout -b feat/minha-alteracao

# fazer alterações...

# commitar
git add .
git commit -m "feat: descrição da alteração"

# merge na master e push
git checkout master
git merge feat/minha-alteracao
git push origin master
```

### 2. Aguardar build automático

O push para `master` dispara o GitHub Actions automaticamente.

- **Duração:** ~2 minutos
- **Monitorar:** https://github.com/ffmenezes/linkding/actions
- **Imagem publicada em:** `ghcr.io/ffmenezes/linkding:latest`

Para disparar build manualmente (sem push):

```bash
gh workflow run "Build and Push" --repo ffmenezes/linkding --ref master
```

### 3. Redeploy no Portainer

1. Acessar Portainer na VPS
2. Ir no stack do Linkding
3. Clicar em **Update the stack** (com "Re-pull image" marcado)

Alternativa via CLI na VPS:

```bash
docker service update --image ghcr.io/ffmenezes/linkding:latest --force linkding_linkding
```

> Os dados no Postgres e no volume `linkding_data` ficam intactos.

---

## Como atualizar com versão nova do Linkding original

Quando o projeto original lança uma versão nova:

```bash
cd C:\Users\filip\Projects\trincheira\linkding

# buscar atualizações do upstream
git fetch upstream

# merge na master local
git checkout master
git merge upstream/master

# resolver conflitos se houver, depois push
git push origin master
```

O build será disparado automaticamente após o push.

### Dica para conflitos

Conflitos geralmente aparecem nos arquivos que você customizou. Para cada arquivo em conflito:

1. Abra o arquivo no editor
2. Resolva os blocos `<<<<<<< HEAD ... >>>>>>> upstream/master`
3. `git add <arquivo>` e `git commit`

---

## Referência rápida

| Ação | Comando / Local |
|---|---|
| Repo fork | https://github.com/ffmenezes/linkding |
| Repo original | https://github.com/sissbruecker/linkding |
| Actions (builds) | https://github.com/ffmenezes/linkding/actions |
| Imagem Docker | `ghcr.io/ffmenezes/linkding:latest` |
| App em produção | https://links.trincheira.dev |
| Stack file | `trincheira-yt/resources/links-uteis/linkding.yaml` |
| Projeto local | `C:\Users\filip\Projects\trincheira\linkding` |

## Arquivos-chave do Linkding

| Arquivo | O que faz |
|---|---|
| `bookmarks/` | App Django principal (models, views, API, templates) |
| `bookmarks/frontend/` | Web components Lit (JS) |
| `bookmarks/styles/` | Estilos PostCSS (temas light/dark) |
| `bookmarks/templates/` | Templates Django (HTML) |
| `docker/default.Dockerfile` | Dockerfile de produção (multi-stage) |
| `.github/workflows/build.yaml` | CI/CD — build e push da imagem |
| `bootstrap.sh` | Entrypoint do container (migrations, setup) |
| `pyproject.toml` | Dependências Python |
| `package.json` | Dependências Node (frontend build) |

## Dev local (opcional)

Para rodar localmente com hot-reload:

```bash
# instalar dependências
make init

# rodar servidor de desenvolvimento
make serve

# buildar frontend (JS + CSS)
make frontend

# rodar testes
make test
```

Requer Python 3.13 + Node 22 + uv instalados.
