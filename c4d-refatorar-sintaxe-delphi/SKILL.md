---
name: c4d-refatorar-sintaxe-delphi
description: Refatorar código Delphi/Object Pascal para padronizar sintaxe, indentação, nomenclatura, uses, helpers de string, chamadas de métodos e validações obrigatórias simples com ExceptionOb. Usar ao editar ou revisar arquivos .pas, .dpr e trechos Delphi conforme as convenções do projeto, sem alterar arquivos .dfm ou .fmx.
---

# Refatorar Sintaxe Delphi

Padronizar código Delphi preservando assinaturas necessárias, SQL e integrações. Permitir a modernização de validações obrigatórias simples para `ExceptionOb` conforme esta skill; fora desse caso, preservar o fluxo e o comportamento existentes.

## Fluxo obrigatório

1. Ler o `AGENTS.md` aplicável e tratar suas regras como complemento prioritário.
2. Inspecionar o arquivo inteiro e localizar declarações, referências e artefatos relacionados antes de renomear símbolos.
3. Aplicar somente mudanças sintáticas e de nomenclatura solicitadas. Não aproveitar a tarefa para alterar regras de negócio.
4. Atualizar todas as referências ao renomear símbolos, incluindo declarações de classe, implementações, propriedades, eventos, `.dpr` e `.dproj` quando aplicável.
5. Não alterar arquivos `.dfm` ou `.fmx` nesta skill. Se a refatoração exigir mudança em evento, componente ou propriedade visual, parar e informar que está fora do escopo desta skill.
6. Revisar o diff para confirmar que nenhuma simplificação mudou escopo ou fluxo.
7. Compilar ou executar a validação mais próxima disponível.

## Nomenclatura

- Usar PascalCase em variáveis, parâmetros, campos, métodos, classes, tipos e demais identificadores, respeitando prefixos específicos definidos pelo projeto.
- Iniciar parâmetros com `A` maiúsculo: `ANome`, `ADataInicial`.
- Iniciar variáveis locais com `L` maiúsculo: `LNome`, `LTotal`.
- Substituir inicio de variáveis locais iniciadas com `v` ou `V` para `L`.
- Preservar a variável de controle `i` com esse nome. Não renomear `i` para `LIndex`.
- Iniciar campos de instância com `F` maiúsculo: `FNome`, `FCliente`.
- Escrever o tipo `string` sempre em minúsculas.
- Preservar nomes exigidos por bibliotecas, frameworks, métodos sobrescritos, interfaces, serialização ou integrações quando a alteração puder quebrar contratos externos.
- Quando a instrução citar `Variavel`, interpretar como variável local `LVariavel` ou campo de instância `FVariavel`.
- Quando a instrução citar `Variavel_ou_Parametro`, interpretar como variável local `LVariavel`, campo de instância `FVariavel` ou parâmetro `AParametro`.
- Manter sempre uma declaração de variável por linha:

```delphi
LPassedKey: string;
LNewKey: string;
LTempS: string;
```

Não manter múltiplas variáveis na mesma declaração:

```delphi
LVar1, LVar2, LVar3: string;
```

- Preferir declaração local inline quando o valor puder ser atribuído no ponto da declaração:

```delphi
var LId := 10;
```

## Formatação

- Usar 2 espaços por nível de indentação. Não usar tabulações.
- Manter exatamente um espaço antes e depois de `:=`.
- Manter exatamente um espaço antes e depois de `=`.
- Colocar cada unit da cláusula `uses` em uma linha separada.
- Ao adicionar units, colocá-las na cláusula `uses` da interface, conforme as regras do projeto.
- Sempre quebrar a linha depois de `then`, `do` e `else`.
- Assinaturas curtas podem ficar em uma linha quando continuarem legíveis.
- Remover comentários vazios como `{ Private declarations }` e `{ Public declarations }` quando não agregarem informação.
- Seções de visibilidade vazias, como `public`, podem ficar sem comentários automáticos desde que o código continue válido e consistente com o padrão local.
- Remover parênteses desnecessários nas declarações de métodos, por exemplo: alterar `SerialRecuperarDados(): Boolean;` para `SerialRecuperarDados: Boolean;`
- Remover parênteses externos de condições quando não forem necessários para precedência ou legibilidade. Esta regra é obrigatória para comparações simples, expressões booleanas simples e seletores de `case`:

Substituir:
```delphi
if (condicao) then
```

Por:
```delphi
if condicao then
```

Substituir:
```delphi
case (condicao) of
```

Por:
```delphi
case condicao of
```

## Blocos e fluxo

- Remover `begin`/`end` quando o bloco controlado tiver somente um comando. Aplicar também em cadeias `if`/`else if`/`else` e em `for`, preservando o `else` vinculado ao `if` correto.
- Quando remover `begin`/`end` antes de um `else`, remover também o ponto e vírgula do comando anterior ao `else`, conforme a sintaxe Delphi.
- Manter `begin`/`end` quando houver dois ou mais comandos pertencentes ao mesmo `if`, `else`, `for`, `while`, `with` ou bloco semelhante.
- Em `case`, remover parênteses do seletor e indentar opções com 2 espaços em relação ao `case`. Manter `else` e o `end` final alinhados com `case`:

```delphi
case LFichaRegistro.Tipo of
  1:
    // código com um comando
  2:
  begin
    // código
    // código
  end;
else
  // código
end;
```

- Em `case`, permitir a lista de comandos própria do `else` sem `begin`/`end` quando a sintaxe Delphi preservar o mesmo agrupamento.
- Nunca remover blocos se isso alterar a associação de um `else`, o alcance de um loop, a execução de `Exit` ou o tratamento de exceções.
- Quando uma validação obrigatória simples for convertida para `ExceptionOb`, remover `Boolean`, `Result`, `if not ... then Exit` e demais retornos usados apenas para controlar essa validação.
- Após converter validações obrigatórias para exception, manter o fluxo como: chamar `Self.ValidarCamposObrigatorios;` e continuar o processamento normalmente.
- Não converter validações com mensagens customizadas, rollback, logs, confirmações, múltiplos efeitos colaterais ou regras de negócio adicionais sem pedido explícito.
- Em `except`, deixar `except` em linha própria quando o handler tiver `begin`/`end` por conter mais de um comando. Indentar o `on E: Exception do` com 2 espaços:

```delphi
except
  on E: Exception do
  begin
    // código com mais de 1 linha
  end
end;
```

- Em `except` com apenas um comando no handler, manter o `on E: Exception do` na mesma linha do `except` e remover `begin`/`end`:

```delphi
except on E: Exception do
  // código com 1 linha
end;
```

- Inserir uma linha em branco entre blocos `if` sequenciais no mesmo nível, principalmente quando cada `if` tiver bloco com mais de uma linha:

```delphi
if LCondicao1 then
begin
  // código
  // código
end;

if LCondicao2 then
begin
  // código
  // código
end;
```

```delphi
if LTemp.IsEmpty then
  LStr := LStr + Self.CriptCaracteres01(LTemp);

for var i := 1 to LPassedKey.Length do
  LCorrectKey := LCorrectKey + ' teste';
```

## Substituições preferenciais

- Trocar subtração unitária usada como limite ou índice por `Pred()`:

```delphi
for var i := 1 to Pred(StringGrid1.RowCount) do
```

- Preferir `.IsEmpty` em comparações de strings com `''`, ajustando negações corretamente.
- Para variáveis ou parâmetros do tipo `string`, usar o helper `.Trim` em vez da função `Trim()`:

```delphi
Variavel_ou_Parametro.Trim
```

- Ao comparar com vazio o retorno de função que retorna `string`, usar helper e `.IsEmpty`:

```delphi
FuncaoRetornaString('Test').Trim.IsEmpty
```

- De modo geral, usar o helper `.Trim` em vez da função `Trim()` sempre que o alvo for uma variável, campo, parâmetro, propriedade ou expressão string que aceite helper sem alterar o comportamento.
- Para componentes que possuem propriedade `Text`, manter a regra do projeto: usar `Trim(Edit1.Text)` em vez de `Edit1.Text.Trim`.
- Em validações obrigatórias de componentes com `.Text`, usar `Trim(Componente.Text).IsEmpty`.
- Ao refatorar comparações de componentes com `.Text` contra vazio, nunca gerar `Componente.Text.IsEmpty`; usar `Trim(Componente.Text).IsEmpty` para preservar compilação e remover espaços.
- Não usar `Componente.Text.IsEmpty` quando a intenção for validar o texto sem espaços.
- Não encadear helpers de string diretamente em propriedades `.Text` ou `.Caption` de componentes. Essas propriedades podem ser `TCaption`, não `string`; portanto `.Length`, `.IsEmpty`, `.Trim`, `.ToUpper`, `.ToLower` e helpers similares podem não compilar.
- Para tamanho de `.Text`/`.Caption`, manter `Length(Componente.Text)` ou usar conversão explícita quando necessário: `string(Componente.Text).Length`.
- Para texto aparado de componentes, aplicar o helper no resultado de `Trim(Componente.Text)`:

```delphi
Length(edtSerial.Text)
string(edtSerial.Text).Length
Trim(edtSerial.Text).IsEmpty
Trim(edtSerial.Text).ToUpper
```

Não usar:

```delphi
edtSerial.Text.Length
edtSerial.Text.IsEmpty
edtSerial.Text.ToUpper
edtSerial.Caption.Length
```
- Substituir comparações booleanas com `= False` por `not`, preservando a precedência:

```delphi
if not Self.SerialRecuperarDados then
```

- Usar `.ToUpper`, `.ToLower` e `.ToInteger` em vez de `UpperCase()`, `LowerCase()` e `StrToInt()`.
- Usar `Variavel_ou_Parametro.Length` em vez de `Length(Variavel_ou_Parametro)`.
- Em SQL escrito em strings Delphi, comparar vazio com aspas duplas:

```delphi
LStringList.Add('coalesce(campo, "") = ""');
```

- Converter validações obrigatórias simples com `ShowOb`, `SetFocus` e `Exit` para `raise ExceptionOb.Create(Componente)`.
- O padrão `if Trim(Componente.Text).IsEmpty then begin ShowOb; Componente.SetFocus; Exit; end;` deve sempre virar `if Trim(Componente.Text).IsEmpty then raise ExceptionOb.Create(Componente);`, removendo o bloco e os comandos `ShowOb`, `SetFocus` e `Exit`.
- Ao usar `ExceptionOb`, adicionar `C4D.Exceptions` na cláusula `uses` da interface, se ainda não existir.
- Quando houver várias validações obrigatórias simples no mesmo método, extrair para `procedure ValidarCamposObrigatorios`.
- Chamar o método extraído como `Self.ValidarCamposObrigatorios;`.

## Laços `for`

- Preferir a declaração inline da variável de controle no próprio `for`.
- Usar `i` como nome da variável de controle quando o código já utilizar esse identificador.
- Remover a declaração anterior de `i` quando ela deixar de ser necessária:

```delphi
function TCommonModelSerialChave.Inverter14(AStr: string): string;
begin
  Result := '';
  for var i := 1 to 14 do
    Result := Result + AStr[c_Seq14_01[i]];
end;
```

## Exemplos obrigatórios

Aplicar estes formatos sempre que preservar comportamento. Se algum formato não puder ser aplicado por risco de compilação ou fluxo, registrar a exceção na entrega:

```delphi
if not ASrBd.IsEmpty then
  LStr := LStr + Self.CriptCaracteres01(ASrBd.ToUpper);
```

```delphi
begin
  Result := '';
  for var i := 1 to 25 do
    Result := Result + ASerial[c_SeqSerialChave1[i]];
end;
```

```delphi
if Pos(LRes[i], LSeq01) <> 0 then
  LRes[i] := LSeq02[Pos(LRes[i], LSeq01)];
```

```delphi
for var i := 1 to LPassedKey.Length do
  LCorrectKey := LCorrectKey + (ord(LPassedKey[i]) * 2) + (i * i * i * i * i * i * 5 * (i * 100)) + IntToStr(LCorrectKey).Length;
```

```delphi
if FSenha <> string(C_SenhaLibera) then
  ShowMsg('Senha informada é inválida');
```

```delphi
procedure TMinhaTela.btnGravarClick(Sender: TObject);
begin
  Self.ValidarCamposObrigatorios;

  QCadastro.Post;
  Self.Close;
end;

procedure TMinhaTela.ValidarCamposObrigatorios;
begin
  if Trim(edtNome.Text).IsEmpty then
    raise ExceptionOb.Create(edtNome);

  if Trim(cBoxTipo.Text).IsEmpty then
    raise ExceptionOb.Create(cBoxTipo);
end;
```

```delphi
case FTipoJuridico of
  1: LTipoJuridicoLB := LSerial + 'E'; //CNPJ
  2: LTipoJuridicoLB := LSerial + 'P'; //CPF
else
  LTipoJuridicoLB := LSerial + 'N';
end;
```

```delphi
if i = 18 then
  LSerial := LSerial + LTipoJuridicoLB
else if ArrayIntLocalizar(c_OrdemDocNoSerial, i) then
  LSerial := LSerial + LCNPJCPF[IncInt(LCont)]
else
  LSerial := LSerial + C_Alfabeto[Random(25) + 1];
```

## Checklist de revisao sintatica

Antes de entregar, revisar especificamente se ainda existem:

- `if (` ou `case (` em condicoes/seletores que nao precisam de parenteses.
- Comparacoes de string com `= ''''` ou `<> ''''` que podem usar `.IsEmpty`.
- `begin`/`end` em blocos `if`, `else`, `for`, `while` ou `case` com apenas um comando.
- Variavel de controle declarada antes do `for` quando pode ser `for var i := ...`.
- Opcoes de `case` sem indentacao de 2 espacos em relacao ao `case`.

## Chamadas locais

- Prefixar com `Self.` chamadas a métodos de instância da própria classe, inclusive chamadas internas como `Self.Clear`.
- Não prefixar componentes ou campos visuais com `Self.`: usar `btnGravar.Click`, não `Self.btnGravar.Click`.
- Não adicionar `Self.` a funções globais, helpers, rotinas locais aninhadas ou métodos de outros objetos.

## Critérios de segurança

- Não alterar banco de dados. Se uma mudança de banco for indispensável, apenas gerar um script para execução pelo usuário.
- Não alterar arquivos `.dfm` ou `.fmx` nesta skill, em nenhuma hipótese.
- Não aplicar nem preservar normalizações de designer em `.dfm`/`.fmx`, mesmo propriedades como `OldCreateOrder = False` ou `PixelsPerInch = 96`.
- Se uma refatoração exigir alteração em evento, componente ou propriedade visual em `.dfm`/`.fmx`, parar e informar que a alteração está fora do escopo desta skill.
- Não modificar arquivos gerados, binários, dependências ou caminhos ignorados pelo `AGENTS.md`.
- Não substituir mecanicamente padrões quando tipos, overloads, generics ou precedência puderem mudar o resultado.
- Se uma regra entrar em conflito com compilação, contrato externo ou comportamento existente, preservar o comportamento e relatar a exceção.

## Validação Delphi no projeto

- Para pasta do projeto, após refatorar, executar primeiro `git diff --check -- <arquivo>` e o checklist sintático desta skill.
- Conferir com `git diff --name-only` que nenhum arquivo `.dfm` ou `.fmx` foi alterado.
- Se `ExceptionOb` for usado, conferir se `C4D.Exceptions` foi adicionada na `uses` da interface.
- Conferir se não restaram validações obrigatórias equivalentes com `ShowOb`, `SetFocus` e `Exit`.
- Tentar o build oficial aprovado antes de montar chamadas avulsas complexas: `msbuild NomeProjeto.dproj /t:Build /p:Config=Debug /p:Platform=Win32`.
- Se o build completo falhar com `MSB6003`, `MSB6002` ou mensagem de linha de comando longa demais, registrar essa limitação e não gastar tempo tentando contornar o build completo por expansão manual de paths.
- Se `dcc32 -M` avançar até dependências transitivas antigas, componentes externos ou units não relacionadas ao arquivo editado, parar após poucas tentativas objetivas de path/alias e reportar a limitação. Não continuar adicionando paths indefinidamente.
- Ao editar arquivos Delphi ANSI/non-UTF8, preservar o encoding original; `apply_patch` pode falhar com `invalid utf-8 sequence`. Nesses casos, usar escrita preservando encoding e revisar o diff com atenção.

## Entrega

Resumir os arquivos alterados, as categorias de padronização aplicadas e as validações executadas. Informar objetivamente qualquer regra que não pôde ser aplicada com segurança.
