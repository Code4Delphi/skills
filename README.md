# Skills - Habilidades (Exemplos)

É um conjunto reutilizável de instruções que ensina o agente a executar uma tarefa específica.


## Links úteis
|[Documentação Skills Codex](https://developers.openai.com/codex/skills)|[OpenAI Academy Skills](https://openai.com/pt-BR/academy/skills/)|
|--|--|

---

## Comandos disponíveis no CLI ou IDE

| Comando             | Uso                                                                                                                                  |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `skill list`        | Lista as skills disponíveis                                                                                                          |
| `/skills`           | Abre a lista de skills disponíveis para você escolher uma. Ao selecionar, o Codex insere o contexto da skill na próxima solicitação. |
| `$`                 | Permite mencionar/invocar uma skill diretamente no prompt. Exemplo: `$skill-creator` ou `$nome-da-skill`.                            |
| `$skill-creator`    | Skill/comando usado para criar uma nova skill a partir de perguntas guiadas.                                                         |
| Invocação implícita | Não é um comando, mas o Codex pode escolher automaticamente uma skill quando a tarefa combina com a `description` da skill.          |

---

##  Skills disponíveis

### ➡️ 1️⃣ Refatora sintaxe Delphi `$c4d-refatorar-sintaxe-delphi`

Refatora units Delphi/Object Pascal conforme os padrões de sintaxe, formatação e nomenclatura do projeto, preservando o comportamento original.

```text
Use a skill $c4d-refatorar-sintaxe-delphi para refatorar a unit MinhaUnit.pas
```

```
$c4d-refatorar-sintaxe-delphi
```

### ➡️ 2️⃣ Validar refatoração pelo Git Diff `$c4d-validar-refatoracao-git`

Analisa as alterações do Git em modo somente leitura, procurando possíveis regressões, erros de compilação, problemas de runtime e alterações indevidas em regras de negócio.

- Alterações antes do commit
```text
Use a skill $c4d-validar-refatoracao-git para revisar as alterações atuais
```

- Entre 2 branchs
```
Use a skill $c4d-validar-refatoracao-git para revisar as alterações da branch <nome_branch> com relação a branch develop
```

- Só nome skill
```
$c4d-validar-refatoracao-git
```

## Local
- 📍 O local padrão, para uma skill pessoal/global, que vale para qualquer projeto. Pela documentação atual do Codex, é:
```
C:\Users\NOME_USUARIO\.agents\skills
```


- Para uma skill específica de um projeto:
```
C:\Projeto\.agents\skills
```

- Já esta é usada por skills internas/sistema, **e não é o local recomendado atual para suas skills pessoais**.
```
C:\Users\NOME_USUARIO\.codex\skills
```


## 📞 Contatos
[![Telegram](https://img.shields.io/badge/Telegram-Join-blue?logo=telegram)](https://t.me/Code4Delphi)
[![YouTube](https://img.shields.io/badge/YouTube-Join-red?logo=youtube&logoColor=red)](https://www.youtube.com/@code4delphi)
[![Instagram](https://img.shields.io/badge/Intagram-Follow-red?logo=instagram&logoColor=pink)](https://www.instagram.com/code4delphi/)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://www.linkedin.com/in/cesar-cardoso-dev)
[![Blog](https://img.shields.io/badge/Blog-Code4Delphi-F00?logo=delphi)](https://code4delphi.com.br/blog/)
[![Course](https://img.shields.io/badge/Course-Delphi-F00?logo=delphi)](https://code4delphi.github.io/ia-devs-delphi-lazarus/)
[![E-mail](https://img.shields.io/badge/E--mail-Send-yellowgreen?logo=maildotru&logoColor=yellowgreen)](mailto:contato@code4delphi.com.br)
