---
name: c4d-validar-refatoracao-git
description: Revisar alterações locais e recentes do Git para validar se uma refatoração preservou o comportamento original e identificar riscos de compilação, runtime, regras de negócio, SQL, validações, telas, eventos, componentes e integrações. Usar quando o usuário pedir revisão de refatoração, análise pré-commit ou pré-deploy, inspeção de diff, avaliação de segurança das últimas alterações ou investigação de possíveis regressões sem modificar arquivos.
---

# Validar Refatoração Git

Executar uma revisão estritamente somente leitura das alterações do repositório. Não editar arquivos, não corrigir código, não criar artefatos, não fazer stage, commit, checkout, reset, stash ou qualquer outro comando que altere o estado do projeto.

## Coletar o contexto

1. Localizar e ler os arquivos `AGENTS.md` aplicáveis antes da análise.
2. Executar, no mínimo:

```text
git status --short
git diff --no-ext-diff
git diff --staged --no-ext-diff
git log --oneline -n 5
```

3. Incluir arquivos não rastreados relevantes listados pelo status, lendo-os diretamente.
4. Ignorar diretórios e artefatos definidos pelo `AGENTS.md`, além de `.git`, binários e arquivos gerados.
5. Identificar claramente o conjunto analisado: alterações unstaged, staged e arquivos não rastreados. Não presumir que commits anteriores fazem parte da refatoração atual.
6. Se o diff for grande, começar por `git diff --stat`, `git diff --name-status` e equivalentes com `--staged`, depois aprofundar arquivo por arquivo.

## Reconstruir o comportamento original

- Comparar cada alteração com a versão anterior usando comandos somente leitura, como `git show HEAD:<arquivo>`, `git diff -U80`, `git log -p -- <arquivo>` e `git blame`.
- Ler o contexto ao redor do trecho alterado, seus chamadores, declarações, tipos relacionados e fluxos de entrada e saída.
- Procurar todas as referências de métodos, campos, componentes, eventos, units e identificadores renomeados ou removidos.
- Distinguir mudança intencional de refatoração comportamental. Não tratar diferenças meramente estilísticas como risco.
- Não afirmar que o comportamento foi preservado apenas porque o código parece equivalente; verificar efeitos colaterais, ordem de execução, exceções, valores padrão e caminhos alternativos.

## Revisar riscos

Verificar, conforme os arquivos afetados:

- Compilação: símbolos ausentes, assinaturas incompatíveis, overloads, visibilidade, tipos, casts, generics, units em `uses`, referências de projeto e condicionais de compilação.
- Runtime: `nil`, objetos não inicializados ou liberados, ownership, ciclos de vida, índices, listas vazias, conversões, exceções, concorrência e recursos.
- Regras de negócio: condições invertidas, perda de validação, mudança de precedência, retornos antecipados, valores padrão, arredondamento, datas, status e permissões.
- SQL e dados: parâmetros ausentes, nomes de campos, aliases, joins, filtros, precedência de `AND`/`OR`, nulos, vazios, tipos, cardinalidade, transações e mudança inesperada de resultado.
- Delphi visual: correspondência entre `.pas` e `.dfm`/`.fmx`, nomes e classes de componentes, eventos associados, propriedades, `DataSource`, `DataField`, tab order e referências removidas.
- Integrações: contratos, JSON/XML, endpoints, autenticação, headers, timeouts, códigos de retorno, serialização e compatibilidade com consumidores.
- Fluxos não tratados: entrada vazia, nula, zero, negativa, duplicada, limite, falha externa e cancelamento.
- Projeto Delphi: novas units presentes em `.dpr` e `.dproj`, forms registrados e recursos referenciados corretamente.

Ignorar alterações que apenas atualizem as constantes de versão `MAJOR`, `MINOR`, `PATCH` ou `PRE_RELEASE` em `VersaoSistema.pas`. Não investigar os efeitos desse incremento, nem reportar achados, ressalvas, testes ou limitações relacionados ao versionamento. Continuar revisando normalmente qualquer outra mudança funcional feita nesse arquivo.

Aplicar também as convenções do `AGENTS.md` como possíveis fontes de defeito, dando prioridade a regras que afetam compilação ou comportamento. Reportar violações puramente estilísticas apenas como risco baixo e somente quando forem relevantes para a alteração.

## Classificar achados

- **🟥 Crítico**: alta probabilidade de impedir compilação, causar falha grave, corrupção/perda de dados, quebra de regra central, contrato incompatível ou deploy inseguro.
- **🟨 Médio**: regressão plausível em cenários específicos, validação ausente, resultado incorreto, falha de tela/evento ou fluxo relevante não tratado.
- **🟦 Baixo**: fragilidade, inconsistência limitada, manutenção arriscada ou convenção violada sem quebra imediata demonstrável.

Basear cada achado em evidência concreta do diff e do código relacionado. Quando não for possível confirmar, marcar explicitamente como hipótese e informar o que falta verificar. Evitar alarmes genéricos e duplicados.

## Produzir o relatório

Começar pelos achados, ordenados por severidade. Para cada risco, informar:

````markdown
🟥 [Crítico 01] - Título objetivo, 🟥 [Crítico 02] - Título objetivo, e assim por diante. Para os demais níveis, usar 🟨 [Médio 01] - e 🟦 [Baixo 01] -. Separar cada achado com um separador (`---` do Markdown).

**Arquivo:** [NomeDoArquivo.pas](/caminho/absoluto/NomeDoArquivo.pas:linha)

```text
NomeDoArquivo.pas
```

**Trecho/método:** símbolo ou linhas aproximadas
**Risco:** Consequência possível
**Motivo:** Evidência e comparação com o comportamento anterior
**Sugestão:** Correção ou verificação recomendada, sem alterar arquivos
````

No bloco `text`, informar somente o nome base do arquivo, sem diretório, link, número da linha ou outro texto, para permitir que o botão nativo copie apenas o nome. Manter o caminho absoluto e, quando disponível, a linha exclusivamente no link **Arquivo:**. O link deve abrir a visualização interna do Codex; não usar `file://` nem esquemas de aplicativos externos.

Quando um achado envolver mais de um arquivo, repetir para cada arquivo o bloco copiável, o link de visualização. Se não houver uma linha específica, omitir `:linha` do destino do link.

Depois dos achados, adicionar obrigatoriamente um separador Markdown e apresentar as quantidades reais neste formato:

```markdown
---

**Resultado**

🟥 Críticos: 0
🟨 Médios: 0
🟦 Baixos: 0
```

Substituir os zeros pelas quantidades identificadas na análise. Depois incluir:

1. **Testes recomendados**: separar testes automatizados e manuais, priorizando os fluxos afetados.
2. **Veredito**: escolher entre:
   - `Não parece segura para commit/deploy`;
   - `Parece segura com ressalvas`;
   - `Parece segura para commit/deploy`.
3. **Limitações da análise**: registrar, de forma breve, itens não verificados, como ausência de compilação, testes ou acesso a serviços.

Se não houver achados, dizer explicitamente que nenhum risco concreto foi identificado, sem omitir riscos residuais e testes recomendados. Nunca apresentar uma análise estática como garantia de ausência de regressões.
