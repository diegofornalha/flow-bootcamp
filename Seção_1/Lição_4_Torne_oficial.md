# 

### 🤑Crie e faça upload de nossos ativos NFT

Todo esse tempo escrevemos código. Vamos mudar de marcha e criar idéias da NFT!

É aqui que você decide sobre o que será sua coleção NFT. É sobre seus personagens favoritos de anime? Talvez alguns pôsteres legais de filmes? Você também pode colocar gatos na blockchain pela enésima vez. É exatamente o que eu vou fazer 😹

**Agora, é a hora de você ser insanamente criativo. Crie cinco NFTs aleatórias para sua coleção.** Suas NFTs podem ser vídeos, imagens ou até arquivos mp3! Vou usar imagens antigas simples no formato .png.

Crie uma pasta chamada "ativos" e adicione seus arquivos nela. Verifique se eles são nomeados sequencialmente de 0 a 5 ( ou mais ). Nós vamos armazená-los [IPFS](https://en.wikipedia.org/wiki/InterPlanetary_File_System?utm_source=buildspace.so&utm_medium=buildspace_project), que é essencialmente um sistema de arquivos distribuídos. Hoje — você pode usar algo como S3, GCP Storage ou Dropbox. Mas, nesse caso, podemos simplesmente confiar no IPFS, administrado por estranhos que estão usando a rede. Dê [isto](https://decrypt.co/resources/how-to-use-ipfs-the-backbone-of-web3?utm_source=buildspace.so&utm_medium=buildspace_project) uma leitura rápida quando puder! Abrange muitos bons conhecimentos básicos: ).

Usaremos um serviço gratuito chamado [NFT.Storage](https://nft.storage/) para carregar nossos ativos no IPFS. Faça uma conta e faça login. Usaremos o aplicativo de desktop NFTUp para fazer upload de arquivos. A instalação levará cerca de 60 segundos, apenas [siga as instruções aqui](https://nft.storage/docs/how-to/nftup/).

Arraste + solte seus arquivos ou clique no botão de upload e selecione-os.

[https://camo.githubusercontent.com/76ec1460e53ee9f03aa7497eadd08d60dcd14f96d7a10e4da8a4d580210e3114/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f7231646b6a462d63632e706e67](https://camo.githubusercontent.com/76ec1460e53ee9f03aa7497eadd08d60dcd14f96d7a10e4da8a4d580210e3114/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f7231646b6a462d63632e706e67)

Isso fornecerá três coisas - o CID ( ID do conteúdo ), o URL do IPFS e o URL do Gateway. O URL do IPFS é o que usaremos em nosso contrato.

[https://camo.githubusercontent.com/54340425a78114e5ba20581391157ee5177bd8166abb64bd650f8c78d80cdd8e/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f484a383859745771352e706e67](https://camo.githubusercontent.com/54340425a78114e5ba20581391157ee5177bd8166abb64bd650f8c78d80cdd8e/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f484a383859745771352e706e67)

Você pode clicar no URL do gateway para ver suas NFTs no navegador. A parte legal aqui é que isso se comporta como uma unidade de armazenamento em nuvem com seus ativos. Você pode acessar todos os seus arquivos com o mesmo link, apenas substituindo o nome do arquivo no final.

[https://camo.githubusercontent.com/b4ba6eec43ad848347643f31dbc380927a9e0e198614b1db75249409c7bf0a25/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f534a687a61595a63632e706e67](https://camo.githubusercontent.com/b4ba6eec43ad848347643f31dbc380927a9e0e198614b1db75249409c7bf0a25/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f534a687a61595a63632e706e67)

Nossos ativos estão prontos para partir! Esses ativos são permanentes - eles nunca caem ou mudam, então você pode ter certeza de que suas NFTs sobreviverão *qualquer coisa!*

**Nota** Você *pode* use imagens na Internet regularmente de sites como Imgur, mas esses links podem quebrar e não são recomendados para NFTs.

Se você decidir usar sua própria imagem, verifique se o URL vai diretamente para a imagem real, não para o site que hospeda a imagem! Por exemplo, links diretos da Imgur são assim - `https://i.imgur.com/123123.png` NÃO `https://imgur.com/gallery/123123`. A maneira mais fácil de dizer é verificar se o URL termina em uma extensão de imagem como .png ou .jpg. Você pode clicar com o botão direito do mouse na imagem do imgur e em "copiar endereço da imagem". Isso fornecerá o URL correto.

Nosso contrato é muito bom agora. É bom o suficiente para podermos implantá-lo na rede de teste. Mas é apenas mais um contrato agora. Não há nada que diga a todos os aplicativos em execução no Flow que é um contrato da NFT.

Se o OpenSea decidisse que o BottomShot era a próxima grande novidade e que eles queriam listar as NFTs, eles teriam que revisar o contrato e examinar todas as funções e criar em torno dele. Isso é meio coxo, devemos seguir o mesmo padrão que outros projetos por aí! Nosso contrato não declara explicitamente que atende a quaisquer padrões da NFT. Vamos consertar isso!

### 🎟O padrão NonFungibleToken

O ecossistema Flow tem um padrão para as NFTs com as quais todos concordaram. A equipe Flow o implantou no [rede de teste e rede principal](https://docs.onflow.org/core-contracts/non-fungible-token/), portanto, quando você chegar a essas redes, poderá importar apenas do endereço existente. Por enquanto, vamos implantá-lo em nosso emulador.

Nós vamos importar um *interface* para tokens não fungíveis que implementa o padrão para todas as NFTs no fluxo. Se você vem de Ethereum, é como o ERC721 do Flow.

Cabeça [aqui](https://github.com/onflow/flow-nft/blob/master/contracts/NonFungibleToken.cdc) para o código. Faça um novo arquivo chamado `NonFungibleToken.cdc` na pasta de contratos e adicione o código.

Depois de copiado, clique no botão "implantar" na parte superior do arquivo NonFungibleToken.cdc. Isso implantará essa interface de contrato na conta de serviço padrão - `0xf8d6e0586b0a20c7`

Volte ao arquivo do contrato para poder importá-lo na parte superior e adicionar várias outras alterações:

`// We're importing the contract from the default service account
import NonFungibleToken from 0xf8d6e0586b0a20c7;

// Here we tell Cadence that our BottomShot contract implements the interface
pub contract BottomShot: NonFungibleToken {

  pub var totalSupply: UInt64

  pub event ContractInitialized()
  pub event Withdraw(id: UInt64, from: Address?)
  pub event Deposit(id: UInt64, to: Address?)

  pub let CollectionStoragePath: StoragePath
  pub let CollectionPublicPath: PublicPath

  // Our NFT resource conforms to the INFT interface
  pub resource NFT: NonFungibleToken.INFT {
    pub let id: UInt64

    pub let name: String
    pub let description: String
    pub let thumbnail: String

    init(
        id: UInt64,
        name: String,
        description: String,
        thumbnail: String,
    ) {
        self.id = id
        self.name = name
        self.description = description
        self.thumbnail = thumbnail
    }
  }

  pub resource interface BottomShotCollectionPublic {
    pub fun deposit(token: @NonFungibleToken.NFT)
    pub fun getIDs(): [UInt64]
    pub fun borrowNFT(id: UInt64): &NonFungibleToken.NFT
  }

  // Same goes for our Collection, it conforms to multiple interfaces 
  pub resource Collection: BottomShotCollectionPublic, NonFungibleToken.Provider, NonFungibleToken.Receiver, NonFungibleToken.CollectionPublic {
    pub var ownedNFTs: @{UInt64: NonFungibleToken.NFT}

    init () {
      self.ownedNFTs <- {}
    }
    
    // An upgraded deposit function
    pub fun deposit(token: @NonFungibleToken.NFT) {
      let token <- token as! @BottomShot.NFT

      let id: UInt64 = token.id

      // Add the new token to the dictionary, this removes the old one
      let oldToken <- self.ownedNFTs[id] <- token
      
      // Trigger an event to let listeners know an NFT was deposited to this collection
      emit Deposit(id: id, to: self.owner?.address)
      
      // Destroy (burn) the old NFT
      destroy oldToken
    }

    pub fun withdraw(withdrawID: UInt64): @NonFungibleToken.NFT {
      let token <- self.ownedNFTs.remove(key: withdrawID) ??
        panic("This collection doesn't contain an NFT with that ID")

      emit Withdraw(id: token.id, from: self.owner?.address)

      return <-token
    }

    // getIDs returns an array of the IDs that are in the collection
    pub fun getIDs(): [UInt64] {
      return self.ownedNFTs.keys
    }

    pub fun borrowNFT(id: UInt64): &NonFungibleToken.NFT {
      return (&self.ownedNFTs[id] as &NonFungibleToken.NFT?)!
    }

    destroy() {
      destroy self.ownedNFTs
    }
  }

  pub fun createEmptyCollection(): @NonFungibleToken.Collection {
    return <- create Collection()
  }

  // Mints a new NFT with a new ID and deposits it 
  // in the recipients collection using their collection reference
  pub fun mintNFT(
    recipient: &{NonFungibleToken.CollectionPublic},
    name: String,
    description: String,
    thumbnail: String,
  ) {
    // create a new NFT
    var newNFT <- create NFT(
      id: BottomShot.totalSupply,
      name: name,
      description: description,
      thumbnail: thumbnail
    )

    // Deposit it in the recipient's account using their collection ref
    recipient.deposit(token: <-newNFT)

    BottomShot.totalSupply = BottomShot.totalSupply + UInt64(1)
  }

  init() {
    self.totalSupply = 0

    self.CollectionStoragePath = /storage/BottomShotCollection
    self.CollectionPublicPath = /public/BottomShotCollection

    // Create a Collection for the deployer
    let collection <- create Collection()
    self.account.save(<-collection, to: self.CollectionStoragePath)

    // Create a public capability for the collection
    self.account.link<&BottomShot.Collection{NonFungibleToken.CollectionPublic, BottomShot.BottomShotCollectionPublic}>(
      self.CollectionPublicPath,
      target: self.CollectionStoragePath
    )

    emit ContractInitialized()
  }
}`

Se a extensão Cadence gritar com você, reinicie o servidor na paleta de comandos (`CMD`/`CTRL`+`SHIFT`+`P`):

[https://camo.githubusercontent.com/6ffe413548d41c95c785708d28f73afd876d349e6db6ea26df0265425dfb02e0/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f726b444f636e4f59712e706e67](https://camo.githubusercontent.com/6ffe413548d41c95c785708d28f73afd876d349e6db6ea26df0265425dfb02e0/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f726b444f636e4f59712e706e67)

Woah. Parece muito código. Não se preocupe, você sabe a maior parte! Além disso, a Cadence torna as novas peças muito simples. Vamos dividir linha por linha.

`import NonFungibleToken from 0xf8d6e0586b0a20c7;

pub contract BottomShot: NonFungibleToken {`

Como mencionado anteriormente, estamos importando a interface NonFungibleToken da conta para a qual foi implantada. Nós o usamos ao declarar nosso contrato, informando ao compilador que nosso contrato atende aos requisitos da interface.

O que é uma interface? É um tipo abstrato que define o comportamento das coisas que o implementam. Ainda confuso? Eu também lol.

Você pode pensar em uma interface como um conjunto de regras ou uma descrição. Imagine a interface para um sedan: 4 portas, um corpo fechado ( sem teto aberto ) e uma seção separada para o motorista e os passageiros. Se eu lhe mostrasse um BMW chique de 2 portas sem teto, você me diria que não é um sedan! Da mesma forma, as interfaces de contrato nos ajudam a definir e definir padrões. Se eu declarar que meu contrato atende à interface NFT, mas não implemente todos os requisitos, ele não será executado.

  `pub var totalSupply: UInt64

  pub event ContractInitialized()
  pub event Withdraw(id: UInt64, from: Address?)
  pub event Deposit(id: UInt64, to: Address?)
  
  pub let CollectionStoragePath: StoragePath
  pub let CollectionPublicPath: PublicPath`

Em seguida, temos várias novas variáveis. Já sabemos `totalSupply`.

O `event` type is new - é basicamente um webhook no blockchain. Podemos acionar / emitir esses eventos em determinados lugares para indicar que algo aconteceu. Os argumentos que aprovamos também são emitidos, facilitando o rastreamento das transferências da NFT neste contrato. Os dois últimos são apenas caminhos de armazenamento para onde estamos armazenando nossa coleção NFT. Armazená-los no contrato significa que scripts e transações não terão que codificá-los repetidamente.

  `pub resource NFT: NonFungibleToken.INFT {
    pub let id: UInt64

    pub let name: String
    pub let description: String
    pub let thumbnail: String

    init(
        id: UInt64,
        name: String,
        description: String,
        thumbnail: String,
    ) {
        self.id = id
        self.name = name
        self.description = description
        self.thumbnail = thumbnail
    }
  }`

Nosso recurso NFT evoluiu! Agora ele tem um nome, uma descrição e uma miniatura. Estes são para se ajustar à nova interface - a NFT agora implementa `INFT` do padrão NonFungibleToken. Todos esses valores são definidos na criação e são todos `let`, o que significa que você não pode alterar o nome, o ID ou a miniatura depois!

  `pub resource interface BottomShotCollectionPublic {
    pub fun deposit(token: @NonFungibleToken.NFT)
    pub fun getIDs(): [UInt64]
    pub fun borrowNFT(id: UInt64): &NonFungibleToken.NFT
  }`

Nossa prática interface de coleção pública também adicionou algumas funções extras. Desde que estamos fazendo `deposit` público, qualquer pessoa pode adicionar NFTs a esta coleção. Se você não gosta de spam, pode remover `deposit`! Eu vou mantê-lo, porque quem sabe o que eu posso ter no ar lol

Também estamos divulgando publicamente uma função de "emprestamento. Isto é da interface, vamos revisar isso quando chegarmos a ela.

`pub resource Collection: BottomShotCollectionPublic, NonFungibleToken.Provider, NonFungibleToken.Receiver, NonFungibleToken.CollectionPublic {`

Nosso principal recurso de coleta muda bastante. Primeiro, implementamos algumas interfaces do padrão NonFungibleToken. Você pode pular no arquivo do contrato para ver o que são.

Em seguida, temos a nova função de depósito:

  `pub fun deposit(token: @NonFungibleToken.NFT) {
    let token <- token as! @BottomShot.NFT

    let id: UInt64 = token.id

    // Add the new token to the dictionary which removes the old one
    let oldToken <- self.ownedNFTs[id] <- token

    emit Deposit(id: id, to: self.owner?.address)

    destroy oldToken
  }`

Aqui estamos duas coisas novas: digitação ( tipos de conversão ) com `as!` e destruindo explicitamente as antigas NFTs. Por que isso é necessário? Porque Cadence não sabe disso `ownedNFTs[id]` vai ficar vazio. Portanto, obtemos o recurso desse índice ( mesmo que ele não exista ) e depois movemos o novo.

Nosso `withdraw` função é bem semelhante, apenas tem um evento e usa novos tipos. `getIDs` é idêntico.

Finalmente, vemos algo novo:

    `pub fun borrowNFT(id: UInt64): &NonFungibleToken.NFT {
      return (&self.ownedNFTs[id] as &NonFungibleToken.NFT?)!
    }`

Como o nome sugere, essa função permite que qualquer pessoa peça emprestada uma NFT de uma coleção. Usando-o, buscamos uma referência a um objeto em armazenamento sem removê-lo do armazenamento. Isso significa que podemos acessar facilmente todos os dados públicos que o recurso possui, como a descrição e a miniatura!

Repartição da sintaxe:

- `pub fun borrowNFT(id: UInt64)UInt64`
    
    : Uma função pública chamada emprestnNFT que contém um argumento "id" do tipo
    
- `&NonFungibleToken.NFT`*referência*`NonFungibleToken.NFT`
    
    : Retorna um
    
    do tipo
    
- `&self.ownedNFTs[id]`
    
    : Isso obtém uma referência ao recurso armazenado no índice [ id ] em NFTs próprios
    
- `as &NonFungibleToken.NFT?`
    
    : Convertendo tipos
    
- `!`[Force Unwrap](https://docs.onflow.org/cadence/language/values-and-types/#force-unwrap-)
    
    :
    
    o valor em ( ) antes de devolvê-lo
    

Finalmente, temos a nova função de hortelã:

  `pub fun mintNFT(
    recipient: &{NonFungibleToken.CollectionPublic},
    name: String,
    description: String,
    thumbnail: String,
  ) {
    // create a new NFT
    var newNFT <- create NFT(
      id: BottomShot.totalSupply,
      name: name,
      description: description,
      thumbnail: thumbnail
    )

    // deposit it in the recipient's account using their reference
    recipient.deposit(token: <-newNFT)

    BottomShot.totalSupply = BottomShot.totalSupply + UInt64(1)
  }`

Isso é bem direto. A principal coisa que você quer prestar atenção é a `thumbnail` campo. Ele armazena a localização da mídia para nossas NFTs. Pode ser um link HTTP simples ou um hash IPFS.

Agora **isto** é um contrato que vale a pena implantar!

### 👀Hortelã e NFT localmente

Vamos fazer uma transação rápida para verificar se nossa função de hortelã funciona. Crie um `mintNFT.cdc` arquive na pasta de transações e adicione o seguinte:

`import BottomShot from 0xf8d6e0586b0a20c7
import NonFungibleToken from 0xf8d6e0586b0a20c7

// Our transaction object now takes in arguments!
transaction(
    recipient: Address,
    name: String,
    description: String,
    thumbnail: String,
) {
  prepare(signer: AuthAccount) {
    // Check if the user sending the transaction has a collection
    if signer.borrow<&BottomShot.Collection>(from: BottomShot.CollectionStoragePath) != nil {
        // If they do, we move on to the execute stage
        return
    }

    // If they don't, we create a new empty collection
    let collection <- BottomShot.createEmptyCollection()

    // Save it to the account
    signer.save(<-collection, to: BottomShot.CollectionStoragePath)

    // Create a public capability for the collection
    signer.link<&{NonFungibleToken.CollectionPublic}>(
        BottomShot.CollectionPublicPath,
        target: BottomShot.CollectionStoragePath
    )
  }

  execute {
    // Borrow the recipient's public NFT collection reference
    let receiver = getAccount(recipient)
        .getCapability(BottomShot.CollectionPublicPath)
        .borrow<&{NonFungibleToken.CollectionPublic}>()
        ?? panic("Could not get receiver reference to the NFT Collection")

    // Mint the NFT and deposit it to the recipient's collection
    BottomShot.mintNFT(
        recipient: receiver,
        name: name,
        description: description,
        thumbnail: thumbnail,
    )
    
    log("Minted an NFT and stored it into the collection")
  } 
}`

Esta é uma transação mais pesada, repasse os comentários! Faz duas coisas: cria uma coleção vazia, se necessário *e* cunha a NFT. Você deve estar familiarizado com a maior parte da sintaxe aqui. Se algo o confundir, basta marcar @Raza em # section-1-help no Discord e eu irei em seu socorro 😎

Não poderemos executar isso com a extensão. Digite isso no seu terminal ( para atualizar os argumentos! ):

`flow transactions send ./transactions/mintNFT.cdc "0xf8d6e0586b0a20c7" "CatMoji #1" "Cat emojis on the blockchain" "ipfs://bafybeigmeykxsur4ya2p3nw6r7hz2kp3r2clhvzwiqaashhz5efhewkkgu/0.png"`

Aqui estou criando uma NFT com o nome "CatMoji # 1" e configurando a miniatura para o hash do IPFS. Se você não estiver usando o IPFS, poderá substituí-lo pelo seu link regular da Internet, como `https://i.imgur.com/123123.png`.

A sintaxe é

`flow transactions send <code filename> [<argument> <argument>...] [flags]`

Você verá uma confirmação impressa:

[https://camo.githubusercontent.com/bcaac2414f79aab21b88c86fc8eb14ea7291076eb5ee6378fe6df42ee8158129/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f48795a555a735a35632e706e67](https://camo.githubusercontent.com/bcaac2414f79aab21b88c86fc8eb14ea7291076eb5ee6378fe6df42ee8158129/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f48795a555a735a35632e706e67)

**ESTAMOS FAZENDO.** Estamos fazendo NFTs no Flow! Vamos sair do localhost a seguir!

### 🚨Relatório de andamento 🚨

Estamos no último trecho.

Poste uma captura de tela dos ativos NFT carregados no IPFS no NFT.storage!