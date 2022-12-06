# 

Hora de fazer nossas NFTs brilharem. Vamos usar as visualizações de metadados que adicionamos para buscar os metadados das NFTs que cunhamos e exibi-las em nosso aplicativo React.

### 💝Atualize nossa transação de hortelã

Agora que temos um novo contrato, precisamos atualizar nossa transação de hortelã. Abra o `mintNFT_tx.js` atualize o arquivo:

`export const mintNFT = 
`
// REPLACE THIS WITH YOUR CONTRACT NAME + ADDRESS
import CatMoji from 0x7b6adb682517f137 

// Do not change these
import NonFungibleToken from 0x631e88ae7f1d7c20
import MetadataViews from 0x631e88ae7f1d7c20

transaction(
  recipient: Address,
  name: String,
  description: String,
  thumbnail: String,
) {
  prepare(signer: AuthAccount) {
    if signer.borrow<&CatMoji.Collection>(from: CatMoji.CollectionStoragePath) != nil {
      return
    }

    // Create a new empty collection
    let collection <- CatMoji.createEmptyCollection()

    // save it to the account
    signer.save(<-collection, to: CatMoji.CollectionStoragePath)

    // create a public capability for the collection
    signer.link<&{NonFungibleToken.CollectionPublic, MetadataViews.ResolverCollection}>(
      CatMoji.CollectionPublicPath,
      target: CatMoji.CollectionStoragePath
    )
  }

  execute {
    // Borrow the recipient's public NFT collection reference
    let receiver = getAccount(recipient)
      .getCapability(CatMoji.CollectionPublicPath)
      .borrow<&{NonFungibleToken.CollectionPublic}>()
      ?? panic("Could not get receiver reference to the NFT Collection")

    // Mint the NFT and deposit it to the recipient's collection
    CatMoji.mintNFT(
      recipient: receiver,
      name: name,
      description: description,
      thumbnail: thumbnail,
    )
    
    log("Minted an NFT and stored it into the collection")
  } 
}
``

A primeira mudança é apenas o nome - eu mudei de `BottomShot` em todo lugar para `CatMoji`, porque esse é o nome no meu contrato. Em seguida, importei o `MetadataViews` do mesmo endereço que o `NonFungibleToken` no testnet.

A última alteração que fiz aqui é o link de capacidade pública para a coleção

`signer.link<&{NonFungibleToken.CollectionPublic, MetadataViews.ResolverCollection}>(`

Isso é para que possamos emprestar nossa coleção NFT como o tipo MetadataViews para buscar a miniatura!

### ✍Buscar metadados com scripts

Em seguida, criaremos dois scripts. Faça um `getID_script.js` arquivo na pasta de scripts. É bem simples:

`export const getIDs = `
import MetadataViews from 0x631e88ae7f1d7c20;

pub fun main(address: Address): [UInt64] {
    
  let account = getAccount(address)

  let collection = account
    .getCapability(/public/CatMojiCollection)
    .borrow<&{MetadataViews.ResolverCollection}>()
    ?? panic("Could not borrow a reference to the collection")

  let IDs = collection.getIDs()
  return IDs;
}
`;`

Passamos em um endereço de usuário, obtemos sua conta, obtemos o recurso de que precisamos e depois emprestamos a coleção de resolvedores de metadados. Cadence não é bonita?

Para buscar os metadados, crie um arquivo chamado `getMetadata_script.js` na pasta de scripts (**lembre-se de atualizar o caminho para corresponder ao seu contrato**):

`export const getMetadata = 
`
import MetadataViews from 0x631e88ae7f1d7c20;

pub fun main(address: Address, id: UInt64): NFTResult {
  
  let account = getAccount(address)

  let collection = account
      .getCapability(/public/CatMojiCollection) // Update the path here!
      .borrow<&{MetadataViews.ResolverCollection}>()
      ?? panic("Could not borrow a reference to the collection")

  let nft = collection.borrowViewResolver(id: id)

  var data = NFTResult()

  // Get the basic display information for this NFT
  if let view = nft.resolveView(Type<MetadataViews.Display>()) {
    let display = view as! MetadataViews.Display

    data.name = display.name
    data.description = display.description
    data.thumbnail = display.thumbnail.uri()
  }

  // The owner is stored directly on the NFT object
  let owner: Address = nft.owner!.address

  data.owner = owner

  return data
}

pub struct NFTResult {
  pub(set) var name: String
  pub(set) var description: String
  pub(set) var thumbnail: String
  pub(set) var owner: Address
  pub(set) var type: String

  init() {
    self.name = ""
    self.description = ""
    self.thumbnail = ""
    self.owner = 0x0
    self.type = ""
  }
}
``

Isso começa exatamente como o `getID` roteiro. A parte interessante é esta parte:

  `// Get the basic display information for this NFT
  if let view = nft.resolveView(Type<MetadataViews.Display>()) {
    let display = view as! MetadataViews.Display

    data.name = display.name
    data.description = display.description
    data.thumbnail = display.thumbnail.uri()
  }

  // The owner is stored directly on the NFT object
  let owner: Address = nft.owner!.address

  data.owner = owner

  return data`

A primeira linha é uma [ligação opcional](https://docs.onflow.org/cadence/language/control-flow/#optional-binding) - pense nisso como uma afirmação sofisticada que faz duas coisas: verifica se existe um valor e, se existir, atribui ( vincula ) esse valor a uma variável.

Recapitulação rápida - um [opcional](https://docs.onflow.org/cadence/language/values-and-types/#optionals) é um valor que pode representar a ausência de um valor. ou seja, em vez de dizer que um valor é indefinido, ele afirma explicitamente que um valor está ausente. Se os booleanos podem ser verdadeiros ou falsos, os opcionais podem estar ausentes ou ter um valor.

Com esta declaração if, verificamos se `nft.resolveView(Type<MetadataViews.Display>())` ( um tipo opcional ) tem um valor. Se isso acontecer, esse valor será temporariamente atribuído a `view` e o código na instrução if é executado.

O conteúdo da instrução if é bastante simples. Nós [força desembrulhar](https://docs.onflow.org/cadence/language/values-and-types/#force-unwrap-) a visualização e extrair os metadados dela. Ezpz!

### 🎁Use os scripts em nosso aplicativo

Comece importando dois scripts que acabamos de criar:

`import { getMetadata } from "./cadence/scripts/getMetadata_script";
import { getIDs } from "./cadence/scripts/getID_script";`

Crie uma matriz com estado chamada `images` na parte superior do seu componente.

`const [ images, setImages ] = useState([])`

Em seguida, temos o `fetchNFTs` função, você pode colocar isso ao lado da sua função de hortelã:

  `const fetchNFTs = async () => {
    // Empty the images array
    setImages([]);
    let IDs = [];
    
    // Fetch the IDs with our script (no fees or signers necessary)
    try {
      IDs = await fcl.query({
        cadence: `${getIDs}`,
        args: (arg, t) => [
          arg(user.addr, types.Address), 
        ],
      })
    } catch(err) {
      console.log("No NFTs Owned")
    }
    
    let _imageSrc = []
    try{
      for(let i=0; i<IDs.length; i++) {
        const result = await fcl.query({
          cadence: `${getMetadata}`,
          args: (arg, t) => [
            arg(user.addr, types.Address), 
            arg(IDs[i].toString(), types.UInt64),
          ],
        })
        // If the source is an IPFS link, remove the "ipfs://" prefix
        if (result["thumbnail"].startsWith("ipfs://")) {
          _imageSrc.push(result["thumbnail"].substring(7))
          // Add a gateway prefix
          _imageSrc[i] = "https://nftstorage.link/ipfs/" + _imageSrc[i]
        }
        else {
          _imageSrc.push(result["thumbnail"])
        }
      }
    } catch(err) {
      console.log(err)
    }
    
    if(images.length < _imageSrc.length) {
      setImages((Array.from({length: _imageSrc.length}, (_, i) => i).map((number, index)=>
        <img style={{margin:"10px", height: "150px"}} src={_imageSrc[index]} key={number} alt={"NFT #"+number}
        />
      )))
    }
  }`

Isso faz 3 coisas:

1. Obtém os IDs de token do contrato
2. Define os metadados NFT para cada token com o ID
3. Atualiza o link da miniatura se estivermos usando o IPFS
4. Adiciona um componente de imagem a uma matriz de imagens para cada NFT

Tudo isso é apenas Javascript com duas funções de consulta usando FCL. Nada de novo aqui!

Tudo o que precisamos fazer agora é renderizar nossas imagens e chamar o `fetchNFTs` função. Acabei de colocar minha chamada de busca em um Effect ao lado do outro useEffect:

  `useEffect(() => {
    if (user && user.addr) {
      fetchNFTs();
    }
  }
  , [user]);`

Para simplificar, acabei de adicionar as imagens ao meu componente de hortelã:

  `const RenderMint = () => {
    return (
      <div>
        <div className="button-container">
          <button className="cta-button button-glow" onClick={() => mint()}>
            Mint
          </button>
        </div>
        {images.length > 0 ? 
          <>
            <h2>Your NFTs</h2>
              <div className="image-container">
                {images}
              </ div>
          </> 
        : ""}
    </div>
    );
  }`

Você precisará limpar as imagens carregadas no logout para que, se o usuário trocar carteiras, não verá suas NFTs antigas:

  `const logOut = () => {
    setImages([]);
    fcl.unauthenticate();
  };`

Finalmente, adicionei alguns gifs para apimentar!

  `const RenderGif = () => {
    const gifUrl = user?.addr
        ? "https://media.giphy.com/media/VbnUQpnihPSIgIXuZv/giphy-downsized.gif"
        : "https://i.giphy.com/media/Y2ZUWLrTy63j9T6qrK/giphy.webp";
    return <img className="gif-image" src={gifUrl} height="300px" alt="Funny gif"/>;
  };`

Juntando tudo, aqui está a final `App.js`:

`import React, { useState, useEffect } from "react";
import './App.css';
import twitterLogo from "./assets/twitter-logo.svg";
import * as fcl from "@onflow/fcl";
import * as types from "@onflow/types";
import { mintNFT } from "./cadence/transactions/mintNFT_tx";
import { getTotalSupply } from "./cadence/scripts/getTotalSupply_script";
import { getIDs } from "./cadence/scripts/getID_script";
import { getMetadata } from "./cadence/scripts/getMetadata_script";

const TWITTER_HANDLE = "_buildspace";
const TWITTER_LINK = `https://twitter.com/${TWITTER_HANDLE}`;

fcl.config({
  "flow.network": "testnet",
  "app.detail.title": "CatMoji", // Change the title!
  "accessNode.api": "https://rest-testnet.onflow.org",
  "app.detail.icon": "https://placekitten.com/g/200/200",
  "discovery.wallet": "https://fcl-discovery.onflow.org/testnet/authn",
});

function App() {

  const [ user, setUser ] = useState();
  const [ images, setImages ] = useState([])

  const logIn = () => {
    fcl.authenticate();
  };

  const logOut = () => {
    setImages([]);
    fcl.unauthenticate();
  };

  const RenderGif = () => {
    const gifUrl = user?.addr
        ? "https://media.giphy.com/media/VbnUQpnihPSIgIXuZv/giphy-downsized.gif"
        : "https://i.giphy.com/media/Y2ZUWLrTy63j9T6qrK/giphy.webp";
    return <img className="gif-image" src={gifUrl} height="300px" alt="Funny gif"/>;
  };

  const RenderLogin = () => {
    return (
      <div>
        <button className="cta-button button-glow" onClick={() => logIn()}>
          Log In
        </button>
      </div>
    );
  };

  const RenderMint = () => {
    return (
      <div>
        <div className="button-container">
          <button className="cta-button button-glow" onClick={() => mint()}>
            Mint
          </button>
        </div>
        {images.length > 0 ? 
          <>
            <h2>Your NFTs</h2>
              <div className="image-container">
                {images}
              </ div>
          </> 
        : ""}
    </div>
    );
  }

  const RenderLogout = () => {
    if (
      user && user.addr) {
      return (
        <div className="logout-container">
          <button className="cta-button logout-btn" onClick={() => logOut()}>
            ❎ {"  "}
            {user.addr.substring(0, 6)}...{user.addr.substring(user.addr.length - 4)}
          </button>
        </div>
      );
    }
    return undefined;
  };

  const fetchNFTs = async () => {
    // Empty the images array
    setImages([]);
    let IDs = [];
    
    // Fetch the IDs with a script (no fees or signers)
    try {
      IDs = await fcl.query({
        cadence: `${getIDs}`,
        args: (arg, t) => [
          arg(user.addr, types.Address), 
        ],
      })
    } catch(err) {
      console.log("No NFTs Owned")
    }
    
    let _imageSrc = []
    try{
      for(let i=0; i<IDs.length; i++) {
        const result = await fcl.query({
          cadence: `${getMetadata}`,
          args: (arg, t) => [
            arg(user.addr, types.Address), 
            arg(IDs[i].toString(), types.UInt64),
          ],
        })
        // If the source is an IPFS link, remove the "ipfs://" prefix
        if (result["thumbnail"].startsWith("ipfs://")) {
          _imageSrc.push(result["thumbnail"].substring(7))
          // Add a gateway prefix
          _imageSrc[i] = "https://nftstorage.link/ipfs/" + _imageSrc[i]
        }
        else {
          _imageSrc.push(result["thumbnail"])
        }
      }
    } catch(err) {
      console.log(err)
    }
    
    if(images.length < _imageSrc.length) {
      setImages((Array.from({length: _imageSrc.length}, (_, i) => i).map((number, index)=>
        <img style={{margin:"10px", height: "150px"}} src={_imageSrc[index]} key={number} alt={"NFT #"+number}
        />
      )))
    }
  }

  const mint = async() => {

    let _totalSupply;
    try {
      _totalSupply = await fcl.query({
        cadence: `${getTotalSupply}`
      })
    } catch(err) {console.log(err)}
    
    const _id = parseInt(_totalSupply) + 1;
    
    try {
      const transactionId = await fcl.mutate({
        cadence: `${mintNFT}`,
        args: (arg, t) => [
          arg(user.addr, types.Address), //address to which NFT should be minted
          arg("CatMoji # "+_id.toString(), types.String),
          arg("Cat emojis on the blockchain", types.String),
          arg("ipfs://bafybeigmeykxsur4ya2p3nw6r7hz2kp3r2clhvzwiqaashhz5efhewkkgu/"+_id+".png", types.String),
        ],
        proposer: fcl.currentUser,
        payer: fcl.currentUser,
        limit: 99
      })
      console.log("Minting NFT now with transaction ID", transactionId);
      const transaction = await fcl.tx(transactionId).onceSealed();
      console.log("Testnet explorer link:", `https://testnet.flowscan.org/transaction/${transactionId}`);
      console.log(transaction);
      alert("NFT minted successfully!")
    } catch (error) {
      console.log(error);
      alert("Error minting NFT, please check the console for error details!")
    }
  }

  useEffect(() => {
    fcl.currentUser().subscribe(setUser);  
  }, [])

  useEffect(() => {
    if (user && user.addr) {
      fetchNFTs();
    }
  }
  , [user]);

  return (
    <div className="App">
      <RenderLogout />
      <div className="container">
        <div className="header-container">
          <div className="logo-container">
            <img src="./logo.png" className="flow-logo" alt="flow logo"/>
            <p className="header">✨Awesome NFTs on Flow ✨</p>
          </div>
          <RenderGif/>
          <p className="sub-text">The easiest NFT mint experience ever!</p>
        </div>

        {user && user.addr ? <RenderMint /> : <RenderLogin />}

        <div className="footer-container">
            <img alt="Twitter Logo" className="twitter-logo" src={twitterLogo} />
            <a className="footer-text" href={TWITTER_LINK} target="_blank" rel="noreferrer">{`built on @${TWITTER_HANDLE}`}</a>
        </div>
      </div>
    </div>
  );
}

export default App;`

E aqui está o que parece!

[https://camo.githubusercontent.com/7efc523d68ec10b90939dd021d702a9dc0d948e604587f3febc8fb23597797f7/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f724a334751443771632e706e67](https://camo.githubusercontent.com/7efc523d68ec10b90939dd021d702a9dc0d948e604587f3febc8fb23597797f7/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f724a334751443771632e706e67)

### 🚨Relatório de andamento 🚨

Você veio por todo o caminho! BEM FEITO!

Poste uma captura de tela do seu aplicativo finalizado em #progress.