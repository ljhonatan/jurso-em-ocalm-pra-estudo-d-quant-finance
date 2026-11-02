# jurso-em-ocalm-pra-estudo-d-quant-finance
juros em ocalm iniciando na linguagem
é so pra pratica aos poucos esudando gradativamente a lingaugem e a matematica por tras
# 💰 Calculadora de Juros Compostos em OCaml

Uma calculadora simples, didática e funcional de **juros compostos (Valor Futuro)** escrita em [OCaml](https://ocaml.org/). O projeto demonstra boas práticas da linguagem — funções puras, tratamento de entrada, formatação de saída — em um exemplo curto e aplicável ao mercado financeiro.

## 📐 Fórmula

$$
VF = VP \times (1 + i)^n
$$

Onde:

| Símbolo | Significado |
|---------|-------------|
| **VP** | Valor Presente (capital inicial) |
| **i**  | Taxa de juros por período (em decimal, ex.: `0.05` = 5%) |
| **n**  | Número de períodos |
| **VF** | Valor Futuro (montante final) |

## 🚀 Como executar

### Pré-requisitos

- [OCaml](https://ocaml.org/docs/install.html) (versão 4.14+ recomendada)
- `dune` (opcional, mas recomendado)

### Opção 1 — Compilação direta com `ocamlfind` ou `ocamlopt`

```bash
ocamlopt -o juros juros.ml
./juros
```

Ou no modo interpretador (mais rápido para testar):

```bash
ocaml juros.ml
```

### Opção 2 — Com Dune (recomendado para projetos)

Crie a estrutura:

```
juros-compostos/
├── dune-project
├── bin/
│   ├── dune
│   └── juros.ml
└── README.md
```

`dune-project`:

```lisp
(lang dune 3.0)
```

`bin/dune`:

```lisp
(executable
 (name juros))
```

Depois:

```bash
dune exec ./bin/juros.exe
```

## 🖥️ Exemplo de uso

```
=== Calculadora de Juros Compostos (Valor Futuro) ===
Fórmula: VF = VP × (1 + i)^n

Digite o Valor Presente (VP): 1000
Digite a taxa de juros por período (i) em decimal (ex: 0.05 para 5%): 0,05
Digite o número de períodos (n): 12

Valor Presente (VP) : R$ 1000.00
Taxa (i)            : 0.0500 (5.00%)
Períodos (n)        : 12
-------------------------------------
Valor Futuro (VF)   : R$ 1795.86
```

> 💡 O programa aceita **vírgula** ou **ponto** como separador decimal — útil para usuários brasileiros.

## 🧠 Estrutura do código

| Função | Responsabilidade |
|--------|------------------|
| `ler_float` | Lê um `float` do usuário, normaliza separador decimal e trata erros. |
| `calcular_vf` | Função pura que aplica a fórmula dos juros compostos. |
| `let () = ...` | Ponto de entrada: coleta dados, calcula e imprime o resultado. |

### Destaques de OCaml usados

- **Imutabilidade por padrão**: `let input = ... in let input = ...` cria um novo binding em vez de mutar.
- **Pattern matching implícito no `try ... with`**: captura qualquer exceção de conversão.
- **Operadores específicos para float**: `*.`, `+.`, `**`.
- **`Printf.printf` com especificadores de tipo** (`%.2f`, `%.4f`, `%.0f`) para saída formatada.

## ⚠️ Tratamento de erros

Se o usuário digitar algo que não seja um número, o programa encerra com:

```
Fatal error: exception Failure("Valor inválido. Digite um número.")
```

Em uma evolução futura, isso pode ser trocado por um loop de repetição até obter entrada válida.

## 🔮 Melhorias possíveis

- [ ] Loop de reentrada em caso de erro (em vez de abortar).
- [ ] Suporte a taxa em **percentual** (ex.: `5` em vez de `0.05`).
- [ ] Cálculo inverso: **Valor Presente** a partir do VF.
- [ ] Cálculo de **taxa implícita** dado VP, VF e n.
- [ ] Testes unitários com `alcotest`.
- [ ] Interface CLI com `cmdliner`.
- [ ] Versão web com `js_of_ocaml`.

## 📚 Contexto

Este código faz parte de uma série de estudos sobre **finanças quantitativas e OCaml**, conectando a fórmula clássica de juros compostos — base de praticamente toda a matemática financeira (valuation, precificação de futuros, cash-and-carry, etc.) — à linguagem usada por firms como a **Jane Street** em sistemas de trading de alta performance.

## 📄 Licença

MIT — use, modifique e distribua livremente.

o codigo 

(* ============================================================
   Calculadora de Juros Compostos — Valor Futuro
   Fórmula: VF = VP × (1 + i)^n
   ============================================================ *)

(* Função auxiliar: lê um float do usuário a partir de um prompt.
   Aceita vírgula como separador decimal (padrão pt-BR) e converte
   para ponto, que é o que float_of_string espera. *)
let ler_float prompt =
  (* Exibe o prompt no stdout *)
  print_string prompt;
  (* Garante que o prompt apareça antes do read_line (evita buffer) *)
  flush stdout;
  (* Lê uma linha inteira digitada pelo usuário *)
  let input = read_line () in
  (* Substitui vírgula por ponto, caso o usuário digite com vírgula *)
  let input = String.map (fun c -> if c = ',' then '.' else c) input in
  (* Converte a string para float, removendo espaços em branco.
     Se falhar (entrada inválida), lança uma exceção com mensagem clara. *)
  try float_of_string (String.trim input)
  with _ -> failwith "Valor inválido. Digite um número."

(* Função pura: calcula o Valor Futuro (VF) a juros compostos.
   Parâmetros:
     vp = Valor Presente (capital inicial)
     i  = taxa de juros por período (em decimal, ex.: 0.05 = 5%)
     n  = número de períodos
   Retorna: vp * (1 + i)^n
   Em OCaml, `*.` e `**` são os operadores de multiplicação e
   exponenciação para floats. *)
let calcular_vf vp i n =
  vp *. (1.0 +. i) ** n

(* Função principal: ponto de entrada do programa.
   O `let () = ...` é a forma idiomática em OCaml de executar
   código de efeito colateral na inicialização do módulo. *)
let () =
  (* Cabeçalho informativo *)
  print_endline "=== Calculadora de Juros Compostos (Valor Futuro) ===";
  print_endline "Fórmula: VF = VP × (1 + i)^n";
  print_newline ();

  (* Coleta das entradas do usuário, uma por linha *)
  let vp = ler_float "Digite o Valor Presente (VP): " in
  let i  = ler_float "Digite a taxa de juros por período (i) em decimal (ex: 0.05 para 5%): " in
  let n  = ler_float "Digite o número de períodos (n): " in

  (* Cálculo do Valor Futuro *)
  let vf = calcular_vf vp i n in

  (* Exibição formatada dos resultados *)
  print_newline ();
  Printf.printf "Valor Presente (VP) : R$ %.2f\n" vp;
  Printf.printf "Taxa (i)            : %.4f (%.2f%%)\n" i (i *. 100.0);
  Printf.printf "Períodos (n)        : %.0f\n" n;
  Printf.printf "-------------------------------------\n";
  Printf.printf "Valor Futuro (VF)   : R$ %.2f\n" vf
