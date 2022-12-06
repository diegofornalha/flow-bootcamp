# 

Você aprendeu o que é o fluxo. Você conhece Cadence e entende o que é um recurso. Seu ambiente está pronto para tirá-lo do host local.

### 👁Um contrato real da NFT

Hora de fazer um contrato NFT adequado. Como você notou, nosso contrato atual com a NFT apenas deixa em branco as NFTs. Também não há função de hortelã, então ninguém mais pode obter NFTs lol

Aqui está o que um contrato NFT com recursos básicos como:

`pub contract BottomShot {
  // This is a simple NFT mint counter
  pub var totalSupply: UInt64
  
  pub resource NFT {
    // The unique ID that differentiates each NFT
    pub let id: UInt64

    // We set the ID of the NFT and update the NFT counter
    init() {
        self.id = BottomShot.totalSupply
        // totalSupply is a global variable for this contract
        BottomShot.totalSupply = BottomShot.totalSupply + (1 as UInt64)
    }
  }
  
  // This is a resource that's going to contain all the NFTs any one account owns
  pub resource Collection {
    // This is a dictionary that maps ID integers with NFT resources
    // the @ indicates that we're working with a resource
    pub var ownedNFTs: @{UInt64: NFT}

    // This function will deposit an NFT into the collection
    // Takes in a variable called token of type NFT that's a resource
    pub fun deposit(token: @NFT) {
      // Move the NFT into the ownedNFTs dictionary
      // <-! is the force-assignment operator
      self.ownedNFTs[token.id] <-! token
    }

    pub fun withdraw(id: UInt64): @NFT {
      // We pull out the NFT resource from the dictionary 
      let token <- self.ownedNFTs.remove(key: id) ??
        panic("This collection doesn't contain an NFT with that id")
      
      return <- token
    }
    
    // Returns an array of integers
    pub fun getIDs(): [UInt64] {
      // The keys in the ownedNFTs dictionary are the IDs
      return self.ownedNFTs.keys
    }

    init() {
      // All resource values MUST be initiated so we make it empty!
      self.ownedNFTs <- {}
    }
  
    // This burns the ENTIRE collection (i.e. every NFT the user owns) 
    destroy () {
      destroy self.ownedNFTs
    }
  }

  pub fun createCollection(): @Collection {
    return <- create Collection()
  }

  pub fun mintNFT(): @NFT {
    return <- create NFT()
  } 

  init() {
    self.totalSupply = 0
  }
}`

Não fique sobrecarregado! Este é o momento em que você percebe como **fácil** Cadência é.

Eu deixei um monte de comentários. **Leia-os.** Então certifique-se de ler o código, ele quase se explica!

Para as partes que não se explicam, você me pegou < 3

Começando do topo:

  `// This is a simple NFT mint counter
  pub var totalSupply: UInt64
  
  pub resource NFT {
    // The unique ID that differentiates each NFT
    pub let id: UInt64

    // We set the ID of the NFT and update the NFT counter
    init() {
        self.id = BottomShot.totalSupply
        // totalSupply is a global variable for this contract
        BottomShot.totalSupply = BottomShot.totalSupply + (1 as UInt64)
    }
  }`

Nossa definição de recurso NFT é um pouco diferente: precisamos apenas do ID NFT aqui. Lembre-se, a função init é executada toda vez que um recurso é criado. Nele, definimos o ID para a NFT criada e atualizamos o contador global. Isso garante que cada NFT tenha um ID exclusivo para que possamos usá-los para identificação!

Logo depois disso, você tem um `Collection` recurso. Pense em quando você era criança. Você tinha uma coleção de cartões Pokemon? Talvez você tenha colecionado bolinhas de gude, moedas ou selos? Ou talvez eu seja um boomer e as crianças colecionem peles de Fortnite hoje em dia. O que quer que você tenha coletado, você provavelmente manteve todos eles *no mesmo lugar* ( ou mesma conta Fortnite lol ). Isso facilitou muito o controle deles.

Estamos fazendo a mesma coisa em Cadence. Uma coleção é um recurso que possui todas as nossas NFTs. Isso facilita o armazenamento de +. Cada conta que quiser cunhar uma NFT precisará primeiro fazer uma coleção.

A nova sintaxe que vemos no recurso de coleção:

`pub var ownedNFTs: @{UInt64: NFT}`

[Dicionários](https://docs.onflow.org/cadence/tutorial/04-non-fungible-tokens/#dictionaries): Estes são como mapeamentos!

`self.ownedNFTs[token.id] <-! token`

[Operador de atribuição de força](https://docs.onflow.org/cadence/language/values-and-types/#force-assignment-operator--): Isso atribui um recurso a uma variável se a variável for nula. Se a variável não for nula, a execução será interrompida lol

### ⁇ ♂️Execute e interaja com nosso contrato

Vamos apenas usar a extensão para implantar nossos contratos localmente a partir de agora. Você pode usar o [Comandos CLI](https://docs.onflow.org/flow-cli/account-update-contract/) se você quiser, mas vou ficar com a extensão para manter as coisas consistentes.

Basta clicar no link / botão "Implantar contrato" na parte superior do arquivo:

[https://camo.githubusercontent.com/a0881ca6ce985e4c3fe0aa4179be2923ff4c989906c5af5ce0ffc1fbdfa047d5/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f5331324249434b74712e706e67](https://camo.githubusercontent.com/a0881ca6ce985e4c3fe0aa4179be2923ff4c989906c5af5ce0ffc1fbdfa047d5/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f5331324249434b74712e706e67)

Para interagir com o nosso contrato, vamos escrever uma transação. Faça um `transactions` pasta na raiz do seu projeto e adicione um arquivo chamado `createCollection.cdc`:

`import BottomShot from 0xf8d6e0586b0a20c7

transaction {
  
  prepare(acct: AuthAccount) {
    acct.save(<- BottomShot.createCollection(), to: /storage/BottomShot)
  }

  execute {
    log("Stored a collection for our NUTTY empty NFTs")
  }
}`

Esta transação chama o `createCollection` função e armazena no `BottomShot` domínio em nosso contrato.

Novamente, para executar isso, basta clicar no prompt na parte superior do arquivo, para transações que diz "Enviar assinado pela ServiceAccount"

[https://camo.githubusercontent.com/87784e18293769a6922f885ecd3384394d64195c0d9565392cf4cc7a4162a7fd/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f426b6f75384a6346712e706e67](https://camo.githubusercontent.com/87784e18293769a6922f885ecd3384394d64195c0d9565392cf4cc7a4162a7fd/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f426b6f75384a6346712e706e67)

ServiceAccount é a conta padrão com a qual você está conectado no emulador. Você verá os logs impressos no terminal executando o emulador:

[https://camo.githubusercontent.com/514fcb48732e2f4b458f3d92ea41a9fc8b9a0b5845576321f74149ee60605585/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f5379584d346b3546632e706e67](https://camo.githubusercontent.com/514fcb48732e2f4b458f3d92ea41a9fc8b9a0b5845576321f74149ee60605585/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f5379584d346b3546632e706e67)

Agradável! Acabamos de executar uma transação localmente e interagimos com nosso contrato no emulador!

Vamos cunhar uma NFT e adicioná-la a esta coleção! Crie outro arquivo na sua pasta de transações chamada `depositNFT.cdc`:

`import BottomShot from 0xf8d6e0586b0a20c7

transaction {
  
  prepare(acct: AuthAccount) {
    let collectionReference = 
      acct.borrow<&BottomShot.Collection>(from: /storage/BottomShot)
      ?? panic("No collection found!")

    collectionReference.deposit(token: <- BottomShot.mintNFT())
  }

  execute {
    log("Minted an NFT and stored it into the collection")
  }
}`

Aqui estamos vendo algo novo: `borrow`.

Lembra como eu disse que os recursos só podem existir em um lugar ao mesmo tempo? Isso também significa que você não pode executar funções ou acessar coisas dentro desses recursos.

Portanto, nesta transação, emprestamos o recurso de coleta do local em que está armazenado (`/storage/BottomShot`). Isso nos dá um temporário *referência* ao recurso ( se tivermos acesso a ele ), para que possamos fazer alterações nele. Quando a transação termina, o recurso é retornado automaticamente.

Usamos a referência para chamar de `deposit` função e passe no recurso NFT retornado pela função de hortelã.

O `?` está indicando que o valor antes de ser um [opcional](https://docs.onflow.org/cadence/language/values-and-types/#optionals) - pode ter um valor ou pode ser `nil`, significando nada. `??` é para uma opção dupla, o que significa que pode ter um valor opcional ou pode ser `nil`. Se a função de empréstimo não devolver nada, nós `[panic](https://docs.onflow.org/cadence/language/built-in-functions/#panic)`, que encerra a transação com o erro.

LETSSS GOOOOO NÓS ESTAMOS BALLIN NOSSA CONTA TEM UMA COLEÇÃO **E** UM NFT DENTRO!!!

### 😯Recursos e scripts

Há um grande problema com o nosso contrato agora. Não há como os outros descobrirem quais NFTs possuo no armazenamento de contratos. Isso é porque o `getIDs` função é *por dentro* o recurso Coleção e acessível apenas pela minha conta. Isso significa que você teria que doar sua NFT a alguém para deixá-lo ver o que é. Caramba.

Volte ao seu contrato, adicionaremos uma interface que resolverá isso. Adicione o `CollectionPublic` recurso na linha 16, logo após onde você declara o recurso NFT:

  `// This interface exposes only the getIDs function
  pub resource interface CollectionPublic {
    pub fun getIDs(): [UInt64]
  }
  
  // Update the Collection resource declaration to implement the new interface
  pub resource Collection: CollectionPublic {
    // The rest of your contract REMAINS THE SAME`

Parece meio estranho, não é? Como adicionar essa interface para uma função que já tínhamos resolve alguma coisa? AHA! Você caiu na minha armadilha. Ao fazer você fazer algo aparentemente inútil, investi você no que vem a seguir.

Cadence tem esse nome "[Controle de acesso baseado em capacidade](https://docs.onflow.org/cadence/language/capability-based-access-control/)" coisa. O que nos permite fazer é criar um **capacidade** que fornece aos usuários específicos acesso a determinados campos ou funções de um objeto armazenado.

Pense nisso como uma chave que desbloqueia uma parte específica do seu cofre. Você pode colocar essa chave em algum lugar ( sob o seu capacho, por exemplo ) e quem sabe sobre a chave tem a capacidade de acessar o objeto. Eles podem buscar a chave e abrir o cofre.

**Nota** Lembre-se de reimplementar seu contrato aqui! Você pode fazer isso clicando em implantar na parte superior do arquivo.

Agora que temos uma interface que expõe a única função que queremos que as pessoas tenham, daremos a todos o *capacidade* para chamar essa função.

Abra seu `createCollection.cdc` transaction e atualize-o para isso:

`import BottomShot from 0xf8d6e0586b0a20c7

transaction {
  
  prepare(acct: AuthAccount) {
                                                // I changed the storage address here
    acct.save(<- BottomShot.createCollection(), to: /storage/BottomShot2)
    
    // We're linking two resources in different storage domains
    acct.link<&BottomShot.Collection{BottomShot.CollectionPublic}>
      (/public/BottomShot2, target: /storage/BottomShot2)
  }
  
  execute {
    log("Stored a collection for our NUTTY empty NFTs")
  }
}`

Eu fiz duas mudanças aqui.

Primeiro: mudei o caminho ( / armazenamento / BottomShot2 ) no qual estamos armazenando nossa coleção. Isso ocorre porque você não pode substituir o armazenamento no Flow ( imagine acidentalmente substituir um CryptoPunk 💀) e se você não fechou o emulador desde a última vez que fez uma coleção, esse caminho será seguido.

A grande mudança aqui é a `link` chamada de função. A coleção existe no `/storage/` domínio. Estamos vinculando ao `/public/` domínio. Lembrete: / armazenamento só pode ser acessado pelo proprietário da conta. / público é acessível por qualquer pessoa.

Repartição da sintaxe:

- `acct.link`
    
    : conta com a qual estamos trabalhando e a função que estamos chamando
    
- `<&BottomShot.Collection{BottomShot.CollectionPublic}>link`
    
    : Este é o tipo nosso
    
    função espera - uma referência ( & ) ao recurso CollectionPublic na coleção de contratos BottomShot
    
- `(/public/BottomShot2, target: /storage/BottomShot2)`
    
    : Os dois caminhos que estamos vinculando
    

Coloque outra maneira:

`account.function<&Contract.Resource{Contract.Interface}>(destinationPath, sourcePath)`

Clique em "Enviar assinado" na parte superior do arquivo da transação para executá-lo. Atualizar o `depositNFT.cdc` transação com o novo caminho ( "BottomShot2" ) e execute-o também.

Agora temos um novo contrato, uma nova coleção e uma nova NFT. Vamos escrever um script para lê-los!

Crie uma pasta chamada `scripts` e adicione um arquivo chamado `getIDs.cdc`:

`import BottomShot from 0xf8d6e0586b0a20c7

pub fun main(acct: Address): [UInt64] {
  let publicRef = getAccount(acct).getCapability(/public/BottomShot2)
            .borrow<&BottomShot.Collection{BottomShot.CollectionPublic}>()
            ?? panic ("Oof ouch owie this account doesn't have a collection there")

  return publicRef.getIDs()
}`

Os scripts são um pouco diferentes das transações. Como eles não exigem assinantes, passamos um endereço ( e não uma conta ) como parâmetro. Usamos o endereço para obter a conta e depois ligamos para o `getCapability` função para acessar o recurso que vinculamos anteriormente. Em termos mais simples, estamos acessando o armazenamento público de uma conta com seu endereço para chamar uma função nela.

O `borrow` função tenta acessar o `CollectionPublic` recurso para que possamos chamar suas funções. Terminamos com pânico, caso as coisas dêem errado: )

Depois de fazer uma referência ao recurso público, acabamos de chegar ao `getIDs()` função.

Para executar isso, volte ao seu terminal ( mantenha o emulador em execução ) e execute este comando:

`# In the FlowNFTs folder
flow scripts execute scripts/getIDs.cdc f8d6e0586b0a20c7`

Isso imprimirá uma variedade de IDs NFT! Se você executasse apenas o `depositNFT` transação uma vez, você verá `Result: [1]`. Execute-o algumas vezes mais! Aqui está o que vejo depois de duas corridas:

[https://camo.githubusercontent.com/9de72b9a4b253eb6a30b0e881f96062e2ccbfa5b3b4d724bcbfba81b87758dfb/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f424a634b50667146352e706e67](https://camo.githubusercontent.com/9de72b9a4b253eb6a30b0e881f96062e2ccbfa5b3b4d724bcbfba81b87758dfb/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f424a634b50667146352e706e67)

### 🚨Relatório de andamento

Você fez muito. Dê um tapinha nas costas. Isso é difícil e você está estragando tudo!

Poste uma captura de tela da saída do script com os IDs em #progress, mostre a todos quanto tempo você está.