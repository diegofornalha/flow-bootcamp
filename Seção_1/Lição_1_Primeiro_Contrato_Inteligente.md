# 

Vamos codificar.

Vamos escrever um contrato inteligente simples que

- Crie um novo recurso
- Salve-o no armazenamento da conta

Em seguida, escreveremos uma transação para recuperá-la do armazenamento.

Vamos lá!

### 🎴O parque infantil Flow

O Flow tem um ambiente de desenvolvimento incrível, perfeito para brincar com a Cadence. Vá para Flow Playground.

[Inicie uma sessão de playground aqui.](https://play.onflow.org/local-project)

Aqui está um vídeo útil que aborda como você pode usar o Flow Playground

[Loom](https://www.loom.com/share/34b1c0f40c7a4a51bcf322ef33c27bec)

### 👋mundo gm

Digamos gm no playground Flow! Na conta 0x01, adicione este código

`// "pub" is shorthand for "public", meaning this contract can be accessed by anyone 
pub contract HelloWorld {

    // Declare a public field of type String.
    //
    // All fields must be initialized in the init() function.
    pub let greeting: String

    // The init() function is required if the contract contains any fields.
    init() {
        self.greeting = "gm, World!"
    }

    // Public function that returns our friendly greeting!
    pub fun hello(): String {
        return self.greeting
    }
}`

Este contrato contém duas coisas muito simples: uma variável de sequência `greeting` e uma função que retorna seu valor.

Em Cadence, você **precisa** declarar os valores iniciais de todos os campos no `init()` função. Pense nisso como um construtor. Você *pode* deixe os campos vazios.

Uma coisa a ser observada aqui - `let` variáveis são **não** mutável / mutável. Isso significa que não temos como mudar o valor de `greeting` uma vez que o contrato seja publicado.

O `hello()` função apenas retorna a saudação variável do contrato. `self` refere-se ao contrato atual. Nada de novo aqui.

O playground não está conectado à blockchain real, é apenas uma blockchain "local" em execução no seu navegador. Você pode implantar o contrato no blockchain do navegador pressionando o botão implantar. Na parte inferior da tela, você deve ver um `Deployed Contract To: 0x01` log. Agradável!

Vamos interagir com isso!

### 🎫Transações e scripts

O fluxo tem um método único de interagir com a blockchain. Você cria um bloco de código de "transação" no Cadence, assina-o e envia-o para o blockchain para ser executado. Pense neles como mini contratos inteligentes.

A beleza das transações é que você não precisa aprender a usar uma biblioteca em outro idioma (, como Éteres / web3js ) para interagir com seus contratos inteligentes. Você tem um tempo mais fácil, porque você pode escrever mais Cadence lol.

Os scripts são iguais às transações, exceto que eles não escrevem nenhum dado para o blockchain. Você pode executá-los sem pagar nenhum gás e sem assinar.

As transações são usadas para ler **e** escreva da blockchain. Os scripts são usados para ler apenas a partir da blockchain ( sorta como consultas SQL ), portanto *poderia* foi feito com um script, mas você verá por que eu fui com uma transação em breve!

Ler nossa saudação é bem fácil, eis como é a transação:

`import HelloWorld from 0x01

transaction {

  prepare(acct: AuthAccount) {}

  execute {
    log(HelloWorld.hello())
  }
}`

Primeiro, importamos o contrato HelloWorld do endereço para o qual foi implantado. Na rede de teste, esse endereço seria parecido com algo `0x4aa1a46d5b95f4bb`.

Em seguida, no organismo da transação, podemos fazer declarações variáveis (, assim como um contrato ou um arquivo JS ) que estará disponível para toda a transação.

As transações têm quatro fases principais, duas das quais estamos vendo aqui:

- `prepare`
    
    : É aqui que você passa pelos assinantes e o único lugar em que você pode acessar as coisas deles.
    
- `execute`
    
    : Ele contém a lógica principal da transação.
    

Em nossa transação, não fazemos nada na fase de preparação e apenas registramos o valor que o `hello()` função dos retornos do contrato HelloWorld.

Como nosso contrato está apenas lendo uma variável pública, não importa qual conta você escolhe assinar. Basta selecionar qualquer um e pressionar o botão enviar. Você deve ver sua saudação impressa na seção Resultados da transação, na parte inferior.

Entediado? Aí vem a parte mais emocionante!

### 😋Tornando nosso gm um recurso

Agora vamos fazer algo especial - faça do nosso gm um "recurso". Clique na conta `0x02` e adicione este contrato:

`pub contract HelloWorld {

    // Declare a resource that only includes one function.
    // Think of this like a recipe
	pub resource HelloAsset {
        // A transaction can call this function to get the "gm, World!"
        // message from the resource.
		pub fun hello(): String {
			return "gm, World!"
		}
	}

	init() {
        // Use the built-in "create" function to create a new instance
        // of the HelloAsset resource. 
        // Think of it like using the recipe to create a dish.
        let newHello <- create HelloAsset()

        // We can do anything in the init function, including accessing
        // the storage of the account that this contract is deployed to.
        //
        // Here we are storing the newly created HelloAsset resource
        // in the private account storage 
        // by specifying a custom path to the resource
        // make sure the path is specific!
		self.account.save(<-newHello, to: /storage/Hello)

        log("HelloAsset created and stored")
	}
}`

Isso é bem legal. Quando fazemos algo um recurso, precisamos seguir algumas regras extras. O principal a ter em mente é que os recursos só podem existir em um lugar ao mesmo tempo.

O que é um "lugar"? Em Cadence, um lugar é uma conta. Se você tentar criar um novo recurso, mas não o armazenar em uma conta, a blockchain o impedirá. Os contratos não serão implantados e as transações serão revertidas.

`pub resource HelloAsset {
    pub fun hello(): String {
        return "gm, World!"
    }
}`

Esta é a nossa definição de recurso. Ele contém uma única função e nada mais.

`let newHello <- create HelloAsset()`

É aqui que realmente fazemos a criação do recurso. Setas `<-` em Cadence, indique que você está lidando com recursos. Se parássemos nosso contrato aqui, ele não seria implantado porque estamos deixando o recurso suspenso sem atribuir um lugar a ele.

`self.account.save(<-newHello, to: /storage/Hello)`

`self` refere-se ao contrato atual. `account` refere-se à conta que está implantando o contrato ( sua conta ). Chamamos a função de salvar para armazenar o `newHello` recurso no `/storage/` caminho no costume da nossa conta `Hello` domínio. Pense nisso como um caminho de diretório no disco rígido do seu computador!

Resumo: definimos um recurso, criamos e depois o armazenamos no armazenamento de nossas contas.

Essas três coisas são comparáveis a um cofre de conta bancária.

1. O banco - Tudo está acontecendo *em nosso contrato*
    
    , que está em uma conta Flow. Este é o banco.
    
2. O proprietário da conta bancária - é você - suas credenciais são a conta Flow que você está usando para assinar a transação de implantação.
3. O cofre da conta bancária - Este é *seu armazenamento*
    
    em um cofre neste banco.
    

Se você tentar acessar o `/storage/Hello` domínio em outro contrato, você receberá uma causa de erro que é como tentar olhar para um cofre em outro banco. Não há nada lá!

### 🏦Carregar recurso do armazenamento

Agora que nosso recurso está armazenado, veja como podemos retirá-lo.

Crie uma nova transação:

`import HelloWorld from 0x02

// This transaction calls the "hello" method on the HelloAsset object
// that is stored in the account's storage by removing that object
// from storage, calling the method, and then putting it back in storage

transaction {

    prepare(acct: AuthAccount) {

        // load the resource from storage, specifying the type to load it as
        // and the path where it is stored
        let helloResource <- acct.load<@HelloWorld.HelloAsset>(from: /storage/Hello)

        // We use optional chaining (?) because the value in storage
        // may or may not exist, and thus is considered optional.
        log(helloResource?.hello())

        // Put the resource back in storage at the same spot
        // We use the force-unwrap operator `!` to get the value
        // out of the optional. It aborts if the optional is nil
        acct.save(<-helloResource!, to: /storage/Hello)
    }
}`

A primeira coisa que você não percebeu: não temos um bloco de execução aqui. Isso é porque podemos fazer tudo o que precisamos no bloco de preparação.

`let helloResource <- acct.load<@HelloWorld.HelloAsset>(from: /storage/Hello)`

Esta linha está fazendo um monte de coisas. Começamos com `acct`, esta é a conta que possui o recurso. Nós chamamos de `load` função. Especificamos o tipo com `<@HelloWorld.HelloAsset>` ( é por isso que importamos o contrato ). Passamos no local em `from: /storage/hello`.

O código quase apenas *diz* você o que está fazendo. É por isso que eu amo Cadence, é tão explicativo se você entende a sintaxe.

O que quer que a função de carga retorne é tratado como um recurso e atribuído ao `helloResource` variável.

`log()` é como `console.log` em Javascript e `print()` em Python. O estranho aqui é o `?`. Isso é usado para indicar um [opcional](https://docs.onflow.org/cadence/language/values-and-types/#optionals) tipo. Opcionais são valores que podem representar a ausência de um valor. ou seja, em vez de dizer que um valor é indefinido, ele afirma explicitamente que um valor está ausente. Se os booleanos podem ser verdadeiros ou falsos, os opcionais podem estar ausentes ou ter um valor. Saída [esta incrível postagem de blog](https://joshuahannan.medium.com/optionals-in-cadence-not-optional-fb39bb4b0081#:~:text=you%E2%80%99re%20done%20reading.-,What%20are%20Optionals%3F,-Optionals%20in%20Cadence) para mais detalhes.

`acct.save(<-helloResource!, to: /storage/Hello)`

Finalmente, precisamos colocar o recurso de volta! Lembre-se, se deixarmos o recurso em uma variável, nossa transação não funcionará! A sintaxe para salvar é a mesma, você deve se acostumar com isso: )

Antes de clicar em "Enviar" na transação, altere o assinante da transação para 0x02:

[https://camo.githubusercontent.com/b697a2aff5b81ef610850f9f0bf1c3ebc3421be760f9f3fb0eb97a98a287141f/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f4831673337644539392e706e67](https://camo.githubusercontent.com/b697a2aff5b81ef610850f9f0bf1c3ebc3421be760f9f3fb0eb97a98a287141f/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f4831673337644539392e706e67)

Você pode fazer isso clicando em 0x01 na caixa quadrada e depois em 0x02.

Precisamos alterar os assinantes porque o contrato foi implantado em 0x02. Se tentarmos acessar um recurso armazenado em 0x02 a partir de 0x01, obteremos um erro.

### 🚨Relatório de andamento

**Uau**. Isso foi muito. Bom trabalho.

Quero que você brinque um pouco com este contrato. Armazene outras variáveis e tente assinar com uma conta que não possui o recurso (, isso será um erro ). É assim que você aprende!

Depois de quebrar alguma coisa, poste a mensagem de erro em #progress. Coisas quebram o tempo todo, nem tudo são arco-íris e unicórnios!