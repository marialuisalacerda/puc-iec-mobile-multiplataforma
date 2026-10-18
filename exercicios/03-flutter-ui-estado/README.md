# Atividade 3 — App Flutter: UI + Estado

Catálogo de filmes em **Flutter**: você compõe a UI (`MovieCard`) e gerencia **estado compartilhado com Riverpod** (favoritos refletindo no card e no contador do header). Começa em aula (DartPad) e termina neste projeto.

- 📄 **[enunciado.md](enunciado.md)** — objetivos, exercícios e rubrica (15 pts)
- 🪜 **[guia-passo-a-passo.md](guia-passo-a-passo.md)** — as 4 TASKs passo a passo
- 📁 **[pratica/](pratica/)** — o projeto Flutter (já roda; complete os scaffolds)

## Em 1 minuto
```bash
cd pratica
flutter pub get
flutter run -d chrome   # app abre (lista de filmes)
flutter test            # 2 testes vermelhos → deixe-os verdes
```

**Entrega:** fork + PR até **23/06**. O `flutter test` é o gate; o J.A.R.V.I.S. comenta no PR.

> Bridging nativo (Platform Channel / KMP) é a **Aula 4** — aqui o foco é **UI + estado**.

## Como rodar - após alterações

Primeiro, acesse a pasta do projeto Flutter:

```bash
cd pratica
```

Depois, instale as dependências declaradas no `pubspec.yaml`:

```bash
flutter pub get
```

Esse comando baixa os pacotes necessários para o projeto funcionar, como o Riverpod e outras bibliotecas utilizadas pela aplicação.

Para executar a aplicação no navegador Chrome:

```bash
flutter run -d chrome
```

O comando `flutter run` compila e inicia o aplicativo. A opção `-d chrome` informa ao Flutter que o dispositivo de execução será o navegador Chrome.

Para executar os testes automatizados:

```bash
flutter test
```

Esse comando procura os testes existentes na pasta `test/` e executa todos eles. Nesta atividade, os testes verificam se o `MovieCard` exibe corretamente os dados do filme, se o estado de favoritos é compartilhado entre os widgets, se o botão de limpar funciona e se o teste autoral do provider foi implementado.

Para realizar uma análise estática do código:

```bash
flutter analyze
```

Esse comando verifica o código sem precisar executar a aplicação, procurando problemas como erros de sintaxe, imports incorretos, código inválido e avisos relacionados às regras do Dart e do Flutter.

### Provider x Prop Drilling

Em uma aplicação Flutter, diferentes widgets podem precisar acessar ou modificar o mesmo estado. Neste projeto, por exemplo, o estado de favoritos precisa ser utilizado pelo `MovieCard`, pelo contador exibido no `AppBar` e pelo botão responsável por limpar os favoritos.

Sem um mecanismo de gerenciamento de estado compartilhado, uma alternativa seria manter esse estado em um widget localizado acima dos demais e passá-lo manualmente por parâmetros para cada widget filho. Quando o estado precisa atravessar vários níveis da árvore de widgets apenas para chegar ao componente que realmente irá utilizá-lo, ocorre o chamado **prop drilling**.

O problema do prop drilling é que widgets intermediários acabam recebendo propriedades que eles próprios não utilizam, apenas para repassá-las para outros componentes. Conforme a aplicação cresce, isso aumenta o acoplamento entre os widgets e torna o código mais difícil de manter.

O Riverpod resolve esse problema por meio de providers. Um provider funciona como uma fonte de estado que pode ser acessada diretamente pelos widgets que realmente precisam dela. Nesta atividade, o `favoritesProvider` mantém o conjunto de IDs dos filmes favoritados e é a única fonte de verdade para esse estado.

O `MovieCard` utiliza `ref.watch(favoritesProvider)` para observar o estado e atualizar o ícone de coração quando ele muda. O `HomeScreen` também observa o mesmo provider para calcular a quantidade de favoritos exibida no contador. Já ações como favoritar um filme ou limpar todos os favoritos utilizam `ref.read(favoritesProvider.notifier)` para acessar o `FavoritesNotifier` e executar os métodos `toggle()` e `clear()`.

Dessa forma, o card, o contador e o botão de limpar trabalham sobre o mesmo estado sem que seja necessário passar esse estado manualmente por vários níveis da árvore de widgets. Isso reduz o prop drilling, diminui o acoplamento entre os componentes e facilita a manutenção e a evolução da aplicação.