Nosso aplicativo está tomando forma. Você pode cunhar um gato imortal chique NFT que ninguém pode tirar de você. Mas poderia ter algo mais: poderia exibir nossas NFTs.

Para fazer isso, precisamos *olha* na mídia que a NFT possui. Como descobriríamos como fazer isso?

Nós olharíamos para o contrato, veríamos que ele tem um `thumbnail` campo e descubra quais funções acessíveis nos dão `thumbnail` valor. Mas e se o OpenSea achar que éramos muito legais e decidir nos listar? Eles teriam que fazer o mesmo! Imagine quanto trabalho seria para o OpenSea.

Isso é semelhante ao problema que tivemos com nosso primeiro contrato. Não seguia um padrão para que ninguém pudesse ser *certo* era um contrato da NFT.

Nosso contrato atual não segue nenhum **Metadados** padrões, para que ninguém descubra facilmente que tipo de mídia nossas NFTs têm e como você pode acessá-la.

A solução? Outra interface! O padrão MetadataViews permite que todos os projetos da NFT falem um idioma comum.

Volte para o seu arquivo de contrato, vamos implementar este padrão!

Como não há muitas mudanças, coloquei o contrato em um Gist, obtê-lo de [aqui](https://gist.github.com/AlmostEfficient/28d2fa6bdf8c920cbbc03f1b9f770b5f).

### 🔍Visualizações

A grande adição neste padrão são Views. Em palavras simples, uma visualização é uma descrição de como uma NFT pode ser visualizada. Uma visão básica seria um nome, uma descrição e uma miniatura.

Aqui estão as duas principais funções adicionadas:

    `pub fun getViews(): [Type] {
      return [
        Type<MetadataViews.Display>()
      ]
    }

    pub fun resolveView(_ view: Type): AnyStruct? {
      switch view {
        case Type<MetadataViews.Display>():
          return MetadataViews.Display(
            name: self.name,
            description: self.description,
            thumbnail: MetadataViews.HTTPFile(
              url: self.thumbnail
            )
          )
      }
      return nil
    }
  }`

A primeira função - `getViews` - retorne uma matriz de Tipo. Isso nos diz todos os tipos que a NFT pode ser vista.

Uma comparação grosseira é pensar na NFT como uma paisagem. Cada vista é uma janela olhando para a paisagem, mostrando diferentes partes dela.

[https://camo.githubusercontent.com/404e6f6c0e3224c42b114b949f5abaa8d43b1d8877e54a3fba49f04c8739654f/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f42793048775a4d39712e6a7067](https://camo.githubusercontent.com/404e6f6c0e3224c42b114b949f5abaa8d43b1d8877e54a3fba49f04c8739654f/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f42793048775a4d39712e6a7067)

Seu contrato pode ter quantas visualizações você quiser, cada uma mostrando uma parte exclusiva da NFT.

O `resolveView` função recebe um tipo de visualização e pode retribuir *qualquer coisa*. Ele joga a vista em um interruptor. Essa opção é onde você adicionaria novas resoluções de visualização.

O `_` no `resolveView` definição de função antes da visualização é uma [rótulo de argumento especial](https://docs.onflow.org/cadence/language/functions/#:~:text=parameter%20name.%20The-,special%20argument%20label,-_%20indicates%20that) o que indica que uma chamada de função pode omitir o rótulo do argumento.

Isso significa que, ao chamar a função, você não precisa incluir o rótulo.

`contract.function{
  label: value,
  name: "CatMoji"
}`

Finalmente, temos uma função de empréstimo para o `viewResolver`:

`pub fun borrowViewResolver(id: UInt64): &AnyResource{MetadataViews.Resolver} {
  let nft = (&self.ownedNFTs[id] as auth &NonFungibleToken.NFT?)!
  let CatMoji = nft as! &CatMoji.NFT
  return CatMoji as &AnyResource{MetadataViews.Resolver}
}`

Isso permite que qualquer pessoa passe em um ID e obtenha uma referência ao resolvedor para que ele possa buscar os metadados desse NFT: )

Confira a proposta de metadados [aqui](https://forum.onflow.org/t/introducing-nft-metadata-on-flow/2798) e todo o padrão [aqui](https://github.com/onflow/flow/blob/master/flips/20210916-nft-metadata.md).

É isso aí! Seu contrato agora está superpowered, compatível com tudo e pronto para a implantação final!

### 🚀Implante nosso contrato final

Você notará que o nome deste contrato é diferente. Também estamos usando diferentes caminhos de armazenamento.

Isso é porque estamos **não** vai atualizar nosso antigo contrato. Vamos implantar este contrato final como um novo.

As etapas para implantar são as mesmas, aqui está uma recapitulação rápida:

1. Crie um novo arquivo de contrato. Estou nomeando o meu `CatMoji.cdc`
    
    - o nome real da minha coleção
    
2. Adicione o contrato ao Gist ( certifique-se de atualizar o nome da coleção dentro do contrato para o seu !!! )
3. Adicione o arquivo no `contracts`
    
    in flow.json
    
4. Substitua o nome do contrato em `deployments`
5. Use o `flow project deploy --network testnet`
    
    comando para implantar
    

Aqui está o que meu fluxo final.json se parece:

`{
  "emulators": {
    "default": {
      "port": 3569,
      "serviceAccount": "emulator-account"
    }
  },
  "contracts": {
		"BottomShot": "./contracts/BottomShot.cdc",
        "CatMoji": "./contracts/CatMoji.cdc"
	},
    "networks": {
    "emulator": "127.0.0.1:3569",
    "mainnet": "access.mainnet.nodes.onflow.org:9000",
    "testnet": "access.devnet.nodes.onflow.org:9000"
  },
  "accounts": {
    "emulator-account": {
      "address": "f8d6e0586b0a20c7",
      "key": "3314a7fca73b5c091da87027265180a5d85e6930047f2d9bf98cc979d52c5022"
    },
    "testnet-account": {
      "address": "YOUR_ADDRESS_HERE",
      "key": "YOUR_PRIVATE_KEY_HERE"
    }
  },
	"deployments": {
    "testnet": {
      "testnet-account": [
        "CatMoji"
      ]
    }
  }
}`

Agora se você for para [Fonte da visualização de fluxo](https://flow-view-source.com/testnet/) você verá seu contrato antigo e novo!

[https://camo.githubusercontent.com/b7608d579b643da86729cf25c65cfb6f346ce3e845ca0660d99db3210b6e0c59/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f72796e63614e5135632e706e67](https://camo.githubusercontent.com/b7608d579b643da86729cf25c65cfb6f346ce3e845ca0660d99db3210b6e0c59/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f72796e63614e5135632e706e67)

Se você deseja atualizar este contrato posteriormente, pode usar o mesmo comando, mas com um sinalizador adicional:

`flow project deploy --network testnet --update`

Isso atualizará seu contrato no mesmo endereço e nome 🤠

Agora que está feito, podemos fazer uso da nova visão chique!

### 🚨Relatório de andamento 🚨

Uau. Não um, mas dois contratos implantados no testnet?? Você provavelmente está entre os 10% melhores dos desenvolvedores de cadência no momento 😎

Mais uma vez, publique o URL do seu contrato implantado no flow-view-source em #progress. Isso mostrará às pessoas todos os outros contratos que estão por aí. Talvez alguém possa aprender com todas as coisas chiques que você fez!